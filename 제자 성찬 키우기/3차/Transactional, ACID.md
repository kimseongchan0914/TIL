# 1. 트랜잭션 & ACID

---

# PART A. @Transactional 작동원리

## A-1. 한 줄 요약
`@Transactional`은 **AOP 프록시**로 동작한다. Spring이 대상 빈을 감싸는 프록시를 만들고, 메서드 호출 앞뒤에 "트랜잭션 시작 → 실행 → 커밋/롤백"을 끼워 넣는다.

## A-2. 전체 동작 구조
```
호출자 → [프록시 객체] → 실제 객체(Target)
             │
             ├─ 1. 트랜잭션 시작 (begin)
             ├─ 2. 실제 메서드 실행
             ├─ 3-a. 정상 종료 → commit
             └─ 3-b. 예외 발생 → rollback
```
프록시가 없으면 `@Transactional`은 아무 일도 안 한다.

## A-3. 내부 상세 흐름
1. **프록시가 호출을 가로챔** (`TransactionInterceptor`)
2. `PlatformTransactionManager`가 트랜잭션 시작 → Connection 획득 → `setAutoCommit(false)`
3. 실제 비즈니스 로직 실행
4. 정상 반환 → `commit()` / `RuntimeException`·`Error` → `rollback()`
5. Connection 반환

## A-4. 핵심 구성요소
| 구성요소 | 역할 |
|----------|------|
| `TransactionInterceptor` | 프록시가 호출을 가로채는 진입점 |
| `PlatformTransactionManager` | 실제 begin/commit/rollback 수행 |
| `TransactionSynchronizationManager` | 스레드마다 Connection 보관 (ThreadLocal) |

## A-5. 자주 나오는 함정
- **Checked Exception은 기본 롤백 X** → `rollbackFor = Exception.class` 필요
- **내부 호출(self-invocation)**: `this.method()`는 프록시를 안 거쳐 트랜잭션 무효
- **public 메서드에만** 적용 (private/protected X)
- **예외를 try-catch로 먹으면** 롤백 안 됨

## A-6. 주요 옵션
```java
@Transactional(
    propagation = Propagation.REQUIRED,   // 전파 방식
    isolation = Isolation.READ_COMMITTED, // 격리 수준
    readOnly = true,                       // 읽기 전용 최적화
    timeout = 10,                          // 타임아웃(초)
    rollbackFor = Exception.class          // 롤백 대상 예외 추가
)
```
- `REQUIRED`(기본): 기존 트랜잭션 있으면 참여, 없으면 새로 생성
- `REQUIRES_NEW`: 항상 새 트랜잭션 생성

---

# PART B. 롤백 심화

## B-1. 롤백 기본 동작
```
원본 메서드에서 예외 throw
      ↓
프록시가 예외를 catch
      ↓
롤백 대상 예외인지 판단 (rollbackOn)
      ↓
맞으면 connection.rollback() / 틀리면 connection.commit() ← 주의!
```

## B-2. 기본 롤백 규칙
| 예외 종류 | 기본 동작 |
|-----------|-----------|
| `RuntimeException`, `Error` | 롤백 O |
| `Exception` (checked) | **롤백 X → 커밋됨!** |

## B-3. 롤백하는 방법 4가지
```java
// 1) RuntimeException 던지기 (가장 기본)
@Transactional
public void save() {
    repository.save(entity);
    if (조건) throw new IllegalStateException("실패"); // ✅ 자동 롤백
}

// 2) checked 예외도 롤백 → rollbackFor
@Transactional(rollbackFor = Exception.class)
public void save() throws IOException { throw new IOException(); }

// 3) 특정 예외는 롤백 제외 → noRollbackFor
@Transactional(noRollbackFor = IllegalArgumentException.class)
public void save() { throw new IllegalArgumentException(); }

// 4) 코드로 직접 롤백
txTemplate.execute(status -> {
    repository.save(entity);
    if (조건) status.setRollbackOnly(); // ✅ 강제 롤백 표시
    return null;
});
```

## B-4. 롤백 흔한 실수
- **예외를 catch로 먹음** → 프록시가 예외를 못 봐서 커밋됨 (제일 흔함)
- **checked 예외인데 rollbackFor 안 붙임** → 커밋됨
- **내부 호출이라 트랜잭션 자체가 없음** → 롤백도 없음

## B-5. UnexpectedRollbackException (전파 함정)
`REQUIRED`로 묶인 트랜잭션에서 내부 메서드가 예외로 rollback-only 마킹되면, 바깥에서 커밋해도 롤백된다. → 트랜잭션 안 예외를 함부로 삼키지 말 것.

---

# PART C. DB 트랜잭션 vs 애플리케이션 트랜잭션

## C-1. 정의
- **DB 트랜잭션**: DBMS가 직접 제공 (`BEGIN`/`COMMIT`/`ROLLBACK`). ACID를 실제로 보장하는 주체.
- **애플리케이션 트랜잭션**: Spring이 DB 트랜잭션을 추상화 (`@Transactional`). 결국 내부에서 DB 트랜잭션을 호출.

## C-2. 관계도
```
[애플리케이션 트랜잭션]  ← Spring @Transactional (추상화, 편의 계층)
        │ 내부적으로 호출
        ▼
[DB 트랜잭션]  ← 실제 BEGIN/COMMIT/ROLLBACK (진짜 일 하는 곳)
        ▼
   [데이터베이스]
```

## C-3. 비교표
| 구분 | DB 트랜잭션 | 애플리케이션 트랜잭션 |
|------|-------------|----------------------|
| 주체 | DBMS | Spring |
| 제어 | SQL | `@Transactional` (AOP) |
| ACID | 직접 보장 | DB에 위임 |
| 추가기능 | 없음 | 전파, 롤백규칙, 여러 자원 묶기 |

## C-4. 애플리케이션 계층만 가능한 것
- **전파(Propagation)**: 기존 트랜잭션 참여 여부 제어
- **여러 리소스 묶기**: DB + 메시지큐 등 (JTA)
- **롤백 규칙 커스텀**: `rollbackFor`, `noRollbackFor`

---

# PART D. ACID (트랜잭션 4대 성질)

## D-1. 네 가지
| 성질 | 키워드 | 보장 내용 |
|------|--------|-----------|
| **Atomicity** (원자성) | All or Nothing | 전부 성공 or 전부 롤백 |
| **Consistency** (일관성) | 정합성 규칙 | 무결성 제약 항상 유지 |
| **Isolation** (격리성) | 간섭 없음 | 동시 트랜잭션 서로 독립 |
| **Durability** (지속성) | 영구 보존 | 커밋되면 장애에도 유지 |

## D-2. 각 성질 설명
- **원자성**: 계좌이체에서 "출금만 되고 입금 안 됨" 불가 → 실패 시 전부 롤백
- **일관성**: PK/FK/NOT NULL 등 무결성 제약이 트랜잭션 전후로 안 깨짐
- **격리성**: 동시 실행돼도 각자 혼자 실행된 것처럼 (아래 PART E)
- **지속성**: 커밋되면 정전/장애에도 유지 → **로그 선기록(WAL/Redo Log)** 으로 보장

---

# PART E. 트랜잭션 격리 수준 (Isolation Level) 분석

## E-1. 동시성 문제 3가지
| 문제 | 설명 |
|------|------|
| **Dirty Read** | 커밋 안 된 데이터를 읽음 |
| **Non-Repeatable Read** | 같은 행을 두 번 읽었는데 값이 다름 |
| **Phantom Read** | 같은 조건 조회인데 행 개수가 달라짐 |

## E-2. 4가지 격리 수준 정리표
| 격리 수준 | Dirty | Non-Repeatable | Phantom | 성능 |
|-----------|:-----:|:--------------:|:-------:|:----:|
| READ UNCOMMITTED | 발생 | 발생 | 발생 | 최고 |
| READ COMMITTED | 방지 | 발생 | 발생 | 높음 |
| REPEATABLE READ | 방지 | 방지 | 발생* | 중간 |
| SERIALIZABLE | 방지 | 방지 | 방지 | 낮음 |

\* MySQL InnoDB는 REPEATABLE READ에서도 Phantom을 대부분 방지

## E-3. DB별 기본값
| DB | 기본값 |
|----|--------|
| MySQL (InnoDB) | REPEATABLE READ |
| Oracle / PostgreSQL / SQL Server | READ COMMITTED |

## E-4. Spring 설정
```java
@Transactional(isolation = Isolation.READ_COMMITTED)
```
> 실제 적용은 DB가 수행. Spring은 값을 전달할 뿐.

## E-5. 상황별 선택 (분석)
- **일반 웹 서비스**: READ COMMITTED (대부분 충분)
- **정합성 매우 중요(금융/재고)**: REPEATABLE READ~SERIALIZABLE 또는 락 병행
- **읽기 통계/로그성**: 낮은 수준도 고려
- **팁**: 무작정 올리지 말고 비관적 락(`FOR UPDATE`)/낙관적 락(`@Version`)으로 특정 지점만 보호

---

# 최종 요약
- `@Transactional` = **프록시 + AOP**, 가로채는 실체는 `TransactionInterceptor`
- 롤백: RuntimeException 자동 / checked는 `rollbackFor` 필요 / 예외 삼키면 롤백 안 됨
- 애플리케이션 트랜잭션은 결국 **DB 트랜잭션을 호출**하는 추상화
- ACID = 원자성/일관성/격리성/지속성
- 격리 수준 = 동시성 vs 정합성 트레이드오프
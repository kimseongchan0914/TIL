# @Transactional 작동원리

## 1. 한 줄 요약

`@Transactional`은 **AOP 프록시**로 동작한다. Spring이 대상 빈을 감싸는 프록시를 만들고, 메서드 호출 앞뒤에 "트랜잭션 시작 → 실행 → 커밋/롤백" 로직을 끼워 넣는다.

---

## 2. 전체 동작 구조

```
호출자 → [프록시 객체] → 실제 객체(Target)
             │
             ├─ 1. 트랜잭션 시작 (begin)
             ├─ 2. 실제 메서드 실행
             ├─ 3-a. 정상 종료 → commit
             └─ 3-b. 예외 발생 → rollback
```

프록시가 없으면 `@Transactional`은 아무 일도 안 한다. 이게 모든 원리의 출발점이다.

---

## 3. 내부 상세 흐름

1. **프록시가 호출을 가로챔** (`TransactionInterceptor`)
2. `PlatformTransactionManager`가 트랜잭션 시작
   - DB `Connection` 획득
   - `connection.setAutoCommit(false)` — 자동 커밋 끔
3. 실제 비즈니스 로직(Target 메서드) 실행
4. 결과 처리
   - 정상 반환 → `connection.commit()`
   - `RuntimeException`/`Error` → `connection.rollback()`
5. `Connection` 반환(close)

---

## 4. 핵심 구성요소

| 구성요소 | 역할 |
|----------|------|
| `TransactionInterceptor` | 프록시가 호출을 가로채는 진입점 |
| `PlatformTransactionManager` | 실제 begin/commit/rollback 수행 (예: `DataSourceTransactionManager`, `JpaTransactionManager`) |
| `TransactionStatus` | 현재 트랜잭션의 상태 정보 |
| `TransactionSynchronizationManager` | 스레드마다 Connection을 보관 (ThreadLocal) |

---

## 5. 자주 나오는 함정 4가지

### (1) 기본 롤백 규칙 — Checked Exception은 롤백 안 됨

```java
@Transactional
public void save() throws Exception {
    repository.save(entity);
    throw new Exception("checked"); // ❌ 롤백 안 됨! 커밋됨
}
```

- `RuntimeException`, `Error` → 롤백 O
- **Checked Exception → 롤백 X** (기본값)
- 강제하려면:
```java
@Transactional(rollbackFor = Exception.class)
```

### (2) 내부 호출(self-invocation) 문제

같은 클래스 안에서 메서드를 직접 호출하면 `this.method()`가 되어 프록시를 안 거친다 → 트랜잭션 적용 안 됨.

```java
@Service
class MyService {
    public void outer() {
        inner(); // ❌ this.inner() → 프록시 통과 X
    }

    @Transactional
    public void inner() { ... } // 트랜잭션 적용 안 됨!
}
```

**해결법**
- 클래스를 분리해서 다른 빈으로 호출
- 자기 자신을 주입받아 호출 (`self.inner()`)
- `AopContext.currentProxy()` 사용

### (3) public 메서드에만 적용

프록시 방식(기본)에서는 `private`, `protected`, `default` 메서드엔 안 걸린다. **public만** 적용.

### (4) 예외를 try-catch로 먹어버리면 롤백 안 됨

```java
@Transactional
public void save() {
    try {
        repository.save(entity);
        throw new RuntimeException();
    } catch (Exception e) {
        // ❌ 예외를 여기서 삼킴 → 프록시가 예외를 못 봄 → 커밋됨
    }
}
```

---

## 6. 주요 옵션

```java
@Transactional(
    propagation = Propagation.REQUIRED,   // 전파 방식
    isolation = Isolation.READ_COMMITTED, // 격리 수준
    readOnly = true,                       // 읽기 전용 최적화
    timeout = 10,                          // 타임아웃(초)
    rollbackFor = Exception.class          // 롤백 대상 예외 추가
)
```

### 전파(Propagation) 핵심 2가지
- `REQUIRED` (기본): 기존 트랜잭션 있으면 참여, 없으면 새로 생성
- `REQUIRES_NEW`: 항상 새 트랜잭션 생성 (기존 건 잠시 보류)

### readOnly = true
- 조회 전용 메서드에 붙이면 성능 최적화 (변경 감지 스냅샷 안 만듦, JPA flush 생략)

---

## 7. 최종 요약

- `@Transactional` = **프록시 + AOP**
- 프록시를 안 거치면(내부호출/private) 동작 안 함
- Checked Exception은 기본 롤백 X → `rollbackFor` 필요
- 예외를 catch로 먹으면 롤백 안 됨
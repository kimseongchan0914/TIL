# 2. AOP & 프록시

---

# PART A. AOP (Aspect Oriented Programming)

## A-1. 한 줄 요약
**관점 지향 프로그래밍.** 여러 곳에 반복되는 부가 기능(트랜잭션, 로깅, 보안)을 한 곳에 모아 핵심 로직과 **분리**하는 기법.

## A-2. 왜 필요한가?
```java
// AOP 없이 - 부가 기능이 모든 메서드에 중복
public void order() {
    log.info("start"); tx.begin();
    try { orderRepository.save(order); tx.commit(); }
    catch (Exception e) { tx.rollback(); }
    log.info("end");
}

// AOP 사용 - 핵심 로직만 남음
@Transactional
public void order() { orderRepository.save(order); }
```

## A-3. 핵심 용어 (필수 암기)
| 용어 | 뜻 |
|------|-----|
| **Aspect** | 부가 기능 모듈 |
| **Advice** | 실제 수행할 부가 기능 코드 |
| **Join Point** | Advice가 끼어들 수 있는 지점 (메서드 호출 등) |
| **Pointcut** | Advice를 적용할 Join Point 선별 조건 |
| **Target** | 부가 기능이 적용되는 실제 객체 |
| **Weaving** | Advice를 핵심 로직에 끼워 넣는 과정 |

```
Pointcut(어디에?) + Advice(무엇을?) = Aspect(부가기능)
        │ Weaving(끼워넣기)
        ▼
    Target(원본 객체)
```

## A-4. Advice 종류 (실행 시점)
| Advice | 시점 |
|--------|------|
| `@Before` | 실행 전 |
| `@AfterReturning` | 정상 반환 후 |
| `@AfterThrowing` | 예외 발생 시 |
| `@After` | 항상 (finally) |
| `@Around` | 전/후 모두 (가장 강력) |

`@Transactional`은 개념적으로 `@Around` (전에 begin, 후에 commit/rollback).

## A-5. 간단 예제 (로깅 AOP)
```java
@Aspect
@Component
public class LogAspect {
    @Around("execution(* com.example.service..*(..))") // Pointcut
    public Object logging(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        Object result = joinPoint.proceed();     // 원본 메서드 실행
        long end = System.currentTimeMillis();
        System.out.println(joinPoint.getSignature() + " : " + (end - start) + "ms");
        return result;
    }
}
```

## A-6. 대표 활용
`@Transactional`, `@Cacheable`, `@Async`, `@PreAuthorize`, `@Retryable`
→ 전부 프록시 기반 AOP → 전부 내부 호출 함정 공유.

---

# PART B. Spring 프록시

## B-1. 프록시란?
원본 객체를 **대신하는 대리 객체**. 원본을 감싸서 호출 전후에 부가 기능을 추가. → **Spring AOP의 핵심 구현.**

```
[프록시] ── 부가 기능 (트랜잭션 시작)
   ▼
[원본 객체] ── 핵심 로직
   ▼
[프록시] ── 부가 기능 (커밋/롤백)
```

## B-2. 두 가지 생성 방식
| 방식 | 대상 조건 | 원리 |
|------|-----------|------|
| **JDK 동적 프록시** | 인터페이스 O | 인터페이스 기반 생성 (`java.lang.reflect.Proxy`) |
| **CGLIB** | 인터페이스 X (클래스) | 대상 클래스를 **상속**해서 생성 |

- **Spring Boot 기본 = CGLIB** (`proxyTargetClass=true`)
- CGLIB 제약(상속 기반): `final` 클래스/메서드, `private` 메서드엔 적용 불가

## B-3. CGLIB 동작 개념
```java
class MyServiceProxy extends MyService {
    @Override
    public void b() {
        // 부가 기능 (트랜잭션 시작)
        super.b();  // 원본 호출
        // 부가 기능 (커밋)
    }
}
```

---

# PART C. 프록시가 호출을 "가로채는" 방식 (심화)

## C-1. 큰 그림
Spring은 시작 시 `@Transactional` 빈을 **원본 대신 프록시 객체**로 컨테이너에 등록한다. 주입받는 건 원본이 아니라 프록시.
```java
@Autowired
MemberService memberService; // ← 실제로는 MemberService$$SpringCGLIB (프록시)
```

## C-2. 가로채기 단계별 흐름 (CGLIB 기준)
```
1. memberService.save() 호출 → 실제로는 프록시의 save() 실행
2. 프록시가 적용할 Advisor(트랜잭션 Advice) 확인
3. TransactionInterceptor.invoke() 실행 (핵심 가로채기 지점)
   ├─ 트랜잭션 시작
   ├─ invocation.proceed()  ← 원본 save() 진짜 호출
   └─ 결과에 따라 commit / rollback
4. 원본 로직 실행 후 프록시로 복귀 → commit or rollback
```
핵심 클래스: **`TransactionInterceptor`** (가로채는 실체).

## C-3. TransactionInterceptor 내부 (의사 코드)
```java
public Object invoke(MethodInvocation invocation) throws Throwable {
    TransactionInfo txInfo = createTransactionIfNecessary(...); // (1) 시작
    Object result;
    try {
        result = invocation.proceed();          // (2) 원본 메서드 실행
    } catch (Throwable ex) {
        completeTransactionAfterThrowing(txInfo, ex); // (3) 예외 → 롤백 판단
        throw ex;
    }
    commitTransactionAfterReturning(txInfo);    // (4) 정상 → 커밋
    return result;
}
```
- `invocation.proceed()` = **원본 메서드 호출 지점.** 이 앞뒤로 트랜잭션이 감싸진다. → `@Around`와 동일 구조.

## C-4. 트랜잭션 시작이 실제로 하는 일
```
1. PlatformTransactionManager.getTransaction() 호출
2. DataSource에서 DB Connection 획득
3. connection.setAutoCommit(false)  ← 자동 커밋 끔
4. Connection을 TransactionSynchronizationManager(ThreadLocal)에 저장
```
**포인트:** 커넥션은 **ThreadLocal**로 보관 → 같은 트랜잭션 안 여러 DAO가 하나의 커넥션으로 묶임.

## C-5. 왜 내부 호출은 안 가로채지나?
```java
public void outer() {
    inner(); // this.inner() → 프록시가 아니라 원본 자기 자신 호출
}
@Transactional
public void inner() { ... }
```
- `this`는 프록시가 아니라 원본 → `TransactionInterceptor`를 안 거침 → 트랜잭션 없음.

**해결: 자기 자신 주입**
```java
@Autowired
private MyService self;      // 프록시 주입

public void outer() {
    self.inner();            // ✅ 프록시 경유 → 트랜잭션 적용
}
```

---

# 최종 요약
- **AOP** = 부가 기능을 핵심 로직에서 분리, Spring AOP는 **프록시**로 구현
- 용어: Aspect / Advice / Pointcut / Join Point / Weaving
- **JDK 동적 프록시**(인터페이스) vs **CGLIB**(상속), Boot 기본 = CGLIB
- 가로채는 실체 = **`TransactionInterceptor.invoke()`**, `proceed()` 앞뒤로 트랜잭션 감쌈
- 커넥션은 **ThreadLocal** 보관 → 같은 트랜잭션 묶기
- **내부 호출은 프록시를 안 거친다** → AOP 어노테이션 무효
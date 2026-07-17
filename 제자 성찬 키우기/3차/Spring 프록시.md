# Spring 프록시

## 1. 한 줄 요약

프록시는 원본 객체를 **대신하는 대리 객체**다. 원본을 감싸서 호출 전후에 부가 기능(트랜잭션, 로깅, 보안, 캐싱 등)을 추가한다. → **Spring AOP의 핵심 구현 방식.**

---

## 2. 왜 프록시를 쓰나?

핵심 로직(비즈니스)과 부가 기능(트랜잭션 등)을 분리하기 위해. 원본 코드를 건드리지 않고 부가 기능을 끼워 넣을 수 있다.

```
[프록시] ── 부가 기능 (트랜잭션 시작)
   │
   ▼
[원본 객체] ── 핵심 로직 실행
   │
   ▼
[프록시] ── 부가 기능 (커밋/롤백)
```

---

## 3. 두 가지 프록시 생성 방식

| 방식 | 대상 조건 | 원리 | 특징 |
|------|-----------|------|------|
| **JDK 동적 프록시** | 인터페이스가 **있을 때** | 인터페이스 기반 프록시 생성 | `java.lang.reflect.Proxy` 사용 |
| **CGLIB** | 인터페이스가 **없어도** (클래스) | 대상 클래스를 **상속**해서 프록시 생성 | 바이트코드 조작 |

### Spring Boot 기본값
- **CGLIB** 사용 (`spring.aop.proxy-target-class=true`가 기본)
- 과거엔 인터페이스 있으면 JDK 동적 프록시였지만, 지금은 일관성을 위해 기본이 CGLIB

---

## 4. JDK 동적 프록시

- **인터페이스가 반드시 필요**
- `InvocationHandler`를 구현해서 호출을 가로챔

```java
public interface MemberService {
    void hello();
}

// JDK 동적 프록시가 이 인터페이스를 구현하는 프록시를 런타임에 생성
MemberService proxy = (MemberService) Proxy.newProxyInstance(
    classLoader,
    new Class[]{ MemberService.class },
    invocationHandler
);
```

**단점:** 인터페이스가 없으면 못 씀.

---

## 5. CGLIB (Code Generator Library)

- **대상 클래스를 상속**해서 프록시를 만듦
- 인터페이스가 없어도 됨

```java
// 개념적으로 이런 프록시가 자동 생성됨
class MyServiceProxy extends MyService {
    @Override
    public void b() {
        // 부가 기능 (트랜잭션 시작)
        super.b();  // 원본 호출
        // 부가 기능 (커밋)
    }
}
```

**제약 (상속 기반이라서)**
- `final` 클래스 → 상속 불가 → 프록시 못 만듦
- `final` 메서드 → 오버라이드 불가 → 부가 기능 적용 안 됨
- `private` 메서드 → 오버라이드 불가 → 적용 안 됨

---

## 6. 프록시의 한계 (매우 중요)

### 내부 호출(self-invocation) 문제
프록시는 **외부에서 들어오는 호출만** 가로챈다. 객체 내부에서 자기 메서드를 직접 호출하면(`this.method()`) 프록시를 안 거친다.

```java
@Service
class MyService {
    public void outer() {
        inner(); // ❌ this.inner() → 프록시 통과 X → @Transactional 무효
    }

    @Transactional
    public void inner() { ... }
}
```

이게 `@Transactional`, `@Cacheable`, `@Async` 등이 내부 호출에서 안 먹는 근본 원인이다.

---

## 7. 어디에 쓰이나?

| 어노테이션 | 부가 기능 |
|-----------|----------|
| `@Transactional` | 트랜잭션 관리 |
| `@Cacheable` | 캐싱 |
| `@Async` | 비동기 실행 |
| `@PreAuthorize` | 보안/권한 체크 |
| `@Retryable` | 재시도 |

전부 **프록시 기반 AOP**로 동작 → 전부 내부 호출 함정을 공유한다.

---

## 8. 최종 요약

- 프록시 = 원본을 감싼 대리 객체 (부가 기능 삽입용)
- **JDK 동적 프록시**: 인터페이스 필요 / **CGLIB**: 상속 기반, 인터페이스 불필요
- Spring Boot 기본 = **CGLIB**
- `final`/`private`엔 적용 안 됨
- **내부 호출은 프록시를 안 거친다** → AOP 어노테이션 무효
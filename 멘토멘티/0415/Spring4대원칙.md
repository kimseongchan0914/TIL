# Spring 4대 원칙 정리

Spring의 핵심 설계 철학은 다음 4가지로 구성된다.

- IoC (제어의 역전)
- DI (의존성 주입)
- AOP (관점 지향 프로그래밍)
- PSA (Portable Service Abstraction)

---

## 1. IoC (Inversion of Control, 제어의 역전)

객체 생성과 관리의 주체가 개발자가 아니라 **Spring 컨테이너로 넘어가는 것**

### 특징
- 개발자가 직접 객체를 생성하지 않음
- Spring이 객체를 생성하고 관리
- 코드의 흐름 제어권이 프레임워크로 이동

### 기존 방식

```java
MyService service = new MyService();
```

→ 개발자가 직접 객체 생성

---

### IoC 적용

```java
@Autowired
private MyService service;
```

→ Spring이 객체를 생성하고 주입

---

### 핵심 요약
- 객체 생성 → Spring이 담당
- 개발자는 사용만 한다

---

## 2. DI (Dependency Injection, 의존성 주입)

객체 간의 의존 관계를 외부(Spring)가 주입해주는 것

### 특징
- 객체 간 결합도를 낮춤
- 유지보수와 테스트 용이
- IoC의 구현 방식

---

### 필드 주입

```java
@Autowired
private MyService service;
```

---

### 생성자 주입 (권장)

```java
@RequiredArgsConstructor
public class MyController {
    private final MyService service;
}
```

---

### 핵심 요약
- 필요한 객체를 직접 만들지 않음
- 외부에서 주입받아 사용

---

## 3. AOP (Aspect Oriented Programming, 관점 지향 프로그래밍)

공통 기능을 따로 분리하여 관리하는 방식

### 특징
- 반복되는 코드 제거
- 핵심 로직과 부가 기능 분리
- 유지보수성 향상

---

### 예시 (공통 기능)

- 로그 출력
- 트랜잭션 처리
- 보안 처리

---

### 기존 방식

```java
public void method() {
    System.out.println("로그");
    // 핵심 로직
}
```

---

### AOP 적용

```java
@Aspect
public class LogAspect {}
```

→ 공통 기능을 따로 분리

---

### 핵심 요약
- 공통 기능을 따로 관리
- 핵심 로직과 분리

---

## 4. PSA (Portable Service Abstraction)

특정 기술에 종속되지 않도록 추상화하는 것

### 특징
- 다양한 기술을 동일한 방식으로 사용
- 기술 교체가 쉬움
- 일관된 코드 작성 가능

---

### 예시

- DB: JDBC → JPA로 변경 가능
- 트랜잭션: 구현체 변경 가능

---

### 코드 예시

```java
@Transactional
public void save() {}
```

→ 내부 구현이 바뀌어도 코드 수정 없음

---

### 핵심 요약
- 기술 변경이 쉬움
- 표준화된 방식 제공

---

## 전체 흐름 정리

```
IoC → 객체 생성 관리
DI → 객체 주입
AOP → 공통 기능 분리
PSA → 기술 추상화
```

---

## 최종 핵심 정리

- IoC: 객체 생성은 Spring이 한다
- DI: 객체는 주입받아 사용한다
- AOP: 공통 기능은 분리한다
- PSA: 기술에 종속되지 않는다

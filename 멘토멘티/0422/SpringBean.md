# Spring Bean / IoC / DI 한 번에 정리 (TIL)

## 공부해야 하는 이유

스프링에서 제일 중요한 핵심 3개다.

* Bean → 스프링이 관리하는 객체
* IoC → 객체 생성/관리 주도권을 스프링이 가져감
* DI → 필요한 객체를 직접 만들지 않고 “주입” 받음

이 3개는 같이 묶어서 이해해야 편하다.

---

## Bean (스프링 빈)

스프링 컨테이너가 대신 만들어서 관리해주는 객체

```java id="k8x2pz"
@Component
class UserService {}
```

이렇게 해두면
new 안 해도 스프링이 알아서 생성하고 관리한다.

---

## IoC (제어의 역전)

원래는 우리가 객체를 직접 만든다.

```java id="m4k7as"
UserService userService = new UserService();
```

근데 스프링에서는 이걸 안 한다.

→ 객체 생성과 관리를 스프링이 대신함

이걸 IoC라고 한다.

---

## DI (의존성 주입)

객체를 직접 생성하지 않고, 필요한 걸 “주입받는 방식”

```java id="q7v9ld"
@Component
class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

UserService가 UserRepository를 직접 new 안 함
→ 스프링이 넣어줌

---

## IoC + DI 관계

* IoC → “스프링이 객체 관리함”
* DI → “그 객체를 필요한 곳에 넣어줌”

둘은 같이 돌아간다.

---

## 왜 쓰냐?

* 코드 결합도 낮아짐
* 테스트 쉬워짐
* 유지보수 편해짐

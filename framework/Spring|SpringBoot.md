# Spring / Spring Boot

## Spring Framework란?

자바 엔터프라이즈 애플리케이션 개발을 위한 오픈소스 프레임워크. 객체(Bean)의 생성과 관리를 프레임워크가 대신해주는 **IoC(제어의 역전)** 컨테이너가 핵심이다.

### 핵심 개념

| 개념 | 설명 |
|------|------|
| **IoC** (Inversion of Control) | 객체의 생성·관리 제어권을 개발자가 아닌 컨테이너가 가짐 |
| **DI** (Dependency Injection) | 의존 객체를 외부에서 주입받음 (결합도 낮춤) |
| **AOP** (Aspect Oriented Programming) | 로깅·트랜잭션 등 공통 관심사를 분리 |
| **PSA** (Portable Service Abstraction) | 일관된 방식으로 기술에 접근하는 추상화 |

---

## Spring vs Spring Boot

- **Spring**: 설정이 많고 유연하지만 복잡함 (XML/자바 설정 직접 작성)
- **Spring Boot**: Spring을 더 쉽게 쓰도록 만든 도구. 복잡한 설정을 자동화

### Spring Boot의 장점

- **자동 설정 (Auto Configuration)**: `@SpringBootApplication` 하나로 다수 설정 자동 적용
- **내장 서버**: Tomcat 등이 내장돼 별도 설치 없이 `main()`으로 실행
- **스타터 의존성**: `spring-boot-starter-web` 등으로 의존성 묶음 관리
- **독립 실행 JAR**: `java -jar`로 바로 실행 가능

---

## 의존성 주입 (DI) 예시

```java
@Service
public class MemberService {

    private final MemberRepository memberRepository;

    // 생성자 주입 (권장 방식)
    @Autowired
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

> 💡 **생성자 주입을 권장하는 이유**: 불변성 보장(`final`), 순환 참조를 컴파일 시점에 발견, 테스트 용이

---

## 주요 어노테이션

### Bean 등록

- `@Component` : 일반 컴포넌트
- `@Controller` / `@RestController` : 웹 요청 처리
- `@Service` : 비즈니스 로직
- `@Repository` : 데이터 접근 계층

### 웹 (MVC)

```java
@RestController
@RequestMapping("/api/members")
public class MemberController {

    @GetMapping("/{id}")
    public MemberDto findOne(@PathVariable Long id) {
        return memberService.findById(id);
    }

    @PostMapping
    public void save(@RequestBody MemberDto dto) {
        memberService.save(dto);
    }
}
```

| 어노테이션 | 역할 |
|-----------|------|
| `@GetMapping` | GET 요청 매핑 |
| `@PostMapping` | POST 요청 매핑 |
| `@PathVariable` | URL 경로 값 바인딩 |
| `@RequestBody` | 요청 본문(JSON)을 객체로 변환 |
| `@RequestParam` | 쿼리 파라미터 바인딩 |

---

## 스프링 컨테이너 & Bean 생명주기

1. 스프링 컨테이너 생성
2. Bean 생성 및 의존관계 주입
3. 초기화 콜백 (`@PostConstruct`)
4. 사용
5. 소멸 전 콜백 (`@PreDestroy`)

---

## 오늘 배운 점 정리

- Spring의 핵심은 **IoC/DI** 로 객체 간 결합도를 낮추는 것
- Spring Boot는 **자동 설정 + 내장 서버**로 개발 생산성을 크게 높임
- 의존성 주입은 **생성자 주입**을 사용하는 것이 안전하다

## 더 공부할 것

- [ ] AOP 동작 원리와 프록시
- [ ] 트랜잭션(`@Transactional`) 관리
- [ ] JPA와 영속성 컨텍스트

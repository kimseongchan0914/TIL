# Spring MVC 정리

## 1. Spring MVC란?
Spring MVC는 자바 기반 웹 애플리케이션을 만들기 위한 **웹 프레임워크**이다.  
MVC 패턴(Model-View-Controller)을 기반으로 구성되어 있으며, 역할을 나누어 코드의 유지보수성과 확장성을 높인다.

---

## 2. MVC 패턴 구조

### 1) Model
- 데이터와 비즈니스 로직을 담당
- 예: DB 조회, 데이터 처리

### 2) View
- 사용자에게 보여지는 화면
- 예: HTML, JSP, Thymeleaf

### 3) Controller
- 사용자의 요청을 받아 처리
- Model과 View를 연결하는 역할

---

## 3. Spring MVC 동작 흐름

1. 클라이언트가 HTTP 요청을 보냄
2. DispatcherServlet이 요청을 받음
3. 적절한 Controller로 요청을 전달
4. Controller에서 비즈니스 로직 수행
5. Model에 데이터 저장
6. View로 결과 전달
7. 최종 응답을 클라이언트에게 반환

---

## 4. 핵심 구성 요소

### DispatcherServlet
- 모든 요청을 받아 처리하는 프론트 컨트롤러

### Controller
- 요청을 처리하는 클래스
- `@Controller`, `@RestController` 사용

### Model
- 데이터를 담는 객체

### View Resolver
- 어떤 View를 사용할지 결정

---

## 5. 주요 어노테이션

### @Controller
- 해당 클래스를 컨트롤러로 지정

### @GetMapping / @PostMapping
- HTTP 요청 방식에 따라 메서드 매핑

### @RequestMapping
- URL을 매핑

### @ResponseBody
- 데이터를 JSON 형태로 반환

---

## 6. 코드 예시

```java
@Controller
@RequestMapping("/board")
public class BoardController {

    @GetMapping("/save")
    public String saveForm() {
        return "save";
    }
}

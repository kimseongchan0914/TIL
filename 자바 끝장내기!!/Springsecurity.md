# Spring Security

Spring Security는 **Spring 기반 애플리케이션에서 인증(Authentication)과 인가(Authorization)를 처리하기 위한 보안 프레임워크**이다.

쉽게 말하면,

> **"누가 로그인했는지 확인하고, 어떤 기능까지 사용할 수 있는지 관리해주는 보안 도구"**이다.

Spring Boot에서 로그인, 로그아웃, 권한 관리 등을 직접 구현하려면 많은 코드를 작성해야 한다.

Spring Security는 이러한 기능을 기본적으로 제공하여 안전한 웹 애플리케이션을 쉽게 만들 수 있도록 도와준다.

---

# Spring Security가 필요한 이유

보안 기능을 직접 구현한다면 다음과 같은 작업이 필요하다.

- 로그인 기능 구현
- 로그아웃 기능 구현
- 비밀번호 암호화
- 세션 관리
- 사용자 권한 관리
- URL 접근 제한
- CSRF 공격 방어
- XSS 방어

이 모든 것을 직접 구현하는 것은 매우 어렵다.

Spring Security는 이러한 기능을 대부분 기본으로 제공한다.

---

# Spring Security의 주요 기능

| 기능 | 설명 |
|------|------|
| 인증(Authentication) | 로그인한 사용자인지 확인 |
| 인가(Authorization) | 접근 권한 확인 |
| 비밀번호 암호화 | BCrypt 등을 이용하여 암호화 |
| 세션 관리 | 로그인 상태 관리 |
| JWT 인증 지원 | JWT 기반 인증 구현 가능 |
| CSRF 방어 | CSRF 공격 방지 |
| CORS 설정 | 다른 도메인 접근 허용 설정 |
| Security Filter 제공 | 모든 요청을 먼저 검사 |

---

# Authentication(인증)

인증(Authentication)은

> **"당신이 누구인지 확인하는 과정"**이다.

예를 들어

```
아이디 : kim
비밀번호 : 1234
```

를 입력하면

```
DB 조회

↓

사용자 존재?

↓

YES

↓

로그인 성공
```

Spring Security는 사용자가 실제 존재하는지 확인한다.

---

# Authorization(인가)

인가(Authorization)는

> **"이 사용자가 이 기능을 사용할 권한이 있는가?"**를 확인하는 과정이다.

예를 들어

```
USER
```

권한을 가진 사용자는

```
게시글 조회

게시글 작성
```

은 가능하지만

```
회원 삭제
```

는 불가능하다.

반면

```
ADMIN
```

권한은

```
회원 삭제

회원 관리

관리자 페이지 접근
```

등이 가능하다.

---

# Spring Security 동작 구조

```
Client
   │
HTTP Request
   ▼
Spring Security Filter Chain
   │
Authentication 확인
   │
Authorization 확인
   ▼
Controller
```

모든 요청은 Controller로 바로 가지 않는다.

반드시

```
Spring Security Filter Chain
```

을 먼저 거친다.

---

# Spring Security 인증 흐름(Session 방식)

Spring Security는 기본적으로 Session 기반 인증을 사용한다.

```
로그인 요청
      │
아이디 / 비밀번호 확인
      │
Authentication 생성
      │
SecurityContext 저장
      │
Session 생성
      │
JSESSIONID 발급
      │
브라우저 Cookie 저장
```

이후 모든 요청에서는

```
Cookie

JSESSIONID=ABC123
```

를 자동으로 보낸다.

Spring Security는 Session을 조회하여 로그인 여부를 확인한다.

---

# Spring Security 인증 흐름(JWT 방식)

JWT를 사용하는 경우에는 Session을 사용하지 않는다.

```
로그인
    │
아이디 / 비밀번호 확인
    │
JWT 생성
    │
클라이언트 저장
    │
API 요청
Authorization : Bearer JWT
    │
JWT Filter
    │
JWT 검증
    │
Authentication 생성
    │
SecurityContext 저장
    │
Controller 실행
```

JWT 방식에서는

```java
SessionCreationPolicy.STATELESS
```

설정을 통해 Session을 생성하지 않는다.

---

# Spring Security 핵심 구성 요소

## 1. Security Filter Chain

Spring Security의 핵심이다.

모든 요청을 가장 먼저 검사한다.

```
HTTP 요청
      │
Security Filter Chain
      │
인증
      │
인가
      │
Controller
```

---

## 2. Authentication

로그인한 사용자의 정보를 저장하는 객체이다.

예를 들어

```java
Authentication

username = kim

role = USER

authenticated = true
```

---

## 3. SecurityContext

Authentication을 저장하는 공간이다.

```
SecurityContext

↓

Authentication
```

현재 로그인한 사용자의 정보를 관리한다.

---

## 4. UserDetails

사용자 정보를 담는 객체이다.

예를 들어

```java
username

password

role
```

등을 저장한다.

Spring Security는 UserDetails를 이용하여 로그인한다.

---

## 5. UserDetailsService

DB에서 사용자 정보를 조회하는 인터페이스이다.

예를 들어

```text
아이디 입력

↓

DB 조회

↓

UserDetails 반환
```

과 같은 역할을 한다.

---

## 6. PasswordEncoder

비밀번호를 암호화한다.

대표적으로

```java
BCryptPasswordEncoder
```

를 사용한다.

예를 들어

```
1234
```

를 저장하지 않고

```
$2a$10$A....
```

처럼 암호화하여 저장한다.

---

# Spring Security에서 많이 사용하는 클래스

| 클래스 | 역할 |
|---------|------|
| SecurityFilterChain | 보안 설정 |
| Authentication | 로그인 사용자 정보 |
| SecurityContext | Authentication 저장 |
| UserDetails | 사용자 정보 |
| UserDetailsService | 사용자 조회 |
| PasswordEncoder | 비밀번호 암호화 |
| UsernamePasswordAuthenticationToken | 로그인 정보 저장 객체 |

---

# Spring Security 장점

- 로그인 기능을 쉽게 구현할 수 있다.
- 권한 관리가 편하다.
- 비밀번호 암호화를 제공한다.
- Session과 JWT 모두 지원한다.
- 다양한 보안 기능을 기본 제공한다.
- Spring Boot와 완벽하게 연동된다.

---

# Spring Security 단점

- 구조가 복잡하다.
- Filter 구조를 이해해야 한다.
- 처음 배우기 어렵다.
- 설정 코드가 많다.

---

# Spring Security 전체 흐름

```
Client
   │
로그인 요청
   ▼
Spring Security
   │
UserDetailsService
   │
DB 조회
   │
PasswordEncoder 비교
   │
Authentication 생성
   │
SecurityContext 저장
   │
(Session 또는 JWT)
   │
Controller 실행
```

---

# Spring Security와 JWT의 관계

많은 사람들이

```
Spring Security = JWT
```

라고 생각하지만 이는 틀린 생각이다.

실제로는

```
Spring Security
      │
      ├──────────────┐
      │              │
  Session 인증    JWT 인증
```

Spring Security는 **보안 프레임워크**이고,

JWT는 **인증 방식** 중 하나이다.

즉,

- Spring Security는 보안을 담당한다.
- JWT는 사용자를 인증하는 방법이다.
- Spring Security는 Session 방식과 JWT 방식 모두 사용할 수 있다.

---

# 핵심 정리

| 용어 | 설명 |
|------|------|
| Spring Security | Spring의 보안 프레임워크 |
| Authentication | 로그인 여부 확인(인증) |
| Authorization | 권한 확인(인가) |
| Security Filter Chain | 모든 요청을 검사하는 필터 |
| SecurityContext | 로그인 사용자 정보 저장 |
| UserDetails | 사용자 정보 객체 |
| UserDetailsService | 사용자 조회 |
| PasswordEncoder | 비밀번호 암호화 |
| Session | 기본 인증 방식 |
| JWT | Session 대신 사용할 수 있는 인증 방식 |

---

# 한 줄 정리

> **Spring Security는 Spring 애플리케이션의 인증(Authentication)과 인가(Authorization)를 담당하는 보안 프레임워크이며, Session 또는 JWT를 이용하여 사용자의 로그인과 권한을 안전하게 관리한다.**
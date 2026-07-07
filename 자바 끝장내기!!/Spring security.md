# Spring Security 주요 구성 요소

Spring Security는 여러 구성 요소가 서로 협력하여 인증(Authentication)과 인가(Authorization)를 수행한다.

가장 많이 사용하는 핵심 구성 요소는 다음과 같다.

```
Client
   │
HTTP Request
   ▼
Security Filter Chain
   │
Authentication
   │
SecurityContext
   │
Controller
```

---

# 1. SecurityFilterChain

Spring Security의 **가장 핵심적인 구성 요소**이다.

클라이언트의 모든 HTTP 요청은 Controller로 바로 가지 않고 **반드시 SecurityFilterChain을 먼저 통과**한다.

```
Client
   │
HTTP Request
   ▼
Security Filter Chain
   │
   ├─ 로그인 여부 확인
   ├─ 권한 확인
   ├─ JWT 또는 Session 검사
   ▼
Controller
```

### 역할

- 모든 요청을 가장 먼저 검사
- 인증(Authentication) 수행
- 인가(Authorization) 수행
- 필요한 Security Filter 실행

예시

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    http
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/login").permitAll()
            .anyRequest().authenticated()
        );

    return http.build();
}
```

---

# 2. Filter

Filter는 요청과 응답을 가로채는 객체이다.

Spring Security에는 여러 개의 Filter가 존재한다.

예를 들어 JWT 방식에서는

```
Client

↓

JWTAuthenticationFilter

↓

JWT 검증

↓

Controller
```

처럼 동작한다.

대표적인 Filter

- UsernamePasswordAuthenticationFilter
- BasicAuthenticationFilter
- JwtAuthenticationFilter(직접 구현)

---

# 3. Authentication

인증된 사용자의 정보를 저장하는 객체이다.

쉽게 말하면

> **"현재 로그인한 사용자"**

를 나타낸다.

예시

```java
Authentication

username = "kim"

role = "USER"

authenticated = true
```

Authentication 안에는

- 사용자 정보
- 권한
- 인증 여부

등이 저장된다.

---

# 4. SecurityContext

Authentication을 저장하는 공간이다.

```
SecurityContext

↓

Authentication
```

즉

```
SecurityContext

↓

현재 로그인한 사용자
```

를 관리한다.

Spring Security는 요청이 들어오면

Authentication을 SecurityContext에 저장한다.

Controller에서는

```java
Authentication authentication
```

으로 현재 로그인한 사용자를 가져올 수 있다.

---

# 5. SecurityContextHolder

SecurityContext를 관리하는 클래스이다.

```
SecurityContextHolder

↓

SecurityContext

↓

Authentication
```

현재 로그인한 사용자를 가져올 때 사용한다.

예시

```java
Authentication authentication =
SecurityContextHolder
        .getContext()
        .getAuthentication();
```

---

# 6. UserDetails

사용자 정보를 담는 객체이다.

Spring Security는 UserDetails를 이용하여 로그인한다.

대표적으로 저장되는 정보

```java
username

password

authorities
```

예시

```java
public class CustomUserDetails
        implements UserDetails {

    private String username;

    private String password;

    private String role;
}
```

---

# 7. UserDetailsService

DB에서 사용자 정보를 조회하는 인터페이스이다.

로그인할 때 가장 먼저 실행된다.

동작 과정

```
아이디 입력

↓

UserDetailsService

↓

DB 조회

↓

UserDetails 반환
```

예시

```java
@Service
public class CustomUserDetailsService
implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(String username) {

        return userRepository.findByUsername(username);

    }

}
```

---

# 8. PasswordEncoder

비밀번호를 암호화하는 객체이다.

Spring Security에서는

```
BCryptPasswordEncoder
```

를 가장 많이 사용한다.

예시

```java
PasswordEncoder encoder =
new BCryptPasswordEncoder();

String password =
encoder.encode("1234");
```

로그인할 때는

```java
encoder.matches(
입력한비밀번호,
DB비밀번호
);
```

를 사용하여 비교한다.

---

# 9. UsernamePasswordAuthenticationToken

아이디와 비밀번호를 저장하는 Authentication 구현체이다.

로그인 시 가장 많이 사용된다.

예시

```java
UsernamePasswordAuthenticationToken token =
new UsernamePasswordAuthenticationToken(
    username,
    password
);
```

로그인이 성공하면

Authentication 객체로 사용된다.

---

# 10. AuthenticationManager

Authentication을 실제로 인증하는 객체이다.

동작 과정

```
아이디

비밀번호

↓

AuthenticationManager

↓

UserDetailsService

↓

PasswordEncoder

↓

인증 성공
```

즉

로그인 처리를 담당하는 핵심 객체이다.

---

# Spring Security 전체 흐름

```
Client
   │
로그인 요청
   ▼
Security Filter Chain
   │
UsernamePasswordAuthenticationFilter
   │
AuthenticationManager
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
Controller 실행
```

JWT 방식이라면 중간에 JWT Filter가 추가된다.

```
Client
   │
Authorization : Bearer JWT
   ▼
Security Filter Chain
   │
JwtAuthenticationFilter
   │
JWT 검증
   │
Authentication 생성
   │
SecurityContext 저장
   │
Controller 실행
```

---

# 구성 요소 관계

```
                Client
                   │
              HTTP Request
                   │
                   ▼
         Security Filter Chain
                   │
      ┌────────────┴────────────┐
      │                         │
AuthenticationManager      JWT Filter
      │                         │
UserDetailsService             JWT 검증
      │                         │
PasswordEncoder                │
      │                         │
Authentication 생성─────────────┘
              │
              ▼
      SecurityContextHolder
              │
              ▼
       SecurityContext
              │
              ▼
         Authentication
              │
              ▼
         Controller
```

---

# 핵심 정리

| 구성 요소 | 역할 |
|-----------|------|
| SecurityFilterChain | 모든 요청을 가장 먼저 검사하는 보안 필터 체인 |
| Filter | 요청을 가로채 인증 및 권한 확인 수행 |
| Authentication | 로그인한 사용자 정보 |
| SecurityContext | Authentication 저장 공간 |
| SecurityContextHolder | SecurityContext를 관리하는 클래스 |
| UserDetails | 사용자 정보를 담는 객체 |
| UserDetailsService | DB에서 사용자 조회 |
| PasswordEncoder | 비밀번호 암호화 및 비교 |
| UsernamePasswordAuthenticationToken | 로그인 정보(Authentication 구현체) |
| AuthenticationManager | 실제 인증을 수행하는 객체 |

---

# 면접에서 가장 중요한 구성 요소

실무와 면접에서 특히 자주 나오는 것은 다음 6가지이다.

1. **SecurityFilterChain** : 모든 요청을 가장 먼저 처리하는 보안 필터
2. **Authentication** : 현재 로그인한 사용자 정보
3. **SecurityContext** : Authentication을 저장하는 공간
4. **UserDetailsService** : DB에서 사용자 정보를 조회하는 역할
5. **PasswordEncoder** : 비밀번호를 암호화하고 검증하는 역할
6. **AuthenticationManager** : 로그인 인증을 수행하는 핵심 객체

이 여섯 가지의 역할과 서로의 관계를 이해하면 Spring Security의 전체 동작 흐름을 이해하는 데 큰 도움이 된다.
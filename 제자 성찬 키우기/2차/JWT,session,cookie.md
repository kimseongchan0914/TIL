# JWT (JSON Web Token)

JWT(Json Web Token)는 **사용자의 정보를 JSON 형태로 안전하게 전달하기 위한 토큰**이다.

쉽게 말하면,

> **"이 사용자는 로그인에 성공한 사용자입니다."**

라는 정보를 하나의 문자열(Token)에 담아 클라이언트와 서버가 주고받는 방식이다.

---

# JWT의 역할

JWT의 대표적인 역할은 다음과 같다.

### 1. 사용자 인증 (Authentication)

사용자가 로그인에 성공했는지 확인한다.

예를 들어 로그인을 한 후 JWT를 가지고 있으면, 이후 요청에서는 아이디와 비밀번호를 다시 보내지 않아도 된다.

```
로그인
    ↓
JWT 발급
    ↓
API 요청마다 JWT 전송
```

---

### 2. 사용자 인가 (Authorization)

사용자가 어떤 권한을 가지고 있는지 판단한다.

예를 들어,

```
role = USER
```

→ 일반 사용자 기능만 사용 가능

```
role = ADMIN
```

→ 관리자 기능까지 사용 가능

JWT 안에 저장된 권한(Role)을 보고 서버가 접근을 허용하거나 거부한다.

---

### 3. Stateless 인증

JWT의 가장 큰 특징이다.

기존 세션 방식은 로그인 정보를 서버 메모리에 저장한다.

```
사용자 로그인
      ↓
서버 메모리에 Session 저장
```

하지만 JWT는 로그인 정보를 토큰 안에 저장한다.

```
사용자 로그인
      ↓
JWT 생성
      ↓
클라이언트 저장
```

따라서 서버는 로그인 상태를 별도로 저장하지 않아도 된다.

이를 **Stateless(무상태)** 방식이라고 한다.

---

# JWT 구조

JWT는 총 **3개의 부분**으로 구성된다.

```
Header
   │
Payload
   │
Signature
```

실제 JWT는 아래처럼 생겼다.

```
Header.Payload.Signature
```

예시

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJzdWIiOiJraW0iLCJyb2xlIjoiVVNFUiJ9
.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

---

# 1. Header (헤더)

Header에는 토큰에 대한 정보가 들어 있다.

대표적으로

- 어떤 알고리즘으로 서명했는지
- JWT 토큰인지

를 저장한다.

예시

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Header 구성 요소

| 항목 | 설명 |
|------|------|
| alg | 서명 알고리즘 |
| typ | JWT 토큰이라는 의미 |

Header는 Base64로 인코딩되어 토큰의 첫 번째 부분이 된다.

---

# 2. Payload (페이로드)

Payload에는 **사용자 정보(Claim)** 가 저장된다.

예시

```json
{
  "sub": "kim",
  "id": 1,
  "name": "김성찬",
  "role": "USER",
  "exp": 1750000000
}
```

### Payload 주요 정보

| 항목 | 설명 |
|------|------|
| sub | 사용자 식별자 |
| id | 사용자 번호 |
| role | 사용자 권한 |
| exp | 만료 시간 |
| iat | 발급 시간 |
| iss | 발급자 |

예를 들어

```
role = ADMIN
```

이라면 관리자 권한을 가진 사용자라는 의미이다.

---

## Payload에 저장하면 안 되는 정보

JWT는 **암호화되는 것이 아니라 Base64로 인코딩되는 것**이다.

즉 누구나 Payload 내용을 확인할 수 있다.

따라서 다음과 같은 정보는 절대 저장하면 안 된다.

❌ 비밀번호

❌ 주민등록번호

❌ 카드번호

❌ 계좌번호

---

# 3. Signature (서명)

Signature는 JWT의 **가장 중요한 보안 요소**이다.

Signature는

```
Header
+
Payload
+
Secret Key
```

를 이용하여 생성된다.

예를 들어

```
HMACSHA256(
Header +
Payload +
SecretKey
)
```

와 같은 방식으로 생성된다.

---

## Signature의 역할

만약 누군가 Payload를

```
role = USER
```

에서

```
role = ADMIN
```

으로 변경했다면,

Signature가 달라진다.

서버는 Secret Key를 이용하여 다시 Signature를 계산한다.

```
JWT 수신
     ↓
Signature 재계산
     ↓
같은가?
     ↓
YES → 정상 토큰
NO  → 위조된 토큰
```

즉 Signature는 **토큰이 변조되지 않았는지 확인하는 역할**을 한다.

---

# JWT 생성 과정

```
로그인 요청
      ↓
아이디 / 비밀번호 확인
      ↓
로그인 성공
      ↓
JWT 생성
      ↓
클라이언트에게 전달
```

---

# JWT 사용 과정

## 1. 로그인

```
Client
   │
ID / Password
   ▼
Server
```

---

## 2. 서버에서 JWT 생성

```
Header
Payload
Signature
        ↓
JWT 생성
```

---

## 3. 클라이언트 저장

클라이언트는 JWT를 저장한다.

대표적인 저장 위치는

- LocalStorage
- SessionStorage
- Cookie

이다.

---

## 4. API 요청

API를 호출할 때마다 JWT를 함께 보낸다.

```
GET /users

Authorization: Bearer JWT
```

---

## 5. 서버 검증

```
JWT 수신
     ↓
Signature 검증
     ↓
만료 시간 확인
     ↓
사용자 정보 추출
     ↓
요청 처리
```

검증에 실패하면

```
401 Unauthorized
```

를 반환한다.

---

# Spring Security에서 JWT 인증 흐름

```
로그인
    │
아이디/비밀번호 확인
    │
JWT 생성
    │
클라이언트 저장
    │
API 요청
Authorization: Bearer JWT
    │
JWT Filter
    │
Signature 검증
    │
Payload에서 사용자 정보 추출
    │
SecurityContext에 저장
    │
Controller 실행
```

JWT Filter는 모든 요청을 먼저 검사하여 토큰이 정상인지 확인하는 역할을 한다.

---

# 세션(Session)과 JWT 비교

| 구분 | Session | JWT |
|------|---------|------|
| 저장 위치 | 서버 | 클라이언트 |
| 로그인 정보 | 서버 메모리 | 토큰 내부 |
| 서버 상태 | Stateful | Stateless |
| 속도 | 세션 조회 필요 | 토큰 검증만 수행 |
| 확장성 | 낮음 | 높음 |

---

# JWT 장점

- 서버가 로그인 상태를 저장하지 않아도 된다.
- 서버 확장(Scale-Out)이 쉽다.
- 모바일, 웹 등 다양한 환경에서 사용 가능하다.
- REST API와 잘 어울린다.
- 인증 속도가 빠르다.

---

# JWT 단점

- 토큰 길이가 세션 ID보다 길다.
- 한 번 발급하면 강제로 폐기하기 어렵다.
- Payload는 누구나 확인할 수 있으므로 민감한 정보를 저장하면 안 된다.
- 토큰이 탈취되면 만료 전까지 사용할 수 있다.

---

# 핵심 정리

| 구성 요소 | 역할 |
|-----------|------|
| Header | 토큰 정보와 서명 알고리즘 |
| Payload | 사용자 정보와 권한 |
| Signature | 토큰 변조 여부 검증 |
<hr>
<hr>

# Session(세션)

Session(세션)은 **사용자의 로그인 정보를 서버가 직접 저장하여 사용자를 인증하는 방식**이다.

쉽게 말하면,

> **"사용자가 로그인했으니 서버가 이 사용자의 로그인 상태를 기억하고 있는 것"**

이라고 생각하면 된다.

---

# 세션이 필요한 이유

HTTP는 **Stateless(무상태) 프로토콜**이다.

즉, 요청이 끝나면 서버는 사용자를 기억하지 못한다.

예를 들어

```
1. 로그인 요청
```

```
ID : kim
PW : 1234
```

↓

```
로그인 성공
```

그런데 다음 요청이 오면

```
게시글 조회
```

서버는

> "너 누구야?"

라고 생각한다.

왜냐하면 HTTP는 이전 요청을 기억하지 않기 때문이다.

그래서 **사용자의 로그인 상태를 저장하기 위해 Session이 등장했다.**

---

# 세션 동작 과정

## 1. 로그인 요청

클라이언트가 아이디와 비밀번호를 보낸다.

```
Client
   │
ID / Password
   ▼
Server
```

---

## 2. 서버가 로그인 확인

서버는 DB에서 아이디와 비밀번호를 확인한다.

```
DB 조회

↓

일치하면 로그인 성공
```

---

## 3. Session 생성

로그인이 성공하면 서버는

```
Session 생성
```

을 한다.

예를 들어

```
Session ID

ABC123XYZ
```

라는 값을 만든다.

그리고

```
ABC123XYZ

↓

김성찬
ROLE_USER
로그인 시간
```

같이 서버 메모리에 저장한다.

---

## 4. Session ID 전달

사용자의 정보는 보내지 않는다.

오직

```
Session ID
```

만 브라우저에게 보낸다.

보통 Cookie에 저장된다.

```
Set-Cookie

JSESSIONID=ABC123XYZ
```

---

## 5. 이후 요청

사용자는 요청할 때마다

```
Cookie

JSESSIONID=ABC123XYZ
```

를 자동으로 보낸다.

```
GET /users

Cookie

JSESSIONID=ABC123XYZ
```

---

## 6. 서버 확인

서버는

```
ABC123XYZ
```

를 보고

```
서버 메모리

↓

ABC123XYZ

↓

김성찬
ROLE_USER
```

를 찾는다.

찾으면

```
인증 성공
```

이다.

---

# 그림으로 보는 Session

```
① 로그인

Client
   │
ID / PW
   ▼
Server

──────────────

② Session 생성

Server

Session

ABC123
↓

김성찬
ROLE_USER

──────────────

③ Session ID 전달

Set-Cookie

JSESSIONID=ABC123

──────────────

④ API 요청

Client

Cookie

JSESSIONID=ABC123

──────────────

⑤ Session 조회

Server

ABC123

↓

김성찬

↓

Controller 실행
```

---

# Session의 구성

Session에는 다양한 정보를 저장할 수 있다.

예를 들어

```java
Session

{
    userId : 1,
    username : "kim",
    role : "USER",
    loginTime : "10:20"
}
```

하지만 브라우저는 이 정보를 모른다.

브라우저는

```
JSESSIONID
```

만 가지고 있다.

---

# Cookie와 Session의 관계

많은 사람들이 헷갈리는 부분이다.

## Cookie

브라우저에 저장된다.

```
JSESSIONID=ABC123
```

만 저장된다.

---

## Session

서버에 저장된다.

```
ABC123

↓

사용자 정보
```

즉,

```
Cookie

↓

Session ID
```

를 저장하고,

```
Server

↓

Session
```

을 저장한다.

---

# Session 장점

### 1. 보안성이 높다.

브라우저에는 Session ID만 저장된다.

실제 사용자 정보는 서버에 있다.

---

### 2. 정보 수정이 쉽다.

사용자 권한을 변경하면

```
Server Session
```

만 수정하면 된다.

---

### 3. 민감한 정보 저장 가능

비밀번호를 저장하는 것은 권장되지 않지만,

JWT처럼 Payload가 노출되는 구조가 아니므로 서버 내부에서 안전하게 관리할 수 있다.

---

# Session 단점

### 1. 서버 메모리를 사용한다.

사용자가 많아질수록

```
Session

100명

↓

100개의 Session
```

```
100만 명

↓

100만 개의 Session
```

이 저장된다.

메모리 사용량이 증가한다.

---

### 2. 서버 확장이 어렵다.

```
Server A
```

에 Session이 있는데

다음 요청이

```
Server B
```

로 가면

Session이 없다.

```
A

Session 있음

B

Session 없음
```

그래서 로그인한 사용자가 로그아웃된 것처럼 보일 수 있다.

이를 해결하려면

- Redis
- DB
- Session Cluster

같은 추가 기술이 필요하다.

---

### 3. 조회 과정이 필요하다.

매 요청마다

```
Cookie

↓

Session ID

↓

Session 조회

↓

사용자 정보
```

를 수행해야 한다.

---

# Session과 JWT 차이

| 구분 | Session | JWT |
|------|---------|------|
| 로그인 정보 저장 | 서버 | 클라이언트 |
| 서버 메모리 사용 | O | X |
| 서버 상태 | Stateful | Stateless |
| 서버 확장 | 어려움 | 쉬움 |
| 사용자 정보 조회 | Session 조회 | JWT 검증 |
| 보안 | 높음 | 토큰 관리 필요 |

---

# Spring Security에서 Session 인증 흐름

```
로그인
    │
아이디 / 비밀번호 확인
    │
Session 생성
    │
JSESSIONID 발급
    │
브라우저 Cookie 저장
    │
API 요청
Cookie : JSESSIONID
    │
Session 조회
    │
사용자 정보 조회
    │
SecurityContext 저장
    │
Controller 실행
```

---

# 핵심 정리

| 용어 | 설명 |
|------|------|
| Session | 서버가 사용자 로그인 정보를 저장하는 공간 |
| Session ID | 사용자를 구분하기 위한 고유한 식별자 |
| Cookie | Session ID를 저장하는 공간 |
| JSESSIONID | Spring에서 사용하는 기본 Session ID 이름 |

<hr>
<hr>

# Cookie(쿠키)

Cookie(쿠키)는 **브라우저(클라이언트)에 저장되는 작은 데이터**이다.

쉽게 말하면,

> **브라우저가 서버로부터 받은 정보를 저장해 두었다가 다음 요청에도 함께 보내주는 저장 공간**이다.

쿠키는 로그인 정보뿐만 아니라 다양한 정보를 저장하는 데 사용된다.

예를 들어

- 자동 로그인
- 장바구니 정보
- 최근 본 상품
- 다크 모드 설정
- 언어 설정

등을 저장할 수 있다.

---

# Cookie가 필요한 이유

HTTP는 **Stateless(무상태) 프로토콜**이다.

즉, 요청이 끝나면 서버는 이전 요청을 기억하지 못한다.

예를 들어

```
첫 번째 요청

로그인
```

↓

```
로그인 성공
```

그런데 다음 요청이 오면

```
게시글 조회
```

서버는

> "이 사용자가 아까 로그인했던 사람인지 모르겠다."

라고 생각한다.

그래서 브라우저가 **쿠키를 이용해 정보를 계속 전달**하게 된다.

---

# Cookie 동작 과정

## 1. 로그인 요청

```
Client
   │
ID / Password
   ▼
Server
```

---

## 2. 서버에서 Cookie 생성

서버는 로그인이 성공하면

```
Set-Cookie
```

헤더를 응답에 담아 보낸다.

예시

```
HTTP/1.1 200 OK

Set-Cookie

JSESSIONID=ABC123
```

여기서

```
JSESSIONID=ABC123
```

이 쿠키이다.

---

## 3. 브라우저 저장

브라우저는 받은 쿠키를 자동으로 저장한다.

```
브라우저

Cookie

JSESSIONID=ABC123
```

---

## 4. 다음 요청

브라우저는 같은 사이트에 요청할 때마다

자동으로 쿠키를 함께 보낸다.

```
GET /users

Cookie

JSESSIONID=ABC123
```

개발자가 직접 추가하지 않아도 브라우저가 자동으로 전송한다.

---

## 5. 서버 확인

서버는 쿠키를 읽고

```
JSESSIONID=ABC123
```

를 확인하여 사용자를 식별한다.

---

# 그림으로 보는 Cookie

```
① 로그인

Client
   │
ID / PW
   ▼
Server

──────────────

② 서버 응답

HTTP Response

Set-Cookie

JSESSIONID=ABC123

──────────────

③ 브라우저 저장

Cookie

JSESSIONID=ABC123

──────────────

④ 다음 요청

GET /users

Cookie

JSESSIONID=ABC123

──────────────

⑤ 서버 확인

Cookie 읽기

↓

사용자 확인
```

---

# Cookie에 저장되는 정보

쿠키에는 다양한 데이터를 저장할 수 있다.

예를 들어

```
theme=dark

language=ko

cart=123

JSESSIONID=ABC123
```

등이 있다.

하지만 **민감한 정보는 저장하면 안 된다.**

---

# Cookie 종류

## 1. Session Cookie

브라우저를 종료하면 사라지는 쿠키이다.

예를 들어

```
JSESSIONID
```

가 대표적이다.

브라우저를 종료하면 자동으로 삭제된다.

---

## 2. Persistent Cookie

유효 기간을 지정하는 쿠키이다.

예를 들어

```
Expires

Max-Age
```

를 설정하면

```
7일

30일

1년
```

등 원하는 기간 동안 유지된다.

자동 로그인 기능에서 많이 사용된다.

---

# Cookie 주요 속성

| 속성 | 설명 |
|------|------|
| Name | 쿠키 이름 |
| Value | 저장되는 값 |
| Expires | 만료 날짜 |
| Max-Age | 유지 시간 |
| Domain | 쿠키를 사용할 도메인 |
| Path | 쿠키를 사용할 경로 |
| Secure | HTTPS에서만 전송 |
| HttpOnly | JavaScript 접근 차단 |
| SameSite | 다른 사이트 요청 시 쿠키 전송 제한(CSRF 방지) |

---

# HttpOnly

```
HttpOnly = true
```

이면

JavaScript에서 쿠키를 읽을 수 없다.

즉

```
document.cookie
```

로 접근이 불가능하다.

XSS 공격을 방지하는 데 매우 중요하다.

---

# Secure

```
Secure = true
```

이면

HTTPS에서만 쿠키를 전송한다.

HTTP에서는 전송되지 않는다.

---

# SameSite

다른 사이트에서 요청이 왔을 때 쿠키를 보낼지 결정하는 옵션이다.

대표적으로

| 옵션 | 설명 |
|------|------|
| Strict | 같은 사이트에서만 쿠키 전송 |
| Lax | 대부분의 일반적인 요청에서만 전송 |
| None | 모든 요청에서 전송(반드시 Secure 필요) |

CSRF 공격을 방지하는 데 중요한 역할을 한다.

---

# Cookie 장점

- 브라우저가 자동으로 관리한다.
- 로그인 유지에 사용할 수 있다.
- 사용자 설정을 저장하기 좋다.
- 서버가 매번 정보를 다시 보내지 않아도 된다.

---

# Cookie 단점

- 사용자가 삭제할 수 있다.
- 용량이 작다(약 4KB).
- 클라이언트에 저장되므로 변조를 시도할 수 있다.
- 민감한 정보를 저장하면 보안상 위험하다.

---

# Cookie와 Session 관계

많은 사람들이 헷갈리는 부분이다.

```
Cookie

↓

Session ID 저장
```

```
Server

↓

Session 저장
```

즉,

쿠키는 **Session ID를 저장하는 역할**을 하고,

실제 로그인 정보는 **Session**에 저장된다.

---

# Cookie와 JWT 관계

JWT도 쿠키에 저장할 수 있다.

```
Cookie

↓

JWT 저장
```

즉 쿠키는 **저장소**일 뿐이다.

안에는

- Session ID
- JWT
- 사용자 설정

등 다양한 데이터를 저장할 수 있다.

---

# Cookie · Session · JWT 관계

```
                로그인
                   │
        ┌──────────┴──────────┐
        │                     │
     Session 방식          JWT 방식
        │                     │
서버에 로그인 정보 저장      JWT 생성
        │                     │
Cookie에는              Cookie 또는 LocalStorage에
Session ID 저장          JWT 저장
        │                     │
다음 요청마다              다음 요청마다
자동으로 Cookie 전송        JWT 전송
```

---

# 핵심 정리

| 용어 | 설명 |
|------|------|
| Cookie | 브라우저에 저장되는 작은 데이터 |
| Session | 서버에 저장되는 로그인 정보 |
| JWT | 로그인 정보와 권한을 담은 토큰 |
| JSESSIONID | Session을 찾기 위한 고유 ID |

---

# 한 줄 정리

> **Cookie는 브라우저에 저장되는 작은 데이터이며, Session ID나 JWT 같은 정보를 저장하여 이후 요청에서 서버가 사용자를 식별할 수 있도록 전달하는 역할을 한다.**
# HTTP 정리 (TIL)

## HTTP란?

HTTP(HyperText Transfer Protocol)는
웹에서 클라이언트와 서버가 데이터를 주고받기 위한 통신 규약이다.

쉽게 말하면
브라우저(클라이언트)가 서버에게 요청(Request)을 보내고
서버가 응답(Response)을 보내는 방식이다.

---

## HTTP 동작 구조

클라이언트 → 요청(Request) → 서버
서버 → 응답(Response) → 클라이언트

---

## HTTP 특징

### 1. 무상태(Stateless)

* 서버는 이전 요청을 기억하지 않는다.
* 각각의 요청은 독립적으로 처리된다.

예시
로그인 후 다른 페이지 이동 시 다시 인증이 필요함
→ 이를 해결하기 위해 쿠키, 세션, 토큰 사용

---

### 2. 비연결성(Connectionless)

* 요청/응답이 끝나면 연결을 끊는다.
* 서버 자원을 효율적으로 사용할 수 있다.

단점
매번 연결을 새로 해야 해서 비용 발생
→ HTTP/1.1부터 Keep-Alive로 보완

---

## HTTP 요청(Request)

### 구조

```
[요청라인]
[헤더]
[빈 줄]
[바디]
```

### 예시

```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Chrome

```

---

## HTTP 응답(Response)

### 구조

```
[상태라인]
[헤더]
[빈 줄]
[바디]
```

### 예시

```
HTTP/1.1 200 OK
Content-Type: text/html

<html>...</html>
```

---

## HTTP 메서드

| 메서드    | 설명        |
| ------ | --------- |
| GET    | 데이터 조회    |
| POST   | 데이터 생성    |
| PUT    | 데이터 전체 수정 |
| PATCH  | 데이터 일부 수정 |
| DELETE | 데이터 삭제    |

---

## HTTP 상태 코드

### 1xx (정보)

* 요청이 수신되어 처리 중

### 2xx (성공)

* 200 OK: 요청 성공
* 201 Created: 생성 성공

### 3xx (리다이렉션)

* 301: 영구 이동
* 302: 임시 이동

### 4xx (클라이언트 오류)

* 400 Bad Request: 잘못된 요청
* 401 Unauthorized: 인증 필요
* 403 Forbidden: 접근 금지
* 404 Not Found: 리소스 없음

### 5xx (서버 오류)

* 500 Internal Server Error: 서버 오류
* 503 Service Unavailable: 서비스 불가

---

## HTTP vs HTTPS

| 구분  | HTTP | HTTPS    |
| --- | ---- | -------- |
| 보안  | 없음   | 있음 (암호화) |
| 포트  | 80   | 443      |
| 데이터 | 평문   | 암호화      |

HTTPS는 SSL/TLS를 사용하여 데이터를 암호화한다.

---

## 쿠키와 세션

### 쿠키 (Cookie)

* 클라이언트(브라우저)에 저장
* 자동으로 서버에 포함되어 전송됨

### 세션 (Session)

* 서버에 저장
* 클라이언트는 세션 ID만 보유

---

## 한 줄 정리

HTTP는 클라이언트와 서버가 요청과 응답으로 데이터를 주고받는 웹 통신 규약이다.

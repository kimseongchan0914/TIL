# 게시판 만들기에 쓰이는 기술

## 1. 서버 언어 (Backend Language)

### 개념
서버에서 동작하며 전체 로직을 처리하는 프로그래밍 언어

### 대표 기술
- Java
- Python
- JavaScript (Node.js)

### 사용 (Spring 기준)
- Java

### 역할
- 요청 처리
- 데이터 가공
- 전체 프로그램 로직 수행

---

## 2. 웹 프레임워크

### 개념
웹 애플리케이션 개발을 쉽게 만들어주는 구조 및 도구

### 대표 기술
- Java: Spring / Spring Boot
- Python: Django / Flask
- Node.js: Express

### 사용
- Spring Boot

### 역할
- 서버 구조 제공
- Controller, Service 등 계층 구조 지원
- 개발 생산성 향상

---

## 3. 데이터베이스 (Database)

### 개념
데이터를 저장하고 관리하는 시스템

### 대표 기술
- MySQL
- PostgreSQL
- Oracle Database

### 역할
- 게시글 저장
- 사용자 정보 관리
- 댓글, 좋아요 데이터 저장

---

## 4. ORM (Object Relational Mapping)

### 개념
객체와 데이터베이스를 연결해주는 기술

### 대표 기술
- JPA
- Hibernate

### 역할
- SQL 없이 Java 코드로 DB 조작
- Entity와 테이블 자동 매핑

---

## 5. API / 통신 방식

### 개념
클라이언트와 서버 간 데이터 통신 방식

### 대표 기술
- HTTP
- REST API

### 역할
- 요청(Request)과 응답(Response) 처리
- JSON 형태로 데이터 전달

---

## 6. 빌드 도구

### 개념
프로젝트를 빌드하고 라이브러리를 관리하는 도구

### 대표 기술
- Gradle
- Maven

### 역할
- 의존성 관리
- 프로젝트 빌드 및 실행

---

## 7. 서버 실행 환경

### 개념
서버를 실행하고 요청을 처리하는 환경

### 대표 기술
- Apache Tomcat (Spring Boot 내장)

### 역할
- HTTP 요청 처리
- 서버 실행

---

## 8. 보안 (선택)

### 개념
사용자 인증 및 권한 관리를 담당

### 대표 기술
- Spring Security

### 역할
- 로그인 / 로그아웃
- 사용자 권한 관리

---

# 전체 구조

```
[프론트엔드]
    ↓ (HTTP 요청)
[Controller]
    ↓
[Service]
    ↓
[Repository (JPA)]
    ↓
[Database]
```

---

# 기능별 사용 기술

| 기능 | 사용 기술 |
|------|----------|
| 글 작성 | Controller + Service + JPA |
| 글 조회 | Repository + DB |
| 로그인 | Spring Security |
| 데이터 저장 | MySQL |
| API 통신 | REST API |

---

# 핵심 정리

- 언어: Java  
- 프레임워크: Spring Boot  
- 데이터베이스: MySQL  
- ORM: JPA  
- 통신: HTTP / REST API

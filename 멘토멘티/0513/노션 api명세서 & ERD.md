# ERD Cloud & API 명세서 학습 정리

## 학습 목적
토이 프로젝트를 진행하기 전, 데이터베이스 구조 설계와 API 설계 방법을 이해하기 위해 ERD Cloud와 API 명세서 작성 방법을 학습하였다.

---

# 1. ERD Cloud 학습 내용

## ERD(Entity Relationship Diagram)란?

ERD는 데이터베이스의 구조를 시각적으로 표현하는 설계도이다.  
어떤 데이터(Entity)가 존재하는지, 데이터 간 관계(Relationship)는 어떻게 연결되는지 나타낸다.

예시:

회원(User)
게시글(Post)
댓글(Comment)

관계:

회원 → 게시글 작성
게시글 → 댓글 보유

---

## ERD를 작성하는 이유

### 1. 데이터 구조 파악
어떤 테이블이 필요한지 미리 설계 가능

### 2. 관계 정의
1:N, N:M 등의 관계를 명확히 표현 가능

예시:

User 1 : N Post

한 명의 사용자는 여러 게시글 작성 가능

### 3. 개발 효율 증가
백엔드 개발 전 DB 구조를 정리하여 수정 비용 감소

### 4. Entity 설계 기반
ERD를 기준으로 JPA Entity 생성 가능

---

## ERD Cloud 사용 과정

### 1단계: Entity 생성

예시

User

속성:

id
email
password
nickname

---

Product

속성:

id
title
price
status

---

### 2단계: Primary Key 설정

PK(Primary Key)

예시:

id (PK)

데이터를 구분하는 고유 값

---

### 3단계: Foreign Key 설정

FK(Foreign Key)

예시:

user_id

다른 테이블 PK를 참조하여 관계 연결

---

### 4단계: 관계 설정

종류:

1:1
1:N
N:M

예시:

User 1 : N Product

한 사용자는 여러 상품 등록 가능

---

## 학습 결과

ERD 작성 시 단순히 테이블을 만드는 것이 아니라

데이터 구조
테이블 관계
PK/FK
정규화

까지 고려해야 한다는 점을 이해하였다.

---

# 2. API 명세서 학습 내용

## API 명세서란?

프론트엔드와 백엔드가 데이터를 주고받는 규칙을 문서화한 것이다.

포함 내용:

URL
Method
Request
Response
설명

---

## API 명세서 작성 이유

### 1. 협업 효율 증가

프론트엔드와 백엔드가 같은 규칙 사용 가능

### 2. 유지보수 편리

API 변경 사항 추적 가능

### 3. 개발 전 구조 설계 가능

구현 전에 필요한 기능 정리 가능

---

## API 명세서 예시

### 회원가입 API

URL

/api/users/signup

Method

POST

Request Body

```json
{
  "email": "test@test.com",
  "password": "1234",
  "nickname": "user"
}

# Spring 주요 구성 요소 정리 (Entity, Controller, Service, Repository, DTO)

## 1. 전체 구조

<img width="654" height="405" alt="image" src="https://github.com/user-attachments/assets/6f6e8bf0-9d08-4cb0-b939-42a745cd8859" />


---

## 2. Entity

### 개념
데이터베이스 테이블과 1:1로 매핑되는 객체

### 역할
- DB에 저장되는 실제 데이터 구조 표현
- 테이블의 컬럼과 동일한 필드를 가짐

### 특징
- `@Entity` 사용
- DB와 직접 연결됨
- 비즈니스 로직보다는 데이터 자체에 집중

### 핵심
- DB 구조를 코드로 표현한 객체
- 변경 시 DB 구조에도 영향 줄 수 있음

---

## 3. Controller

### 개념
클라이언트의 HTTP 요청을 받아 처리하는 계층

### 역할
- URL 요청을 받음
- Service 계층에 처리 위임
- 결과를 클라이언트에게 반환

### 특징
- `@Controller`, `@RestController` 사용
- 가장 바깥쪽 계층 (입구)

### 핵심
- 요청을 받고 응답을 반환하는 역할
- 비즈니스 로직은 직접 처리하지 않음

---

## 4. Service

### 개념
비즈니스 로직을 처리하는 계층

### 역할
- 핵심 로직 수행
- 여러 Repository를 조합하여 처리
- 트랜잭션 관리

### 특징
- `@Service` 사용
- Controller와 Repository 사이에 위치

### 핵심
- “실제 기능이 구현되는 곳”
- 로직의 중심

---

## 5. Repository

### 개념
데이터베이스와 직접 통신하는 계층

### 역할
- 데이터 저장, 조회, 수정, 삭제 (CRUD)
- DB 접근 로직 담당

### 특징
- `@Repository` 사용
- 보통 JPA 사용 (`JpaRepository`)

### 핵심
- DB와 연결된 데이터 처리 담당

---

## 6. DTO (Data Transfer Object)

### 개념
계층 간 데이터 전달을 위한 객체

### 역할
- Controller ↔ Service 간 데이터 전달
- 클라이언트와 서버 간 데이터 전달

### 특징
- Entity와 분리해서 사용
- 필요한 데이터만 포함

### 핵심
- 데이터 전달 전용 객체
- Entity 보호 역할

---

## 7. Entity vs DTO 차이

| 구분 | Entity | DTO |
|------|--------|-----|
| 목적 | DB 저장 | 데이터 전달 |
| 위치 | DB와 연결 | 계층 간 |
| 구조 | 테이블과 동일 | 필요한 데이터만 |
| 사용 이유 | 영속성 관리 | 보안, 유연성 |

---

## 8. 전체 흐름 정리

1. 클라이언트가 요청을 보냄
2. Controller가 요청을 받음
3. DTO로 데이터를 전달
4. Service에서 비즈니스 로직 처리
5. Repository를 통해 DB 접근
6. Entity를 통해 데이터 관리
7. 결과를 DTO로 변환
8. Controller가 응답 반환

---

## 9. 한줄 정리

Spring은 Controller, Service, Repository 계층 구조를 통해 역할을 분리하고,  
Entity와 DTO를 활용하여 데이터 관리와 전달을 효율적으로 수행한다.

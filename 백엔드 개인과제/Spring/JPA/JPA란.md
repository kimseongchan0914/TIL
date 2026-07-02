# JPA
### JPA는 자바 객체와 관계형 데이터베이스를 연결해 주는 자바 ORM표준 기술이다.

### JPA는 데이터베이스에 데이터를 저장하거나 조회할때 SQL을 직접 작성하는 대신 자바 객체를 사용하여 데이터를 관리할수있도록 지원합니다.

# JPA가 등장한 이유
### 기존 JDBC 방식에서는 데이터를 처리할 때마다 SQL을 직접 작성해야 했다.

INSERT INTO member(name, age) VALUES('Kim', 17);

SELECT * FROM member;

UPDATE member SET name='Lee' WHERE id=1;

DELETE FROM member WHERE id=1;

## 문제점
### 1. SQL 작성 코드가 반복된다.
### 2. 객체와 데이터베이스의 구조가 서로 다르다.
### 3. 유지보수가 어렵다.
### 4. 데이터베이스의 종속적인 코드가 많아진다.

# JPA 동작 원리
개발자 -> Entity(Java 객체) -> EntityManager -> 영속성 컨텍스트 -> Hibernate -> SQL 생성 및 실행 -> Database

### 즉 개발자는 객체만 다루고 SQL은 JPA가 관리함

# JPA 구현체
### JPA는 표준(API)이므로 실제 기능을 수행하는 구현체가 필요하다.
## 대표적인 구현체
* Hibernate (가장 많이 사용)
* EclipseLink
* OpenJPA
#### 현재 대부분의 spring boot 프로젝트에서는 Hibernate를 기본 구현체로 사용한다.

# JPA 장점
* SQL 작성량 감소
* 객체 중심 개발 가능
* 생산성 향상
* 유지보수 용이
* 데이터베이스 변경에 유연하게 대응 가능
* 캐시 및 변경 감지 기능 제공

# JPA 단점
* 학습 난도가 높다.
* 내부 동작 원리를 이해해야 한다.
* 복잡한 SQL은 직접 작성하는 것이 효율적인 경우도 있다.
* 성능 최적화를 위해 SQL과 데이터베이스 지식이 필요하다.

# Entity
Entity는 데이터베이스의 테이블과 매핑되는 자바객체를 의미한다.

# EntityManager
EntityManager은 Entity를 관리하는 객체이다.
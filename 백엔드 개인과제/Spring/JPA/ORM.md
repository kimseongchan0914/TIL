# ORM
ORM은 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑해주는 기술이다.

객체지향 언어에서 사용하는 객체와 관계형 데이터베이스의 테이블은 구조가 다르다. ORM은 이 둘 사이를 연결하여 개발자가 SQL을 직접 작성하지 않고도 객체를 이용해 데이터베이스를 사용할수 있도록 도와준다.

즉 ORM은 객체와 관계형 데이터베이스 사이의 중간다리 역할을 한다.

# 1. ORM이 필요한 이유
JAVA는 객체를 사용하여 데이터를 관리한다.
반면 관계형 데이터베이스는 테이블을 사용하여 데이터를 저장한다. 

예를들어 회원정보를 저장한다고 가정하면

Member member = new Member();<br>
member.setName("김성찬");

JAVA에서는 Member라는 객체를 생성하여 데이터를 관리한다. 하지만 데이터베이스 에서는 객체를 저장할수 없기 때문에 다음과 같이 테이블에 저장해야한다.

| ID | NAME |
| -- | ---- |
| 1  | 김성찬  |
즉 자바 -> 객체
   Database -> 테이블

ORM은 이런 차이를 자동으로 변환해줌

# 2. ORM의 동작 과정
ORM은 객체를 데이터베이스 테이블로 변환하고 테이블의 데이터를 다시 객체로 변환한다.

Java 객체

↓

ORM

↓

SQL 생성

↓

데이터베이스

#### 반대로 데이터를 조회할때는

데이터베이스

↓

SQL 실행

↓

ORM

↓

Java 객체

#### 즉 ORM은 객체와 데이터베이스 사이에서 데이터를 반환하는 역할을 수행한다.

# 3. ORM을 사용하지 않는 경우
ORM을 사용하지 않으면 SQL을 직접 작성해야한다.

    String sql = "INSERT INTO MEMBER(name) VALUES(?)";
    PreparedStatement pstmt = conn.prepareStatement(sql);

회원을 조회 할때도

     SELECT * FROM MEMBER;

수정할때도

    UPDATE MEMBER
    SET NAME='홍길동'
    WHERE ID=1;

삭제할때도

    DELETE FROM MEMBER
    WHERE ID=1;

# 4. ORM을 사용하는 경우
ORM을 사용하면 직접 SQL을 작성하지 않아도 된다.

    Member member = new Member();
    member.setName("김성찬");

    entityManager.persist(member);

그러면 ORM이 내부적으로

    INSERT INTO MEMBER(NAME)
    VALUES ('김성찬');

SQL을 자동으로 생성하여 실행한다.

개발자는 객체만 관리하면 된다.

# 5. ORM의 장점
1. SQL의 작성량 감소

2. 객체 중심 개발

3. 유지 보수 향상

4. 생산성 향상

5. 데이터베이스의 독립성

# 6. ORM의 단점

1. 학습 난이도가 있다.

2. 복잡한 SQL은 직접 작성해야 할수도 있다.

3. SQL을 몰라도 되는 것은 아니다.

# 7. ORM과 JPA의 관계

- ORM은 객체와 데이터베이스를 매핑하는 기술이다
- JPA는 JAVA에서 ORM을 사용하기 위한 표준 명세이다.
- Hibernate는 JPA를 실제로 구현한 ORM 프레임워크이다.

| 구분 | ORM                 | JPA               |
| -- | ------------------- | ----------------- |
| 의미 | 객체와 데이터베이스를 매핑하는 기술 | Java ORM 표준 명세    |
| 역할 | 객체와 테이블 연결          | ORM 사용 규칙 제공      |
| 종류 | 개념(기술)              | 표준(Specification) |

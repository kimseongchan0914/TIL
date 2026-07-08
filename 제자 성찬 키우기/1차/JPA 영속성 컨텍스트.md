# 1. JPA 영속성 컨텍스트란?
### 영속성 컨텍스트란 JPA에서 Entity를 저장하고 관리하는 논리적인 메모리 공간이다.
### JPA는 Entity를 바로 데이터베이스에 저장하거나 조회하지 않는다. 모든 Entity는 먼저 영속성 컨텍스트를 거쳐 관리된다.
### 즉 영속성 컨텍스트는 JPA와 데이터베이스 사이에서 Entity를 관리하는 중간 저장소 역할을 한다.

# 영속성 컨텍스트가 필요한 이유
### 만약 영속성 컨텍스트가 없다면 객체를 수정하거나 조회할때 직접 데이터베이스에 접근해야한다.

이러한 방식은 데이터베이스 접근이 많아져 성능이 저하될수있다. 영속성 컨텍스트를 Entity를 메모리에서 관리하면서 필요한 시점에만 SQL을 실행하여 서능을 향상시킴.
또한 Entity를 효율적으로 관리할수있으며, 변경사항도 자동으로 감지할수있다.

# 2. 영속성 컨텍스트의 역할
* Entity 생명주기 관리
* 1차 캐시(First Level Cache)
* 동일성 보장
* 변경 감지(Dirty Checking)
* 쓰기 지연(Write Behind)
* Entity와 데이터베이스 동기화

# 3. EntityManager와 영속성 컨텍스트
### 영속성 컨텍스트는 반드시 EntityManager를 통해 접근해야한다.


### EntityManager는 내부적으로 영속성 컨텍스트를 생성하고 관리한다.

### 즉 개발자는 EntityManager를 사용하지만 실제 Entity관리는 영속성 컨텍스트에서 이루어진다.

# 4. Entity 저장 과정
예를 들어 Entity를 저장한다고 가장 해보자

Member member = new Member();<br>
member.setName("김성찬");

entityManager.persist(member);  

### 동작 과정
Member 객체 생성

↓

persist()

↓

영속성 컨텍스트 저장

↓

쓰기 지연 저장소에 INSERT SQL 저장

↓

commit()

↓

INSERT 실행

↓

Database 저장

persist()를 호출하면 바로 INSERT가 실행된다고 생각하지만 실제로는 영속성 컨텍스트에만 저장된다.

데이터 베이스에는 flush() 와 commit()이 호출될때만 반영된다.

# 5. Entity 조회 과정
조회 과정도 먼저 영속성 컨텍스트를 확인한다.

Member member = entityManager.find(Member.class, 1L);

### 동작 과정

find()

↓

1차 캐시 확인

↓

있으면 객체 반환

↓

없으면 DB 조회

↓

Entity 생성

↓

영속성 컨텍스트 저장

↓

객체 반환

# 6. Entity 수정 과정
JPA에서는 수정을 위한 별도의 메서드가 존재하지 않는다.

Member member = entityManager.find(Member.class,1L);<br>
member.setName("홍길동");

그저 객체의 값을 변경하기만 하면 됩니다. 트랜잭션이 종료될 때 JPA가 변경사항을 감지하여 UPDATE SQL을 자동을 생성한다.

### 동작과정

Entity 조회

↓

영속성 컨텍스트 관리

↓

객체 수정

↓

commit()

↓

변경 감지

↓

UPDATE 실행

# 7. Entity 삭제 과정
삭제는 remove()를 사용한다.

entityManager.remove(member);

### 동작과정

remove()

↓

삭제 상태

↓

commit()

↓

DELETE SQL 실행

삭제도 즉시 실행되지 않고 트랜잭션이 종료될 때 데이터베이스에 반영된다.

# 8. 영속성 컨텍스트의 주요 기능

## 1차 캐시
영속성 컨텍스트는 조회한 Entity를 메모리에 저장한다.

같은 entity를 다시 조회하면 데이터베이스 대신 메모리에서 가져온다.

### 장점

* 조회 속도 향상
* 데이터베이스 접근 횟수 감소 
<hr>

## 동일성 보장

Member m1 = entityManager.find(Member.class,1L);<br>
Member m2 = entityManager.find(Member.class,1L);

결과는 m1 == m2 이다.

같은 Entity는 항상 같은 객체를 반환한다.

## 변경 감지
영속 상태의 Entity가 수정되면 JPA가 변경내용을 자동으로 감지하여 UPDATE SQL을 생성한다.

member.setName("홍길동");<br>
↓<br>
UPDATE MEMBER<br>
SET NAME='홍길동'<br>
WHERE ID=1;

개발자가 UPDATE SQL을 직접 작성할 필요가 없다.

## 쓰기 지연
persist()를 호출해도 SQL은 즉시 실행되지 않는다.

entityManager.persist(member);

↓

영속성 컨텍스트 저장

↓

INSERT SQL 보관

↓

commit()

↓

INSERT 실행

여러개의 SQL을 모아서 한 번에 실행하기 때문에 성능 향상에 도움이 된다.

# 9. 영속성 컨텍스트와 데이터베이스의 관계

Entity 생성 -> EntityManager.persist() -> 영속성 컨텍스트 저장 -> 쓰기 지연 저장소 -> flush() -> SQL 실행 -> Database

영속성 컨텍스트는 객체를 관리하고 데이터베이스와의 동기화 시점을 결정하는 역할을 수행한다. 

# 10. 영속성 컨텍스트의 장점

* Entity를 효율적으로 관리할 수 있다.
* 데이터베이스 접근 횟수를 줄일 수 있다.
* 조회 성능이 향상된다.
* 객체 변경만으로 UPDATE가 가능하다.
* SQL 실행 시점을 조절하여 성능을 향상시킨다.
* 동일한 Entity에 대해 동일한 객체를 보장한다.

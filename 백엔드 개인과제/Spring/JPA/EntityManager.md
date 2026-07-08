# EntityManager 정리

## 한 줄 정의

**EntityManager는 엔티티(객체)를 관리하고, 영속성 컨텍스트를 통해 DB와의 저장·조회·수정·삭제를 담당하는 JPA의 핵심 도구**다.

개발자가 DB를 직접 다루지 않고, EntityManager를 통해 객체를 다루면 JPA가 알아서 SQL을 만들어 처리해준다.

---

## 1. EntityManager의 위치

전체 계층에서 EntityManager가 어디 있는지 보면 이해가 쉽다.

```
[개발자 코드]  persist(), find(), JPQL 등
        │
        ▼
[EntityManager]  ← 여기! 엔티티 관리 + 영속성 컨텍스트 관리
        │
        ▼
[JDBC]  SQL을 DB에 실제로 전달·실행
        │
        ▼
[데이터베이스]
```

- **JPA** = 규칙(표준 명세)
- **Hibernate** = JPA를 구현한 라이브러리(실제 동작)
- **EntityManager** = JPA가 정의한 핵심 도구(인터페이스). 실제로는 Hibernate가 만든 구현체가 동작한다.

> 비유: JPA는 "운전 규칙", Hibernate는 "자동차", EntityManager는 "핸들". 개발자는 핸들을 조작하고, 실제로 움직이는 건 자동차다.

---

## 2. 영속성 컨텍스트와의 관계

EntityManager는 **영속성 컨텍스트(Persistence Context)** 를 관리한다.

- 영속성 컨텍스트 = 엔티티를 보관하는 메모리 공간(1차 캐시)
- EntityManager를 통해 엔티티를 저장/조회하면, 그 엔티티는 영속성 컨텍스트에 관리된다
- **EntityManager 하나당 영속성 컨텍스트 하나**가 연결된다고 이해하면 된다

즉, EntityManager는 영속성 컨텍스트에 접근하는 **창구**다.

---

## 3. 엔티티의 생명주기 (EntityManager가 관리)

EntityManager는 엔티티의 상태(생명주기)를 관리한다.

| 상태 | 설명 |
|------|------|
| **비영속 (new/transient)** | 객체를 막 생성한 상태. 영속성 컨텍스트와 아무 관계 없음 |
| **영속 (managed)** | `persist()` 등으로 영속성 컨텍스트가 관리하는 상태. 변경 감지 대상 |
| **준영속 (detached)** | 관리되다가 분리된 상태. 더 이상 변경 감지 안 됨 |
| **삭제 (removed)** | `remove()`로 삭제 예약된 상태 |

```kotlin
val member = Member(name = "홍길동")   // 비영속 (그냥 객체)
entityManager.persist(member)          // 영속 (이제 관리됨)
entityManager.detach(member)           // 준영속 (관리에서 분리)
entityManager.remove(member)           // 삭제
```

---

## 4. 주요 메서드

### persist() — 저장
엔티티를 영속성 컨텍스트에 저장한다. (아직 DB엔 안 감, flush 시점에 INSERT)

```kotlin
val member = Member(name = "홍길동")
entityManager.persist(member)   // 영속 상태로 만듦
```

### find() — 조회 (기본키로)
기본키(PK)로 엔티티를 조회한다. **1차 캐시를 먼저 확인**하고, 없으면 DB에서 가져온다.

```kotlin
val member = entityManager.find(Member::class.java, 1L)   // id=1 조회
```

### remove() — 삭제
엔티티를 삭제 상태로 만든다. (flush 시점에 DELETE)

```kotlin
entityManager.remove(member)
```

### flush() — DB에 반영
영속성 컨텍스트의 변경사항을 DB에 SQL로 내보낸다. (확정은 아님, rollback 가능)

```kotlin
entityManager.flush()
```

### clear() / detach() — 관리 해제
- `clear()`: 영속성 컨텍스트를 통째로 비움 (모든 엔티티 준영속化)
- `detach(entity)`: 특정 엔티티만 관리에서 분리

```kotlin
entityManager.clear()          // 전체 비우기
entityManager.detach(member)   // 하나만 분리
```

### createQuery() — JPQL 실행
JPQL 쿼리를 실행한다.

```kotlin
val members = entityManager
    .createQuery("SELECT m FROM Member m WHERE m.age >= 20", Member::class.java)
    .resultList
```

---

## 5. 변경 감지 (Dirty Checking)

EntityManager의 강력한 기능. **영속 상태 엔티티의 값을 바꾸면, 별도로 `save()`나 `update()`를 호출하지 않아도 자동으로 UPDATE SQL이 나간다.**

```kotlin
@Transactional
fun updateName(id: Long) {
    val member = entityManager.find(Member::class.java, id)  // 영속 상태
    member.name = "김철수"   // 값만 바꿈 (update 호출 안 함!)

}   // 트랜잭션 끝 → flush 시 변경 감지 → UPDATE SQL 자동 실행
```

원리: 영속성 컨텍스트가 엔티티를 처음 가져올 때의 상태(스냅샷)를 기억해두고, flush 시점에 현재 값과 비교해서 달라진 부분만 UPDATE 한다.

---

## 6. flush와 commit과의 연결

- **flush** → EntityManager가 실행. 쌓인 SQL을 DB로 내보냄
- **commit** → 트랜잭션 매니저가 실행. commit 직전에 EntityManager의 flush가 자동 호출됨

```kotlin
@Transactional
fun example() {
    entityManager.persist(member)   // 1차 캐시에 저장
    // ...
}   // 메서드 끝 → commit 호출 → 그 직전 flush() 자동 실행 → 확정
```

---

## 7. 실무에서는 직접 안 쓰는 경우가 많다

Spring Data JPA를 쓰면 EntityManager를 직접 다루지 않고 **Repository**로 대신하는 경우가 많다.

```kotlin
// 직접 EntityManager 사용
entityManager.persist(member)
entityManager.find(Member::class.java, 1L)

// Spring Data JPA Repository 사용 (내부적으로 EntityManager를 씀)
memberRepository.save(member)
memberRepository.findById(1L)
```

Repository의 `save()`, `findById()` 등도 내부적으로는 결국 **EntityManager를 호출**한다. 즉 Repository는 EntityManager를 편하게 감싼 것이다.

---

## 핵심 정리

- **EntityManager = 엔티티를 관리하고 영속성 컨텍스트에 접근하는 JPA의 핵심 도구**
- 엔티티의 생명주기(비영속/영속/준영속/삭제)를 관리한다
- 주요 메서드: `persist`(저장), `find`(조회), `remove`(삭제), `flush`(DB 반영), `createQuery`(JPQL)
- **변경 감지**: 영속 엔티티의 값만 바꿔도 UPDATE가 자동 실행됨
- flush는 EntityManager가 직접 실행하고, commit 직전에 자동 호출된다
- 실무에선 Spring Data JPA의 Repository가 EntityManager를 대신 감싸서 처리한다

## 한 줄 요약

**EntityManager는 개발자와 DB 사이에서 엔티티(객체)를 관리하는 관리자다. 개발자는 객체만 다루면, EntityManager가 영속성 컨텍스트를 통해 저장·조회·수정·삭제를 알아서 처리해준다.**
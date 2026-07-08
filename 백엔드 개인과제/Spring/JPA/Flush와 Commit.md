# Flush와 Commit

## 사전 개념: 영속성 컨텍스트

JPA는 엔티티를 바로 DB에 저장하지 않는다. 먼저 **영속성 컨텍스트(Persistence Context)** 라는 메모리 공간(1차 캐시)에 모아둔다. 여기서 실행될 SQL은 **쓰기 지연 SQL 저장소**에 쌓였다가 특정 시점에 DB로 나간다. Flush와 Commit은 바로 이 "언제, 어떻게 DB로 나가고 확정되는가"를 다루는 개념이다.

> **비유 — 은행 송금**
> - 계좌이체 정보를 입력하는 것 = 영속성 컨텍스트에 저장
> - **송금 버튼을 눌러 상대 계좌에 돈이 찍히는 것 = Flush** (보이긴 하는데, 아직 취소 가능)
> - **"이 거래 확정" 도장을 찍는 것 = Commit** (이제 취소 불가)

---

## Flush

영속성 컨텍스트에 쌓인 변경사항(등록·수정·삭제)을 **DB에 SQL로 내보내는 동작**이다.

### 특징
- **DB 반영 O, 확정 X** — SQL은 DB로 전송되지만 트랜잭션은 아직 끝나지 않았다. 이 시점에도 rollback으로 되돌릴 수 있다.
- **캐시를 비우지 않는다** — flush는 변경 내용을 DB에 전송할 뿐, 1차 캐시의 엔티티는 그대로 남는다. (캐시를 비우는 건 `clear()`다.)

### 발생하는 3가지 경우
1. **직접 호출** — `entityManager.flush()`를 명시적으로 실행할 때
2. **트랜잭션 commit 직전** — commit 전에 JPA가 자동으로 flush 호출
3. **JPQL 쿼리 실행 직전** — 정확한 조회 결과 보장을 위해 쿼리 전에 자동 flush

### 예시: flush가 필요한 상황 (커밋 전에 ID를 알고 싶을 때)

```kotlin
val channel = Channel(name = "general")
entityManager.persist(channel)
// 아직 DB에 안 갔으므로 channel.id 는 null 일 수 있음

entityManager.flush()
// 여기서 INSERT SQL 실행 → DB가 auto-increment ID를 생성
println(channel.id)   // 이제 생성된 ID(예: 1)를 커밋 전에 확인 가능
```

### 예시: JPQL 실행 전 자동 flush

```kotlin
val member = Member(name = "홍길동")
entityManager.persist(member)   // 1차 캐시에만 있음, DB엔 아직 X

// JPQL 조회 실행 → JPA가 "조회 전에 반영부터 하자"며 자동 flush 먼저 실행
val result = entityManager
    .createQuery("SELECT m FROM Member m WHERE m.name = '홍길동'", Member::class.java)
    .resultList
// 자동 flush 덕분에 방금 저장한 홍길동도 조회 결과에 포함됨
```

---

## Commit

트랜잭션을 **최종 확정하는 동작**이다.

### 특징
- **되돌릴 수 없다** — 확정 이후에는 rollback 불가능. DB에 영구 저장된다.
- **Flush를 포함한다** — commit은 항상 flush를 먼저 실행한 뒤 트랜잭션을 확정한다.

### 동작 순서
1. **Flush 자동 실행** — 아직 DB에 안 보낸 변경사항을 SQL로 내보냄
2. **DB 트랜잭션 Commit** — DB에게 "이 트랜잭션 확정해"라고 명령
3. 이후 변경 불가

### 예시: flush 후 rollback vs commit 차이

```kotlin
// 상황 A — flush만 하고 rollback
entityManager.persist(channel)
entityManager.flush()      // DB에 INSERT SQL 전송됨
transaction.rollback()     // → 취소됨! DB에 최종 저장 안 됨

// 상황 B — flush 후 commit
entityManager.persist(channel)
entityManager.
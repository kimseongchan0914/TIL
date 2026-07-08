# Dirty Checking (변경 감지) 정리

## 한 줄 정의

**Dirty Checking은 영속 상태 엔티티의 값이 바뀌면, `update()`를 직접 호출하지 않아도 JPA가 변경을 자동으로 감지해서 UPDATE SQL을 실행해주는 기능**이다.

`dirty` = "값이 바뀐(더러워진)" 상태를 뜻하고, `checking` = "그걸 검사한다"는 의미다. 즉 "바뀐 게 있는지 검사한다"는 뜻이다.

---

## 1. 핵심 개념

JPA에서는 이렇게 하면 자동으로 수정이 반영된다.

```kotlin
@Transactional
fun updateName(id: Long) {
    val member = memberRepository.findById(id).get()  // 영속 상태로 조회
    member.name = "김철수"   // 값만 바꿈 (save/update 호출 안 함!)

}   // 트랜잭션 끝 → 변경 감지 → UPDATE SQL 자동 실행
```

**주목할 점**: `save()`나 `update()`를 부르지 않았는데도 `name`이 "김철수"로 DB에 반영된다. 이것이 Dirty Checking이다.

---

## 2. 동작 원리 (스냅샷 비교)

JPA는 어떻게 값이 바뀐 걸 알까? **스냅샷(snapshot)** 을 이용한다.

### 흐름

1. **엔티티를 영속성 컨텍스트에 처음 가져올 때**, JPA가 그 순간의 값을 **스냅샷(사진)** 으로 복사해둔다.
2. 개발자가 엔티티 값을 바꾼다. (스냅샷은 그대로, 실제 엔티티만 바뀜)
3. **flush 시점**(트랜잭션 commit 직전 등)에, JPA가 **현재 엔티티 값 vs 스냅샷**을 비교한다.
4. 달라진 필드가 있으면 → **UPDATE SQL 자동 생성 및 실행**.
5. 똑같으면 → 아무 SQL도 안 나감.

```
[조회 시점]
   엔티티: name="홍길동"
   스냅샷: name="홍길동"   ← 사진 찍어둠

[값 변경]
   엔티티: name="김철수"   ← 바뀜
   스냅샷: name="홍길동"   ← 그대로

[flush 시점 - 비교]
   엔티티(김철수) ≠ 스냅샷(홍길동)  → 다름! → UPDATE 실행
```

---

## 3. 발생 조건 (중요)

Dirty Checking은 아무 때나 되는 게 아니다. **다음 조건을 모두 만족해야** 한다.

1. **영속 상태(managed)의 엔티티여야 한다**
   - `find()`나 JPQL로 조회해서 영속성 컨텍스트가 관리 중인 엔티티만 대상
   - 비영속(new)이나 준영속(detached) 엔티티는 변경 감지 안 됨
2. **트랜잭션 안에서 이뤄져야 한다**
   - `@Transactional` 범위 안에서 값을 바꿔야 flush/commit이 일어나며 감지됨

```kotlin
// 변경 감지 O
@Transactional
fun ok(id: Long) {
    val member = memberRepository.findById(id).get()  // 영속 상태
    member.name = "김철수"                             // 감지됨
}

// 변경 감지 X (준영속 상태)
fun no(member: Member) {   // @Transactional 없음, 이미 분리된 엔티티
    member.name = "김철수"  // 감지 안 됨 → DB 반영 안 됨
}
```

---

## 4. 왜 편리한가

Dirty Checking이 없다면 이렇게 명시적으로 저장해야 한다.

```kotlin
// Dirty Checking 없이 (수동)
val member = memberRepository.findById(id).get()
member.name = "김철수"
memberRepository.save(member)   // 직접 저장 호출 필요
```

Dirty Checking이 있으면 `save()` 호출이 필요 없다.

```kotlin
// Dirty Checking 활용 (자동)
val member = memberRepository.findById(id).get()
member.name = "김철수"
// 끝. 트랜잭션 종료 시 자동 UPDATE
```

**객체의 값을 바꾸는 것만으로 DB가 동기화**되므로, 마치 메모리 객체를 다루듯 자연스럽게 데이터를 수정할 수 있다.

---

## 5. 주의점

### (1) UPDATE는 기본적으로 모든 필드를 포함한다
Hibernate는 기본 설정에서 바뀐 필드만이 아니라 **엔티티의 전체 필드를 UPDATE SQL에 담는다.**
(성능 최적화가 필요하면 `@DynamicUpdate`를 붙여 바뀐 필드만 UPDATE하도록 할 수 있다.)

### (2) 준영속 엔티티는 감지 안 됨
트랜잭션 밖에서 받은 엔티티나 `detach()`/`clear()`된 엔티티는 변경 감지 대상이 아니다. 이 경우 `save()`(merge)로 다시 영속화해야 반영된다.

### (3) 조회한 뒤 바꿔야 한다
새로 만든 객체(`new Member()`)의 값을 바꾸는 건 Dirty Checking과 무관하다. 반드시 **영속성 컨텍스트가 관리하는(조회된) 엔티티**여야 한다.

---

## 6. flush/commit과의 연결

Dirty Checking은 **flush 시점에 일어난다.**

```kotlin
@Transactional
fun example(id: Long) {
    val member = memberRepository.findById(id).get()  // 스냅샷 저장
    member.name = "김철수"                             // 엔티티만 변경

}   // 트랜잭션 종료
    // → commit 직전 flush 자동 실행
    // → flush가 스냅샷과 비교(Dirty Checking) → UPDATE SQL 생성
    // → commit으로 확정
```

즉, **flush → 변경 감지 → UPDATE 생성 → commit 확정** 순서로 이어진다.

---

## 핵심 정리

- **Dirty Checking = 영속 엔티티의 값이 바뀌면 자동으로 UPDATE 해주는 기능**
- 원리: 조회 시 **스냅샷**을 저장해두고, flush 시점에 **현재 값과 비교**해서 다르면 UPDATE
- 조건: **영속 상태 엔티티 + 트랜잭션 안**에서만 동작
- `save()`/`update()` 호출 없이 **객체 값만 바꿔도** DB에 반영됨
- 준영속·비영속 엔티티는 감지되지 않는다
- flush 시점에 동작하며, commit 직전에 자동으로 이뤄진다

## 한 줄 요약

**조회한 엔티티의 값만 바꾸면, JPA가 스냅샷과 비교해 바뀐 걸 알아채고 트랜잭션이 끝날 때 UPDATE를 자동으로 실행해준다 — 이것이 Dirty Checking이다.**
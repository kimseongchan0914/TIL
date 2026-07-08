# Stream API

## Stream API란?

**Stream API**는 Java 8부터 추가된 기능으로, 컬렉션(List, Set 등)에 저장된 데이터를 **반복문 없이 선언형 방식으로 처리**할 수 있도록 도와주는 API이다.

기존에는 `for`문과 `if`문을 사용해 데이터를 처리했지만, Stream API를 사용하면 **가독성이 높고 유지보수가 쉬운 코드**를 작성할 수 있다.

> Stream은 데이터를 저장하는 것이 아니라, 데이터를 **처리하기 위한 흐름(Stream)** 을 만들어 작업하는 것이다.

---

# Stream API를 사용하는 이유

기존 방식

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

List<Integer> result = new ArrayList<>();

for (Integer number : numbers) {
    if (number % 2 == 0) {
        result.add(number * 10);
    }
}

System.out.println(result);
```

### 문제점

- 코드가 길어진다.
- 반복문과 조건문이 많아질수록 가독성이 떨어진다.
- 원하는 작업이 한눈에 들어오지 않는다.

---

Stream API 사용

```java
List<Integer> result = numbers.stream()
        .filter(n -> n % 2 == 0)
        .map(n -> n * 10)
        .toList();
```

### 장점

- 코드가 간결하다.
- 읽기 쉽다.
- 유지보수가 쉽다.
- 병렬 처리(parallelStream)를 쉽게 사용할 수 있다.

---

# Stream API 처리 과정

```
원본 데이터(List)

        │

        ▼

stream()

        │

        ▼

filter()

        │

        ▼

map()

        │

        ▼

toList()

        │

        ▼

최종 결과
```

예시

```
[1,2,3,4,5,6]

↓

stream()

↓

filter()

↓

[2,4,6]

↓

map()

↓

[20,40,60]

↓

toList()

↓

[20,40,60]
```

---

# Stream API의 특징

### 1. 원본 데이터를 변경하지 않는다.

```java
List<Integer> numbers = List.of(1,2,3);

List<Integer> result = numbers.stream()
        .map(n -> n * 10)
        .toList();

System.out.println(numbers);
```

출력

```
[1, 2, 3]
```

원본은 그대로 유지된다.

---

### 2. 일회성이다.

한 번 사용한 Stream은 다시 사용할 수 없다.

```java
Stream<Integer> stream = numbers.stream();

stream.forEach(System.out::println);

// 오류 발생
stream.forEach(System.out::println);
```

새로운 Stream을 다시 만들어야 한다.

---

### 3. 중간 연산과 최종 연산으로 이루어진다.

중간 연산

- filter()
- map()
- sorted()
- distinct()

최종 연산

- toList()
- collect()
- forEach()
- count()
- reduce()

---

# stream()

컬렉션을 Stream으로 변환한다.

```java
numbers.stream()
```

```
List

↓

Stream 생성
```

아직 아무 작업도 하지 않는다.

---

# filter()

조건에 맞는 데이터만 선택한다.

```java
.filter(n -> n % 2 == 0)
```

의미

```
짝수만 남겨라.
```

처리 과정

```
1 ❌

2 ✅

3 ❌

4 ✅

5 ❌

6 ✅
```

결과

```
[2,4,6]
```

---

# map()

데이터를 다른 형태로 변환한다.

```java
.map(n -> n * 10)
```

처리 과정

```
2 → 20

4 → 40

6 → 60
```

결과

```
[20,40,60]
```

### 문자열 변환 예제

```java
List<String> result = numbers.stream()
        .map(n -> n + "점")
        .toList();
```

결과

```
["1점","2점","3점"]
```

---

# toList()

Stream의 결과를 List로 변환한다.

```java
.toList();
```

최종 결과

```
[20,40,60]
```

---

# 전체 코드

```java
import java.util.List;

public class Main {

    public static void main(String[] args) {

        List<Integer> numbers = List.of(1,2,3,4,5,6);

        List<Integer> result = numbers.stream()
                .filter(n -> n % 2 == 0)
                .map(n -> n * 10)
                .toList();

        System.out.println(result);
    }
}
```

실행 결과

```
[20,40,60]
```

---

# 람다식(Lambda)

Stream API에서 가장 많이 사용하는 문법이다.

형식

```java
매개변수 -> 실행할 코드
```

예시

```java
n -> n * 10
```

의미

```
n을 받아서

10을 곱한 값을 반환한다.
```

---

또 다른 예시

```java
n -> n % 2 == 0
```

의미

```
n을 받아서

짝수인지 검사한다.
```

---

기존 방식과 비교

기존 코드

```java
for (Integer n : numbers) {
    if (n % 2 == 0) {

    }
}
```

람다식

```java
.filter(n -> n % 2 == 0)
```

---

# 백엔드에서 가장 많이 사용하는 예제

회원 목록

```java
public class Member {

    private String name;
    private int age;

    public Member(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

회원 데이터

```java
List<Member> members = List.of(
        new Member("김철수", 17),
        new Member("이영희", 21),
        new Member("박민수", 25)
);
```

성인 회원 이름만 가져오기

```java
List<String> names = members.stream()
        .filter(member -> member.getAge() >= 20)
        .map(Member::getName)
        .toList();
```

결과

```
[이영희, 박민수]
```

---

# Spring Boot에서 사용하는 이유

JPA로 데이터를 조회하면 대부분 List<Entity>가 반환된다.

```
DB

↓

Entity 조회

↓

Stream

↓

DTO 변환

↓

JSON 응답
```

예시

```java
List<MemberDto> result = members.stream()
        .map(member -> new MemberDto(
                member.getId(),
                member.getName()
        ))
        .toList();
```

서비스 계층에서 가장 많이 볼 수 있는 코드 형태이다.

---

# 자주 사용하는 Stream 메서드

| 메서드 | 설명 |
|---------|------|
| stream() | Stream 생성 |
| filter() | 조건에 맞는 데이터 선택 |
| map() | 데이터 변환 |
| sorted() | 정렬 |
| distinct() | 중복 제거 |
| limit() | 개수 제한 |
| skip() | 건너뛰기 |
| count() | 개수 세기 |
| findFirst() | 첫 번째 데이터 |
| anyMatch() | 하나라도 만족하는지 검사 |
| allMatch() | 모두 만족하는지 검사 |
| noneMatch() | 모두 만족하지 않는지 검사 |
| forEach() | 반복 실행 |
| reduce() | 하나의 결과값으로 합치기 |
| toList() | List로 변환 |

---

# 핵심 정리

- Stream API는 컬렉션 데이터를 효율적으로 처리하기 위한 API이다.
- 원본 데이터를 변경하지 않는다.
- `stream()`으로 데이터를 처리할 준비를 한다.
- `filter()`는 조건에 맞는 데이터만 선택한다.
- `map()`은 데이터를 다른 형태로 변환한다.
- `toList()`는 처리 결과를 List로 변환한다.
- Stream은 한 번만 사용할 수 있다.
- Spring Boot와 JPA에서는 Entity를 DTO로 변환할 때 매우 자주 사용된다.

---

# 한 줄 요약

> **Stream API는 "반복문을 더 간결하고 읽기 쉽게 작성하기 위한 Java의 데이터 처리 API"이며, 백엔드 개발에서는 Entity 조회, DTO 변환, 데이터 필터링 및 가공에 매우 자주 사용된다.**
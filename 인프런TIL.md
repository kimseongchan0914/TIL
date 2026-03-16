# 📌 Java 변수 (Variable)

## 변수란?
**변수(Variable)** 는 데이터를 저장하기 위한 **이름이 붙은 공간**이다.  
프로그램에서 값을 저장하고 필요할 때 다시 사용하기 위해 사용한다.

--- 변수 선언 하는 방식

int age = 17;

String name = "Kim";

double height = 175.5;

### 자주 사용하는 자료형

| 자료형     | 설명   | 예시      |
| ------- | ---- | ------- |
| int     | 정수   | 10      |
| double  | 소수   | 3.14    |
| char    | 문자   | 'A'     |
| boolean | 참/거짓 | true    |
| String  | 문자열  | "Hello" |


# 📌 Java 연산자 (Operator)

## 연산자란?

**연산자(Operator)** 는 변수나 값에 대해 **계산을 수행하는 기호**이다.
예를 들어 더하기, 빼기, 비교 같은 연산을 할 때 사용한다.

---

## 산술 연산자

| 연산자 | 설명  | 예시    |
| --- | --- | ----- |
| +   | 덧셈  | a + b |
| -   | 뺄셈  | a - b |
| *   | 곱셈  | a * b |
| /   | 나눗셈 | a / b |
| %   | 나머지 | a % b |

예시

```java
int a = 10;
int b = 3;

System.out.println(a + b); // 13
System.out.println(a % b); // 1
```

---

## 비교 연산자

| 연산자 | 설명     |
| --- | ------ |
| ==  | 같다     |
| !=  | 같지 않다  |
| >   | 크다     |
| <   | 작다     |
| >=  | 크거나 같다 |
| <=  | 작거나 같다 |

예시

```java
int a = 10;
int b = 5;

System.out.println(a > b); // true
System.out.println(a == b); // false
```

---

## 논리 연산자

| 연산자 | 설명        |
| --- | --------- |
| &&  | AND (그리고) |
| ||  | OR (또는)   |
| !   | NOT (부정)  |

예시

```java
boolean a = true;
boolean b = false;

System.out.println(a && b); // false
System.out.println(a || b); // true
```

# 📌 Java 조건문 (Conditional Statement)

## 조건문이란?

**조건문(Conditional Statement)** 은 주어진 **조건의 결과에 따라 다른 코드를 실행**하도록 하는 문법이다.
조건이 **참(true)** 이면 특정 코드를 실행하고, **거짓(false)** 이면 다른 코드를 실행한다.

---

## if 문

`if` 문은 조건이 **true일 때만 코드가 실행**된다.

문법

```java
if (조건식) {
    실행할 코드
}
```

예시

```java
int age = 18;

if (age >= 18) {
    System.out.println("성인입니다.");
}
```

---

## if - else 문

조건이 **true면 if**, **false면 else**가 실행된다.

```java
int age = 16;

if (age >= 18) {
    System.out.println("성인입니다.");
} else {
    System.out.println("미성년자입니다.");
}
```

---

## if - else if - else 문

여러 개의 조건을 검사할 때 사용한다.

```java
int score = 85;

if (score >= 90) {
    System.out.println("A");
} else if (score >= 80) {
    System.out.println("B");
} else {
    System.out.println("C");
}
```

# 📌 Java 반복문 (Loop)

## 반복문이란?

**반복문(Loop)** 은 특정 코드를 **여러 번 반복해서 실행**할 때 사용하는 문법이다.
같은 작업을 반복해야 할 때 코드를 여러 번 작성하지 않아도 된다.

---

## for 문

`for` 문은 **반복 횟수가 정해져 있을 때** 많이 사용한다.

문법

```java
for (초기식; 조건식; 증감식) {
    실행할 코드
}
```

예시

```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}
```

출력

```
1
2
3
4
5
```

---

## while 문

`while` 문은 **조건이 true인 동안 계속 반복**된다.

문법

```java
while (조건식) {
    실행할 코드
}
```

예시

```java
int i = 1;

while (i <= 5) {
    System.out.println(i);
    i++;
}
```

---

## do - while 문

`do-while` 문은 **조건과 상관없이 최소 한 번 실행**된다.

```java
int i = 1;

do {
    System.out.println(i);
    i++;
} while (i <= 5);
```

# 📌 Java 스코프 (Scope)

## 스코프란?

**스코프(Scope)** 는 **변수를 사용할 수 있는 범위**를 의미한다.
변수는 선언된 위치에 따라 사용할 수 있는 범위가 달라진다.

---

## 지역 변수 (Local Variable)

**지역 변수**는 메서드나 블록 `{ }` 안에서 선언된 변수이다.
해당 블록 안에서만 사용할 수 있다.

예시

```java
public class Main {
    public static void main(String[] args) {

        int number = 10; // 지역 변수

        if (number > 5) {
            int result = 20;
            System.out.println(result);
        }

    }
}
```

---

## 블록 스코프

변수는 **선언된 블록 안에서만 사용 가능**하다.

```java
if (true) {
    int x = 10;
}

System.out.println(x); // 오류 발생
```

# 📌 Java 형변환 (Type Casting)

## 형변환이란?

**형변환(Type Casting)** 은 **데이터의 자료형을 다른 자료형으로 바꾸는 것**이다.

예를 들어 `int`를 `double`로 바꾸거나 `double`을 `int`로 바꿀 수 있다.

---

## 자동 형변환 (Implicit Casting)

작은 범위의 자료형이 **큰 범위의 자료형으로 자동 변환**되는 것이다.

```java
int num = 10;
double result = num;

System.out.println(result);
```

출력

```
10.0
```

---

## 강제 형변환 (Explicit Casting)

큰 범위의 자료형을 **작은 범위로 바꿀 때 직접 형변환**을 해야 한다.

```java
double num = 3.14;
int result = (int) num;

System.out.println(result);
```

출력

```
3
```

---





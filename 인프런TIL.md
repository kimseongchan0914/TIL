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

# 📌 Java Scanner

## Scanner란?

**Scanner**는 사용자로부터 **입력을 받기 위한 클래스**이다.
키보드로 값을 입력받아 변수에 저장할 때 사용한다.

---

## Scanner 사용 방법

Scanner를 사용하려면 먼저 import가 필요하다.

```java id="s2k9q1"
import java.util.Scanner;
```

객체 생성

```java id="x8d2lm"
Scanner sc = new Scanner(System.in);
```

---

## 입력 받기

| 메서드          | 설명           |
| ------------ | ------------ |
| nextInt()    | 정수 입력        |
| nextDouble() | 실수 입력        |
| next()       | 문자열(한 단어) 입력 |
| nextLine()   | 문자열(한 줄) 입력  |

예시

```java id="a7p3zm"
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int age = sc.nextInt();
        String name = sc.next();

        System.out.println(age);
        System.out.println(name);
    }
}
```

---

## 주의할 점

`nextInt()` 다음에 `nextLine()`을 사용할 때는 **엔터가 남아서 문제가 생길 수 있다.**

```java id="p6n2rk"
int num = sc.nextInt();
sc.nextLine(); // 엔터 제거

String str = sc.nextLine();
```
# 📌 Java 배열 (Array)

## 배열이란?

**배열(Array)** 은 같은 자료형의 데이터를 **여러 개 저장할 수 있는 자료구조**이다.
여러 개의 값을 하나의 변수로 관리할 수 있다.

---

## 배열 선언 방법

배열은 다음과 같이 선언한다.

```java id="k3m8qp"
자료형[] 배열이름 = new 자료형[크기];
```

예시

```java id="w7d2ls"
int[] arr = new int[3];
```

---

## 배열 초기화

배열을 선언하면서 값을 바로 넣을 수도 있다.

```java id="n5z8yx"
int[] arr = {10, 20, 30};
```

---

## 배열 사용

배열은 **인덱스(index)** 를 사용하여 값을 저장하고 꺼낸다.
인덱스는 **0부터 시작**한다.

```java id="p4c9ut"
int[] arr = {10, 20, 30};

System.out.println(arr[0]); // 10
System.out.println(arr[1]); // 20

arr[2] = 50; // 값 변경
```

---

## 반복문과 배열

배열은 반복문과 함께 자주 사용된다.

```java id="z8q1mv"
int[] arr = {10, 20, 30};

for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
```
# 📌 Java 메서드 (Method)

## 메서드란?

**메서드(Method)** 는 특정 기능을 수행하는 **코드의 묶음**이다.
코드를 반복해서 사용할 수 있어 프로그램을 더 효율적으로 만들 수 있다.

---

## 메서드 선언 방법

메서드는 다음과 같이 선언한다.

```java id="d9k2qw"
반환타입 메서드이름(매개변수) {
    실행할 코드
}
```

예시

```java id="x3p7mv"
public static void hello() {
    System.out.println("Hello");
}
```

---

## 메서드 호출

메서드는 호출해야 실행된다.

```java id="k8z1rt"
public class Main {
    public static void main(String[] args) {

        hello(); // 메서드 호출

    }

    public static void hello() {
        System.out.println("Hello");
    }
}
```

---

## 매개변수와 반환값

메서드는 값을 전달받고 결과를 반환할 수 있다.

```java id="u4m9lc"
public static int add(int a, int b) {
    return a + b;
}
```

사용 예시

```java id="h7q2xn"
int result = add(3, 5);
System.out.println(result); // 8
```

# 📌 Java HTTP / HTTPS

## HTTP란?

**HTTP(HyperText Transfer Protocol)** 는 웹에서 **클라이언트와 서버가 데이터를 주고받는 규칙**이다.
브라우저가 서버에 요청을 보내고, 서버가 응답을 보내는 방식으로 동작한다.

---

## HTTPS란?

**HTTPS** 는 HTTP에 **보안(암호화)** 이 추가된 프로토콜이다.
데이터를 암호화하여 **해킹이나 정보 유출을 방지**한다.

---

## HTTP vs HTTPS

| 구분 | HTTP  | HTTPS     |
| -- | ----- | --------- |
| 보안 | 없음    | 있음 (암호화)  |
| 포트 | 80    | 443       |
| 사용 | 일반 통신 | 로그인, 결제 등 |

---

## HTTP 요청 방식

| 메서드    | 설명     |
| ------ | ------ |
| GET    | 데이터 요청 |
| POST   | 데이터 전송 |
| PUT    | 데이터 수정 |
| DELETE | 데이터 삭제 |

---

## 자바에서 HTTP 요청 예시

자바에서는 `URL`과 `URLConnection`을 사용해 HTTP 요청을 보낼 수 있다.

```java id="a2k9mz"
import java.net.URL;
import java.net.URLConnection;

public class Main {
    public static void main(String[] args) throws Exception {

        URL url = new URL("http://example.com");
        URLConnection conn = url.openConnection();

        System.out.println(conn.getContentType());

    }
}









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
```

---


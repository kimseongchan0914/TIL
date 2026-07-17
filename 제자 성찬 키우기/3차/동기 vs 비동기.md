# 4. 동기(Synchronous) vs 비동기(Asynchronous)

## 1. 한 줄 요약
- **동기**: 요청하고 **결과가 올 때까지 기다림** (순차)
- **비동기**: 요청하고 **기다리지 않고 다음 일 진행**, 결과는 나중에

---

## 2. 개념 비유
- **동기**: 커피 나올 때까지 카운터에서 기다림. 그동안 아무것도 못 함.
- **비동기**: 진동벨 받고 자리에서 다른 일 함. 울리면 받으러 감.
```
[동기]   요청 ──── 대기 ──── 결과 → 다음 작업
[비동기] 요청 → 다음 작업 진행 ... (나중에) 결과 도착
```

---

## 3. 동기 코드 예제 (Java)
```java
public class SyncExample {
    public static void main(String[] args) {
        System.out.println("1. 주문 시작");
        String coffee = makeCoffee();   // ← 3초간 여기서 멈춤(블로킹)
        System.out.println("2. " + coffee + " 받음");
        System.out.println("3. 다음 작업");
    }
    static String makeCoffee() {
        try { Thread.sleep(3000); } catch (InterruptedException e) {}
        return "커피";
    }
}
```
**출력 (순서 고정, 총 3초):**
```
1. 주문 시작
(3초 대기)
2. 커피 받음
3. 다음 작업
```

---

## 4. 비동기 코드 예제 (CompletableFuture)
```java
import java.util.concurrent.CompletableFuture;

public class AsyncExample {
    public static void main(String[] args) throws Exception {
        System.out.println("1. 주문 시작");
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(3000); } catch (InterruptedException e) {}
            return "커피";
        });
        System.out.println("2. 다음 작업 (커피 기다리지 않음)");
        String coffee = future.get(); // 결과가 필요할 때 받음
        System.out.println("3. " + coffee + " 받음");
    }
}
```
**출력 (2번이 먼저):**
```
1. 주문 시작
2. 다음 작업 (커피 기다리지 않음)
(3초 후)
3. 커피 받음
```

---

## 5. Spring 비동기 (@Async)
```java
@Service
public class MailService {
    @Async // 별도 스레드에서 실행 (비동기)
    public void sendMail(String to) {
        try { Thread.sleep(3000); } catch (InterruptedException e) {}
        System.out.println("메일 전송 완료: " + to);
    }
}
```
```java
@SpringBootApplication
@EnableAsync // 비동기 활성화 필수!
public class App { ... }
```
```java
mailService.sendMail("user@test.com"); // 기다리지 않고 바로 다음 줄
System.out.println("응답 먼저 반환");
```
> `@Async`도 AOP 프록시 기반 → **내부 호출/private엔 적용 안 됨**, `@EnableAsync` 필요.

---

## 6. 비교표
| 구분 | 동기 | 비동기 |
|------|------|--------|
| 실행 방식 | 순차 (기다림) | 논블로킹 (안 기다림) |
| 결과 시점 | 즉시 | 나중에 콜백/Future |
| 코드 복잡도 | 단순 | 복잡 (콜백·예외처리) |
| 흐름 추적 | 쉬움 | 어려움 |
| 자원 효율 | 대기 중 놀고 있음 | 대기 중 다른 일 |

---

## 7. 언제 동기? 언제 비동기? (핵심)
### 동기가 좋은 경우
- **결과가 바로 필요**할 때 (다음 로직이 그 결과에 의존) — 예: 잔액 조회 후 이체 계산
- **순서가 중요**할 때 (A 끝나야 B)
- **트랜잭션으로 원자성**이 필요할 때
- 로직이 단순하고 응답이 빠를 때

### 비동기가 좋은 경우
- **결과를 당장 안 기다려도 될 때** (fire-and-forget) — 이메일/알림/로그 적재
- **오래 걸리는 작업**으로 사용자 응답이 느려질 때 — 회원가입 후 환영 메일
- **여러 독립 작업 동시 처리** — 외부 API 3개 동시 호출 (3초→1초)
- **외부 I/O 대기가 긴 작업** (네트워크, 파일)

### 판단 기준 한 줄
> **"이 결과가 지금 당장 필요한가?"** 필요하면 동기, 아니면 비동기.

---

## 8. 실전 예시
```java
@Transactional
public void signup(SignupRequest req) {
    Member member = memberRepository.save(req.toEntity()); // 동기: 저장 결과 필요
    mailService.sendWelcomeMail(member.getEmail());        // 비동기: 메일은 안 기다림
    // → 가입 응답은 즉시 반환, 메일은 백그라운드
}
```

---

## 9. 최종 요약
- **동기** = 기다림(순차), **비동기** = 안 기다림(논블로킹)
- 결과가 당장 필요/순서 중요/트랜잭션 → **동기**
- 알림·메일·로그·긴 I/O·병렬 처리 → **비동기**
- Spring: `@Async` + `@EnableAsync`, `CompletableFuture`
# Spring 핵심 구성 요소 정리 (상세)

## 1. Controller

### 개념
사용자의 HTTP 요청(Request)을 가장 먼저 받는 계층

### 역할
- URL과 메서드 매핑 (@GetMapping, @PostMapping 등)
- 클라이언트 요청 데이터 수신
- Service 계층 호출
- 처리 결과를 View 또는 JSON 형태로 반환

### 동작 흐름
1. 사용자가 URL 요청을 보냄
2. Controller가 해당 URL을 매핑하여 요청을 받음
3. 필요한 데이터를 DTO로 받음
4. Service에 처리 요청

### 예시
```java
@Controller
@RequiredArgsConstructor
public class BoardController {

    private final BoardService boardService;

    @GetMapping("/board")
    public String list(Model model) {
        model.addAttribute("list", boardService.findAll());
        return "boardList";
    }
}
```

### 실무 포인트
- Controller에는 로직을 최소화해야 함
- 요청/응답 처리만 담당 (비즈니스 로직 금지)

### 한 줄 정리
요청을 받고 응답을 반환하는 입구 역할

---

## 2. Service

### 개념
비즈니스 로직을 처리하는 핵심 계층

### 역할
- 데이터 가공 및 처리
- 여러 Repository를 조합하여 작업 수행
- 트랜잭션 관리

### 동작 흐름
1. Controller로부터 요청을 받음
2. 필요한 비즈니스 로직 수행
3. Repository 호출하여 DB 작업 수행
4. 결과를 Controller로 반환

### 예시
```java
@Service
@RequiredArgsConstructor
public class BoardService {

    private final BoardRepository boardRepository;

    public List<BoardDTO> findAll() {
        List<BoardEntity> entities = boardRepository.findAll();
        return entities.stream()
                .map(BoardDTO::toDTO)
                .toList();
    }
}
```

### 실무 포인트
- 핵심 로직은 전부 Service에 작성
- @Transactional을 사용해 데이터 일관성 유지

### 한 줄 정리
비즈니스 로직을 처리하는 핵심 계층

---

## 3. Repository

### 개념
데이터베이스와 직접 통신하는 계층

### 역할
- CRUD 작업 수행 (Create, Read, Update, Delete)
- JPA를 통해 DB 자동 처리

### 동작 흐름
1. Service로부터 요청 받음
2. DB에 SQL 실행 (내부적으로 처리됨)
3. 결과 반환

### 예시
```java
public interface BoardRepository extends JpaRepository<BoardEntity, Long> {
}
```

### 실무 포인트
- 대부분 인터페이스만 정의하면 됨
- Spring Data JPA가 자동 구현

### 한 줄 정리
DB와 직접 통신하는 계층

---

## 4. Entity

### 개념
DB 테이블과 1:1로 매핑되는 객체

### 역할
- 테이블 구조 정의
- 컬럼과 필드 매핑

### 특징
- @Entity 어노테이션 사용
- 실제 DB에 저장되는 객체
- 변경 시 DB에 반영됨

### 예시
```java
@Entity
@Getter
@Setter
public class BoardEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String content;
}
```

### 실무 포인트
- Entity는 DB 중심 구조
- Controller에서 직접 사용하지 않는 것이 좋음

### 한 줄 정리
DB 테이블과 연결된 실제 데이터 객체

---

## 5. DTO (Data Transfer Object)

### 개념
계층 간 데이터 전달을 위한 객체

### 역할
- Controller ↔ Service 간 데이터 전달
- Entity 보호 (직접 노출 방지)
- 필요한 데이터만 선택적으로 전달

### 특징
- 가볍고 단순한 구조
- 로직이 거의 없음

### 예시
```java
@Getter
@Setter
public class BoardDTO {

    private String title;
    private String content;

    public static BoardDTO toDTO(BoardEntity entity) {
        BoardDTO dto = new BoardDTO();
        dto.setTitle(entity.getTitle());
        dto.setContent(entity.getContent());
        return dto;
    }
}
```

### 실무 포인트
- Entity → DTO 변환 필수 (보안 + 유지보수)
- API 응답은 DTO로 반환하는 것이 기본

### 한 줄 정리
데이터 전달을 위한 객체

---

# 전체 흐름 (중요)

## 요청 흐름
```
사용자 → Controller → Service → Repository → DB
```

## 응답 흐름
```
DB → Repository → Service → Controller → 사용자
```

---

# 계층을 나누는 이유

## 1. 역할 분리
각 계층이 담당하는 역할이 명확해짐

## 2. 유지보수 용이
하나의 계층만 수정해도 전체 영향 최소화

## 3. 재사용성 증가
Service 로직을 여러 Controller에서 사용 가능

## 4. 테스트 용이
각 계층을 따로 테스트 가능

---

# 핵심 요약

| 구성 요소   | 역할 |
|------------|------|
| Controller | 요청/응답 처리 |
| Service    | 비즈니스 로직 |
| Repository | DB 처리 |
| Entity     | DB 구조 |
| DTO        | 데이터 전달 |

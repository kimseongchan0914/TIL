# Spring 이란?
### spring은 자바로 웹 서버를 만들 때 사용하는 프레임워크 이다.
## Spring을 쓰는 이유
원래 자바로 서버를 만들면

-객체 생성 직접 관리

-DB 연결 직접 설정

-코드 엄청 길어짐

이것들을 Spring이 대시 해줌

## Spring의 핵심 기능 
### 1. loC(제어의 역전)
-> 객체를 내가 만드는게 아니라 Spring이 대신 관리

<img width="601" height="163" alt="image" src="https://github.com/user-attachments/assets/a5bcc1fa-584b-4d18-8e9e-d25707f819fb" />
-> Spring이 알아서 객체를 생성하고 주입함

### 2. DI(의존성 주입)
-> 필요한 객체를 자동으로 넣어줌

<img width="520" height="163" alt="image" src="https://github.com/user-attachments/assets/26763313-0778-452d-a145-f496384fb4f1" />
-> Spring은 자동 연결

### 3. AOP(관점 지향 프로그래밍)
-> 공통 기능을 따로 분리

<img width="567" height="185" alt="image" src="https://github.com/user-attachments/assets/1277d422-fea1-47b4-8514-b1d248e8d323" />

## Spring의 단점
- 설정이 너무 많음

- 처음 세팅 어려움

- 서버 띄우기 복잡

  ->그래서 나온게 Spring Boot

# Spring Boot란?
### Spring을 쉽게 만든 버전

## Spring Boot의 특징
### 1. 자동 설정
-> 알아서 다 해줌

-DB 연결

-서버 설정

-라이브러리 연결

### 2. 내장 서버
-> 톰캣 설치 필요 없음

<img width="659" height="107" alt="image" src="https://github.com/user-attachments/assets/b18aed57-372e-47c2-b798-25685ff0390d" />
->바로 실행됨

### 3.starter 의존성
-> 필요한 기능 묶음으로 제공

<img width="714" height="99" alt="image" src="https://github.com/user-attachments/assets/3c5926a1-bae5-466c-9176-0772e8dbad01" />

### 4. 설정 간소화
XML 거의 안씀

## Spring VS Spring Boot

<img width="842" height="439" alt="image" src="https://github.com/user-attachments/assets/d0c17d1a-5a36-4fff-8b7f-b8dd20afd992" />

# Spring 4원칙
### 한줄 정리
-> Controller -> Service -> Repository -> DB

## 1. Controller
### 역할
-> 요청 받는 입구

#### 설명

- 사용자 요청 받음

- URL 처리

- Service 호출

<img width="777" height="359" alt="image" src="https://github.com/user-attachments/assets/9ac09460-26fa-4d1a-9d6e-98abd7e493f9" />

## 2. Service
### 역할
-> 로직 처리

#### 설명

- 계산

- 조건 처리

- 핵심 기능 구현

<img width="715" height="349" alt="image" src="https://github.com/user-attachments/assets/e0aa62b7-30c0-484c-9e67-4bd99b2748c2" />

## 3. Repository
### 역할
-> DB 접근 담당

####  설명
- 데이터 저장

- 데이터 조회

<img width="696" height="361" alt="image" src="https://github.com/user-attachments/assets/090d188a-1910-407b-8dcb-a376a6feeede" />

## 4. DB
### 역할
-> 데이터 저장소

####  설명

- 유저 정보

- 게시글

- 댓글 등 저장

## 전체 흐름
<img width="774" height="551" alt="image" src="https://github.com/user-attachments/assets/d0ae8a2c-b33b-4e43-ba27-b7163bfa8f8c" />

## 비유
<img width="737" height="432" alt="image" src="https://github.com/user-attachments/assets/d43315d2-20c8-41b7-94fb-b80fd5c9af98" />












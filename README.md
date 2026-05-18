
## 안녕하세요, 백엔드 개발자 정예나입니다 👋

Java/Spring 기반 백엔드 개발자로, **동시성 제어 · 실시간 데이터 처리 · 서비스 안정성** 문제 해결에 강점이 있습니다.  
경매 서비스 프로젝트에서 입찰 정합성, 상태 전이 자동화, 실시간 이벤트 전송 구조를 설계·개발하며  
기능 구현을 넘어 **운영 가능한 시스템 구조**를 고민해왔습니다.

- 단순한 기능 구현을 넘어 시스템 흐름 전체를 이해하고 사용자 경험과 운영 효율의 균형을 고려합니다.
- 실전 프로젝트 경험을 바탕으로 집요함과 팀워크로 함께 성취를 이뤄낸 경험이 있습니다.
- 기술적 성장과 몰입을 즐기는 도전적이고 능동적인 인재입니다.

<br>

## 📬 Contact

| GitHub | Velog | Email |
|--------|-------|-------|
| [github.com/yenazigeumena](https://github.com/yenazigeumena) | [velog.io/@yenazigeumena](https://velog.io/@yenazigeumena/posts) | yena8085@naver.com |

<br>

## 🛠 Tech Stack

**Backend**  
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat&logo=openjdk&logoColor=white)
![Kotlin](https://img.shields.io/badge/Kotlin-7F52FF?style=flat&logo=kotlin&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat&logo=springboot&logoColor=white)
![Spring MVC](https://img.shields.io/badge/Spring_MVC-6DB33F?style=flat&logo=spring&logoColor=white)
![JPA](https://img.shields.io/badge/JPA/Hibernate-59666C?style=flat&logo=hibernate&logoColor=white)

**Database & Caching**  
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat&logo=redis&logoColor=white)

**Infra & DevOps**  
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat&logo=githubactions&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazonaws&logoColor=white)

**Collaboration**  
![Git](https://img.shields.io/badge/Git-F05032?style=flat&logo=git&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)
![Notion](https://img.shields.io/badge/Notion-000000?style=flat&logo=notion&logoColor=white)
![Swagger](https://img.shields.io/badge/Swagger-85EA2D?style=flat&logo=swagger&logoColor=black)

<br>

## 💻 Projects

### 🏷 DROP - 굿즈 경매 플랫폼
> 2025.12 – 2026.01 | 역할: 백엔드 개발, PO

희소 굿즈·리미티드 아이템 거래 시장의 불투명성을 해결하는 굿즈 경매 플랫폼입니다.  
사용자가 실시간으로 입찰하고 낙찰 결과를 확인할 수 있는 경매 서비스의 백엔드를 개발했습니다.

**핵심 구현**
- JPA Pessimistic Lock을 활용한 동시 입찰 환경에서의 중복 낙찰 방지
- 버전 관리 기반 충돌 감지 및 비즈니스 예외 처리로 데이터 정합성 확보
- Spring Scheduler를 통한 경매 생명주기 자동화 관리 (시작·종료 시점 자동 감지)
- Spring Event를 활용한 경매 핵심 로직과 알림 로직 분리 설계
- SSE를 통한 실시간 입찰 정보 스트리밍 및 폴링 없는 클라이언트 데이터 푸시
- Redisson 기반 분산 락으로 동시 결제 요청 시 중복 승인 방지
- Redis 장애 발생 시 Fallback 구조로 서비스 연속성 확보

**문제 해결 경험**

<details>
<summary>⚡ 동시 입찰 환경에서의 정합성 보장</summary>

여러 사용자가 동시에 같은 경매에 입찰할 때, 최고 입찰가 갱신이 꼬이거나 낙찰자가 중복 저장될 수 있는 문제가 있었습니다.

- 입찰 가능 조건, 최소 입찰 단위, 판매자 본인 입찰 제한 등 비즈니스 규칙을 서비스 계층에서 명시적으로 검증
- `winnerRepository.existsByAuction_Id()` 검증을 통해 중복 낙찰 저장 방지
- 경매 종료 시점에 최고 입찰 조회와 낙찰 저장을 하나의 흐름으로 관리해 멱등성 확보
- JPA 락 전략과 Redis 기반 분산락 확장 가능성을 비교 검토하며 구조 개선 방향 정리

</details>

<details>
<summary>📡 실시간 입찰 정보 전달 구조 개선</summary>

사용자가 경매 화면에서 새 입찰을 즉시 확인할 수 있어야 했기 때문에 실시간 데이터 전달 방식이 필요했습니다.

- 단순 조회 반복 방식 대신 SSE를 도입해 불필요한 요청을 줄이고 사용자 경험 개선
- `/bid-stream` SSE 구독 엔드포인트 구현
- 연결 직후 초기 이벤트 전송으로 클라이언트 연결 상태 명확히 확인
- heartbeat 전송 로직으로 유휴 연결 종료 문제 완화
- 운영 환경 프록시/Nginx 설정에 따른 SSE 조기 종료 문제 확인 및 버퍼링·타임아웃 설정 조정

</details>

📽 [시연 영상 보기](https://youtu.be/f9svDsBLgb8)

---

### 🗺 도장찍어가유 - QR 기반 스탬프 투어 앱
> 해커톤 프로젝트 | 역할: 백엔드 개발

전통시장 관광 활성화를 위한 QR 기반 스탬프 투어 어플리케이션입니다.

**핵심 구현**
- QR 코드 스캔 시 사용자-QR-상점 관계 검증 후 스탬프 적립 서버 로직 구현
- QR 만료 시간 검증을 통한 비정상·재사용 스캔 차단
- 단일 트랜잭션 기반 스탬프 적립 처리로 데이터 정합성 확보
- GitFlow 전략 도입으로 기능 병렬 개발 및 작은 단위 PR로 충돌 최소화

---

### 📔 POVI - 감정 공유 다이어리 서비스
> 2025.10 | 역할: 커뮤니티 기능 백엔드 개발

일상을 기록하고 위로를 나누는 감정 공유 다이어리 서비스입니다.

**핵심 구현**
- 익명 게시글/댓글 CRUD 구현
- API 설계, 유효성 검증, 페이지네이션 등 기본 서버 구조 정리
- Java 기반 Spring Boot 프로젝트에서 Kotlin 마이그레이션 실습

<br>

## 🎓 Education

| 기간 | 내용 |
|------|------|
| 2021.03 – 2025.08 | 백석대학교 컴퓨터공학부 졸업 |
| 2025.07 – 2026.01 | 프로그래머스 데브코스 - 클라우드 기반 백엔드 엔지니어링 수료 |

<br>

## 🏆 Awards & Certificates

- 정보처리기사 필기 합격 (2026.02)
- Great Challenge - IIBC SIC 동상 (2024.12)

<br>

## 🌱 Growth

동시성, 락 전략, 실시간 처리 구조처럼 서비스 안정성과 직결되는 주제를 중심으로 학습하고 있습니다.  
프로젝트에서 마주친 문제를 단순히 해결하는 데 그치지 않고, JPA 락 전략과 Redis 분산락 같은 대안을 비교하며 더 나은 구조를 고민해 왔습니다.  
테스트 코드, CI, 운영 환경 이슈까지 경험 범위를 넓히며 **"기능 개발자"가 아니라 "안정적으로 운영되는 서비스를 만드는 개발자"** 로 성장하고자 합니다.

# 1. Entity 위치, 네이밍, 관계 모델링, 공통 규칙
## 1.1 엔티티 네이밍 규칙
* 반드시 `도메인명 + 드라이빙 테이블명 + Entity` 형태로 명명
  * 예: `CustomAuthUserEntity`, `TemplateKeywordEntity`
* 조인 전용 엔티티는 `Join` 키워드를 포함
  * 예: `CustomAuthUserJoinEntity`, `PayHistoryChatbotCashJoinEntity`

---

## 1.2 설계 원칙
* 모든 테이블은 **1:1로 Entity 생성**
* **단일 책임 원칙(SRP)** 적용: 하나의 Entity는 하나의 테이블만 책임
* 도메인 경계를 넘지 않도록 반드시 해당 도메인의 `data.entity` 패키지 내에 위치시킴

---

## 1.3 CodeRabbit 인식 기준 및 리뷰 항목
### 1.3.1 CodeRabbit 인식 기준
CodeRabbit은 도메인 기반 아키텍처를 바탕으로 다음 경로와 구조를 기준으로 인식하도록 한다.
* 도메인 루트는 반드시 `io.happytalk.api.domain.{도메인}` 구조여야 함
  * 예: `io.happytalk.api.domain.customauth`
* 모든 Entity 클래스는 해당 도메인의 `data.entity` 패키지 하위에 존재해야 하며, 다른 도메인의 Entity를 직접 참조해서는 안 됨
### 1.3.2 CodeRabbit 리뷰 기준
아래 항목 중 하나라도 위반 시 리뷰에서 반드시 **지적**되어야 한다.
1. `*.entity` 클래스가 도메인 외부(`global`, `common`, `shared` 등)에 위치한 경우
2. Entity 클래스 명이 `도메인명 + 테이블명 + Entity` 형식을 따르지 않은 경우
3. 조인 전용 Entity인데 클래스명에 `Join` 접미어가 없는 경우
4. 한 도메인의 Entity가 다른 도메인의 Entity를 직접 참조한 경우

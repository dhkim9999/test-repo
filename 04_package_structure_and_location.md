# 4. 패키지 구성 전략, 도메인/계층별 구조 및 책임 분리
## 4.1 DDD 기반 도메인 단위 패키지 구조 적용
* 모든 코드는 `io.happytalk.api.domain.{도메인}` 또는 `io.kakao.api.domain.{도메인}` 하위에서만 정의되어야 하며, 아래 구조를 따른다:
  ```plaintext
  /domain
    └── {도메인}
        ├── data
        │   ├── entity        # DB 테이블 매핑용 Entity 클래스
        │   ├── dto           # 요청 및 응답 DTO
        │   │   ├── request   # 요청 DTO
        │   │   └── response  # 응답 DTO
        │   ├── constant      # enum, 상수 정의
        │   └── mapper        # DTO ↔ Entity 변환 Mapper
        ├── repository        # DB 접근 레이어 (Query/Command)
        ├── service           # 비즈니스 로직 처리
        ├── util              # 매퍼를 제외한 각종 유틸들
        ├── validation        # 커스텀 유효성 검증기 및 어노테이션
        └── controller        # API 핸들러
  ```
* **Global 패키지는 공통 유틸/설정만 포함** (`/global/util`, `/global/config`, `/global/constant`, 등)
* **같은 테이블을 다른 목적에서 사용할 경우라도 → 도메인 기준으로 Entity/DTO/Repository 분리하여 응집도 유지**

---

## 4.2 CodeRabbit 리뷰 기준
* 위 구조를 따르지 않고 다른 도메인의 구조를 참조하거나 공유하는 경우, 도메인 경계 침범으로 간주해야 함
* 각 구성 요소(entity, dto, mapper, repository 등)는 반드시 같은 도메인 패키지 안에 정의되어야 함
* KDoc에 남긴 사유없이 도메인 경계를 침범할 경우 수정하길 권장하거나 도메인 경계 침범 사유를 KDoc에 작성해달라고 경고해야함
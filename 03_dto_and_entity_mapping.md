# 3. DTO ↔ Entity 매핑 전략, Mapper 위치 및 사용법
## 3.1 개요
* DTO는 클라이언트 요청/응답 전용, Entity는 DB 테이블 전용이며, 책임이 명확히 분리되어야 함 (단일 책임 원칙 - SRP)
* DTO ↔ Entity 간 변환은 반드시 Mapper를 통해 수행해야 하며, 위치와 책임 범위가 명확히 정의되어야 함

---

## 3.2 변환 방식
* DTO ↔ Entity 변환은 **확장 함수 기반 Mapper 클래스**를 사용
  * 예: `fun ChatbotEntity.toDto(): ChatbotDto`
* 함수명은 `toDto()`, `toEntity()`를 **명시적으로 통일**

---

## 3.3 Mapper 클래스 위치 규칙
* 도메인 경계를 넘지 않도록 반드시 **자신의 도메인 내부에 위치**
  * 경로 예시: `io.happytalk.api.domain.{도메인}.data.mapper.{도메인명}Mapper.kt`

---

## 3.4 책임 분리 및 위치 제약
| 역할 | 목적 | 위치 |
| --- | --- | --- |
| DTO | 외부 요청/응답 전용 객체 | `data.dto` |
| Entity | DB 테이블 매핑 객체 | `data.entity` |
| Mapper | DTO ↔ Entity 변환 | `data.mapper` (도메인 내부) |

---

## 3.5 CodeRabbit 리뷰 기준: DTO ↔ Entity 변환
| 항목 | 기준 | 지적 대상 예시 |
| --- | --- | --- |
| 변환 함수 | 반드시 Mapper의 `toDto()`, `toEntity()` 사용 | 컨트롤러/서비스 내부에서 수동 변환 |
| 위치 | 각 도메인 내부의 `data.mapper` 디렉토리 | `global.mapper`, 타 도메인에서 변환 수행 |
| 경계 유지 | 변환 대상 도메인의 Mapper만 사용 가능 | A 도메인의 Mapper에서 B 도메인 Entity 변환 수행 |
| 책임 혼합 금지 | Mapper 외의 클래스에 변환 로직 존재 금지 | DTO 클래스 내 `toEntity()` 정의 |

---

## 3.6 CodeRabbit 리뷰 적용 안내
* CodeRabbit은 `io.happytalk.api.domain.{도메인}` 구조를 도메인 단위로 인식
* `data.mapper` 디렉토리는 반드시 해당 도메인 내부에 존재해야 하며, 다른 도메인 객체 변환을 포함해서는 안 됨
* Mapper 클래스 간 **cross-domain 변환 로직**은 경계 위반으로 간주되어 리뷰 대상이 됨

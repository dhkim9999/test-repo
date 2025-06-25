# 2. Repository 함수 구조, 분리 전략, naming convention
## 2.1 Repository 클래스 네이밍
* 반드시 `도메인명 + 드라이빙 테이블명 + Repository` 형태로 명명
  * 예시: `CustomAuthUserRepository`, `TemplateKeywordRepository`
* 조인 전용: `JoinRepository` 접미어 사용
  * 예시: `CustomAuthUserJoinRepository`, `TemplateKeywordJoinRepository`
* 목적 기반으로 `DashboardRepository`, `WithXxxxRepository` 등으로 확장 가능

---

## 2.2 Repository 함수 네이밍
* 기본 패턴: `동작(동사) + 대상 + 조건` 순으로 구성
* **SQL 행위 기반 명확한 동사 사용 필수 (`select`, `insert`, `update`, `delete`)**
  | 동작 유형 | 예시 함수명 | 설명 |
  | --- | --- | --- |
  | 단건 조회 | `selectUserById` | PK 기반 단건 |
  | 다건 조회 | `selectUsersByStatus`  | 조건 기반 리스트 |
  | 전체 조회 | `selectAllUsers` | 조건 없이 전체 |
  | 페이징 조회 | `selectUserPageBySiteId` | 페이지네이션 포함 |
  | 삽입 | `insertUser` | 신규 등록 |
  | 일괄 삽입 | `insertUserBatch` | 여러 건 등록 |
  | 수정 | `updateUserStatusById` | 조건 기반 일부 필드 수정 |
  | 삭제 | `deleteUserByRole` | 조건 기반 삭제 |

---

## 2.3 네이밍 Anti-Pattern (피해야 할 예)
| 잘못된 함수명 | 이유 | 개선안 |
| --- | --- | --- |
| `getUser()` | 의도 불분명 | `selectUserById()` |
| `doInsertUser()` | 중복 표현 | `insertUser()` |
| `findSomething()` | SQL 행위 불명확 | `selectTargetByCondition()` |
| `delete()` | 대상/조건 불분명 | `deleteUserById()` |

---

## 2.4 복잡한 조인 처리
* **3개 이상의 테이블 조인 or 타 도메인 침범** 시 별도 `JoinRepository`로 분리
* 반환 객체가 DTO인 경우 함수명은 `select{JoinEntity}DtoBy{조건}` 형태 권장
* ViewModel 전용 Repository도 업무 목적에 따라 허용

---

## 2.5 기타 참고 사항
### 다수 도메인에서 동일 테이블을 사용할 경우
* 로그성 또는 단순 데이터 기록 용도의 테이블(`site_logs` 등)은 **전역 공통 도메인으로 분리**
  * 예: `domain.logs.*`
* 복수 도메인이 공통으로 참조하더라도 **업무 단위가 명확하면 별도 도메인 구성**

---

## 2.6 CodeRabbit 리뷰용 인식 안내
| 항목 | 기준 | 지적 대상 예시 |
| --- | --- | --- |
| 클래스명 | 도메인 + 테이블명 + `Repository` | `DataHandler`, `XxxRepo` 등 |
| 패키지 | `domain.{도메인}.repository` 내 구성 | 외부 경로 사용 |
| 함수명 | `select`, `update` 등 SQL 행위 기반 명확한 동사 | `find`, `handle`, `doInsert` 등 |
| 복잡도 분리 | JOIN 3개 이상 또는 cross-domain 시 분리 필요 | 단일 리포지토리에 모든 조인 포함 시 |

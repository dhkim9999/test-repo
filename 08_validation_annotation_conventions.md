# 8. Validation 어노테이션 메시지 처리 원칙
## 8.1 유효성 검증 처리 원칙
* 기본적으로 일관된 유효성 검증 메시지 관리를 위해 유효성 검증 어노테이션 적용 시 다음 방식을 사용하는것을 권장한다.(메시지 자동화 룰)
  ```
  @field:NotBlank
  val username: String
  ```
* `@field:NotBlank(message = "직접 작성")`은 해당 문자열을 그대로 사용 (우선순위 높음)
* 기본 메시지 패턴(`must`, `invalid`, `not` 등)은 무시되고 키 기반 메시지로 fallback됨
* 메시지 생략 시 자동으로 메시지 키를 생성하여 messages.yml에서 조회
* `@DisplayName` 또는 `@Schema(description = "...")`이 존재하면 해당 값으로 필드명을 바인딩함

---

## 8.2 필드명 바인딩 우선순위
* 자동 생성 메시지에서 필드명을 대체할 때의 추출 우선순위는 다음과 같다.
  1. @DisplayName("사용자명")
  2. @Schema(description = "사용자명")
  3. 명시된 것이 없으면 필드명 그대로 사용

---

## 8.3 어노테이션 사용 원칙 - 문자열과 컬렉션
### 8.3.1 문자열 길이 검증
* 문자열 길이 체크 시 반드시 `@field:Length(min = ..., max = ...)`를 사용하도록 한다.
* `@field:Length`를 사용시 반드시 min, max값을 기입해야한다.
  | 규칙 | 설명 |
  | --- | --- |
  | 사용 어노테이션 | 반드시 `@field:Length(min, max)` 사용 |
  | 금지 사항 | `@field:Size`는 문자열에서 사용 금지 (컬렉션 전용) |
  ```kotlin
  // 올바른 예시 (문자열)
  @field:Length(min = 1, max = 10)
  val name: String
  
  // 잘못된 예시 (문자열에 Size 사용)
  @field:Size(min = 1, max = 10)
  val name: String
  
  // 잘못된 예시 (Length를 사용헀으나 min 누락)
  @field:Length(max = 10)
  val name: String
  ```

### 8.3.2 컬렉션/배열 길이 검증
* `@field:Size`는 컬렉션이나 배열의 길이 체크 시 사용하며, 문자열 길이 체크 시 사용을 금지한다.
* `@field:Size`를 사용시 반드시 min, max값을 기입해야한다.
  ```kotlin
  // 올바른 예시 (컬렉션)
  @field:Size(min = 1, max = 10)
  val tags: List<String>
  
  // 잘못된 예시 (max만 명시)
  @field:Size(max = 10)
  val tags: List<String>
  ```

---

## 8.4 CodeRabbit 리뷰 기준
| 항목 | 조건 | 리뷰 기준 |
| --- | --- | --- |
| 메시지 하드코딩 | `message = "..."` 사용 | 사용 가능하나 자동화 예외 처리 대상 |
| 메시지 생략 | message 속성 생략 | i18n 메시지 자동화 대상 |
| 필드명 바인딩 | `@DisplayName`, `@Schema` 미사용 | 경고: 가독성 및 메시지 대체 실패 우려 |
| 문자열 길이 검증 | `@field:Size` 사용 | 오류: `@field:Length` 사용해야 함 |
| 컬렉션 검증 | `@field:Size(min/max)` 누락 | 경고: 유효 범위 누락으로 리뷰 대상 |
| `min`, `max` 누락 | `Size`, `Length` 둘 다 해당 | 명확성 부족으로 강한 경고 처리 |

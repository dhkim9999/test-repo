# 6. 공통 응답 객체 및 Swagger 명세화 규칙
## 6.1 공통 응답 객체 사용 원칙
* 모든 API 응답은 반드시 표준 응답 구조(`BaseSingleResponse<T>`, `BaseListResponse<T>`)를 사용해야 한다.(파일을 반환하는 케이스는 제외)
  * 단일 객체 응답: `BaseSingleResponse<T>`
  * 목록 응답: `BaseListResponse<T>`

---

## 6.2 Swagger 문서화 전략
* **Swagger 문서 자동화를 고려하여 반드시 제네릭을 확장한 명시적 클래스 정의를 통해 문서화할 것**
  * 예: `class UserParameterResponse : BaseListResponse<UserParameter>`
  * 예: `class UserUpdateResponse : BaseListResponse<Unit>`
* **제네릭 타입을 그대로 사용하면 Swagger UI에서 `data` 구조가 누락되거나 해석이 되지 않음 → 반드시 구현체로 wrapping 필요**
* **Swagger 문서에 명시할 때는 `@Schema(implementation = XxxResponse::class)` 형태로 명시적으로 작성**
  ```kotlin
  Content(
      mediaType = MediaType.APPLICATION_JSON_VALUE,
      schema = Schema(implementation = UserParameterResponse::class),
      examples = [
          ExampleObject(
              name = "정상",
              value = GET_PARAMETERS_RESPONSE_200
          )
      ]
  )
  ```

---

## 6.3 CodeRabbit 리뷰 기준
| 검사 항목 | 조건 | 리뷰 기준 및 설명 |
| --- | --- | --- |
| 공통 응답 객체 사용 여부 | 응답 DTO가 `BaseSingleResponse` 또는 `BaseListResponse`를 사용하지 않은 경우 | 공통 응답 구조 위반으로 경고                              |
| Swagger 문서화 시 제네릭 타입 직접 사용 | `BaseListResponse<T>` 또는 `BaseSingleResponse<T>`를 문서화에 직접 사용 | Swagger UI에서 `data` 내부 구조 누락 → 래핑 클래스 미정의로 경고 |
| Swagger 예시 누락 | `@Schema` 또는 `@ExampleObject`가 누락된 경우 | 응답 예시 부족으로 API 문서 불완전 판단 |

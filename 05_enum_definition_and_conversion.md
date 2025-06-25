# 5. Enum 관리
## 5.1 정의 규칙
* **DTO에서 사용하는 모든 enum 클래스는 `val value: {타입}` 형태로 정의하는것을 권장함**
  ```kotlin
  enum class UserRole(override val value: String) {
      ADMIN("admin"),
      USER("user")
  }
  ```
* **Entity 클래스에서 사용하는 모든 enum은 `PersistentEnum` 인터페이스를 상속해야 함**
  ```kotlin
  @Schema(description = "채널 과금 내역 채널타입")
  enum class CashChannelChannelType(override val value: String) : PersistentEnum {
    /** 해피톡 */
    HAPPYTALK("0"),

    /** 네이버 */
    NAVER("1"),

    /** 카카오 상담톡 */
    KAKAO("2"),

    /** 값 없음/알 수 없음 */
    EMPTY_TYPE("")
  }
  ```

---

## 5.2 복합 Enum 예외 규칙
* `value` 외에 2개 이상의 필드를 가지는 복합 Enum은 예외 처리 가능 (단, 명시 필요)
  ```kotlin
  enum class PaymentStatus(
      override val value: String,
      val displayName: String,
      val description: String
  ) : PersistentEnum {
      SUCCESS("S", "성공", "결제가 정상적으로 완료됨"),
      FAIL("F", "실패", "결제 실패")
  }
  ```
* 복합 Enum일 경우에는 `PersistentEnum`을 여전히 상속해야 하며, value 기준 컨버팅만 유지되면 됨

---

## 5.3 CodeRabbit 리뷰 기준

| 검사 항목 | 조건 | 지적 내용 |
| - | - | - |
| **Enum 필드 정의** | `val value` 없음 | 필드명 `code`, `type` 등 혼용 시 경고 |
| **Entity 내 Enum 필드 사용 시 인터페이스 상속 여부** | `Entity`에서 사용하는 필드가 `enum`인데 해당 enum 클래스가 `PersistentEnum` 미상속 | DB ↔ Enum 자동 변환 불가로 간주 |
| **복합 Enum 예외 명시 여부** | 복합 Enum인데 사유 명시 없음 | 리뷰에서 사유 누락 지적 |

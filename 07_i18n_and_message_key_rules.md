# 7. 에러 메시지 자동화 전략
## 7.1. 메시지 구성 전략
* 메시지는 **공통 메시지**와 **도메인 전용 메시지**로 구분하여 YAML 파일(messages.yml)로 구성한다.
### 7.1.1 공통 메시지 (`resources/i18n/common/messages.yml`)
* 형식: `E400.key`
* 전역적으로 재사용 가능한 메시지만 정의
* 공통 메시지는 **가급적 수정하지 않고 유지**하는 것이 원칙
  ```yaml
  E400:
    invalid_request: "잘못된 요청입니다."
    unauthorized: "인증이 필요합니다."
  ```

### 7.1.2 도메인 전용 메시지 (`resources/i18n/{도메인}/messages.yml`)
* 형식: `E400.{도메인}.key` (**3단계 키 구조 필수**)
* 해당 도메인에서만 사용하는 오류 메시지를 정의
* 200 OK인 경우에는 되도록이면 제외하도록 한다.
* 메시지 키는 반드시 도메인명과 정확히 매핑된 디렉토리에 존재해야 함
  ```yaml
  E400:
    customauth:
      invalid_phone: "휴대폰 번호가 유효하지 않습니다."
      config_exists: "{0} 설정이 이미 존재합니다."
  ```
  > 경로가 실제 도메인 구조(`io.happytalk.api.domain.{도메인}`)와 일치하지 않으면 **자동화 스크립트에서 오류 발생**

---

## 7.2 프로젝트 내 디렉토리 구조 예시
```plaintext
resources/
 └── i18n/
     ├── common/messages.yml
     └── customauth/messages.yml

kotlin/
 └── io/happytalk/api/
     ├── global/constant/errorcode/
     │   ├── ClientErrorCode.kt
     │   ├── ServerErrorCode.kt
     │   └── MessageValue.kt
     └── domain/customauth/constant/
         └── CustomauthErrorCode.kt
```

---

## 7.3 오류 메시지 키 작성 규칙
| 구분 | 키 구조 | 예시 |
| --- | --- | --- |
| 공통 메시지  | `E400.key` | `E400.invalid_request` |
| 도메인 메시지 | `E400.{도메인}.key` | `E400.customauth.invalid_phone` |
> 메시지 값 내 변수는 `{0}`, `{1}` 형식으로 지정 (예: `"필드 {0}의 값이 유효하지 않습니다"`)

---

## 7.4 사용자 정의 예외 사용 방법
* 모든 예외는 다음과 같이 메시지 코드 기반으로 throw한다.
  ```kotlin
  throw OperationFailedException(messageValue = ServerErrorCode.E500_INTERNAL_ERROR)
  ```

---

## 7.5 CodeRabbit 리뷰 기준
| 검사 항목 | 조건 | 리뷰 기준 및 설명 |
| --- | --- | --- |
| 메시지 키 포맷 | 키 형식이 `E400.key` 또는 `E400.{도메인}.key`를 따르지 않을 경우 | 경고: 메시지 구조 위반 |
| 메시지 하드코딩 여부 | 예외 메시지가 문자열로 직접 작성된 경우 | 경고: 하드코딩 → i18n 메시지로 치환 필요 |
| 메시지 키 중복 | 동일 키가 여러 파일에서 중복 정의된 경우 | 경고: 유지보수 혼란 가능성 |
| 도메인 메시지 위치 | 메시지 파일이 도메인 경로와 불일치 | 자동화 스크립트 오류 가능성 경고 |
| 메시지 값 누락 | 메시지 키는 존재하나 값이 비어 있는 경우 | 경고: 문서화 및 응답 누락 우려 |

## 도메인 모델 개념

- 도메인 모델은 ___앱 안에서 실제로 사용하는 데이터 형태___ 이다.

    - 간단한 프로젝트는 도메인모델과 매핑을 안쓰고 __Repogitory 로 바로 전환해서 사용함__

-> 예시 

```json
// 서버 응답
{
  "access_token": "abc123",
  "refresh_token": "def456",
  "user_name": "홍길동"
}
```

-> DomainModel(kotlin형식에 맞게 작성)

```kotlin
data class LoginToken(
    val accessToken: String,
    val refreshToken: String,
    val userName: String
)
```

---
---

## 도메인 모델 쓰는 이유

서버 응답 DTO를 화면이나 ViewModel에서 바로 쓰면 문제가 생길 수도 있음

-> 예시

    필드명이 바뀜
    응답 구조가 바뀜

: 앱 전체 코드가 같이 흔들릴 수 있다.

-> _서버 응답은 DTO_ 로 받고,
___앱 내부에서는 도메인 모델___ 로 변환해서 사용

-> ___구조___

    Server JSON
    ↓
    ResponseDto
    ↓ 매핑
    Domain Model
    ↓
    ViewModel / UI


---

## 매핑

개념 : DTO를 도메인 모델로 바꾸는 작업

-> 형식

```kotlin
fun LoginResponseDto.toDomain(): LoginToken {
    return LoginToken(
        accessToken = access_token,
        refreshToken = refresh_token,
        userName = user_name
    )
}
```

: 메소드 설명 

1. toDomain() :LoginResponseDto를 LoginToken으로 바꾸겠다는 뜻

2.  LoginToken의 예시 

```kotlin
data class LoginToken(
    val accessToken: String,
    val refreshToken: String,
    val userName: String
)
```
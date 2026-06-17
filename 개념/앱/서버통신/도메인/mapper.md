## 개념
### 쓰는 이유
    mapper은 서버응답 값이 바뀌면 앱 내 Dto 값도 바뀌기에, Dto 값을 바꾸어서 다른 계층에도 영향을 끼치지 않도록 mapper로 필터를 한 번 더 만든다. 그리하여 mapper와 Dto만 수정하여 다른 계층에 영향을 끼치지 않도록 한다?
```
    서버 응답 JSON
    ↓
    DTO
    ↓ mapper
    Domain Model
    ↓
    ViewModel / UI
```


# 구조

### 서버 응답

```kt
{
  "access_token": "abc123",
  "refresh_token": "def456",
  "nickname": "지성"
}
```

### DTO 

```kotlin
package com.example.realapp.data.remote.dto

import com.google.gson.annotations.SerializedName

// 서버 로그인 응답을 그대로 받기 위한 DTO다.
//
// DTO의 역할:
// - 서버 JSON key와 Kotlin 필드를 연결한다.
// - 서버 응답 구조 변경의 영향을 data 계층 안에 가둔다.
data class LoginResponseDto(
    @SerializedName("access_token")
    val accessToken: String,

    @SerializedName("refresh_token")
    val refreshToken: String,

    @SerializedName("nickname")
    val nickname: String
)
```

### Domain Model

```kotlin
package com.example.realapp.domain.model

// 앱 내부 로그인 결과 모델이다.
//
// Domain Model의 역할:
// - 서버 응답 이름에 의존하지 않는다.
// - Repository, ViewModel, UseCase에서 안정적으로 사용한다.
data class LoginResult(
    val accessToken: String,
    val refreshToken: String,
    val userName: String
)
```

### _Mapper_

```kt
package com.example.realapp.data.mapper

import com.example.realapp.data.remote.dto.LoginResponseDto
import com.example.realapp.domain.model.LoginResult

// LoginResponseDto를 앱 내부에서 사용할 LoginResult로 변환하는 메소드다.
//
// 이 메소드를 쓰는 이유:
// - 서버 응답 DTO를 ViewModel까지 직접 넘기지 않는다.
// - 서버 필드명 nickname을 앱 내부 이름 userName으로 바꾼다.
// - 서버 응답 구조가 바뀌어도 domain 계층이 최대한 영향을 덜 받게 한다.
fun LoginResponseDto.toDomain(): LoginResult {
    return LoginResult(
        accessToken = accessToken,
        refreshToken = refreshToken,
        userName = nickname
    )
}
```
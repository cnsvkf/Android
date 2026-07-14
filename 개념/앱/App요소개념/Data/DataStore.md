## 개념

- 앱 내부에 값을 저장

-> 예시

    accessToken
    refreshToken
    사용자 권한
    테마 설정
    알림 ON/OFF 설정

## 기본 구조 

```kotlin
val Context.dataStore by preferencesDataStore(name = "token_store")
```

: context.dataStore는 Context 안에 원래 있던 값을 꺼내는 게 아니라,
Context를 기준으로 DataStore에 접근하도록 만든 확장 프로퍼티다.

#### -> 모든 Context 타입 객체에서 context.dataStore 문법으로 접근 가능

    Application : Context
    Activity : Context
    Service : Context

#### ___보통 Application Context를 사용___
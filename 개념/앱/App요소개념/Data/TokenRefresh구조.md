## 개념

- 로그인 토큰을 저장하고, refreshToken으로 토큰을 갱신하는 구조

| 상황                     |           refreshToken 사용 여부 |
| ---------------------- | ---------------------------: |
| 앱 처음 실행                |                    보통 사용 안 함 |
| 홈 화면 데이터 조회            |                       사용 안 함 |
| accessToken 정상 상태      |                       사용 안 함 |
| accessToken 만료로 401 발생 |                          사용함 |
| 로그아웃                   | 서버에 refreshToken 폐기 요청할 수 있음 |
| 앱 자동 로그인 확인            |                 경우에 따라 사용 가능 |


-> 예시

    로그인 성공
    → accessToken 저장
    → refreshToken 저장
    → 서버 요청할 때 accessToken 붙임
    → accessToken 만료
    → refreshToken으로 새 accessToken 발급
    → 다시 요청

| 구성 요소           | 역할                                     |
| --------------- | -------------------------------------- |
| `DataStore`     | accessToken, refreshToken 저장           |
| `Interceptor`   | 요청마다 accessToken을 Header에 붙임           |
| `Authenticator` | 401이 오면 refreshToken으로 accessToken 재발급 |
| `Repository`    | ViewModel에게 데이터 제공                     |
| `ViewModel`     | 화면 상태 관리                               |




## 예시

### 1. API 요청했는데 401이 오면 재발급

```kotlin
 class TokenAuthenticator(
    private val tokenDataStore: TokenDataStore,
    private val authApi: AuthApi
) : Authenticator {

    // OkHttp의 Authenticator 인터페이스에서 제공하는 메소드다.
    // 서버가 401 Unauthorized를 반환하면 OkHttp가 자동으로 호출한다.
    // 새 인증 정보를 담은 Request를 반환하면 OkHttp가 그 요청으로 재시도한다.
    // null을 반환하면 OkHttp는 재시도하지 않는다.
    override fun authenticate(route: Route?, response: Response): Request? {

        // 직접 만든 메소드다.
        // DataStore에 저장된 refreshToken을 동기 방식으로 가져온다.
        // refreshToken이 없으면 accessToken을 재발급할 수 없으므로 null을 반환한다.
        val refreshToken = tokenDataStore.getRefreshTokenBlocking()
            ?: return null

        // 직접 만든 메소드다.
        // refreshToken을 서버에 보내서 새 accessToken을 발급받는다.
        // authenticate()는 suspend 함수가 아니므로 blocking 방식 메소드를 사용한다.
        val newTokenResponse = authApi.refreshTokenBlocking(
            refreshToken = refreshToken
        )

        // 서버 응답 DTO에서 새 accessToken 값을 꺼낸다.
        // accessToken은 메소드가 아니라 응답 객체의 프로퍼티다.
        val newAccessToken = newTokenResponse.accessToken

        // 직접 만든 메소드다.
        // 새 accessToken을 DataStore에 저장한다.
        // 이후 요청부터는 AuthInterceptor가 이 토큰을 꺼내 Authorization 헤더에 붙인다.
        tokenDataStore.saveAccessTokenBlocking(newAccessToken)

        // OkHttp의 Response 객체가 가진 request 프로퍼티다.
        // 401로 실패했던 원래 요청을 가져온다.
        return response.request

            // OkHttp의 Request 메소드다.
            // 기존 요청을 바탕으로 새 요청을 만들 수 있는 Builder를 생성한다.
            .newBuilder()

            // OkHttp의 Request.Builder 메소드다.
            // 기존 Authorization 헤더를 새 accessToken 값으로 교체한다.
            .header("Authorization", "Bearer $newAccessToken")

            // OkHttp의 Request.Builder 메소드다.
            // Builder에 설정한 내용을 실제 Request 객체로 완성한다.
            .build()
    }
}
```

Authenticator : OkHttp 라이브러리 안에 들어있는 타입

\+

아 response를 내가 만든 게 아니라 이미 연결해둔 okhttp가 관리한다. 

-> 서버랑 요청했던 내용이 뭔 지 dto에 적지 않아도  프로퍼티로 알 수 있다.
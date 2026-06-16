## 개념

- Interceptor = 서버 요청/응답을 중간에서 가로채서 토큰 추가, 로그 출력, 에러 처리 등을 하는 도구

-> 과정

    앱에서 API 요청
    → Interceptor가 요청을 중간에서 가로챔
    → Header에 accessToken 추가
    → OkHttp가 서버로 요청 전송
    → 서버 응답 받음
    → Interceptor가 응답도 확인 가능
    → 앱으로 응답 전달

\+

    앱 코드
    → OkHttp Interceptor
    → 서버
    → OkHttp Interceptor
    → 앱 코드
## 가로채는 것

1. Request
   서버로 가기 전의 요청

- accessToken Header 추가
- 공통 Header 추가
- 요청 URL 확인
- 요청 Body 확인
- 로그 출력

2. Response
   서버에서 돌아온 응답
   
- 응답 코드 확인
- 401 에러 확인
- 500 서버 에러 확인
- 응답 로그 출력
- 필요하면 재시도 처리

## 구조

    ViewModel
    → Repository
    → Retrofit Service 함수 호출
    → Retrofit이 OkHttp Request 생성
    → OkHttpClient가 그 Request를 처리
    → OkHttpClient 내부의 Interceptor Chain 실행
    → Server

: WebSocket은 Retrofit Service로 이렇게 만들지 않는다. 

-> Retrofit은 HTTP API를 Java/Kotlin interface로 바꿔주는 도구

## 쓰는 이유

- 로그인 후 서버 통신할 때 대부분의 API 요청에는 토큰이 필요

-> 

```kotlin
@GET("user/profile")
suspend fun getProfile(
    @Header("Authorization") token: String
): Response<UserDto>

@GET("post/list")
suspend fun getPosts(
    @Header("Authorization") token: String
): Response<List<PostDto>>
```

### 예시

```kotlin
import okhttp3.Interceptor
import okhttp3.Response

// AuthInterceptor는 서버 요청을 중간에서 가로채는 클래스다.
// Interceptor 인터페이스를 구현했기 때문에 intercept() 메소드를 반드시 override 해야 한다.
class AuthInterceptor : Interceptor {

    // intercept()는 OkHttp가 HTTP 요청을 보낼 때 자동으로 호출하는 메소드다.
    //
    // 파라미터:
    // chain: Interceptor.Chain
    // - 현재 요청 흐름을 관리하는 객체다.
    // - 원래 요청을 꺼낼 수도 있고,
    // - 수정한 요청을 다음 단계로 넘길 수도 있다.
    //
    // 반환 타입:
    // Response
    // - 서버 요청을 보낸 뒤 받은 응답 객체다.
    override fun intercept(chain: Interceptor.Chain): Response {

        // chain.request()
        // - 현재 OkHttp가 보내려고 하는 원래 요청 객체를 가져오는 메소드다.
        // - 이 요청은 Retrofit이 만든 요청이다.
        //
        // 예:
        // GET /users
        // POST /login
        // 이런 요청 정보가 들어 있다.
        val originalRequest = chain.request()

        // originalRequest.newBuilder()
        // - 기존 요청을 바로 수정하는 것이 아니라,
        //   기존 요청을 복사해서 수정 가능한 Builder 객체로 바꾸는 메소드다.
        //
        // Request는 보통 불변 객체이기 때문에 직접 수정하지 않고 Builder를 사용한다.
        val newRequest = originalRequest.newBuilder()

            // addHeader(name, value)
            // - HTTP 요청 Header에 값을 추가하는 메소드다.
            //
            // 여기서는 Authorization이라는 Header를 추가한다.
            //
            // Header 결과:
            // Authorization: Bearer accessToken
            //
            // Bearer는 "이 뒤에 오는 값이 인증 토큰이다"라는 의미로 자주 사용된다.
            //
            // 실제 코드에서는 "accessToken"을 직접 쓰지 않고,
            // DataStore나 SharedPreferences 등에 저장된 실제 토큰 값을 가져와 넣는다.
            .addHeader("Authorization", "Bearer accessToken")

            // build()
            // - Builder로 수정한 내용을 바탕으로 최종 Request 객체를 만드는 메소드다.
            //
            // 여기까지 하면 Authorization Header가 추가된 새로운 요청이 만들어진다.
            .build()

        // chain.proceed(request)
        // - 요청을 다음 단계로 넘기는 메소드다.
        // - 이 메소드를 호출해야 실제 서버 요청이 진행된다.
        //
        // newRequest를 넘기기 때문에,
        // 서버에는 Authorization Header가 추가된 요청이 전송된다.
        //
        // 반환값은 서버에서 받은 Response다.
        return chain.proceed(newRequest)
    }
}
```


### 예시 이외의 기능들

```kotlin
// 응답 코드 확인
val response = chain.proceed(newRequest)

if (response.code == 401) {
    // 토큰 만료 처리
}

// 요청 메소드 확인
val method = originalRequest.method

// 요청 URL 확인
val url = originalRequest.url

// 기존 Header 확인
val token = originalRequest.header("Authorization")

// Header 제거
val newRequest = originalRequest.newBuilder()
    .removeHeader("Authorization")
    .build()

// Header 교체
val newRequest = originalRequest.newBuilder()
    .header("Authorization", "Bearer newToken")
    .build()
```
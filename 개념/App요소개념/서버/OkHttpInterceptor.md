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
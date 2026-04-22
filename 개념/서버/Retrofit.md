## 개념

- Service 인터페이스를 객체로 바꿈

- HTTP 요청을 실제로 보내고 받는 네트워크 도구

-> 서버에 요청하는 방식

```kotlin
package com.example.realapp.data.remote

import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

object RetrofitInstance {

    // 반드시 / 로 끝나는 baseUrl 이어야 함
    private const val BASE_URL = "https://example.com/api/"

    // Retrofit 객체 틀 만듦
    private val retrofit: Retrofit = Retrofit.Builder()
        .baseUrl(BASE_URL)
        .addConverterFactory(GsonConverterFactory.create())
        .build()

    // 위에서 만든 인터페이스를 실제 사용 가능한 객체로 바꿈
    val api: StudentApiService = retrofit.create(StudentApiService::class.java)
}
```

- BaseURL 설정

- 캡슐화

- Retrofit.Builder() : Retrofit은 설정을 쌓아올림

-> 
```kotlin
Retrofit.Builder()
    .설정1()
    .설정2()
    .설정3()
    .build()
```

- .addConverterFactory(GsonConverterFactory.create()) 
    - GsonConverterFactory : Gson JSON라이브러리를 이용해서 변환해줌 
    - addConverterFactory() : Retrofit Builder에 변환기 등록

### -> DTO 변환 가능



- val api: StudentApiService = retrofit.create(StudentApiService::class.java)

    - StudentApiService 인터페이스를 기반으로 실제 네트워크 호출이 가능한 객체를 만듦

    - StudentApiService::class.java : StudentApiService라는 인터페이스 자체의 타입 정보

-> 
### _자동으로 Retrofit 내부 처리_

: 

```kotlin
api.getStudents()를 호출하면,  직접 HTTP 코드를 안 써도 Retrofit이 내부적으로:
@GET("students") 확인

baseUrl과 합쳐 최종 URL 생성

요청 전송

응답 수신

GsonConverterFactory로 JSON을 List<Student>로 변환
결과 반환
```
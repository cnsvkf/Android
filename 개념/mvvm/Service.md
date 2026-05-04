## 개념
API 호출 함수의 설계도

```kotlin
package com.example.realapp.data.remote

import com.example.realapp.data.model.Student
import retrofit2.http.GET

// Retrofit이 이 인터페이스를 보고 실제 네트워크 호출 객체를 만들어줌
interface StudentApiService {

    // GET /students 요청
    @GET("students")
    suspend fun getStudents(): List<Student>
}
```

---


## 방식

1.
- GET : 데이터 조회
- POST : 데이터 생성 / 로그인 요청 / 전송
- PUT : 전체 수정
- PATCH : 일부 수정
- DELETE : 삭제

\+ @POST("login") : login이 EndPoint가 됨

->             val response = api.login(request)


2.
| 구분        | 역할                  | 예시                |
| --------- | ------------------- | ----------------- |
| `Headers` | 요청에 대한 부가 정보        | 토큰, 데이터 형식        |
| `Query`   | URL 뒤에 붙는 조건값       | `?page=1&size=10` |
| `Body`    | 서버에 보내는 실제 JSON 데이터 | 로그인 아이디, 비밀번호     |
| `Path`    | URL 경로 중간에 들어가는 변수값 | Get/usews/{user_id} 에서 {user_id}     |


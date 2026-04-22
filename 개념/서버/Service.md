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
## 역할

- 서버 주소 

- Retrofit 설정(Service)

- ViewModel 요청 받고 결과 돌려줌

## 기본 형식

```kotlin
class StudentRepository(
    private val api: StudentApiService
) {
    suspend fun getStudents(): List<StudentDto> {
        return api.getStudents()
    }
}
```
- 서버 통신은 시간이 걸리기에 suspend 사용

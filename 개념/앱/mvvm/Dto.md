## 개념

- 서버에서 보내거나 받는 데이터 형식 

- 옵셔널 가능

->

```kotlin
package com.example.realapp.data.model

// 서버에서 받아올 학생 1명의 데이터 형태
// JSON 응답과 필드명이 맞아야 자동 변환됨
data class StudentDto(
    val id: Int,
    val name: String,
    val major: String
)
```

---
---

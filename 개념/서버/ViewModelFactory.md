## 필요한 상황

```kotlin
class StudentViewModel(
    private val repository: StudentRepository
) : ViewModel()
```
 생성 시 StudentRepository 가 필요

___하지만___

보통 ViewModel의 생성방식은
StudentViewModel() 같은
기본 생성자

-> 

### ViewModelFactory은 뷰모델 생성 시 속성이 필요할 시 사용(의존성 필요 시)

---
---

## 예제

```kotlin
package com.example.realapp.ui.student

import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider
import com.example.realapp.data.repository.StudentRepository

class StudentViewModelFactory(
    private val repository: StudentRepository
) : ViewModelProvider.Factory {

    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(StudentViewModel::class.java)) {
            @Suppress("UNCHECKED_CAST")
            return StudentViewModel(repository) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}
```

- ViewModelProvider.Factory
    - 안드로이드 제공 ViewModel 생성 규칙 인터페이스

- \<T : ViewModel> : ViewModel 속성이기만하면 됨

- override fun <T : ViewModel> create(modelClass: Class<T>): T
    - <T> : 제너릭, ViewModel 타입이여야함
    - 안드로이드가 어떤 ViewModel을 만들어 달라고 요청하면, 그 클래스 정보(modelClass)를 보고 실제 ViewModel 객체를 만들어서 반환
    - ViewModelProvider.Factory 규칙을 따르기에 override

- isAssignableFrom() : () 속성인지 확인(boolean)

- return StudentViewModel(repository) as T : 

- as T : create() 은 반환값이 T 강제 적용
    - Factory은 요청한 타입 그대로 반환이기에 T로 함

-  @Suppress("UNCHECKED_CAST") : as T 강제 적용 경고 숨김

- throw IllegalArgumentException("Unknown ViewModel class") : 다른 모델 생성 요청이 오면 에러

---
---

## ViewModel로 구현

#### 방식 1

```kotlin
private val viewModel: StudentViewModel by viewModels {
    StudentViewModelFactory(repository)
}
```
- StudentViewModel 속성의 viewModel 생성 -> by로 자동관리(by 오른쪽이 관리함) -> viewModels로 Factory 찾아서 생성
    - viewModels : { } 로 넘겨준 Factory 찾음

#### 방식 2
```kotlin
val repository = StudentRepository(api)
val factory = StudentViewModelFactory(repository)

val viewModel = ViewModelProvider(this, factory)[StudentViewModel::class.java]
```

- ViewModelProvider : 범위(Activity/Fragment)에서 이 StudentViewModel이 이미 있으면 그걸 주고, 없으면 만들어서 저장한 뒤 주는 관리자
    - this : 보통 Activity or Fragment

- [] : get() 문법
    - Factory 가 Class\<T> 를 가지므로 []로 무엇을 가지는 지 전딜

     1. 먼저 저장소에서 같은 ViewModel이 있는지 찾음
    2. 있으면 기존 거 반환
    3. 없으면 Factory로 새로 만듦
    4. 만든 뒤 저장소에 넣고 반환
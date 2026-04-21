

# 함수

---
### suspend

- 중단 가능한 함수 

```kotlin
suspend fun loadUser(): String {
    delay(2000)
    return "박지성"
}
```

---

### launch 

- 결과값이 필요 없는 코루틴 시작

- 반환형은 job 

-> ___형식___

```kotlin
viewModelScope.launch {
    repository.refreshData()
}
```
---

### asacy

- 결과를 나중에 받는 코루틴

- 결과를 밖에서 받아 사용 가능

- __동시에 여러 작업을 해야할 때 사용__

-> ___예시___

```kotlin
viewModelScope.launch {
    val deferred = async {
        10 + 20
    }

    val result = deferred.await()
    println(result)
}
```
---

### withContext

- 메인 스레드에서 하면 안되는 작업을 다른 스레드에서 실행하기 위함

-> ___형식___
```kotlin
withContext(Dispatchers.IO) {
    // 시간이 걸리는 작업
}
```

: Dispatchers.IO 에서 실행

----
----
###     Dispatcher
- 코루틴을 실행할 스레드 환경을 지정하는 객체

-> ___종류___

```kotlin
Dispatchers.Main // UI 전용 스레드

Dispatchers.IO // 입출력 작업용

Dispatchers.Default // 계산작업
```


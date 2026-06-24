

# 함수


### suspend(스위프트에서 await)

- 중단 가능하게 만들어줌

- 코루틴이 아니라, 코루틴 안에서 멈출 수 있는 함수 표시다

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

### async

    async = 코루틴을 실행하고, 결과를 나중에 받을 수 있게 Deferred로 감싼다

    await = async 결과를 꺼낸다

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
###     Dispatcher
- 코루틴을 실행할 스레드 환경을 지정하는 객체

-> ___종류___

```kotlin
Dispatchers.Main // UI 전용 스레드

Dispatchers.IO // 입출력 작업용

Dispatchers.Default // 계산작업
```
---
### Mutex()

- 여러 코루틴이 동시에 작업해서 오류 일으키지 않도록 하는 메소드

    - ___한 번에 한 코루틴만 이 코드 구역에 들어오게 하는 기능___

        - ____보호하려는 데이터마다 같은 Mutex를 공유해야 한다____
-> _예시_

```kt
private var isInitialized = false

suspend fun refreshA() {
    val mutexA = Mutex()

    mutexA.withLock {
        if (!isInitialized) {
            isInitialized = true
        }
    }
}

suspend fun refreshB() {
    val mutexB = Mutex()

    mutexB.withLock {
        if (!isInitialized) {
            isInitialized = true
        }
    }
}
// A는 A 자물쇠만 확인함
// B는 B 자물쇠만 확인함
// 그래서 둘 다 동시에 들어올 수 있음
```

\+

\* Mutex는 __non-reentrant__ 다
: 즉, 이미 잠근 코루틴이 다시 같은 Mutex를 잠그려고 해도 다시 대기한다.
```kt
mutex.withLock {
    mutex.withLock {
        // 위험
    }
}
//이런 식으로 같은 Mutex를 중첩해서 잡으면 멈춘 것처럼 될 수 있다.
```


-> _관련메소드_

    // 가장 추천() 
        -> 이유
    // withLock은 lock()을 걸고,
    // 블록 실행이 끝나면 자동으로 unlock()을 해준다.
    // 중간에 에러가 나도 unlock이 보장되기 때문에 가장 안전하다.
    withLock { }

    // 직접 잠금
    lock()

    // 직접 해제
    unlock()

    // 잠금 시도만 하고 실패하면 false
    tryLock()

    // 현재 잠겨 있는지 확인
    isLocked

    // 특정 owner가 잠금을 가지고 있는지 확인
holdsLock(owner)

#### 사용X

    // A 코루틴: isInitialized == false 확인
    // B 코루틴: isInitialized == false 확인
    // A 코루틴: 데이터 초기화
    // B 코루틴: 데이터 또 초기화

#### 사용

    // A 코루틴만 먼저 들어감
    // B 코루틴은 대기
    // A 코루틴이 isInitialized = true 설정
    // B 코루틴이 들어왔을 때는 이미 true라서 return

---

### synchronized
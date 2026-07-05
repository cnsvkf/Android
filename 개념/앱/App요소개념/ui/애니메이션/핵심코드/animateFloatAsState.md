- 서버 진행률 애니메이션

-> _기본형태_

```kotlin
@Composable
fun animateFloatAsState(
    targetValue: Float,
    animationSpec: AnimationSpec<Float> = defaultAnimation,
    visibilityThreshold: Float = DefaultFloatVisibilityThreshold,
    label: String = "FloatAnimation",
    finishedListener: ((Float) -> Unit)? = null
): State<Float>
```


| 매개변수                  |                     타입 | 생략 가능? | 설명                                              |
| --------------------- | ---------------------: | -----: | ----------------------------------------------- |
| `targetValue`         |                `Float` |    불가능 | 애니메이션이 도착해야 하는 최종 값                             |
| `animationSpec`       | `AnimationSpec<Float>` |     가능 | 애니메이션 방식. 속도, 탄성, easing 등을 정함                  |
| `visibilityThreshold` |                `Float` |     가능 | 목표값에 얼마나 가까워지면 애니메이션이 끝났다고 볼지 정함                |
| `label`               |               `String` |     가능 | Android Studio Animation Preview 등에서 구분하기 위한 이름 |
| `finishedListener`    |   `((Float) -> Unit)?` |     가능 | 애니메이션이 끝났을 때 실행할 함수                             |
| 반환값                   |         `State<Float>` |      - | 애니메이션 중 계속 변하는 Float 상태값                        |           |


- visibilityThreshold 쓰는 이유 : 

목표값이 1f일 때 실제 애니메이션 값이 0.999f까지 왔다면, ___사람 눈에는 거의 1f처럼 보인다.___ 이런 경우 굳이 계속 계산하지 않고 끝났다고 판단할 수 있다.

-> 

    visibilityThreshold = 0.01f

---

예시

```kotlin
val alpha by animateFloatAsState(
    targetValue = if (isVisible) 1f else 0f,
    label = "AlphaAnimation",
    finishedListener = { finishedValue ->
        println("애니메이션 종료 값: $finishedValue")
    }
)
```
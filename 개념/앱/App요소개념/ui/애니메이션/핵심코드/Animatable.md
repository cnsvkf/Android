- Animatable은 animate*AsState보다 한 단계 더 직접 제어하는 애니메이션 객체

    - “한 번 계산하고 끝나는 함수”가 아니라, ___여러 프레임에 걸쳐 값을 계속 바꾸는 suspend 함수___

        - 다른 애니메이션도 비동기지만, Animatable은 직접 제어하기에 suspend 

- 값을 직접 움직이는 애니메이션 객체

| 상황                          | 추천 API             |
| --------------------------- | ------------------ |
| 버튼 색상, 카드 높이처럼 상태에 따라 자동 변경 | `animate*AsState`  |
| 여러 값을 하나의 상태로 같이 변경         | `updateTransition` |
| 직접 시작/정지/순서 제어 필요           | `Animatable`       |
| 드래그, 스와이프, fling 같은 제스처     | `Animatable`       |
| 애니메이션 중간에 끊고 새 애니메이션 시작     | `Animatable`       |


## 기본 구조

| 핵심                             | 설명            |
| ------------------------------ | ------------- |
| `remember { Animatable(...) }` | 애니메이션 값을 기억   |
| `value`                        | 현재 값          |
| `animateTo()`                  | 목표값까지 부드럽게 이동 |
| `snapTo()`                     | 즉시 값 변경       |
| `stop()`                       | 현재 애니메이션 정지   |


```kotlin
val animatable = remember { Animatable(초기값) }
```
 _예시_

```kotlin
val scale = remember { Animatable(1f) }
```

#### -> 애니메이션을 실행할 때는 Coroutine 안에서 실행


```kotlin
LaunchedEffect(Unit) {
    scale.animateTo(1.2f)
}
```

| 메소드                                            | 역할                      |
| ---------------------------------------------- | ----------------------- |
| `animateTo(targetValue)`                       | 현재 값에서 목표 값까지 부드럽게 이동   |
| `snapTo(targetValue)`                          | 애니메이션 없이 즉시 값 변경        |
| `stop()`                                       | 현재 진행 중인 애니메이션 정지       |
| `animateDecay(initialVelocity, animationSpec)` | 속도를 기반으로 점점 감속하는 애니메이션  |
| `updateBounds(lowerBound, upperBound)`         | 값이 움직일 수 있는 최소/최대 범위 설정 |
| `value`                                        | 현재 애니메이션 값              |
| `targetValue`                                  | 현재 목표 값                 |
| `isRunning`                                    | 애니메이션 실행 중인지 여부         |

## 기본 구조
```kotlin
@Composable
fun AnimatedScaleCard() {
    // scale 값을 직접 제어하는 Animatable 객체를 기억하는 코드다.
    val scale = remember { Animatable(1f) }

    // 클릭 이벤트에서 suspend 함수를 실행하기 위한 CoroutineScope다.
    val coroutineScope = rememberCoroutineScope()

    Card(
        modifier = Modifier
            .graphicsLayer {
                // Animatable의 현재 값을 카드 크기에 연결하는 코드다.
                scaleX = scale.value
                scaleY = scale.value
            }
            .clickable {
                coroutineScope.launch {
                    // 카드를 1.15배 크기로 부드럽게 키우는 애니메이션이다.
                    scale.animateTo(
                        targetValue = 1.15f,
                        animationSpec = tween(durationMillis = 120)
                    )

                    // 카드를 다시 원래 크기인 1.0배로 줄이는 애니메이션이다.
                    scale.animateTo(
                        targetValue = 1f,
                        animationSpec = tween(durationMillis = 120)
                    )
                }
            }
            .padding(16.dp)
    ) {
        Text(
            text = "클릭하면 커졌다가 돌아오는 카드",
            modifier = Modifier.padding(24.dp)
        )
    }
}

```
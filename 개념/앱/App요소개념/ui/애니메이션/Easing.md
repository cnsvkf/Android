## 기본 Easing


| 종류                      | 시작 속도 | 중간 속도 |     종료 속도 | 적합한 상황        |
| ----------------------- | ----: | ----: | --------: | ------------- |
| `LinearEasing`          |    일정 |    일정 |        일정 | 무한 회전, 로딩 바   |
| `FastOutSlowInEasing`   |    느림 |    빠름 |        느림 | 일반적인 이동·크기 변경 |
| `LinearOutSlowInEasing` |    빠름 | 점점 느림 |        정지 | 화면에 들어오는 요소   |
| `FastOutLinearInEasing` |    느림 | 점점 빠름 | 빠른 상태로 종료 | 화면에서 나가는 요소   |


#### 추가 Easing

| 계열      | 예시                            | 움직임                |
| ------- | ----------------------------- | ------------------ |
| 기본 가속   | `EaseIn`                      | 느리게 시작하고 빠르게 종료    |
| 기본 감속   | `EaseOut`                     | 빠르게 시작하고 느리게 종료    |
| 가속·감속   | `EaseInOut`                   | 느림 → 빠름 → 느림       |
| 사인 곡선   | `EaseInSine`, `EaseOutSine`   | 비교적 부드러운 가속·감속     |
| 제곱 곡선   | `EaseInQuad`, `EaseOutQuad`   | 일반적인 가속·감속         |
| 세제곱 곡선  | `EaseInCubic`, `EaseOutCubic` | Quad보다 변화가 강함      |
| 강한 곡선   | `EaseInQuart`, `EaseOutQuart` | 가속·감속 차이가 큼        |
| 더 강한 곡선 | `EaseInQuint`, `EaseOutQuint` | 매우 강한 속도 변화        |
| 원형 곡선   | `EaseInCirc`, `EaseOutCirc`   | 끝부분의 속도 변화가 큼      |
| 지수 곡선   | `EaseInExpo`, `EaseOutExpo`   | 매우 극적인 가속·감속       |
| 뒤로 당김   | `EaseInBack`, `EaseOutBack`   | 반대 방향으로 살짝 움직였다 진행 |
| 튕김      | `EaseOutBounce`               | 끝에서 여러 번 튕기는 것처럼 움직임      |
| 탄성      | `EaseOutElastic`              | 고무줄처럼 여러 번 흔들림     |

## 직접 정의 가능


```kotlin
val CustomEasing = Easing { fraction ->
    // 진행률을 제곱해 처음에는 느리고 뒤로 갈수록 빠르게 만든다.
    fraction * fraction
}
```
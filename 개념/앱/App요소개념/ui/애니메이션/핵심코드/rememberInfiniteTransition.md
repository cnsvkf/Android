- 끝없이 반복되는 애니메이션 API

| 상황    | 예시                             |
| ----- | ------------------------------ |
| 로딩 중  | 점점 밝아졌다 어두워지는 skeleton/shimmer |
| 강조 효과 | 좋아요 버튼이 계속 pulse               |
| 대기 상태 | 로딩 아이콘 회전                      |
| 알림 효과 | 선택된 카드 테두리 깜빡임                 |


    rememberInfiniteTransition은 InfiniteTransition을 만들고, 그 안에서 여러 개의 애니메이션 값을 동시에 관리할 수 있다.
    
\* 중단을 직접 정하지 못함

#### -> “중단”은 이렇게 한다: 조건부로 Composition에서 제거

```kotlin
@Composable
fun LoadingContent(
    isLoading: Boolean
) {
    if (isLoading) {
        LoadingPulse()
    } else {
        Text(text = "로딩 완료")
    }
}
```

#### “몇 번 반복하고 멈추기”는 rememberInfiniteTransition 말고 다른 API가 맞다

## 기본 구조






| 코드                     | 정체                           |
| ---------------------- | ---------------------------- |
| `initialValue`         | `animateFloat()`의 매개변수       |
| `targetValue`          | `animateFloat()`의 매개변수       |
| `animationSpec`        | `animateFloat()`의 매개변수       |
| `infiniteRepeatable()` | `animationSpec`에 넣는 함수       |
| `repeatMode`           | `infiniteRepeatable()`의 매개변수 |
| `tween()`              | 한 번 움직이는 방식 설정               |


```kotlin
val infiniteTransition = rememberInfiniteTransition(label = "LoadingTransition")

val alpha by infiniteTransition.animateFloat(
    initialValue = 0.3f,
    targetValue = 1f,
    animationSpec = infiniteRepeatable(
        animation = tween(durationMillis = 700),
        repeatMode = RepeatMode.Reverse
    ),
    label = "AlphaAnimation"
)
```
| 메소드              | 애니메이션 값 타입 | 예시                |
| ---------------- | ---------: | ----------------- |
| `animateFloat()` |    `Float` | 투명도, 크기, 회전값, 진행률 |
| `animateColor()` |    `Color` | 색상 변화             |
| `animateValue()` |     커스텀 타입 | 직접 만든 타입, 특수한 값   |


| repeatMode           | 동작                 |
| -------------------- | ------------------ |
| `RepeatMode.Reverse` | 갔다가 되돌아옴           |
| `RepeatMode.Restart` | 끝까지 간 뒤 다시 처음부터 시작 |


    rememberInfiniteTransition()
            ↓
    InfiniteTransition 객체 생성
            ↓
    animateFloat / animateColor / animateValue로 애니메이션 값 생성
            ↓
    infiniteRepeatable로 계속 반복
            ↓
    Modifier.alpha(alpha), Modifier.scale(scale) 등에 적용
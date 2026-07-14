```kotlin
// 가로 방향 그라데이션
Brush.horizontalGradient(
    colors = listOf(Color.Blue, Color.Cyan),
)

// 세로 방향 그라데이션
Brush.verticalGradient(
    colors = listOf(Color.Blue, Color.Cyan),
)

// 시작점에서 끝점으로 진행하는 선형 그라데이션
Brush.linearGradient(
    colors = listOf(Color.Blue, Color.Cyan),
)

// 중심에서 바깥으로 퍼지는 원형 그라데이션
Brush.radialGradient(
    colors = listOf(Color.White, Color.Blue),
)

// 원을 따라 회전하는 그라데이션
Brush.sweepGradient(
    colors = listOf(
        Color.Red,
        Color.Yellow,
        Color.Green,
        Color.Blue,
        Color.Red,
    ),
)
```
-> 사용

```kotlin
Canvas(
    modifier = Modifier.size(200.dp),
) {
    drawCircle(
        brush = Brush.linearGradient(
            colors = listOf(
                Color.Blue,
                Color.Cyan,
            ),
        ),
        radius = 80.dp.toPx(),
    )
}
```
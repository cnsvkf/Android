- 도형을 어떻게 표현할지를 결정

        Fill
        Stroke()

-----
### Fill

```kotlin
drawCircle(
    color = Color.Blue,
    style = Fill,
)
```

- 대부분 그리기도형은 기본 값이 Fill

### Stroke

- 내부를 칠하지 않고, 테두리만 그림

```kotlin
style = Stroke(
    // 테두리 두께
    width = 8.dp.toPx(),

    // 선 끝 모양
    cap = StrokeCap.Round,

    // 선이 꺾이는 부분의 연결 모양
    join = StrokeJoin.Round,
)
```

-> _예시_

```kotlin
drawArc(
    color = Color.Blue,
    startAngle = -90f,
    sweepAngle = 270f,
    useCenter = false,
    style = Stroke(
        width = 12.dp.toPx(),
        cap = StrokeCap.Round,
    ),
)
```
- 기본 도형 함수만으로 만들기 어려운 모양을 직접 정의하는 객체

## Method

```kotlin
val path = Path().apply {
    // 현재 위치를 선을 긋지 않고 이동한다.
    moveTo(x = 100f, y = 0f)

    // 현재 위치부터 지정 좌표까지 직선을 긋는다.
    lineTo(x = 200f, y = 200f)

    // 2차 베지어 곡선을 만든다.
    quadraticTo(
        x1 = 150f,
        y1 = 100f,
        x2 = 100f,
        y2 = 200f,
    )

    // 3차 베지어 곡선을 만든다.
    cubicTo(
        x1 = 50f,
        y1 = 50f,
        x2 = 150f,
        y2 = 150f,
        x3 = 200f,
        y3 = 200f,
    )

    // 마지막 위치와 시작 위치를 연결한다.
    close()
}
```

---

### 삼각형 예시

```kotlin
Canvas(
    modifier = Modifier.size(200.dp),
) {
    val trianglePath = Path().apply {
        // 펜을 위쪽 가운데로 이동한다.
        moveTo(
            x = size.width / 2f,
            y = 0f,
        )

        // 오른쪽 아래까지 선을 긋는다.
        lineTo(
            x = size.width,
            y = size.height,
        )

        // 왼쪽 아래까지 선을 긋는다.
        lineTo(
            x = 0f,
            y = size.height,
        )

        // 현재 지점과 시작 지점을 연결해 도형을 닫는다.
        close()
    }

    drawPath(
        path = trianglePath,
        color = Color.Blue,
    )
}
```
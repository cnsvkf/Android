
-  drawContent()를 어느 위치에서 호출하느냐가 중요

- ContentDrawScope

| Scope              | 사용 가능한 핵심 기능                                          |
| ------------------ | ----------------------------------------------------- |
| `DrawScope`        | `size`, `center`, `drawCircle`, `drawArc`, `drawPath` |
| `ContentDrawScope` | `DrawScope` 기능 + `drawContent()`                      |
| `CacheDrawScope`   | 크기 기반 객체 생성 및 캐싱 + `onDrawBehind`/`onDrawWithContent` |

#### drawContent() // 원래 Composable의 콘텐츠를 그려라.
```kotlin
@Composable
fun DrawWithContentExample() {
    Text(
        modifier = Modifier.drawWithContent {
            // 1. 기존 Text를 먼저 그린다.
            drawContent()

            // 2. 그 위에 반투명 사각형을 그린다.
            drawRect(
                color = Color.Black.copy(alpha = 0.3f),
            )
        },
        text = "잠금 상태",
    )
}
```


-> 반대


```kotlin
Modifier.drawWithContent {
    // 배경을 먼저 그린다.
    drawRect(Color.Yellow)

    // 기존 콘텐츠를 나중에 그린다.
    drawContent()
}
```

---

#### drawContent()를 호출하지 않으면

->

``kotlin
Text(
    modifier = Modifier.drawWithContent {
        // drawContent()를 호출하지 않았다.
        drawCircle(Color.Red)
    },
    text = "보이지 않음",
)
```

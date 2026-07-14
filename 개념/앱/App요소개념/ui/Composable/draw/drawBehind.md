    1. 그림 자체가 하나의 UI 요소
    → Canvas

    2. 이미 존재하는 UI에 그림을 추가
    → drawBehind
    → drawWithContent
    → drawWithCache

-> 

    Text, Image, Box 같은 기존 컴포넌트가 없다면 Canvas를 사용하고,

    기존 컴포넌트의 배경·테두리·효과를 직접 그리고 싶다면 Modifier.draw... 계열을 사용



- DrawScope

| Scope              | 사용 가능한 핵심 기능                                          |
| ------------------ | ----------------------------------------------------- |
| `DrawScope`        | `size`, `center`, `drawCircle`, `drawArc`, `drawPath` |
| `ContentDrawScope` | `DrawScope` 기능 + `drawContent()`                      |
| `CacheDrawScope`   | 크기 기반 객체 생성 및 캐싱 + `onDrawBehind`/`onDrawWithContent` |

```kotlin
@Composable
fun DrawBehindExample() {
    Text(
        modifier = Modifier
            .padding(20.dp)
            .drawBehind {
                // Text가 그려지기 전에 배경 원을 그린다.
                drawCircle(
                    color = Color.Yellow,
                    radius = size.minDimension / 2f,
                    center = center,
                )
            },
        text = "NEW",
    )
}
```
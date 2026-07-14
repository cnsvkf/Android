- Brush, Path 등 무거운 객체를 캐시하면서 그리기

| 구분          | 역할                               |
| ----------- | -------------------------------- |
| `DrawScope` | **그림을 그릴 수 있는 작업 공간과 기능**        |
| `Brush`     | 도형 내부를 **어떤 색상 규칙으로 칠할지** 정하는 객체 |
| `Path`      | **어떤 모양과 경로로 그릴지** 정하는 객체        |
| `DrawStyle` | 내부를 채울지, 테두리만 그릴지 결정             |

:

    1. 그리기에 필요한 객체를 준비하는 단계
    → Brush, Path, Shader 등을 생성하고 보관

    2. 실제로 화면에 그리는 단계
    → 준비해 둔 객체를 반복해서 사용

#### -> Box은 다시 그려질 때마다 Brush/Path를 새로 만듦

- ContentDrawScope or onDrawWithContent 


| Scope              | 사용 가능한 핵심 기능                                          |
| ------------------ | ----------------------------------------------------- |
| `DrawScope`        | `size`, `center`, `drawCircle`, `drawArc`, `drawPath` |
| `ContentDrawScope` | `DrawScope` 기능 + `drawContent()`                      |
| `CacheDrawScope`   | 크기 기반 객체 생성 및 캐싱 + `onDrawBehind`/`onDrawWithContent` |

#### 구조

```kotlin
Modifier.drawWithCache {
    // 1. 준비 및 캐시 영역
    val brush = Brush.linearGradient(...)
    val path = Path().apply {
        // 복잡한 경로 설정
    }

    // 2. 실제 그리기 방법 반환
    onDrawBehind {
        drawPath(
            path = path,
            brush = brush,
        )
    }
}
```

#### _내부에서 다음 중 하나를 사용해야 한다._

->

```kotlin
onDrawBehind {
    // 기존 콘텐츠 뒤에 그린다.
}
```

or


```kotlin
onDrawWithContent {
    // 기존 콘텐츠와 그리기 순서를 직접 제어한다.
    drawContent()
}
```
----
```kotlin
@Composable
fun DrawWithCacheExample() {
    Text(
        modifier = Modifier
            .padding(20.dp)
            .drawWithCache {
                // Brush 객체를 캐시한다.
                // 크기나 여기서 읽은 상태가 바뀌기 전까지 재사용된다.
                val backgroundBrush = Brush.linearGradient(
                    colors = listOf(
                        Color.Blue,
                        Color.Cyan,
                    ),
                )

                // 실제 그리기 단계에서 콘텐츠 뒤에 배경을 그린다.
                onDrawBehind {
                    drawRoundRect(
                        brush = backgroundBrush,
                        cornerRadius = CornerRadius(
                            x = 16.dp.toPx(),
                            y = 16.dp.toPx(),
                        ),
                    )
                }
            },
        text = "캐시된 그라데이션",
        color = Color.White,
    )
}
```
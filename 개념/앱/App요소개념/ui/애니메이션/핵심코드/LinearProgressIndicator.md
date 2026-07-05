![alt](https://lh3.googleusercontent.com/Ow-zWyAH1C0qHjmXUjeQdCDdbk9gXtbfMeppyZOpc0NBIarFaRDkCt6OBYzVGLMQc0v6rJgu_so3k5Lnu0IZECmUpC88SDC8wQ1WsWeTNg3iQH8Qbx0=w1064-v0)

## 기본 형태

### 1. 진행률을 모를 때

- 막대가 계속 움직이면서 “작업*중” 이라는 것만 알려준다.

```kotlin
@Composable
fun LoadingProgressBar() {
    // 진행률을 모르는 로딩 상태를 보여주는 메소드다.
    LinearProgressIndicator(
        modifier = Modifier.fillMaxWidth()
    )
}
```

-> _예시_
```kotlin
if (uiState.isLoading) {
    LinearProgressIndicator(
        modifier = Modifier.fillMaxWidth()
    )
}
```

## 2. 진행률을 알 때

```kotlin
@Composable
fun ProjectProgressBar(
    progress: Float
) {
    val animatedProgress by animateFloatAsState(
        targetValue = progress,
        animationSpec = tween(durationMillis = 500),
        label = "ProjectProgress"
    )

    LinearProgressIndicator(
        progress = { animatedProgress },
        modifier = Modifier.fillMaxWidth()
    )
}  
```

|       값 | 의미   |
| ------: | ---- |
|    `0f` | 0%   |
| `0.25f` | 25%  |
|  `0.5f` | 50%  |
| `0.75f` | 75%  |
|    `1f` | 100% |


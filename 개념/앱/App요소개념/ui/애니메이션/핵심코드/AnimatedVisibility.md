## 기본 형태

- 상태값이 바뀔 때, 그 상태에 맞는 Composable 내용을 애니메이션으로 교체하는 Composable

        isLoading = true  -> 로딩 UI
        isLoading = false -> 실제 목록 UI

```kotlin
@Composable
fun <S> AnimatedContent(
    targetState: S,
    modifier: Modifier = Modifier,
    transitionSpec: AnimatedContentTransitionScope<S>.() -> ContentTransform = { 기본 애니메이션 },
    contentAlignment: Alignment = Alignment.TopStart,
    label: String = "AnimatedContent",
    contentKey: (targetState: S) -> Any? = { it },
    content: @Composable AnimatedContentScope.(targetState: S) -> Unit
)
```

| 매개변수               | 필수 여부 | 역할                                                    |
| ------------------ | ----: | ----------------------------------------------------- |
| `targetState`      |    필수 | 어떤 상태를 기준으로 content를 바꿀지 정한다.                         |
| `modifier`         |    선택 | `AnimatedContent` 전체 박스에 적용할 Modifier다.               |
| `transitionSpec`   |    선택 | 들어오는 content와 나가는 content의 애니메이션 방식을 정한다.             |
| `contentAlignment` |    선택 | 애니메이션 중 old content와 new content가 겹칠 때 내부 정렬 기준을 정한다. |
| `label`            |    선택 | Android Studio Animation Preview에서 구분하기 위한 이름이다.      |
| `contentKey`       |    선택 | 어떤 값이 바뀔 때만 애니메이션할지 결정하는 key다.                        |
| `content`          |    필수 | `targetState`에 따라 실제로 보여줄 Composable이다.               |

## 예시 코드

```kotlin
var expanded by remember { mutableStateOf(false) }
Surface(
    color = MaterialTheme.colorScheme.primary,
    onClick = { expanded = !expanded }
) {
    AnimatedContent(
        targetState = expanded,
        transitionSpec = {
            fadeIn(animationSpec = tween(150, 150)) togetherWith
                fadeOut(animationSpec = tween(150)) using
                SizeTransform { initialSize, targetSize ->
                    if (targetState) {
                        keyframes {
                            // Expand horizontally first.
                            IntSize(targetSize.width, initialSize.height) at 150
                            durationMillis = 300
                        }
                    } else {
                        keyframes {
                            // Shrink vertically first.
                            IntSize(initialSize.width, targetSize.height) at 150
                            durationMillis = 300
                        }
                    }
                }
        }, label = "size transform"
    ) { targetExpanded ->
        if (targetExpanded) {
            Expanded()
        } else {
            ContentIcon()
        }
    }
}
```

| 매개변수               | 사용 여부 | 역할                          |
| ------------------ | ----: | --------------------------- |
| `targetState`      |   사용함 | 어떤 상태가 바뀔 때 content를 바꿀지 정함 |
| `transitionSpec`   |   사용함 | 전환 애니메이션 방식 지정              |
| `label`            |   사용함 | 애니메이션 디버깅용 이름               |
| `content`          |   사용함 | 상태에 따라 실제 UI 결정             |
| `modifier`         |   생략함 | 기본값 사용                      |
| `contentAlignment` |   생략함 | 기본값 사용                      |
| `contentKey`       |   생략함 | 기본값 사용                      |

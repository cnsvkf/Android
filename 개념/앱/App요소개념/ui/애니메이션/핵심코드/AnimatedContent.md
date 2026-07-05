
## 기본 구조

- ___상태값이 바뀔 때___, 그 상태에 맞는 __UI를 다른 UI로 애니메이션 전환__ 해주는 Composable

- ___SizeTransform___ 와 중요

        isLoading = true  -> 로딩 화면
        isLoading = false -> 실제 목록 화면



```kotlin
AnimatedContent(
    targetState = 상태값,
    label = "애니메이션 이름"
) { targetState ->
    when (targetState) {
        상태1 -> 상태1일 때 보여줄 UI()
        상태2 -> 상태2일 때 보여줄 UI()
    }
}
```


\+ __전체 매개변수 구조__
```kotlin
@Composable
fun <S> AnimatedContent(
    targetState: S,
    modifier: Modifier = Modifier,
    transitionSpec: AnimatedContentTransitionScope<S>.() -> ContentTransform = { ... },
    contentAlignment: Alignment = Alignment.TopStart,
    label: String = "AnimatedContent",
    contentKey: (targetState: S) -> Any? = { it },
    content: @Composable AnimatedContentScope.(targetState: S) -> Unit
)
```

| 매개변수               | 필수 여부 | 실무 사용 빈도 | 설명                                     |
| ------------------ | ----: | -------: | -------------------------------------- |
| `targetState`      |    필수 |    매우 높음 | 어떤 상태가 바뀔 때 애니메이션할지 정함                 |
| `modifier`         |    선택 |       높음 | 크기, padding, fillMaxSize 등 UI 배치       |
| `transitionSpec`   |    선택 |    중간~높음 | 들어오는 UI / 나가는 UI 애니메이션 설정              |
| `contentAlignment` |    선택 |    낮음~중간 | 전환 중 겹치는 content의 정렬 위치/ 겹처 있는 content를 컨테이너 안 어디에 맞출지                |
| `label`            |    선택 |       높음 | Android Studio Animation Preview에서 구분용 |
| `contentKey`       |    선택 |    낮음~중간 | 어떤 상태 변화에서만 애니메이션할지 key 지정             |
| `content`          |    필수 |    매우 높음 | 상태에 따라 실제로 보여줄 UI 작성                   |

## 예시 2
```kotlin
@Composable
fun ProjectContent(
    contentState: ProjectContentState,
    onRetryClick: () -> Unit
) {
    // contentState 값이 Loading, Success, Error 사이에서 바뀔 때
    // 화면 내용을 애니메이션으로 전환하는 메소드다.
    AnimatedContent(
        targetState = contentState,
        modifier = Modifier.fillMaxSize(),
        transitionSpec = {
            fadeIn(
                animationSpec = tween(durationMillis = 300)
            ) togetherWith fadeOut(
                animationSpec = tween(durationMillis = 200)
            )
        },
        label = "ProjectContent"
    ) { targetContentState ->

        // targetContentState는 AnimatedContent가 현재 그려야 할 상태다.
        // 외부 contentState를 직접 쓰지 않고 이 값을 기준으로 UI를 나눈다.
        when (targetContentState) {
            ProjectContentState.Loading -> {
                ProjectLoadingScreen()
            }

            is ProjectContentState.Success -> {
                ProjectListScreen(
                    projects = targetContentState.projects
                )
            }

            is ProjectContentState.Error -> {
                ProjectErrorScreen(
                    message = targetContentState.message,
                    onRetryClick = onRetryClick
                )
            }
        }
    }
}
```

->
### 좋지 않은 코드
```kotlin
AnimatedContent(
    targetState = expanded,
    label = "size transform"
) {
    if (expanded) {
        Expanded()
    } else {
        ContentIcon()
    }
}
```

#### 이유 : AnimatedContent는 애니메이션 중에 이전 content와 새 content를 동시에 관리


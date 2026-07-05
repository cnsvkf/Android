## 기본 형태

- ___Composable의 크기가 바뀔 때___ width/height 변화를 자동으로 애니메이션 처리하는 Modifier

-  animateContentSize가 배치되는 순서가 중요합니다. 부드러운 애니메이션의 경우 animateContentSize가 레이아웃에 애니메이션 값 변경을 보고하도록 size 또는 defaultMinSize와 같은 ___크기 수정자 앞에 배치해야 합니다.___

| 구분       | `AnimatedContent`                   | `Modifier.animateContentSize()` |
| -------- | ----------------------------------- | ------------------------------- |
| 정체       | **Composable**                      | **Modifier**                    |
| 목적       | 상태에 따라 **다른 UI로 전환**                | 같은 UI 안에서 **크기 변화만 부드럽게 처리**    |
| 대상       | 로딩 화면 ↔ 내용 화면, 성공 ↔ 실패 화면           | 카드 펼치기, 댓글 더보기, 텍스트 길이 변화       |
| 애니메이션 범위 | fade, slide, scale, size 등 전환 효과 가능 | width/height 크기 변화 중심           |
| 사용 위치    | UI를 감싸는 Composable로 사용              | `Modifier` 체인에 붙여서 사용           |
| 전환 기준    | `targetState`                       | 자식 content의 크기 변화               |

-> _예시_
```kotlin
fun Modifier.animateContentSize(
    animationSpec: FiniteAnimationSpec<IntSize> = spring(...),
    alignment: Alignment = Alignment.TopStart,
    finishedListener: ((initialValue: IntSize, targetValue: IntSize) -> Unit)? = null
): Modifier
```

| 매개변수               |                             타입 | 생략 가능 | 역할                                  |
| ------------------ | -----------------------------: | ----: | ----------------------------------- |
| `animationSpec`    | `FiniteAnimationSpec<IntSize>` |    가능 | 크기 변화 애니메이션 방식                      |
| `alignment`        |                    `Alignment` |    가능 | 크기가 변하는 동안 내부 content를 어느 기준으로 배치할지 |
| `finishedListener` |   `(IntSize, IntSize) -> Unit` |    가능 | 크기 애니메이션이 끝났을 때 실행할 콜백              |


## 예시 코드

- 서버 설명글 길이 + 펼치기

```kotlin
@Composable
fun ProjectDescription(
    description: String,
    isExpanded: Boolean,
    onExpandClick: () -> Unit
) {
    Column(
        modifier = Modifier
            .fillMaxWidth()
            .animateContentSize()
            .clickable(onClick = onExpandClick)
    ) {
        Text(
            text = description,
            maxLines = if (isExpanded) Int.MAX_VALUE else 2,
            overflow = TextOverflow.Ellipsis
        )
    }
}
```
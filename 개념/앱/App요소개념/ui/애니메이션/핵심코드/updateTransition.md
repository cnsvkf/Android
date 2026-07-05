## 기본 구조



```kotlin
@Composable
fun <T : Any?> updateTransition(
    targetState: T,
    label: String? = null
): Transition<T>

```
```kotlin
val transition = updateTransition(
    targetState = selected,
    label = "ProjectCardTransition"
)

val scale by transition.animateFloat(
    label = "CardScale"
) { isSelected ->
    if (isSelected) 1.05f else 1f
}

val alpha by transition.animateFloat(
    label = "CardAlpha"
) { isSelected ->
    if (isSelected) 1f else 0.7f
}
```

    selected 값 변경
            ↓
    updateTransition이 감지
            ↓
    transition 안에 있는 animateFloat, animateDp, animateColor 등이 같이 실행
            ↓
    UI가 자연스럽게 바뀜
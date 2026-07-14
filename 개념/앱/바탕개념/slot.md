    Slot 방식이란?
    **컴포넌트 내부의 특정 위치를 비워두고, 외부에서 원하는 UI를 넣게 하는 방식이야.
    Compose에서는 보통 아래처럼 @Composable 람다를 매개변수로 받아서 만든다.

## 기본 구조

```kotlin
@Composable
fun BookCard(
    // BookCard 내부의 표지 위치에 넣을 UI를 외부에서 받는다.
    cover: @Composable () -> Unit,
) {
    Column {
        // 외부에서 전달받은 UI를 이 위치에 배치한다.
        cover()

        Text(text = "책 제목")
    }
}
```

### 기본값이 있는 구조

```kotlin
@Composable
fun BookCard(
    // cover를 전달하지 않으면 BookCoverPlaceholder를 사용한다.
    cover: @Composable () -> Unit = {
        BookCoverPlaceholder()
    },
) {
    Column {
        cover()
        Text(text = "책 제목")
    }
}
```
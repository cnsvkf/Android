![alt](https://blog.kakaocdn.net/dna/dwawA5/btrmZmfKEUf/AAAAAAAAAAAAAAAAAAAAAI9pNDqaqsqHfkyuSPlUESYPRo9lckSUDGzzkZ00m2pU/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1785509999&allow_ip=&allow_referer=&signature=WnlGmlTwf6U6tZvNFzLplSZ3%2Fk0%3D)

| 매개변수                | 종류            | 기능                     | 실행·표시 시점        | 예시                                                   |
| ------------------- | ------------- | ---------------------- | --------------- |-- |
| `onDismissRequest`  | 함수형 콜백        | 다이얼로그를 닫으려고 할 때 실행할 동작 | 바깥 영역 클릭, 뒤로 가기 | `onDismissRequest = { showDialog = false }`          |
| `confirmButton`     | Composable 슬롯 | 확인 버튼 UI를 배치하는 영역      | 다이얼로그가 표시될 때    | `confirmButton = { TextButton(...) }`                |
| `dismissButton`     | Composable 슬롯 | 취소 버튼 UI를 배치하는 영역      | 다이얼로그가 표시될 때    | `dismissButton = { TextButton(...) }`                |
| `title`             | Composable 슬롯 | 다이얼로그 제목 UI            | 다이얼로그 상단        | `title = { Text("알림") }`                             |
| `text`              | Composable 슬롯 | 다이얼로그 본문 UI            | 제목 아래           | `text = { Text("삭제할까요?") }`                          |
| `icon`              | Composable 슬롯 | 제목 위쪽 아이콘 UI           | 다이얼로그 상단        | `icon = { Icon(...) }`                               |
| `modifier`          | 일반 설정값        | 크기, 패딩 등 Modifier 적용   | 다이얼로그 생성 시      | `modifier = Modifier.padding(16.dp)`                 |
| `shape`             | 일반 설정값        | 다이얼로그 모서리 모양           | 다이얼로그 생성 시      | `shape = RoundedCornerShape(20.dp)`                  |
| `containerColor`    | 일반 설정값        | 다이얼로그 배경색              | 다이얼로그 생성 시      | `containerColor = MaterialTheme.colorScheme.surface` |
| `titleContentColor` | 일반 설정값        | 제목의 기본 색상              | 제목 UI에 적용       | `titleContentColor = Color.Black`                    |
| `textContentColor`  | 일반 설정값        | 본문의 기본 색상              | 본문 UI에 적용       | `textContentColor = Color.Gray`                      |
| `iconContentColor`  | 일반 설정값        | 아이콘의 기본 색상             | 아이콘 UI에 적용      | `iconContentColor = Color.Red`                       |


## 예시

```kotlin
@Composable
fun DeleteBookExample(
    modifier: Modifier = Modifier,
) {
    // 팝업이 현재 화면에 표시되어야 하는지 저장하는 상태
    var showDeleteDialog by rememberSaveable {
        mutableStateOf(false)
    }

    Column(
        modifier = modifier.padding(24.dp),
    ) {
        Button(
            onClick = {
                // 상태를 true로 변경하면 아래 if문이 실행되면서 팝업이 나타난다.
                showDeleteDialog = true
            },
        ) {
            Text(text = "도서 삭제")
        }

        // showDeleteDialog가 true일 때만 팝업을 Composition에 추가한다.
        if (showDeleteDialog) {
            DeleteBookAlertDialog(
                bookTitle = "코틀린 인 액션",

                // 바깥 영역, 뒤로가기 또는 취소 버튼으로 닫기를 요청했을 때 실행된다.
                onDismiss = {
                    showDeleteDialog = false
                },

                // 삭제 확인 버튼을 눌렀을 때 실행된다.
                onConfirm = {
                    // 실제 프로젝트에서는 ViewModel의 삭제 함수를 호출한다.
                    // viewModel.deleteBook()

                    // 삭제 요청 후 팝업을 닫는다.
                    showDeleteDialog = false
                },
            )
        }
    }
}
```

### 팝업 Component 

```kotlin
@Composable
private fun DeleteBookAlertDialog(
    bookTitle: String,
    onDismiss: () -> Unit,
    onConfirm: () -> Unit,
) {
    AlertDialog(
        // 시스템 뒤로가기나 팝업 바깥쪽을 눌렀을 때 호출되는 함수
        onDismissRequest = onDismiss,

        // 팝업 위쪽 아이콘 영역
        icon = {
            Icon(
                imageVector = Icons.Default.Delete,
                contentDescription = null,
            )
        },

        // 팝업 제목 영역
        title = {
            Text(text = "도서를 삭제할까요?")
        },

        // 팝업 본문 영역
        text = {
            Text(
                text = "'$bookTitle' 도서를 목록에서 삭제합니다.",
            )
        },

        // 오른쪽 또는 주요 동작 버튼 영역
        confirmButton = {
            TextButton(
                onClick = onConfirm,
            ) {
                Text(text = "삭제")
            }
        },

        // 왼쪽 또는 보조 동작 버튼 영역
        dismissButton = {
            TextButton(
                onClick = onDismiss,
            ) {
                Text(text = "취소")
            }
        },
    )
}
```
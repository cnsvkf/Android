- 내용물이 비어 있는 Dialog 컨테이너

        배경
        크기
        모양
        그림자
        제목
        본문
        버튼
        정렬
        여백

## 기본 구조 

#### - Card 및 Box, Surface, Column 등 가능

```kotlin
@Composable
fun CustomDialog(
    onDismissRequest: () -> Unit,
) {
    Dialog(
        // 바깥 영역 터치 또는 뒤로가기로 닫기가 요청될 때 실행된다.
        onDismissRequest = onDismissRequest,
    ) {
        Card(
            modifier = Modifier
                .fillMaxWidth()
                .padding(24.dp),
            shape = RoundedCornerShape(20.dp),
        ) {
            Column(
                modifier = Modifier.padding(24.dp),
            ) {
                Text(
                    text = "커스텀 Dialog",
                    style = MaterialTheme.typography.titleLarge,
                )

                Spacer(modifier = Modifier.height(12.dp))

                Text(
                    text = "Dialog 내부의 모든 UI를 직접 배치할 수 있습니다.",
                )

                Spacer(modifier = Modifier.height(24.dp))

                TextButton(
                    // 확인 버튼을 눌렀을 때 Dialog를 닫도록 부모에게 요청한다.
                    onClick = onDismissRequest,
                    modifier = Modifier.align(Alignment.End),
                ) {
                    Text(text = "확인")
                }
            }
        }
    }
}
```
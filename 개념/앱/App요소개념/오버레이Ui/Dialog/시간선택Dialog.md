| 컴포넌트                        | 역할             |
| --------------------------- | -------------- |
| `TimePicker`                | 시계판으로 시간 선택    |
| `TimeInput`                 | 숫자를 입력해서 시간 선택 |
| `rememberTimePickerState()` | 선택된 시각 상태 관리   |

- 시간 선택기는 보통 AlertDialog, BasicAlertDialog 또는 Dialog 안에 넣어서 사용합니다. 공식 가이드도 최소 형태의 Dialog 또는 더 자유로운 커스텀 Dialog에 시간 선택기를 넣는 방식을 설명합


## 예시

```kotlin
@Composable
fun CustomTimePickerDialog(
    onDismissRequest: () -> Unit,
    onConfirm: (
        hour: Int,
        minute: Int,
    ) -> Unit,
) {
    // 선택된 시와 분을 관리하는 상태 객체다.
    val timePickerState = rememberTimePickerState(
        initialHour = 9,
        initialMinute = 0,
        is24Hour = true,
    )

    AlertDialog(
        onDismissRequest = onDismissRequest,

        title = {
            Text(text = "시간 선택")
        },

        text = {
            TimePicker(
                // 시간 선택 결과가 timePickerState에 저장된다.
                state = timePickerState,
            )
        },

        confirmButton = {
            TextButton(
                onClick = {
                    // 현재 선택된 시각을 외부로 전달한다.
                    onConfirm(
                        timePickerState.hour,
                        timePickerState.minute,
                    )

                    onDismissRequest()
                },
            ) {
                Text(text = "확인")
            }
        },

        dismissButton = {
            TextButton(
                onClick = onDismissRequest,
            ) {
                Text(text = "취소")
            }
        },
    )
}
```
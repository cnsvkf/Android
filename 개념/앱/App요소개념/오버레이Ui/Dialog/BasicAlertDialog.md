| 구분            | `Dialog`                     | `BasicAlertDialog`            |
| ------------- | ---------------------------- | ----------------------------- |
| 패키지           | `androidx.compose.ui.window` | `androidx.compose.material3`  |
| 설계 기준         | Compose UI 기본 기능             | Material Design 3             |
| 내부 UI         | 전부 직접 작성                     | 전부 직접 작성                      |
| 배경·모양         | 직접 작성                        | 직접 작성                         |
| 기본 크기 규칙      | 거의 없음                        | Material 규격의 최소 너비·높이 적용      |
| `modifier` 위치 | 별도 `modifier` 매개변수 없음        | `modifier` 매개변수 제공            |
| 실험 API        | 아님                           | 현재 `ExperimentalMaterial3Api` |
| 사용 목적         | 완전히 자유로운 다이얼로그               | Material 스타일의 커스텀 알림창         |

## 매개변수 

```kotlin
BasicAlertDialog(
    onDismissRequest = { },
    modifier = Modifier,
    properties = DialogProperties(),
) {
    // content
}
```

| 매개변수               | 역할                                |
| ------------------ | --------------------------------- |
| `onDismissRequest` | 다이얼로그 바깥을 누르거나 시스템 뒤로가기를 눌렀을 때 호출 |
| `modifier`         | 다이얼로그 콘텐츠의 크기, 너비 등을 설정           |
| `properties`       | 바깥 클릭, 뒤로가기, 기본 너비 등의 동작 설정       |
| `content`          | 다이얼로그 내부 UI                       |

-> AlertDialog와 Dialog의 중간

## 예시

```kotlin
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.width
import androidx.compose.material3.AlertDialogDefaults
import androidx.compose.material3.BasicAlertDialog
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.material3.Text
import androidx.compose.material3.TextButton
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun LogoutBasicAlertDialog(
    onDismiss: () -> Unit,
    onConfirm: () -> Unit,
    modifier: Modifier = Modifier,
) {
    BasicAlertDialog(
        // 다이얼로그 바깥 클릭 또는 시스템 뒤로가기 요청을 처리한다.
        onDismissRequest = onDismiss,

        // BasicAlertDialog 내부 콘텐츠의 크기를 설정한다.
        modifier = modifier.width(320.dp),
    ) {
        // 다이얼로그의 배경, 모양, 그림자를 담당한다.
        Surface(
            modifier = Modifier.fillMaxWidth(),
            shape = MaterialTheme.shapes.extraLarge,
            color = MaterialTheme.colorScheme.surface,
            tonalElevation = AlertDialogDefaults.TonalElevation,
        ) {
            Column(
                modifier = Modifier.padding(24.dp),
                verticalArrangement = Arrangement.spacedBy(16.dp),
            ) {
                Text(
                    text = "로그아웃",
                    style = MaterialTheme.typography.headlineSmall,
                )

                Text(
                    text = "정말 로그아웃하시겠습니까?",
                    style = MaterialTheme.typography.bodyMedium,
                    color = MaterialTheme.colorScheme.onSurfaceVariant,
                )

                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.End,
                ) {
                    TextButton(
                        // 취소 버튼 클릭 시 부모에게 닫기 이벤트를 전달한다.
                        onClick = onDismiss,
                    ) {
                        Text(text = "취소")
                    }

                    TextButton(
                        onClick = {
                            // 실제 로그아웃 작업을 부모 또는 ViewModel에 요청한다.
                            onConfirm()

                            // 로그아웃 요청 후 다이얼로그를 닫는다.
                            onDismiss()
                        },
                    ) {
                        Text(text = "로그아웃")
                    }
                }
            }
        }
    }
}
```
## 개념

- Compose에서 화면의 기본 뼈대를 만들어주는 레이아웃 컴포넌트   
    - ___TopBar, BottomBar, FloatingButton, Snackbar, 실제 화면 내용(content)___ 을 정해진 위치에 배치해주는 틀

- Scaffold는 앱 화면에서 자주 쓰는 고정 UI 구조를 관리

## 구조

```kotlin
@Composable
fun MainScreen() {
    Scaffold(
        // 화면 상단에 고정되는 영역
        topBar = {
            TopAppBar(
                title = {
                    Text(text = "홈")
                }
            )
        },

        // 화면 하단에 고정되는 영역
        bottomBar = {
            BottomNavigationBar()
        },

        // 오른쪽 아래에 뜨는 버튼
        floatingActionButton = {
            FloatingActionButton(
                onClick = {
                    // 버튼 클릭 시 실행
                }
            ) {
                Text("+")
            }
        }
    ) { innerPadding ->
        // Scaffold 내부의 실제 화면 내용
        // innerPadding은 topBar, bottomBar에 가려지지 않게 자동으로     들어오는 패딩값
        HomeContent(
            modifier = Modifier.padding(innerPadding)
        )
    }
}
```

## 주요 파라미터

```kotlin
Scaffold(
    modifier = Modifier.fillMaxSize(),

    topBar = {
        // 상단 앱바 자리
    },

    bottomBar = {
        // 하단 바텀바 자리
    },

    floatingActionButton = {
        // 플로팅 버튼 자리
    },

    snackbarHost = {
        // 스낵바 자리
    }
) { innerPadding ->
    // 실제 화면 내용
}
```

| 파라미터                   | 역할                              |
| ---------------------- | ------------------------------- |
| `topBar`               | 화면 위쪽 앱바                        |
| `bottomBar`            | 화면 아래쪽 바텀바                      |
| `floatingActionButton` | 오른쪽 아래 떠 있는 버튼                  |
| `snackbarHost`         | 하단 메시지 UI                       |
| `content`              | 실제 화면 내용                        |
| `innerPadding`         | topBar/bottomBar에 가려지지 않게 주는 패딩 |

#### innerPadding은 Scaffold가 계산한 패딩값을 하위 화면에 넘겨주고, _하위 화면에서 Modifier.padding(innerPadding)_ 으로 직접 적용하는 값이다.

: 자동적용이 아닌 직접 적용

\+ 

content가 안 보이는 이유는 Kotlin 문법상 마지막 람다 파라미터는 괄호 밖으로 뺄 수 있기 때문

-> content 적용 버전

```kotlin
Scaffold(
    content = { innerPadding ->
        HomeScreen(
            modifier = Modifier.padding(innerPadding)
        )
    }
)
```
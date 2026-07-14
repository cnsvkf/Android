## 개념

- 이 코루틴을 어디서 실행시킬지 결정하는 관리자 타입

| 코드                    | 의미                  |
| --------------------- | ------------------- |
| `Dispatchers.Main`    | UI 작업용, 메인 스레드에서 실행 |
| `Dispatchers.IO`      | 서버 통신, DB, 파일 작업용   |
| `Dispatchers.Default` | CPU 계산 작업용          |
| `CoroutineDispatcher` | 위 값들을 담을 수 있는 공통 타입 |
 

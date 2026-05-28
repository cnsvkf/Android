## 개념

- Hilt는 Android에서 객체를 자동으로 만들어서 필요한 곳에 넣어주는 도구

- __어노테이션__ 이 중요

->

```kotlin
@HiltAndroidApp      // 앱 전체 Hilt 시작
@AndroidEntryPoint   // Activity에서 Hilt 사용 가능
@HiltViewModel       // ViewModel을 Hilt가 생성
@Inject              // 생성자 주입
@Module              // 객체 만드는 방법 모음
@InstallIn           // 객체 사용 범위 설정
@Provides            // 객체 제공 메소드
@Singleton           // 객체 하나만 생성
```




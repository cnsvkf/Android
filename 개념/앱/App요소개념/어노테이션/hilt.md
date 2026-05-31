## 개념

- Hilt는 Android에서 객체를 자동으로 만들어서 필요한 곳에 넣어주는 도구 (DI 라이브러리)
    
    - DI는 Dependency Injection,즉 ___의존성 주입___

-> 

    현재 객체에서 다른 객체가 필요 시에 다른 객체 사용할 수 있게 하는 거고, hilt가 그 기능들

    DI 컨테이너는 쉽게 말하면 객체 보관함 + 객체 조립 공장

: 객체를 직접 만들지 않고, Hilt이 만드는 구조
-> ViewModel, Repository, ApiService 같은 ___객체를 직접 만들지 않고 Hilt가 대신 만듦___

- __어노테이션__ 이 중요

    - Hilt는 라이브러리이고, __어노테이션은 Hilt에게 명령을 전달하는 표시__ 다.


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

---
## 기능


### @HiltAndroidApp

- Application 클래스에 붙힘 

- 앱 전체에서 Hilt를 사용할 수 있게 시작 설정을 한다.
    - ___이걸 붙이지 않으면 @AndroidEntryPoint, @HiltViewModel 등이 제대로 동작하지 않는다___

```kotlin
@HiltAndroidApp
class MyApplication : Application()
```

----
----
### @AndroidEntryPoint
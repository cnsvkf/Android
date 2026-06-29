## 개념
- Hilt는 결국 클래스를 기반으로 객체를 만든다.
    - 중요한 건 모든 클래스를 자동으로 만드는 게 아니라, Hilt가 “만드는 법을 아는 클래스”만 객체로 만든다.

- _____Hilt는 생성자 파라미터를 보고 필요한 객체들을 먼저 만들고, 그 객체들을 조립해서 최종 객체를 만든다._____

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
# 어노테이션 종류


### @HiltAndroidApp

- Application 클래스에 붙힘 

- 앱 전체에서 Hilt를 사용할 수 있게 시작 설정을 한다.
    - ___이걸 붙이지 않으면 @AndroidEntryPoint, @HiltViewModel 등이 제대로 동작하지 않는다___


            앱 시작
            ↓
            MyApplication 생성
            ↓
            @HiltAndroidApp 확인
            ↓
            Hilt가 앱 전체 의존성 관리 준비
\+ AndroidManifest.xml에 등록
```kotlin
@HiltAndroidApp
class MyApplication : Application()
```

----
----
### @AndroidEntryPoint

- 클래스에서 Hilt가 만든 객체를 받을 수 있게 만든다.

- Activity / Fragment / Service / BroadcastReceiver / View에 붙힘

->
 ___실제로 객체를 주입받는 Android 컴포넌트에 붙인다.___

        MainActivity 생성
        ↓
        @AndroidEntryPoint 확인
        ↓
        Hilt가 MainActivity용 컴포넌트 생성
        ↓
        @Inject, hiltViewModel() 등을 사용할 수 있음

| 구분         | Activity             | Application/App            |
| ---------- | -------------------- | -------------------------- |
| 역할         | 화면 담당                | 앱 전체 설정 담당                 |
| 예시         | `MainActivity`       | `ItDaApp`, `MyApplication` |
| 실행 시점      | 화면이 열릴 때             | 앱 프로세스가 시작될 때              |
| UI 있음?     | 있음                   | 없음                         |
| Compose 연결 | `setContent {}` 사용   | 보통 UI 없음                   |
| Hilt 어노테이션 | `@AndroidEntryPoint` | `@HiltAndroidApp`          |

---
### @Inject constructor


- "이 클래스는 Hilt가 생성자를 보고 직접 만들 수 있다" 선언 

    - Hilt은 파라미터를 보고 파라미터 객체를 먼저 만듦
-> 매개변수를 Hilt 가 관리함

            ViewModel
            ↓ 필요
            Repository
            ↓ 필요
            ApiService, Store
        
1. 생성자 매개변수를 Hilt로 받겠다는 뜻도 있다.

2. 더 근본적으로는 객체 자체를 Hilt가 만들 수 있게 등록하는 뜻이다.

-> _예시_

```kt
// useApiService + tokenStore 를 Hilt 관리
class UserRepository @Inject constructor(
    private val userApiService: UserApiService,
    private val tokenStore: TokenStore
) {

    // 서버에서 내 정보를 가져오는 메소드다.
    suspend fun getMyProfile(): UserDto {
        return userApiService.getMyProfile()
    }
}
```

---

### @HiltViewModel


- __ViewModel__ 을 Hilt가 생성하게 만드는 어노테이션

```kt

@HiltViewModel
class HomeViewModel @Inject constructor(
    // HomeRepository는 홈 화면에 필요한 데이터를 가져오는 역할이다.
    // @Inject constructor 덕분에 Hilt가 HomeRepository 구현체를 주입해준다.
    private val homeRepository: HomeRepository
) : ViewModel() {

    // 처음 화면 상태는 HomeUiState() 기본값으로 시작한다.
    private val _uiState = MutableStateFlow(HomeUiState())

    // 외부 화면에서는 uiState를 읽기만 할 수 있다.
    // asStateFlow()를 사용하면 MutableStateFlow를 StateFlow로 감춰서 공개한다.
    // 즉, 화면에서는 상태를 관찰만 하고 직접 수정할 수 없다.
    val uiState: StateFlow<HomeUiState> = _uiState.asStateFlow()

    // 홈 화면 데이터를 불러오는 메소드다.
    // 화면이 처음 켜졌을 때 Route나 Composable에서 호출할 수 있다.
    fun loadHome() {
        // ViewModel 전용 CoroutineScope다.
        // ViewModel이 사라지면 이 코루틴도 자동으로 취소된다.
        viewModelScope.launch {
            // Repository에서 홈 대시보드 데이터를 가져온다.
            // 서버, 로컬 DB, 가짜 데이터 등 실제 데이터 출처는 Repository가 담당한다.
            val dashboard = homeRepository.getHomeDashboard()

            // Repository에서 받은 Domain 데이터를 UI에서 쓰기 좋은 상태로 변환한다.
            // dashboard.toUiState()는 mapper 역할을 한다.
            // 변환된 값을 _uiState에 넣으면 화면이 자동으로 다시 그려진다.
            _uiState.value = dashboard.toUiState()
        }
    }
}
```

\+ Route 코드
```kt
@Composable
fun HomeRoute(
    // Hilt가 HomeViewModel 객체를 생성해서 넣어준다.
    // 직접 HomeViewModel()을 만들지 않아도 된다.
    viewModel: HomeViewModel = hiltViewModel()
) {
    // ViewModel이 가진 uiState(StateFlow)를 Compose State로 변환한다.
    // uiState 값이 바뀌면 HomeRoute가 다시 recomposition 된다.
    val uiState by viewModel.uiState.collectAsState()

    // 실제 화면 UI를 그리는 HomeScreen을 호출한다.
    HomeScreen(
        // 현재 화면 상태를 HomeScreen에 전달한다.
        uiState = uiState,

        // 새로고침 버튼을 눌렀을 때 ViewModel의 loadHome()을 실행하게 넘긴다.
        onRefreshClick = viewModel::loadHome
    )
}
```

---

### @Mudule + @InstallIn
- 클래스 / object / interface에 붙는 어노테이션들

___1. @Mudule___ :  Hilt에게 ____“직접 만들기 어려운 객체를 어떻게 만들지 알려주는 클래스”____

___2. @InstallIn___ : ____"Module을 어느 Hilt 컴포넌트 범위에 등록할지"____ 정함

- Hilt Module은 @Module이 붙은 클래스이고, 모든 Module은 @InstallIn으로 어느 컴포넌트에 설치될지 지정해야 한다. (_거의 항상 같이 사용_ )

| Component                   |           생명주기 범위 | 같이 쓰는 Scope               | 주로 쓰는 곳                                      |
| --------------------------- | ----------------: | ------------------------- | -------------------------------------------- |
| `SingletonComponent`        |              앱 전체 | `@Singleton`              | Repository, Retrofit, Room, DataStore, Store |
| `ActivityRetainedComponent` | Activity 재생성에도 유지 | `@ActivityRetainedScoped` | Activity 회전에도 유지할 객체                         |
| `ViewModelComponent`        |    ViewModel 생명주기 | `@ViewModelScoped`        | ViewModel 내부 전용 UseCase, Repository 보조 객체    |
| `ActivityComponent`         |       Activity 하나 | `@ActivityScoped`         | Activity 단위 객체                               |
| `FragmentComponent`         |       Fragment 하나 | `@FragmentScoped`         | Fragment 전용 객체                               |
| `ViewComponent`             |           View 하나 | `@ViewScoped`             | 커스텀 View 전용 객체                               |
| `ViewWithFragmentComponent` |  Fragment 안의 View | `@ViewScoped`             | Fragment 바인딩이 필요한 View                       |
| `ServiceComponent`          |        Service 하나 | `@ServiceScoped`          | ForegroundService, 백그라운드 Service용 객체         |


### Hilt Component / Scope 종류

- ___종류___

/*
___Hilt Component 구조___

1. SingletonComponent
   - 앱 전체 생명주기
   - @Singleton 사용
   - Retrofit, Room, DataStore, Repository 등에 자주 사용

2. ActivityRetainedComponent
   - Activity가 회전 등으로 재생성되어도 유지
   - @ActivityRetainedScoped 사용
   - Activity 흐름 단위 상태 저장에 사용

3. ViewModelComponent
   - ViewModel 생명주기
   - @ViewModelScoped 사용
   - 특정 ViewModel 안에서만 공유할 객체에 사용

4. ActivityComponent
   - Activity 하나의 생명주기
   - @ActivityScoped 사용
   - Activity가 사라지면 객체도 사라짐

5. FragmentComponent
   - Fragment 하나의 생명주기
   - @FragmentScoped 사용
   - Fragment 안에서만 유지할 객체에 사용

6. ViewComponent
   - Custom View 하나의 생명주기
   - @ViewScoped 사용
   - 일반 Compose 앱에서는 잘 안 씀

7. ServiceComponent
   - Service 하나의 생명주기
   - @ServiceScoped 사용
   - ForegroundService 같은 곳에서 사용
*/



### _싱글톤 예시_ 
    
- 앱 전체에서 하나만 유지할 객체.

        UserStore()로 직접 만들면 @Singleton 의미 없음

        Hilt가 대신 UserStore를 만들어서 넣어줄 때만
        @Singleton 규칙이 적용됨

```kt
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {

    @Provides
    @Singleton
    fun provideApiService(): ApiService {
        return Retrofit.Builder()
            .baseUrl("https://example.com/")
            .build()
            .create(ApiService::class.java)
    }
}
```

-> _예시 2_


```kt
// 싱글톤
@Singleton
class UserStore @Inject constructor() {

    private var name: String = ""

    // 유저 이름을 저장하는 메소드다.
    fun updateName(newName: String) {
        name = newName
    }

    // 현재 저장된 유저 이름을 반환하는 메소드다.
    fun getName(): String {
        return name
    }
}
// 싱글톤 사용
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val userStore: UserStore
) : ViewModel()

@HiltViewModel
class MyPageViewModel @Inject constructor(
    private val userStore: UserStore
) : ViewModel()
```

둘 다 UserStore를 받는다.

#### 1. @Singleton이 없으면:
- HomeViewModel용 UserStore 따로 생성될 수 있음
MyPageViewModel용 UserStore 따로 생성될 수 있음

#### 2.@Singleton이 있으면:
- HomeViewModel → 같은 UserStore 객체
MyPageViewModel → 같은 UserStore 객체
즉, 앱 전체에서 하나를 같이 쓴다.

### _ActivityRetainedComponent + @ActivityRetainedScoped 예시_

- Activity가 화면 회전으로 재생성되어도 유지되는 객체.

1. 쓰면 좋을 때

    - 1. 여러 ViewModel이 같은 객체를 공유해야 한다.

    - 2. 그 객체 안에 유지해야 하는 상태가 있다.

    - 3. 화면 회전 때 그 상태가 초기화되면 안 된다.
    - 4. 하지만 앱 전체 Singleton까지는 필요 없다.

2. 안써도 좋을 때

    - 1. ViewModel 하나에서만 쓴다.
    - 2. 객체 안에 상태가 없다.
    - 3. 다시 만들어져도 문제 없다.
    - 4. 서버, DB, DataStore에서 다시 읽으면 된다.

-> 단, ____실무에서 초기화 되면 안되는 값은 DB에서 꺼내옴____
```kt
@Module
@InstallIn(ActivityRetainedComponent::class)
object SessionModule {

    @Provides
    @ActivityRetainedScoped
    fun provideActivitySession(): ActivitySession {
        return ActivitySession()
    }
}

// 객체가 다시 만들어져도 상관없다
// → 스코프 안 붙임

// ViewModel 하나 안에서만 유지되면 된다
// → @ViewModelScoped

// 같은 Activity 안의 여러 ViewModel이 공유해야 한다
// → @ActivityRetainedScoped
```
이유 : ViewModel은 화면 회전 시에도 유지이기에 ViewModel에서만 쓰인다면 ViewModel에 주입된 것도 살아남음

### ViewModelComponent + @ViewModelScoped

```kt
@Module
@InstallIn(ViewModelComponent::class)
object HomeViewModelModule {

    // HomeViewModel이 살아있는 동안만 유지되는 객체를 제공하는 메소드다.
    @Provides
    @ViewModelScoped
    fun provideHomeUseCase(
        homeRepository: HomeRepository
    ): HomeUseCase {
        return HomeUseCase(homeRepository)
    }
}
```

### FragmentComponent + @FragmentScoped 
- Fragment는 거의 무조건 사용하는 게 아님.
     - _____완전히 Compose 기반_____ 일 시 Fragment가 아닌, Navigation Compose 사용 
    -> ___Views 또는 Views+Compose 혼합___ 이면 __Fragment 기반 Navigation__
- _Fragment 하나가 살아있는 동안 유지되는 객체_

    - Fragment = Activity 안에 들어가는 화면 조각
하지만 단순한 View가 아니라 생명주기를 가진 클래스
    - Fragment = Activity 안에서 갈아끼울 수 있는 작은 화면 컨트롤러

| 구분      | Fragment 방식                                  | Compose 방식                                                            |
| ------- | -------------------------------------------- | --------------------------------------------------------------------- |
| 화면 단위   | `Fragment` 클래스                               | `@Composable` 함수                                                      |
| UI 작성   | XML 또는 View 코드                               | Kotlin 함수                                                             |
| 화면 생성   | `onCreateView()`                             | `setContent {}`                                                       |
| 화면 전환   | `FragmentManager` 또는 Navigation Component    | `NavHost`, `composable()`                                             |
| 상태 관리   | Fragment 내부 변수, ViewModel                    | ViewModel + StateFlow + Compose State                                 |
| 생명주기    | Fragment 자체 생명주기 있음                          | Composable은 함수라 자체 Fragment 생명주기는 없음                                  |
| Hilt 범위 | `FragmentComponent`, `@FragmentScoped` 사용 가능 | 보통 `ViewModelComponent`, `ActivityComponent`, `SingletonComponent` 사용 |

#### ex) ___Activity 하나를 큰 틀로 두고, 그 안의 화면 조각들을 Fragment로 갈아끼우는 구조___

    MainActivity
    ├── HomeFragment
    ├── DetailFragment
    ├── ProfileFragment
    └── SettingFragment

-> _코드예시_

```kotlin
@Module
@InstallIn(FragmentComponent::class)
object FragmentModule {

    @Provides
    @FragmentScoped
    // Fragment 하나가 살아있는 동안 같은 객체를 재사용하게 한다.
    // 같은 Fragment 안에서는 같은 FragmentLogger 인스턴스가 주입된다.
    // 다른 Fragment에서는 다른 FragmentLogger 인스턴스가 만들어진다.
    fun provideFragmentLogger(): FragmentLogger {
        return FragmentLogger()
    }  
}
```

### ServiceComponent + @ServiceScoped

- 사용법
    
    1. 개발자가 Service 클래스를 만든다.
    2. Activity / Compose 화면에서는 Service 객체를 직접 만들지 않는다.
    3. Intent로 "이 Service를 실행해라"라고 Android 시스템에 요청한다.
    4. Android 시스템이 Service 객체를 생성한다.
    5. Service 객체 안에서 내가 만든 메소드를 호출한다.

| 위치                  | 쓰는 것                              |
| ------------------- | --------------------------------- |
| Activity            | `startService()`, `bindService()` |
| Service 클래스         | `@AndroidEntryPoint`, `@Inject`   |
| Service 내부에서 사용할 객체 | `@ServiceScoped`                  |
| Module에서 제공하는 객체    | `@Provides` + `@ServiceScoped`    |


- Service가 살아있는 동안 유지되는 객체
    
    - 사용 예시

    | 사용 상황                    | 예시 객체                           | 이유                                       |
    | ------------------------ | ------------------------------- | ---------------------------------------- |
    | 업로드 / 다운로드 진행 상태         | `UploadSession`                 | Service가 살아있는 동안 진행률 유지                  |
    | 위치 추적                    | `LocationTrackingSession`       | Foreground Service가 켜져 있는 동안 위치 추적 상태 유지 |
    | 음악 재생                    | `MusicPlaybackSession`          | 재생 중인 곡, 재생 상태 유지                        |
    | 타이머 / 운동 기록              | `TimerSession`                  | Service가 살아있는 동안 시간 상태 유지                |
    | Service 전용 알림 관리자        | `ServiceNotificationController` | Service 알림 생성/수정 담당                      |
    | WebSocket / Bluetooth 연결 | `ConnectionSession`             | Service가 연결을 소유할 때 연결 상태 유지              |

-> _예시_ (만든 Service를 사용)

```kotlin
import dagger.hilt.android.scopes.ServiceScoped
import javax.inject.Inject

// 이 클래스는 Service 하나가 살아있는 동안만 같은 객체로 유지된다.
// Service가 종료되면 이 객체도 같이 제거된다.
@ServiceScoped
class UploadSession @Inject constructor() {

    // 현재 업로드가 진행 중인지 저장하는 변수다.
    private var isUploading: Boolean = false

    // 현재 업로드 진행률을 저장하는 변수다.
    private var progress: Int = 0

    // 업로드 세션을 시작하는 메소드다.
    fun startUpload() {
        isUploading = true
        progress = 0
    }

    // 업로드 진행률을 변경하는 메소드다.
    fun updateProgress(newProgress: Int) {
        progress = newProgress
    }

    // 현재 업로드 진행률을 반환하는 메소드다.
    fun getProgress(): Int {
        return progress
    }

    // 현재 업로드 중인지 반환하는 메소드다.
    fun isUploading(): Boolean {
        return isUploading
    }

    // 업로드 세션을 종료하는 메소드다.
    fun stopUpload() {
        isUploading = false
        progress = 0
    }
}
```

- Service는 ___AndroidManifest.xml에 등록___

```kt
<service
    android:name=".service.UploadForegroundService"
    android:exported="false" />
```

### ViewComponent + @ViewModelScoped

- ___ViewModel에 붙히지 않는다.___

#### 중요 개념

___1. ViewModelStoreOwner___

| ViewModelStoreOwner | 의미                                          |
| ------------------- | ------------------------------------------- |
| Activity            | Activity 기준으로 ViewModel 저장                  |
| Fragment            | Fragment 기준으로 ViewModel 저장                  |
| NavBackStackEntry   | Compose Navigation의 특정 화면 기준으로 ViewModel 저장 |

___2. NavBackStackEntry___

| 개념                    | 의미                                        |
| --------------------- | ----------------------------------------- |
| **Back Stack**        | 사용자가 이동한 화면들의 기록 목록                       |
| **NavBackStackEntry** | 그 목록 안에 들어있는 화면 하나의 정보                    |
| **뒤로 가기**             | Back Stack에서 맨 위 화면을 제거하고 이전 화면으로 돌아가는 동작 |

: NavBackStackEntry가 ViewModelStoreOwner가 될 수 있다/

- Navigation back stack에 쌓인 화면 기록 하나

        NavBackStackEntry = 네비게이션 back stack에 들어있는 화면 기록 1개
        ViewModelStoreOwner = ViewModel을 저장할 수 있는 주인
    ->

        NavBackStackEntry는 ViewModelStoreOwner 역할을 할 수 있다.
        하지만 ViewModelStoreOwner가 항상 NavBackStackEntry인 것은 아니다.

| 구분                    | 의미                                        |
| --------------------- | ----------------------------------------- |
| `ViewModelComponent`  | `ViewModel` 하나의 생명주기를 따라가는 Hilt DI 컨테이너   |
| `@ViewModelScoped`    | 같은 `ViewModel` 안에서만 같은 객체를 재사용하게 하는 Scope |
| `@HiltViewModel`      | Hilt가 이 ViewModel을 생성하게 만드는 어노테이션         |
| `@Inject constructor` | Hilt가 생성자에 필요한 객체를 자동으로 넣어주게 하는 문법        |

- 스코프를 쬘 ___뷰모델과 연결___ 하는 법

    - 1. hiltViewModel()
    - 2. ViewModelProvider

-> _예시_

```kotlin
import dagger.hilt.android.scopes.ViewModelScoped
import javax.inject.Inject

// 이 객체는 ViewModelComponent 안에서 한 번 만들어지고 재사용된다.
// 즉, 같은 ViewModel 안에서는 같은 ProjectSession 객체가 사용된다.
@ViewModelScoped
class ProjectSession @Inject constructor() {

    // 현재 ProjectSession 객체가 어떤 객체인지 확인하기 위한 값이다.
    // 같은 객체면 같은 instanceId가 나온다.
    val instanceId: Int = System.identityHashCode(this)

    private var title: String = ""

    // 제목을 저장하는 메소드다.
    fun updateTitle(newTitle: String) {
        title = newTitle
    }

    // 현재 저장된 제목을 반환하는 메소드다.
    fun getTitle(): String {
        return title
    }

    // 제목이 비어 있지 않은지 검사하는 메소드다.
    fun hasValidTitle(): Boolean {
        return title.isNotBlank()
    }
}
```

```kotlin
@ViewModelScoped(ProjectCreateViewModel::class) 
```


대신    

```kotlin
hiltViewModel<ProjectCreateViewModel>()
```

을 호출한 현재 화면 위치를 기준으로 Hilt가 알아서 ProjectCreateViewModel 전용 DI 공간을 만든다.

-> _예시_

: 과정

    1. 화면에서 hiltViewModel<ProjectCreateViewModel>() 요청
    2. HiltViewModelFactory가 ProjectCreateViewModel을 만들 준비
    3. Hilt가 ProjectCreateViewModel 전용 ViewModelComponent 생성
    4. 생성자에 필요한 ProjectSession 확인
    5. ProjectSession에 @ViewModelScoped가 붙어 있으므로 ViewModelComponent 안에 캐싱
    6. ProjectCreateViewModel이 살아있는 동안 같은 ProjectSession 사용
    7. ViewModel이 clear되면 ViewModelComponent도 제거
    8. ProjectSession도 제거

-> 구조

    ProjectCreateViewModel
    ├── ProjectSession(@ViewModelScoped)
    ├── ProjectValidator(스코프 덕분에 ProjectSession 사용)
    └── ProjectSubmitter

---

### @Binds / @Provides

___1. @Binds___ : 인터페이스 구현체 연결할 때 사용(___규칙선언___ 이다, 구현이 아니다.)
    
         Ex) HomeRepository가 필요하면 FakeHomeRepository를 쓰라는 규칙

         // HomeRepository가 필요할 때 어떤 구현체를 쓸지

___2. @Provides___ : 직접 객체를 생성 (___함수 본문에서 직접 객체를 만든다.___)

        Ex) 누가 HomeRepository를 달라고 하면, FakeHomeRepository()를 만들어서 줘야하는 규칙 + 함수 본문에서 직접 객체를 만든다

| 구분     | `@Provides`            | `@Binds`                |
| ------ | ---------------------- | ----------------------- |
| 용도     | 직접 객체를 생성              | 인터페이스와 구현체 연결           |
| 메소드 형태 | 일반 함수                  | abstract(미완성 선언으로 본문 X) 함수             |
| 함수 본문  | 있음                     | 없음                      |
| 예시     | Retrofit, OkHttp, Room | Repository interface 연결 |
\+

    @Binds은 만들어져 있는 걸 사용하게 구현체가 뭔지 알려줌
    @Provides은 직접 만들어줌

- @Provides를 ___object___ 로 만드는 이유

    1. 모듈 자체에 상태가 필요 없어서
    2. object로 하면 Hilt가 모듈 인스턴스를 따로 만들 필요가 적다

- @Binds를 ___abstract___ 로 만드는 이유
    
    1. @Binds 메소드에 본문이 없기 때문이다.


@Binds는 인터페이스를 구현하는 게 아니라,
이미 인터페이스를 구현한 클래스를 Hilt에 등록해서
인터페이스 요청이 들어왔을 때 그 구현체를 넣게 해주는 것이다.


-> _@Binds 예시_

```kt
@Module
@InstallIn(SingletonComponent::class)
abstract class RepositoryModule {

    // HomeRepository 타입이 필요할 때 FakeHomeRepository 구현체를 사용하라고 Hilt에게 알려주는 메소드다.
    @Binds
    abstract fun bindHomeRepository(
        fakeHomeRepository: FakeHomeRepository
    ): HomeRepository
}
```

-> _@Provides 예시_

```kt
@Module
@InstallIn(SingletonComponent::class)
object AppModule {

    // HomeRepository 타입이 필요하면 FakeHomeRepository 객체를 만들어서 넣어준다.
    @Provides
    fun provideHomeRepository(): HomeRepository {
        return FakeHomeRepository()
    }
}
```

차이 : @Binds은 생성코드 재활용 가능(객체 재활용은 @Singleton) / @Provides은 객체 생성

---


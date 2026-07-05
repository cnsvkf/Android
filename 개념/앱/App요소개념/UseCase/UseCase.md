## 쓰는 이유


#### 데이터를 Repository로 가져오고, ___UseCase로 화면/기능 규칙 생성___ 후에 ViewModel에게 넘김

    ViewModel
    ↓
    UseCase        // 기능 규칙, 비즈니스 로직
    ↓
    Repository     // 데이터 출처 관리
    ↓
    API / DB

-> _예시_ 

```kt
// UseCase
class GetRecommendedProjectsUseCase(
    private val projectRepository: ProjectRepository
) {

    // 홈 화면에 보여줄 추천 프로젝트를 가져오는 메소드다.
    suspend operator fun invoke(): List<Project> {
        return projectRepository.getProjects()
            .filter { project -> project.isRecruiting }  // 모집 중인 것만 보여준다.
            .take(5)                                     // 최대 5개만 보여준다.
    }
}
```

    UseCase 실행
    ↓
    결과를 UiState로 변경

---

## 쓰면 좋은 점
### 사용 X
```kt
UseCase 없을 때
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val projectRepository: ProjectRepository,
    private val userRepository: UserRepository,
    private val notificationRepository: NotificationRepository
) : ViewModel() {

    private val _uiState = MutableStateFlow(HomeUiState())
    val uiState = _uiState.asStateFlow()

    // 홈 화면에 필요한 데이터를 불러오는 메소드다.
    fun loadHome() {
        viewModelScope.launch {
            val user = userRepository.getUser()
            val projects = projectRepository.getProjects()
            val notifications = notificationRepository.getNotifications()

            val recommendedProjects = projects
                .filter { project -> project.isRecruiting }
                .filter { project -> project.memberCount < project.maxMemberCount }
                .take(5)

            val recentNotifications = notifications.take(2)

            _uiState.value = HomeUiState(
                userName = user.name,
                recommendedProjects = recommendedProjects.map { it.toUiModel() },
                notifications = recentNotifications.map { it.toUiModel() }
            )
        }
    }
}
```

ViewModel 책임:
- 사용자 이벤트 처리
- 로딩 상태 관리
- Repository 호출
- 데이터 필터링
- 데이터 개수 제한
- 여러 Repository 조합
- UiState 변환
ViewModel이 커지면 나중에 유지보수하기 힘들어짐.

### 사용
```kt
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val getHomeDashboardUseCase: GetHomeDashboardUseCase
) : ViewModel() {

    private val _uiState = MutableStateFlow(HomeUiState())
    val uiState = _uiState.asStateFlow()

    // 홈 화면 데이터를 불러와 UiState로 변경하는 메소드다.
    fun loadHome() {
        viewModelScope.launch {
            val dashboard = getHomeDashboardUseCase()

            _uiState.value = dashboard.toUiState()
        }
    }
}
```
: ViewModel 안에 있던 ___로직 파일이 UseCase로 이동___


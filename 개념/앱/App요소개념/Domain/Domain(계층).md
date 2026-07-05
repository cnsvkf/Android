## 개념

- 앱이 실제 중요한 __"규칙"__ 을 모아두는 영역

    - 화면 구성 코드 or 서버 요청 코드 아님


| 구분                  | 의미                          |
| ------------------- | --------------------------- |
| `Domain`            | 앱의 핵심 규칙을 담는 계층             |
| `UseCase`           | Domain 안에서 하나의 기능을 담당하는 클래스 |
| `Domain Model`      | 앱 내부 핵심 데이터                 |
| `Domain Repository` | 데이터 기능에 대한 약속               |
| `Data Repository`   | 실제 서버/DB 접근 구현체             |


- ____파일 위치____

        ui/
        └── login/
            ├── LoginScreen.kt
            └── LoginViewModel.kt

        domain/
        ├── model/
        │    └── User.kt
        ├── repository/
        │    └── AuthRepository.kt
        └── usecase/
            └── LoginUseCase.kt

        data/
        ├── remote/
        │    └── AuthApiService.kt
        ├── dto/
        │    └── LoginResponseDto.kt
        └── repository/
            └── AuthRepositoryImpl.kt

## 쓰는 이유

- 로그인 상황을 예시로 로그인 시 필요한 구조

    1. 아이디가 비어 있는지 확인

    2. 비밀번호가 비어 있는지 확인
    3. Repository에 로그인 요청
    4. 성공하면 사용자 정보 반환
    5. 실패하면 에러 처리

-> 이걸 ViewModel에 넣을 시 ViewModel 복잡해짐

-> Domain 활용 : 

    Screen
    ↓
    ViewModel
    ↓
    Domain
    ↓
    Repository
    ↓
    Retrofit / DataStore / Room
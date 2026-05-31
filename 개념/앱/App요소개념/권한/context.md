## 개념

Context = 앱/화면이 안드로이드 시스템,리소스, 저장소에 접근할 때 사용하는 ___"연결통로" + "현재 앱 환경 정보"___

- 어느 환경을 관리해야할 지 적어야함

## 예시

```kotlin
val Context.dataStore by preferencesDataStore(name = "token_store")
```

: 의미

```kotlin
// 이 앱의 내부 저장소 안에 token_store라는 DataStore를 만들어서 사용한다
context.dataStore
```
\+

__preferencesDataStore__ 로 만든 프로퍼티는 Kotlin 파일의 _____최상단에 한 번 선언하고, 앱 전체에서 그 프로퍼티로 접근_____ 하라고 설명합니다. 이렇게 하면 DataStore를 singleton처럼 사용

Context를 가진 곳에서 .dataStore라고 부르면, 그 Context가 속한 앱의 내부 저장소에서 token_store라는 DataStore를 꺼내 쓰게 하는 구조

## 종류

| Context 종류            | 의미                | 주 사용 위치                          |
| --------------------- | ----------------- | -------------------------------- |
| `Application Context` | 앱 전체 환경           | DataStore, Repository, Singleton |
| `Activity Context`    | 현재 Activity 화면 환경 | Toast, Intent, 화면 관련 기능          |
| `ContextThemeWrapper` | 테마가 감싸진 Context   | Compose/MaterialTheme 내부에서 자주 보임 |

-> ContextThemeWrapper : 기존 Context를 감싸서, 테마 정보까지 추가하거나 바꿔주는 Context

---

## 주의사항

| 위치                  |                  `Context` 사용 가능? | 설명                            |
| ------------------- | --------------------------------: | ----------------------------- |
| `@Composable` 내부    |                                가능 | `LocalContext.current`로 잠깐 사용 |
| Button `onClick` 내부 |                                가능 | 상위에서 가져온 context 사용 가능        |
| `Route` Composable  |                                권장 | Toast, Intent, 화면 이동 처리하기 좋음  |
| `Screen` Composable |                        가능하지만 덜 권장 | UI와 Android 기능이 섞일 수 있음       |
| ViewModel           |        직접 Activity Context 저장 비추천 | 필요하면 `ApplicationContext` 사용  |
| Repository          | Hilt로 `@ApplicationContext` 주입 권장 | DataStore 등에 사용               |
| `object` 싱글톤        |            Activity Context 저장 금지 | 메모리 누수 위험                     |


\+ 

context 어노테이션을 사용할 때에는 “Context 자체를 어노테이션으로 만드는 것”이 아니라, ___어떤 종류의 Context를 주입받을지 어노테이션으로 표시___
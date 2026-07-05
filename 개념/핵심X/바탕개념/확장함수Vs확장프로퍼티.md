| 구분      | 확장함수                      | 확장 프로퍼티                  |
| ------- | ------------------------- | ------------------------ |
| 선언 키워드  | `fun`                     | `val` / `var`            |
| 사용 형태   | `context.doSomething()`   | `context.someValue`      |
| `()` 사용 | 있음                        | 없음                       |
| 목적      | 동작, 기능 실행                 | 값처럼 접근                   |
| 파라미터    | 받을 수 있음                   | 일반적으로 받을 수 없음            |
| 예시      | `Context.showToast("안녕")` | `Context.tokenDataStore` |

## 예시

1. ### 확장함수
```kotlin
fun Context.showToast(message: String)
```
사용:
```kotlin
context.showToast("안녕")
```
뜻:
 context를 이용해서 showToast라는 기능을 ___실행___ 한다.

 2. ### 확장프로퍼티
```kotlin
val Context.tokenDataStore
```
사용:
```kotlin
context.tokenDataStore
```

뜻:
// context를 이용해서 tokenDataStore라는 값에 ___접근___ 한다.
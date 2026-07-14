## 메모리 누수 예시

```kotlin
object BadContextHolder {
    var context: Context? = null
}

class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        BadContextHolder.context = this
    }
}
```

: object BadContextHolder가 앱 실행 중 계속 살아있을 수 있다는 점이다. 그러면 Activity가 종료되어도 BadContextHolder가 Activity를 붙잡고 있어서 GC가 회수하지 못할 수 있다.

#### -> @ActivityScoped 활용

- 스코프가 맞아도 누수가 생길 수 있다.



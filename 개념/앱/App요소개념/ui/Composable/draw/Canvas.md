- 그림을 그릴 공간을 만드는 Composable

    Canvas 생성
    ↓
    Canvas가 DrawScope 제공
    ↓
    DrawScope 안에서 drawArc(), drawCircle() 사용

#### - 그림 자체가 화면의 주된 UI일 때 사용.


    원형 그래프

    로딩 인디케이터

    직접 만든 체크박스

    선, 원, 호, 도형

    커스텀 차트

    시계

    프로그레스 바


->

```kotlin
Canvas(
    modifier = Modifier.size(200.dp),
) {
    drawCircle(
        color = Color.Blue,
    )
}
```

---

### 기본 구조

```kotlin
@Composable
fun Canvas(
    modifier: Modifier,
    onDraw: DrawScope.() -> Unit,
)
```

#### -> Canvas의 중괄호 안은 DrawScope 범위

\+

    onDraw       : 매개변수 이름
    DrawScope.   : 이 함수 내부의 this가 DrawScope
    ()           : 외부에서 전달받는 일반 매개변수 없음
    -> Unit      : 실행 후 반환값 없음

---

### 예시

```kotlin
@Composable
fun CircleCanvas(
    modifier: Modifier = Modifier,
) {
    Canvas(
        modifier = modifier.size(200.dp),
    ) {
        // Canvas가 차지하는 영역의 중앙 좌표
        val centerX = size.width / 2f
        val centerY = size.height / 2f

        // Canvas 영역 안에 원을 그린다.

        // 후행람다로 onDraw을 뺐다.
        drawCircle(
            color = Color.Blue,
            radius = 60.dp.toPx(),
            center = Offset(
                x = centerX,
                y = centerY,
            ),
        )
    }
}
```

람다 내부에는 보이지 않는 DrawScope 타입의 this가 존재


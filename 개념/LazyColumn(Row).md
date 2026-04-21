## 개념

- Column / Row 만을 쓸 시 데이터를 전부 출력해 버퍼링 등 걸림

-> LazyColumn(Row) : Lazy(게으르게)하게 화면에 출력되는 데이터만 출력함

---
---

### 기본 형식

```kotlin
LazyColumn {
    item {
        Text("첫 번째 한 칸")
    }

    items(10) { index ->
        Text("아이템 $index")
    }
}
```

- item : 화면에 출력되는 요소

- itmes() { } : ()만큼 반복해서 람다를 씀
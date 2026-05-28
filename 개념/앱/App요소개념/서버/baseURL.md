## 위치

```kotlin
android {
    buildTypes {
        debug {
            buildConfigField(
                "String",
                "BASE_URL",
                "\"https://dev-api.example.com/\""
            )
        }

        release {
            buildConfigField(
                "String",
                "BASE_URL",
                "\"https://api.example.com/\""
            )
        }
    }
}
```

-> 선택하는 법

    Android Studio에서 직접 확인하는 곳
    왼쪽 아래 또는 오른쪽 사이드에 Build Variants 창이 있다.
    거기서 모듈별로 선택할 수 있다.
    app     debug

    또는

    app     release
    
    여기서 debug로 되어 있으면 개발 서버를 사용한다.
    release로 바꾸면 운영 서버를 사용한다.
## 권한 등록 방법

### xml에서 권한 등록

-> 예시

```kotlin
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
<uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
```

:

    이 앱은 인터넷을 씁니다.
    이 앱은 카메라를 씁니다.
    이 앱은 알림 권한을 씁니다.
    이 앱의 시작 Activity는 MainActivity입니다.
    이 앱은 FCM Service를 사용합니다.
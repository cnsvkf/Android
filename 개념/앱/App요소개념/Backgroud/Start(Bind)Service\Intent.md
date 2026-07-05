## 개념
1. Service는 Activity처럼 ___화면을 만드는 객체가 아니다.___

2. Service는 앱 화면이 없어도 일정 작업을 실행하기 위해 Android 시스템이 관리하는 ___컴포넌트___


| 구분           | `startService()`                | `bindService()`         |
| ------------ | ------------------------------- | ----------------------- |
| 목적           | Service에게 작업 명령                 | Service와 연결             |
| 사용 방식        | `Intent.action`, `extra`로 명령 전달 | Binder로 Service 객체 접근   |
| Service 생명주기 | 화면이 사라져도 계속 실행 가능               | 연결한 화면이 사라지면 보통 종료      |
| 호출되는 메소드     | `onStartCommand()`              | `onBind()`              |
| 메소드 직접 호출    | 불가능                             | 가능                      |
| 예시           | 업로드 시작, 음악 재생 시작, 백그라운드 작업      | 업로드 진행률 확인, 음악 현재 상태 제어 |

-> __말로 풀어서 설명__

    startService()
    = Intent로 Android 시스템에게
    “이 Service 실행해서 이 명령 처리하게 해줘”라고 시키는 방식

    bindService()
    = Intent로 Android 시스템에게
    “이 Activity와 이 Service를 연결해줘”라고 시키는 방식
    + 
    Android가 관리하는 것
    = Service 생성, 연결, 해제, 생명주기

    Android가 직접 관리하지 않는 것
    = 연결 후 Activity가 Service 메소드를 직접 호출하는 부분

| 상황 예시                   | 담당                                |
| ---------------------- | --------------------------------- |
| 음악을 계속 재생              | `startForegroundService()`        |
| 알림에 재생 상태 표시           | `Foreground Service Notification` |
| 화면에서 Service 메소드 직접 호출 | `bindService()`                   |
| 화면 닫혀도 계속 실행           | started 상태가 유지                    |
| 화면과 연결만 담당             | bound 상태                          |




-> _구조 (startService)_

    Activity / ViewModel / Receiver
            ↓
    Intent 생성
            ↓
    context.startService() 또는 context.startForegroundService()
            ↓
    Android 시스템이 Service 객체 생성
            ↓
    Service의 onCreate()
            ↓
    Service의 onStartCommand()
            ↓
    Service 내부에서 실제 작업 실행
            ↓
    작업 끝나면 stopSelf()
            ↓
    onDestroy()

-> _구조 2(bindService)_

    Activity / Fragment / Compose Route
            ↓
    Intent 생성
            ↓
    context.bindService(intent, connection, Context.BIND_AUTO_CREATE)
            ↓
    Android 시스템이 Service 객체 생성
            ↓
    Service의 onCreate()
            ↓
    Service의 onBind()
            ↓
    Service가 Binder 반환
            ↓
    Activity의 onServiceConnected()
            ↓
    Binder로 Service 객체 받음
            ↓
    Activity에서 service.play(), service.pause(), service.getProgress() 직접 호출
            ↓
    화면 사라짐
            ↓
    unbindService(connection)
            ↓
    연결한 곳이 없으면 onUnbind()
            ↓
    startService로 시작된 상태가 아니면 onDestroy()
## 만드는 법
- Service는 그냥 클래스만 만들면 안 된다.

    - Android 시스템이 찾을 수 있게 ___Manifest에 등록___ 해야 한다.

```kt
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- Foreground Service를 사용할 때 필요한 기본 권한이다. -->
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

    <!-- Android 14 이상에서 dataSync 타입의 Foreground Service를 쓸 때 필요한 권한이다. -->
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE_DATA_SYNC" />

    <!-- Android 13 이상에서 알림 권한 요청이 필요할 수 있다. -->
    <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />

    <application>

        <!--
            UploadForegroundService를 Android 시스템에 등록한다.

            android:name:
            - 실제 Service 클래스 경로다.

            android:exported="false":
            - 다른 앱에서 이 Service를 실행하지 못하게 막는다.
            - 보통 앱 내부 Service는 false로 둔다.

            android:foregroundServiceType="dataSync":
            - 이 Service가 어떤 종류의 Foreground Service인지 선언한다.
            - 업로드/다운로드/동기화 작업이면 dataSync를 사용한다.
        -->
        <service
            android:name=".upload.UploadForegroundService"
            android:exported="false"
            android:foregroundServiceType="dataSync" />

    </application>
</manifest>
```

: 
- dataSync 타입

    1. ___사용자가 앱을 직접 보지 않는___ 상태에서도 __기기 상단 알림창에 진행 상태를 표시__ 하며 안정적으로 데이터를 주고받음 + __실행 시간에 제한__
    2. 대용량 파일 다운로드, 사진 클라우드 백업 등 ___데이터를 동기화하는 중___ 임을 보여주는 안내용 창 __(작업이 끝나면 자동으로 사라집니다.)__

### -> _코드 예시_ (startService)

```kt
// Service
class UploadService : Service() {

    companion object {
        // Service에게 업로드 시작 작업을 시키기 위한 action 값이다.
        const val ACTION_START_UPLOAD = "ACTION_START_UPLOAD"

        // Service에게 업로드 중지 작업을 시키기 위한 action 값이다.
        const val ACTION_STOP_UPLOAD = "ACTION_STOP_UPLOAD"

        // Intent extra에서 파일 ID를 꺼낼 때 사용할 key 값이다.
        const val EXTRA_FILE_ID = "EXTRA_FILE_ID"
    }

    // Service가 처음 생성될 때 한 번 호출되는 메소드다.
    // Activity의 onCreate()처럼 초기 설정을 하는 곳이다.
    override fun onCreate() {
        super.onCreate()

        // 예: 알림 채널 생성, 초기 객체 준비, 로그 출력 등을 여기서 할 수 있다.
    }

    // startService(intent) 또는 startForegroundService(intent)를 호출하면 실행되는 메소드다.
    // 여기서 Intent의 action과 extra를 읽고 실제 작업을 분기한다.
    override fun onStartCommand(
        intent: Intent?,
        flags: Int,
        startId: Int
    ): Int {

        // Intent가 null이면 처리할 명령이 없으므로 Service를 재시작하지 않도록 설정한다.
        if (intent == null) {
            return START_NOT_STICKY
        }

        // Intent의 action 값에 따라 Service가 해야 할 작업을 나눈다.
        when (intent.action) {

            ACTION_START_UPLOAD -> {
                // Intent에서 파일 ID를 꺼낸다.
                val fileId = intent.getStringExtra(EXTRA_FILE_ID)

                // 파일 ID가 null이 아니면 업로드를 시작한다.
                if (fileId != null) {
                    startUpload(fileId)
                }
            }

            ACTION_STOP_UPLOAD -> {
                // 업로드 중지 작업을 실행한다.
                stopUpload()
            }
        }

        // Service가 강제로 종료되어도 Intent를 다시 전달하지 않는다.
        // 일반적으로 명령형 작업에는 START_NOT_STICKY를 많이 사용한다.
        return START_NOT_STICKY
    }

    // bindService() 방식으로 연결할 때 호출되는 메소드다.
    // 지금 예시는 startService 방식만 사용하므로 null을 반환한다.
    override fun onBind(intent: Intent?): IBinder? {
        return null
    }

    // 실제 업로드 작업을 시작하는 메소드다.
    private fun startUpload(fileId: String) {
        // 여기서 Coroutine, Repository, UploadManager 등을 사용해서 업로드를 시작할 수 있다.
        println("업로드 시작: $fileId")
    }

    // 현재 업로드 작업을 중지하는 메소드다.
    private fun stopUpload() {
        // 여기서 업로드 취소, 리소스 정리 등을 처리할 수 있다.
        println("업로드 중지")
    }

    // Service가 종료될 때 호출되는 메소드다.
    // 사용하던 리소스, Coroutine, Listener 등을 정리하는 곳이다.
    override fun onDestroy() {
        super.onDestroy()

        // 예: 업로드 작업 취소, 알림 제거, 리소스 해제
    }
}


// Intent 연결

//"UploadService를 실행하고 싶다"라는 요청 객체 생성
val intent = Intent(context, UploadService::class.java).apply {
    // 이 Service에게 "업로드 시작" 작업을 하라고 명령한다.
    action = UploadService.ACTION_START_UPLOAD  

    // Service가 업로드할 파일 ID를 받을 수 있도록 데이터를 넣는다.
    putExtra(UploadService.EXTRA_FILE_ID, "file_123")
}

context.startService(intent)
```

### ___-> 코드 예시(bindService)___

```kt
class MusicService : Service() {

    // Activity에게 넘겨줄 Binder 객체다.
    // Activity는 이 Binder를 통해 MusicService 객체를 가져올 수 있다.
    private val musicBinder = MusicBinder()

    // 현재 음악 재생 상태를 저장하는 변수다.
    private var isPlaying: Boolean = false

    // 현재 재생 중인 음악 제목을 저장하는 변수다.
    private var currentMusicTitle: String = "선택된 음악 없음"

    // Activity와 MusicService를 연결해주는 Binder 클래스다.
    // Binder는 Activity가 Service 안의 메소드에 접근할 수 있게 해주는 중간 통로다.
    inner class MusicBinder : Binder() {

        // Activity가 실제 MusicService 객체를 가져갈 수 있게 해주는 메소드다.
        // this@MusicService는 "현재 MusicService 객체"를 의미한다.
        fun getService(): MusicService {
            return this@MusicService
        }
    }

    // Service가 처음 생성될 때 한 번 호출되는 메소드다.
    // Activity의 onCreate()와 비슷하게 초기 설정을 하는 곳이다.
    override fun onCreate() {
        super.onCreate()

        // 예:
        // MediaPlayer 생성
        // 음악 관련 객체 준비
        // 로그 출력
    }

    // Activity가 bindService()를 호출하면 실행되는 메소드다.
    // 여기서 반환한 IBinder가 Activity의 onServiceConnected()로 전달된다.
    override fun onBind(intent: Intent?): IBinder {
        return musicBinder
    }

    // 모든 Activity가 unbindService()로 연결을 끊었을 때 호출될 수 있는 메소드다.
    // true를 반환하면 나중에 재연결될 때 onRebind()가 호출될 수 있다.
    // false를 반환하면 일반적으로 다음 연결 때 다시 onBind() 흐름으로 간다.
    override fun onUnbind(intent: Intent?): Boolean {
        return super.onUnbind(intent)
    }

    // Service가 완전히 종료될 때 호출되는 메소드다.
    // bindService()만 사용한 Service는 연결된 Activity가 없으면 종료될 수 있다.
    override fun onDestroy() {
        super.onDestroy()

        // 예:
        // MediaPlayer 해제
        // 리소스 정리
    }

    // 음악을 재생하는 메소드다.
    // Activity가 Service에 연결된 뒤 호출할 수 있다.
    fun playMusic(title: String) {
        currentMusicTitle = title
        isPlaying = true
    }

    // 음악을 일시정지하는 메소드다.
    // Activity가 Service에 연결된 뒤 호출할 수 있다.
    fun pauseMusic() {
        isPlaying = false
    }

    // 음악을 정지하는 메소드다.
    // Activity가 Service에 연결된 뒤 호출할 수 있다.
    fun stopMusic() {
        isPlaying = false
        currentMusicTitle = "선택된 음악 없음"
    }

    // 현재 음악이 재생 중인지 반환하는 메소드다.
    // Activity가 Service의 현재 상태를 확인할 때 사용한다.
    fun isMusicPlaying(): Boolean {
        return isPlaying
    }

    // 현재 재생 중인 음악 제목을 반환하는 메소드다.
    // Activity가 Service의 현재 데이터를 가져올 때 사용한다.
    fun getCurrentMusicTitle(): String {
        return currentMusicTitle
    }
}
```

#### Service Method
| 메소드                | 호출 시점                            | 역할                   |
| ------------------ | -------------------------------- | -------------------- |
| `onCreate()`       | Service가 처음 만들어질 때               | 초기 설정                |
| `onBind(intent)`   | Activity가 `bindService()` 호출했을 때 | Activity에게 Binder 반환 |
| `onUnbind(intent)` | 모든 연결이 끊겼을 때                     | 연결 해제 처리             |
| `onDestroy()`      | Service가 종료될 때                   | 리소스 정리               |
| `playMusic()`      | Activity가 직접 호출                  | 음악 재생                |
| `pauseMusic()`     | Activity가 직접 호출                  | 음악 일시정지              |
| `stopMusic()`      | Activity가 직접 호출                  | 음악 정지                |

#### Activity Method

| 메소드                       | 역할                          |
| ------------------------- | --------------------------- |
| `bindMusicService()`      | Service와 연결                 |
| `unbindMusicService()`    | Service와 연결 해제              |
| `onServiceConnected()`    | Service 연결 성공 시 실행          |
| `onServiceDisconnected()` | Service 비정상 연결 해제 시 실행      |
| `playMusic()`             | Service의 `playMusic()` 호출   |
| `pauseMusic()`            | Service의 `pauseMusic()` 호출  |
| `stopMusic()`             | Service의 `stopMusic()` 호출   |
| `onDestroy()`             | Activity 종료 시 Service 연결 해제 |

\+

return this@MusicService 쓰는 이유 : __inner class__ 이기에 this 시에 가까운 MusicBinder 반환해서 @MusicService 사용

=> qualified this 문법

## Intent 

사용하는 이유 : ____Android가 직접 관리하는 컴포넌트____ 이기에 Intent로 Android에게 요청

    Android가 관리하는 것

    Service 생성
    Service 실행
    Service 종료
    앱 프로세스 관리
    백그라운드 제한 관리
    권한 관리
    Manifest 등록 확인

- Service에게 보내는 실행 요청서

| 항목       | 역할                      | 예시                        |
| -------- | ----------------------- | ------------------------- |
| `action` | Service에게 어떤 작업을 할지 알려줌 | `"START_UPLOAD"`          |
| `extra`  | 작업에 필요한 데이터 전달          | `fileId`, `userId`, `url` |

-> _기본 사용 흐름_

```kt
// 1. UploadService를 실행하기 위한 Intent 객체를 만든다.
val intent = Intent(context, UploadService::class.java)

// 2. 이 Intent가 어떤 작업을 요청하는지 action으로 설정한다.
intent.action = UploadService.ACTION_START_UPLOAD

// 3. Service에게 전달할 데이터를 extra로 넣는다.
intent.putExtra(UploadService.EXTRA_FILE_NAME, "profile.png")

// 4. Android 시스템에게 이 Intent로 Service를 실행하라고 요청한다.
context.startService(intent)
```

#### __잘못된 사용법__

```kt
val service = UploadService()
service.startUpload()
```

#### __올바른 사용법__ + ___코드 단축법(apply로)___
```kt
val intent = Intent(context, UploadService::class.java)
context.startService(intent)
```


    Activity / Composable
    ↓
    Intent 생성
    ↓
    Android OS에게 Service 실행 요청
    ↓
    OS가 Service 객체를 생성하거나 기존 Service 재사용
    ↓
    Service의 onStartCommand(intent, flags, startId) 호출
    ↓
    Service가 Intent 안의 action / extra 값을 보고 작업 실행
## 활용하는 곳

| 종류                 | 사용하는 상황                           | 직접 메소드 호출 가능? | 예시                     |
| ------------------ | --------------------------------- | ------------: | ---------------------- |
| Started Service    | 명령만 보내고 백그라운드에서 작업                |             X | 파일 업로드, 동기화            |
| Foreground Service | 사용자가 알아야 하는 오래 걸리는 작업             |             X | 음악 재생, 위치 추적, 업로드 진행   |
| Bound Service      | Activity가 Service에 연결해서 메소드 직접 호출 |             O | 음악 플레이어 컨트롤, BLE 연결 제어 |

    Compose UI
    ↓
    ViewModel
    ↓
    Repository
    ↓
    Retrofit ApiService
    ↓
    백엔드 서버
    ↓
    AI API 서버
    예: OpenAI API, Gemini API, Claude API 등

---

### AI API 직접 호출 안하는 이유

1. API key 노출

    -> Android 앱 안에 API Key를 넣으면 APK를 뜯어서 키를 볼 수 있다.
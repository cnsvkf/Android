## 개념

- 로그인 토큰을 저장하고, refreshToken으로 토큰을 갱신하는 구조


-> 예시

    로그인 성공
    → accessToken 저장
    → refreshToken 저장
    → 서버 요청할 때 accessToken 붙임
    → accessToken 만료
    → refreshToken으로 새 accessToken 발급
    → 다시 요청
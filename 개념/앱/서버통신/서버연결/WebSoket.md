
### https Vs WebSocket Vs TcpSocket

- https : 클라이언트 요청보내고 서버가 응답 보낸 후 끝 (request-response 구조)

- WebSocket : 서버와 한 번 연결을 맺은 뒤 연결 유지 (full-duplex 구조)
\+ 자동으로 TcpSocket에서 설정하는 해야하는 것을 설정해줌

-> OkHttp로 구현

->

    OkHttp = HTTP/HTTPS도 하고, WebSocket(ws/wss)도 할 수 있는 네트워크 엔진
    Retrofit = HTTP/HTTPS REST API를 편하게 쓰기 위한 도구

- TcpSocket : 아래를 일일히 처리
    - 메시지 경계 구분
    - 텍스트/바이너리 프로토콜 정의
    - 재연결 정책
    - heartbeat
    - 인증 처리
    - 브라우저/프록시/로드밸런서 친화성

---

## 설정하는 법


```
1. 클라이언트가 HTTP 방식으로 접속 시도

2. Upgrade: websocket

3. 서버가 101 Switching Protocols

4. 이후부터는 같은 TCP 연결 위에서 WebSocket 메시지 교환
```

---
---
Retrofit = API 함수 만들기 편하게 해주는 도구
OkHttp = 실제 네트워크 요청을 보내는 엔진

    OkHttpClient
    ├── HTTP/HTTPS 요청 보내기
    ├── WebSocket 연결 만들기
    ├── Interceptor로 요청/응답 중간 처리하기
    ├── timeout 설정
    ├── connection pool 관리
    ├── cache 처리
    └── retry, redirect 처리
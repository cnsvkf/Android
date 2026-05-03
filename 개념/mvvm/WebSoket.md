
### https Vs WebSocket Vs TcpSocket

- https : 클라이언트 요청보내고 서버가 응답 보낸 후 끝 (request-response 구조)

- WebSocket : 서버와 한 번 연결을 맺은 뒤 연결 유지 (full-duplex 구조)
\+ 자동으로 TcpSocket에서 설정하는 해야하는 것을 설정해줌

- TcpSocket : 아래를 일일히 처리
    - 메시지 경계 구분
    - 텍스트/바이너리 프로토콜 정의
    - 재연결 정책
    - heartbeat
    - 인증 처리
    - 브라우저/프록시/로드밸런서 친화성

---
---
### 설정하는 법


```
1. 클라이언트가 HTTP 방식으로 접속 시도

2. Upgrade: websocket

3. 서버가 101 Switching Protocols

4. 이후부터는 같은 TCP 연결 위에서 WebSocket 메시지 교환
```
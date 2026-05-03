# 구조
## 명세서 항목

| 명세서 항목              | Android에서 쓰는 곳                    |
| ------------------- | --------------------------------- |
| WebSocket URL       | `Request.Builder().url(...)`      |
| Header              | `addHeader("Authorization", ...)` |
| Client → Server 메시지 | 보내는 DTO                           |
| Server → Client 메시지 | 받는 DTO                            |
| 메시지 type            | `when(type)` 분기                   |
| 연결 성공               | `onOpen()`                        |
| 서버 메시지 수신           | `onMessage()`                     |
| 연결 실패               | `onFailure()`                     |
| 연결 종료               | `onClosing()`, `onClosed()`       |

---
## 파일 구조

    data/
    remote/
        socket/
        OkHttpClientProvider.kt
        WebSocketServiceFactory.kt

        dto/
            JoinRoomSocketRequestDto.kt
            SendChatSocketRequestDto.kt
            SocketTypeDto.kt
            ConnectedSocketResponseDto.kt
            NewMessageSocketResponseDto.kt
            ErrorSocketResponseDto.kt

        service/
            ChatWebSocketService.kt

        model/
            ChatSocketEvent.kt

    repository/
        ChatRepository.kt

    presentation/
    chat/
        ChatViewModel.kt
        ChatUiState.kt
        ChatMessageUiModel.kt
        ChatScreen.kt
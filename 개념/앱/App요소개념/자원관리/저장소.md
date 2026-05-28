| 목적         | 예시                           | 주로 쓰는 기술                          |
| ---------- | ---------------------------- | --------------------------------- |
| 로그인 유지     | accessToken, refreshToken 저장 | DataStore                         |
| 사용자 설정 저장  | 다크모드, 알림 설정, 자동 로그인 여부       | DataStore                         |
| 서버 데이터 캐싱  | 게시글 목록, 유저 정보, 검색 결과         | Room                              |
| 파일 저장      | 이미지, 음성, PDF, 임시 파일          | File, MediaStore                  |
| 오프라인 지원    | 인터넷 없어도 이전 데이터 보여주기          | Room + Repository                 |
| 앱 재실행 후 복구 | 앱 꺼졌다 켜져도 데이터 유지             | DataStore, Room, SavedStateHandle |

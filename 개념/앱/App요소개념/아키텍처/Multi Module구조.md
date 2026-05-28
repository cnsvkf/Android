## 개념

 - 기능별/역할별로 폴더보다 더 강하게 분리하는 구조

 ## 예시

 ### 기본 

 ->

    app/
    ┣ ui/
    ┣ data/
    ┣ viewmodel/
    ┗ MainActivity.kt

 ### Multi Module 구조

 -> 

    feature/login
    feature/main
    feature/qrcode
    core/network
    core/data
    core/domain
    core/design-system

: 

    login 기능은 login 모듈에
    qrcode 기능은 qrcode 모듈에
    공통 버튼/색상은 design-system 모듈에
    서버 통신은 network 모듈에
    repository는 data 모듈에
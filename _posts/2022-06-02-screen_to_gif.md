---
toc: true
layout: post
title: screen to gif(짤 생성)
description: gif로 움짤을 생성하는 프로그램
categories: [configuration, windows, git, screentogif, 짤]

image: "images/posts/config.png"

---
1. `screen to gif`를 검색해서 프로그램을 다운 받는다.
    ![20220602162115](https://raw.githubusercontent.com/is2js/screenshots/main/20220602162115.png)

2. 설정
    1. 프로그램: 
        ![20220603222454](https://raw.githubusercontent.com/is2js/screenshots/main/20220603222454.png)
        - `수동으로 시작` + `복수의 프로그램 허용`
        - 처음화면: `화면녹화기`
        - 색 설정: `중간`
        - `모든 창이 닫혀 있어도 프로그램을 종료하지 않습니다.`
        - `트레이 > 종료` 체크해제
        - `업데이트 확인` 2개 체크해제
    2. 녹화기:
        ![20220604120556](https://raw.githubusercontent.com/is2js/screenshots/main/20220604120556.png)
        - `새 버전` 체크
        - 녹화모드: ~~BitBit -> `DirectX` 체크~~ -> **`BitBit` 체크유지**(DirectX 체크시 에러남)
        - 기타: `이전 녹화구역 저장` 체크해제
        - 기타: `Ask me before discarding the recoding` 체크해제
            - 이것을 체크해제해야 F7 -> F9(취소)시 종료메세지 안물어본다.
            ![20220614110232](https://raw.githubusercontent.com/is2js/screenshots/main/20220614110232.png)
    3. 편집기:
        ![20220603222708](https://raw.githubusercontent.com/is2js/screenshots/main/20220603222708.png)
        - 색 설정: `중간`
        - 일반: `확인하기 3종` 체크 해제
        - `인코딩을 별도 창에 표시합니다` 체크
            ![20220613213332](https://raw.githubusercontent.com/is2js/screenshots/main/20220613213332.png)
    4. **기타:**
        ![20220604120741](https://raw.githubusercontent.com/is2js/screenshots/main/20220604120741.png)
        - `FFmpeg 다운로드`후 경로 지정: 동영상 > screentogif폴더생성 > 저장
    5. 단축키:
        ![20220603222814](https://raw.githubusercontent.com/is2js/screenshots/main/20220603222814.png)
        - `화면 녹화기`:**`ctrl+alt+shift + F12`**
            - **이 설정을 해주고 직전 편집기(캡처 후 저장하는 창)상태에서 하나 더 켜놔야, (편집기 종료 -> 프로그램 종료) 추가로 gif촬영을 지속할 수 있다.**
            - 만약 편집기를 [닫기]하면, 프로그램 자체도 종료되어버린다. **만약, 녹화기가 추가로 열리면, 편집기 닫아도 프로그램 안꺼진다.**
            - 편집기 상태에서 다음 촬영할거면, 하나 더 켜두자.
        - `종료`: **`ctrl+alt+shift + F4`**
        - `녹화 취소`  > **`F9`**
            - **녹화 취소할 때, 중지(F8) -> 취소(F9) 연타 치면 된다.**
    5. 편집기-저장탭(첫 녹화 종료F7->F8시 설정 가능)
        ![20220604120929](https://raw.githubusercontent.com/is2js/screenshots/main/20220604120929.png)
        - **파일종류: 2번째를 screentogif -> `FFmpeg - high qualyity` 선택**
        - ~~`중복 픽셀 감지` 체크해제~~
        - `지정 경로에 파일 저장하기` 체크해제 
        - **`클립보드에 복사하기` 체크 > `파일` 선택**
        - **세팅이 끝난 뒤, `autosave` 탭 > `+`해서 저장시키기**
            ![20220604122403](https://raw.githubusercontent.com/is2js/screenshots/main/20220604122403.png)



3. 단축키 사용하기
    - **`ctrl+alt+shift + F12`: (프로그램 킨 상태)화면 녹화기 바로 켜기**
        - 활용은, 첫번째 촬영 후, 편집기를 닫기 전에, 추가촬영을 이어가야할 때, 미리 하나 켜둔다. 안켜두면 프로그램 종료
    - **`F7` : 녹화 시작 -> 일시 중지**
    - **`F8`: 녹화 종료 및 편집기 열기**
    - **`F9`: (F7 2번 눌러 일시중지 상태시) 녹화 취소**
        - F7 녹화 중에  F7-> F9 연달아서 사용
    - `Ctrl+S`: (편집기 열린 상태) 녹화한 것 저장
    - **(편집기에서 저장까지 끝난 상태)`Ctrl+N`: F7 -> F8로 편집기 -> 저장후 다시 녹화기로 돌아갈 때**
        - 안하면, 편집기 껐다가 녹화기 다시 켜야함.
    - **`ctrl+alt+shift + F4`**:  프로그램 종료


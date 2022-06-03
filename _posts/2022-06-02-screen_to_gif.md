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
    
2. **`설정` > `녹화기`에서 인터페이스를 `새 버전`으로 변경한다**
    - 해줘야 `사각형(Select an area)`한 녹화가 가능해진다

    ![image-20220602161633663](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220602161633663.png)
    ![image-20220602161748661](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220602161748661.png)

3. **첫 저장시, `저장 옵션`에서 파일 -> `클립보드 복사 | 파일`로 변경한다**
    ![20220602212034](https://raw.githubusercontent.com/is2js/screenshots/main/20220602212034.png)

4. 설정
    1. 프로그램: 
        ![20220603222454](https://raw.githubusercontent.com/is2js/screenshots/main/20220603222454.png)
        - `수동으로 시작` + `복수의 프로그램 허용`
        - 처음화면: `화면녹화기`
        - 색 설정: `중간`
        - `모든 창이 닫혀 있어도 프로그램을 종료하지 않습니다.`
        - `트레이 > 종료` 체크해제
        - `업데이트 확인` 2개 체크해제
    2. 녹화기:
        - `새 버전` 체크
    3. 편집기:
        ![20220603222708](https://raw.githubusercontent.com/is2js/screenshots/main/20220603222708.png)
        - 색 설정: `중간`
        - 일반: `확인하기 3종` 체크 해제
    4. 단축키:
        ![20220603222814](https://raw.githubusercontent.com/is2js/screenshots/main/20220603222814.png)
        - `화면 녹화기`:**`ctrl+alt+shift + F12`**
            - **이 설정을 해주고 직전 편집기(캡처 후 저장하는 창)상태에서 하나 더 켜놔야, (편집기 종료 -> 프로그램 종료) 추가로 gif촬영을 지속할 수 있다.**
            - 만약 편집기를 [닫기]하면, 프로그램 자체도 종료되어버린다. **만약, 녹화기가 추가로 열리면, 편집기 닫아도 프로그램 안꺼진다.**
            - 편집기 상태에서 다음 촬영할거면, 하나 더 켜두자.
        - `종료`: **`ctrl+alt+shift + F4`**
        - `녹화 취소`  > **`F9`**
            - **녹화 취소할 때, 중지(F8) -> 취소(F9) 연타 치면 된다.**
    5. 편집기-저장탭(첫 녹화 종료F7->F8시 설정 가능)
        ![20220603223236](https://raw.githubusercontent.com/is2js/screenshots/main/20220603223236.png)
        - 샘플링: `Fastest`로 바꾸기
        - `중복 픽셀 감지` 체크해제
        - `지정 경로에 파일 저장하기` 체크해제
        - `클립보드에 복사하기` 체크
            - `파일` 선택



6. 단축키 사용하기
    - **`ctrl+alt+shift + F12`: (프로그램 킨 상태)화면 녹화기 바로 켜기**
        - 활용은, 첫번째 촬영 후, 편집기를 닫기 전에, 추가촬영을 이어가야할 때, 미리 하나 켜둔다. 안켜두면 프로그램 종료
    - **`F7` : 녹화 시작 -> 일시 중지**
    - **`F8`: 녹화 종료 및 편집기 열기**
    - **`F9`: (F7 2번 눌러 일시중지 상태시) 녹화 취소**
        - F7 녹화 중에  F7-> F9 연달아서 사용
    - `Ctrl+S`: (편집기 열린 상태) 녹화한 것 저장
    - **`ctrl+alt+shift + F12`**:  프로그램 종료


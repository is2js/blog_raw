---
toc: true
layout: post
categories: [configuration, vscode, picgo, github-token, settings]
title: vscode image uploader(picGo+token)
description: vscode screenshot uploader setting by picGo + token

image: "images/posts/config.png"
---


### vscode picGo setting

1. typora picGo 설정을 참고할 준비를 한다.

    ```json
    {
    "picBed": {
            "current": "github",
            "github": {
            "repo": "is2js/screenshots",
            "token": "ghp_",
            "path": "",
            "customUrl": "https://raw.githubusercontent.com/is2js/screenshots/main",
            "branch": "main"
            }
    },
    "settings": {
    "showUpdateTip": true,
    "autoStart": true,
    "uploadNotification": true,
    "miniWindowOntop": true
    },
    "needReload": false,
    "picgoPlugins": {}
    }
    ```

2. vscode 확장에서  `picGo`를 검색하여 설치한다.

    ![image-20220517094138689](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220517094138689.png)
    

3. 설정(ctrl + ,)로 들어가 `picGo`를  검색한다.

    1. `Pic Bed: Current`를 `github`로 선택

    2. `Branch`를 `main`으로 선택

        ```
        main
        ```

    3. `Custom Url`을 **계정/내 스크린샷폴더/branch**를 입력한다.

        ```
        https://raw.githubusercontent.com/is2js/screenshots/main
        ```

    4. `Path`는 생략

    5. `Repo`는 **계정/내 스크린샷폴더**를 입력

        ```
        is2js/screenshots
        ```

    6. `Token`은 typora설정시 받아왔던 github token을 가져온다.

        ```
        ghp_
        ```

        - 그외 설정은 비워둔다.



4. **내 대문자변환 단축키`c+a+U`과 겹친 기본 `붙여넣기 단축키`를 따로 설정한다.**

    1. ctrl + k + s 으로 단축키 설정으로 들어간다

    2. `upload`를 검색하여 해당 picgo 업로드 단축키를 <kbd>ctrl + alt + v</kbd>로 설정해준다.

        ![image-20220517095722967](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220517095722967.png)

        



### 단축키

#### ctrl + alt + v(클립보드 그림 붙여넣기)

#### [ctrl + k] + v (측면 미리보기) -> shift+alt+0 (아래로 내리기)

![image-20220517100019522](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220517100019522.png)

![image-20220517100113530](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220517100113530.png)

![image-20220517100121379](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220517100121379.png)
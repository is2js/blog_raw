---
toc: true
layout: post
categories: [configuration, typora, picgo, github-token, settings]
title: typora image uploader(picGo+token)
description: typora screenshot uploader setting by picGo + token

image: "images/posts/config.png"
---

#### 스샷 레포 생성 후 토큰 생성

- [레포 설정 참고 블로그](https://taeuk-gang.github.io/wiki/Typora%20%EC%8B%A0%EA%B8%B0%EB%8A%A5%20-%20%EC%9D%B4%EB%AF%B8%EC%A7%80%20%EC%9E%90%EB%8F%99%20%EC%97%85%EB%A1%9C%EB%93%9C/)



1. 빈 github_id로 로그인해서 스크린샷 레포를 만든다.

    ![image-20220317230902061](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317230902061.png)
    ![image-20220317231006766](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231006766.png)

2. 프로필 > Settings > `Developer settings`로 넘어간다.
    ![image-20220317231044509](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231044509.png)
    ![image-20220317231116510](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231116510.png)





3. Personal aceess tokens로 넘어와 > `Generate new token`을 만든다.
    ![image-20220317231202549](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231202549.png)
    ![image-20220317231225140](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231225140.png)

    - 각종 필요한 설정을 해준다.

        - Note: 아무거나
            ![image-20220317231322305](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231322305.png)

        - Expiration: 무기한으로

            ![image-20220317231340789](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231340789.png)

        - scopes

            - repo 전체 체크

            - write: packages 체크

            - admin:repo_hook 체크

            - write: discussion 체크

                ![image-20220317231402898](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231402898.png)
                ![image-20220317231429258](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231429258.png)
                ![image-20220317231511028](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231511028.png)
                ![image-20220317231533045](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317231533045.png)

        - 토큰을 챙겨놓는다.

            







#### TYPORA에 적용

![image-20220317235240441](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317235240441.png)

- 서식 > 이미지 > `전역 이미지 설정`

    1. **when Insert : `upload image`로 선택**

    2. **[x] `로컬 이미지에 위 규칙을 적용` 체크**

    3. **image Uploader: `PicGo-Core(command line)` 선택**

    4. **`Open Config File`클릭후 아래 템플릿대로 작성**

        - 예시

        ```json
        {
            "picBed": {
                "current": "github",
                "github": {
                    "repo": "is2js/screenshots", //변경
                    "token": "ghp_8nmOf//ymaH8bBHqXNsLRtoTHUEN8j1B6vh/", //변경
                    "path": "",
                    "customUrl": "https://raw.githubusercontent.com/is2js/screenshots/main", //변경
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

        







#### 사용 중 레포지토리만 교체

- typora > 서식 > 이미지 > 전역이미지 설정 > `Open config file` > 

    - **`"repo"`와 `"customUrl"`에서 폴더명만 바꿔주면 된다.**

    - 토큰은 계속 유지중이니.. 레포만 바꿔주면 끝.

        ![image-20220317232429326](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220317232429326.png)


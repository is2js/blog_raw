---
toc: true
layout: post
title: pycharm 세팅 및 단축키
description: pycharm vscode keymap기준 정리

categories: [pycharm, settings, shortcut]
image: "images/posts/python.png"
---


## 플러그인


### Vscode Keymap 설치 및 intellij 설정 가져오기
1. intellij `F1> export settings`
2. **vscode keymap 플러그인 설치**
3. pycharm `F1> import settings`

### Pylint 설치
1. plugin에서 검색하여 설치
2. **View > Tool Windows > pylint 체크**
	![20220615182317](https://raw.githubusercontent.com/is2js/screenshots/main/20220615182317.png)

### Black 설치
#### 로컬python에 black설치 -> external tools에 추가

1. pylint와 같은 위치에 설치되어있을 것이니 `pylint의 경로`부터 살펴본다.

    - local python은 `%homepath%\anaconda3\Scripts` 폴더에 깔려있다.

        ![image-20220615174824551](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220615174824551.png)

2. black을 anaconda3로 설치하고 경로를 확인한다.

    ```shell
    conda install black
    ```

    - **powerShell에서는 which(linux) / where(cmd) 대신 `Get-command [프로그램명]`로 바로 검색할 수 있다.**

        ![image-20220615175325599](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220615175325599.png)

        - 혹은 약자인 `gcm [프로그래명]`으로 찾을 수 있다.

        ![image-20220615175516390](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220615175516390.png)

    ![image-20220615174854635](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220615174854635.png)

    - `C:\Users\cho_desktop\anaconda3\Scripts\black.exe`

3. `settings > Tools > External tools`에 black 추가하기

    - Program: `C:\Users\cho_desktop\anaconda3\Scripts\black.exe`

    - Arguments: `$FilePath$`
    - Working directory: `$ProjectFileDir$`
    - **파이참 Tools > External tools > Black**으로 포맷팅 확인해보기
        - 문자열 싱글 따옴표 -> 더블 따옴표로 바뀌는지 확인

    ![42d443a7-37e0-486a-b5bb-a5f1e03b1549](https://raw.githubusercontent.com/is2js/screenshots/main/42d443a7-37e0-486a-b5bb-a5f1e03b1549.gif)



#### File Watchers에 Black 추가하여 실시간 반영

1. `settings > tools > File Watchers`

2. `+` > `custom` > 

    1. Name: `Black`
    2. File type: `python`
    3. Score(deafult): Project Files
    4. Program: `black 경로`
    5. Arguments: `$FilePath$`
    6. Output path to : `$FilePath$`
    7. Working directory: `$ProjectFileDir$`
    8. **Advanced Options에서 `auto-save edited files to trigger the watcher` 체크 확인**

    ![9b2a113c-7453-4f5d-90c2-af6b406eda6a](https://raw.githubusercontent.com/is2js/screenshots/main/9b2a113c-7453-4f5d-90c2-af6b406eda6a.gif)


#### 실시간 반영 지우고 단축키로 
- c + s + a + B로 하면 될 듯
	- [참고](https://medium.com/daehyun-baek/python-%EC%BD%94%EB%93%9C-%EC%8A%A4%ED%83%80%EC%9D%BC-%ED%88%B4-pycharm-%ED%99%98%EA%B2%BD-flake8-black-4adba134696a)



### 단축키
1. 
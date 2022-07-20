---
toc: true
layout: post
title: pycharm 세팅 및 단축키
description: pycharm vscode keymap기준 정리

categories: [pycharm, settings, shortcut]
image: "images/posts/python.png"
---

## 세팅

- 일반적인 것은 intellij에서 가져온다.

### 시작시 최근 프로젝트 reopen 해제
- settings >  appearance & behavior > `system settings` > [ ] **reopen projects on startup 체크해제**
![20220616111941](https://raw.githubusercontent.com/is2js/screenshots/main/20220616111941.png)

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



#### ~~File Watchers에 Black 추가하여 실시간 반영~~

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


#### External Tool로 등록한 Black의 단축키로 반영
- keymap > `external tool`검색 > `black` 확인하여 단축키 설정
- `ctrl + shift + alt + B`(assign)
    ![20220716132507](https://raw.githubusercontent.com/is3js/screenshots/main/20220716132507.png)
	- [참고](https://medium.com/daehyun-baek/python-%EC%BD%94%EB%93%9C-%EC%8A%A4%ED%83%80%EC%9D%BC-%ED%88%B4-pycharm-%ED%99%98%EA%B2%BD-flake8-black-4adba134696a)






### 단축키
- **move statement: 메서드 이동 단축키 지정**
    ![20220706182502](https://raw.githubusercontent.com/is3js/screenshots/main/20220706182502.png)
    - Move staetment Down : **`alt + shift + PgUp`**
    - Move staetment Up: **`alt +shift + PgUp`**

- **`alt + shift + o`: optimize import**
    - **리포맷팅 해도 import는 정렬안해주는데, 해당 기능을 이용해야한다.**
    ![c754c2b1-df35-49a5-98e7-2e6991b03da4](https://raw.githubusercontent.com/is3js/screenshots/main/c754c2b1-df35-49a5-98e7-2e6991b03da4.gif)


- **`(드래그 후)ctrl + alt + F8`(assign) : Evaluate Expression**
    - 기존 quick evluate expression을 삭제하고 배정한다.
    - 디버깅시 `의심Line F9` -> 디버깅 -> `수정해볼 부분만 드래그후 Evaluate Pression (Ctrl + Alt + F8)`**
        - **`ctrl + shift + enter`를 치면 아래 watcher부분에 기록을 남긴다.**
    ![20220707004036](https://raw.githubusercontent.com/is3js/screenshots/main/20220707004036.png)
    ![20220707004102](https://raw.githubusercontent.com/is3js/screenshots/main/20220707004102.png)
    ![8f9c3a17-afa3-4a23-a474-829913b929af](https://raw.githubusercontent.com/is3js/screenshots/main/8f9c3a17-afa3-4a23-a474-829913b929af.gif)

- `ctrl + shift + alt + B`(assign): black 포맷터 실행
    ![20220716132507](https://raw.githubusercontent.com/is3js/screenshots/main/20220716132507.png)


- **생성자 및 필드 추가 과정**
    1. client에서 해당 객체로 필드들 노란줄 작성하고
    2. action으로 `add field`를 해주어, None상태로 바로 enter쳐서 확정짓는다.
        - **파라미터 추출시 default value의 변수명이 자동으로 바뀌기 때문에 다음에 바꿔줄 것**
    3. None들을 하나하나 `ctrl + alt + p`로 파라미터 추출한 뒤
    4. **None을 삭제후 `alt + /`를 직전단어를 추출해서, 필드명과 파라미터명을 맞춘다.**
    5. 모든 필드의 값들을 파라미터 추출하고 난 뒤,`ctrl + F6`으로 enter + tab + space를 활용해서 default값 사용을 체크해제 시킨다.
        ![983c637c-e1a7-4908-a042-f2f13318fc17](https://raw.githubusercontent.com/is3js/screenshots/main/983c637c-e1a7-4908-a042-f2f13318fc17.gif)

- **`_필드와 property` 추가 과정**
    1. client에서 해당 객체로 필드들 `_필드`명으로 작성하고
    2. action `add field` 부터 필드 추가과정을 따른다.
    3. 다시 client코드의 _필드에  > `add property`를 사용해서 property코드도 추가한다.

- **`.setter` 추가 과정**
    1. property가 정의된 상태에서 client에서  **`객체.property = "값"`을 할당하여 작성해야만 action에 `create propery`가 추가로 생기며 누르면 .setter가 자동 정의 된다.**
        ![f1fbb3cb-c840-4f76-98a4-6b624537b768](https://raw.githubusercontent.com/is3js/screenshots/main/f1fbb3cb-c840-4f76-98a4-6b624537b768.gif)


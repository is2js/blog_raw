---
toc: true
layout: post
categories: [configuration, jupyter, toc, nbextension, toggle, settings]
title: jupyter nbextension & toggle
description: toc 등을 위한 nbextension 설치 + toggle snippet

image: "images/posts/config.png"
---


### nbextension 설치

1. `관리자 권한`으로 터미널 열기

2. 아래 커맨드 2개 치기
    ```shell
    pip install jupyter_contrib_nbextensions
    ```

    ```shell
    jupyter contrib nbextension install
    ```

3. jupyter notebook 실행 후
    1. [체크해제] disable configuration for nbextensions 
    2. Table of Contents 선택


### jupyter용 toggle code
- 클릭으로 토글할 수 있게 된다.
    ```python
    from IPython.core.display import HTML

    display(
        HTML('''<script>
        
    code_show=true; 

    function code_toggle() {
    if (code_show){
    $('div.input').hide();
    } else {
    $('div.input').show();
    }
    code_show = !code_show
    } 
    $( document ).ready(code_toggle);
    </script>
    <form action="javascript:code_toggle()"><input type="submit" value="Click here to toggle on/off the raw code."></form>''')
    )
    ```


### 브라우저 지정(크롬 안될 경우)

1. cmd에서 config 파일 생성
    ```shell
    jupyter notebook --generate-config
    ```

2. 생성된 jupyter_notebook_config.py의 경로로 가서 열고, `.browser` 검색하여 아래처럼 수정
    ```python
    #c.NotebookApp.browser로 되어있는 주석을 풀고 windows10 크롬경로 + %s를 입력해주고 재시작
    c.NotebookApp.browser = 'C:/Program Files (x86)/Google/Chrome/Application/chrome.exe %s'
    ```

### 참고) 크롬을 기본 브라우저로 지정

1. 크롬 열기 -> `우측상단에 점점점` -> `설정`
2. 내리다 보면 `기본 브라우저` -> 크롬으로 변경
---
toc: true
layout: post
title: window에서 sqlite 버전 수동 업그레이드
description: window function 사용을 위한 sqlite3.dll 업그레이드

categories: [python, database, sqlalchemy, sqlite, windowfunction, sqlite3.dll]
image: "images/posts/python.png"
---

### sqlite 자체 버전 업그레이드
- sqlite는 하위버전(3.25.0미만)에서 window function을 제공하지 않으므로 직접 업데이트 해야합니다.

1. (venv) sqlite 현재 버전 확인(3.25.0 이상 버전부터 window function 지원한다)
    ```python
    >>> import sqlite3
    >>> sqlite3.version # python librrary 버전에 불과함.
    '2.6.0'
    >>> sqlite3.sqlite_version # sqlite3.dll 버전임 -> 이것을 업데이트 해야한다.
    '3.21.0'
    ```
2. venv를 만들어주는 `base interpreter`의 sqlite3.dll부터 변경
    1. 현재 venv를 만드는 local python의 버전을 확인한다.
        - 환경설정 > Project: > Python Interpreter > 점점점 Add (+) > Base Interpreter 경로 확인
        ![20221019222134](https://raw.githubusercontent.com/is3js/screenshots/main/20221019222134.png)
        - 실행 > `%appdata%` > base 경로 > `Dlls`폴더까지 들어가서 sqlite3.dll 파일 확인하기
        ![20221019222609](https://raw.githubusercontent.com/is3js/screenshots/main/20221019222609.png)
    2. [sqlite3 홈페이지](https://sqlite.org/download.html)를 방문하여 `x64`검색으로 3.25.0 이상버전을 다운받는다.
        - ctrl+f로 `x64` 검색 -> sqlite-dll-win64-x64-3390400.zip 다운로드
    3. 기존 sqlite3.dll 에 `.old`를 붙여 백업하고, `zip 속의 sqlite3.dll로 교체`한다.

3. venv의 sqlite3.dll 변경
    - venv > Scrips 폴더에 > sqlite3.dll에 위치
        ![20221019224637](https://raw.githubusercontent.com/is3js/screenshots/main/20221019224637.png)
    - 다운 받은 것으로 교체해준다
    
4. (venv) sqlite 현재 버전 바뀐 것 다시 확인
    ```python
    >>> import sqlite3
    >>> sqlite3.sqlite_version 
    '3.39.0'
    ```
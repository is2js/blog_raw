---
toc: true
layout: post
title: python project 설정
description: python lint, formatter, hook 설정

categories: [python, project, configuration, settings, lint, formatter, black, flake8, pre-commit, dotenv]
image: "images/posts/python.png"
---

# Project init

## 01 git init 및 .gitignore 생성

- 프로젝트 폴더 생성 
- `wt -d .`로 터미널 열기
- `git init`
- root에 `.gitignore` 생성하기



## 02 virtualenv 설치 및 실행

```powershell
# 1. OS python으로 가상환경 패키지 설치
pip3 install virtualenv

# 2. 가상환경 생성
python -m virtualenv -p python310 venv
#virtualenv -p python310 venv

# 3. .gitignore에 venv 추가

# 4. 가상환경 실행
.\venv\Scripts\activate
#. venv/bin/activate
```

- vscode에서도 Python interpreter에 가상환경 선택

  - `code .`

  - `F1 > Python Interpreter Select`

    ![image-20220925023738394](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220925023738394.png)



## 03 pylint with vscode ext.

### pylint(style checking) with vscode extension

- **style checking**, **error checking**, **리팩토링과 관련된 조언**과 함께 **코드에 점수**도 매겨 준다. 외부 의존성으로 **syntax tree**를 위한 `astroid`, 알파벳 순서로 **import를 정리**하는 `isort`, **McCabe 복잡도**를 구현한 `mccabe`만을 가지고 있는 것을 보아, **자체적인 lint 알고리즘**을 사용했다는 것을 알 수 있다. 설정을 customize할 수 있는 범위도 꽤 다양해서, IDE를 사용하고 있더라도 서드파티로 추천하고 싶다.



```powershell
# 1. 가상환경 실행
.\venv\Scripts\activate
#. venv/bin/activate

# 2. pylint 설치
pip3 install pylint

# 3. .pylintrc 설정파일 생성
pylint --generate-rcfile > .pylintrc
```

- **.pylinctrc 설정 변경**

  1. `C0114` : 독스트링 없어도 무시하도록 **disable=**

  2. `max-line-length` 수정 100 -> 120

  3. `min-public-methods` 갯수 줄이기 2-> 1

     ![image-20221011221557288](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011221557288.png)



- **vscode에서 pylint extension 설치**



## 04 flake8, black 설치후 세팅

### flake8(style and error checking) ex> unused

- **pycodestyle + pyflakes + 복잡도 검사 기능**이라고 생각하면 된다. pylint처럼 복잡도 검사를 위해 `mccabe`를 사용하지만, style/error checking에 대해 `pycodestyle`과 `pyflakes`를 사용한다. 그냥 **라이브러리 여러 개 묶은 wrapper** 형태다. 아래는 usage다

  ```
  $ flake8 ./parse.py
  ./parse.py:2:1: F401 'logging' imported but unused
  ./parse.py:3:1: E302 expected 2 blank lines, found 0
  ./parse.py:6:5: E303 too many blank lines (2)
  ./parse.py:7:24: W291 trailing whitespace
  ./parse.py:8:15: E221 multiple spaces before operator
  ./parse.py:9:18: E701 multiple statements on one line (colon
  ./parse.py:15:1: W391 blank line at end of file
  ```



```powershell
# 1. 가상환경 실행
.\venv\Scripts\activate
#. venv/bin/activate

# 2. flask8 설치
pip3 install flake8
```

3. **`.flake8` 파일 생성**

   - ignore
     - [Do not use bare except, specify exception instead (E722)](https://www.flake8rules.com/rules/E722.html)
     - [Line break occurred before a binary operator (W503)](https://www.flake8rules.com/rules/W503.html)
   - max-line-length 수정 100 -> 120
   - **code없는 init 허용**

   ```
   [flake8]
   ignore = E722, W503
   max-line-length = 120
   per-file-ignores =
   	__init__.py: F401
   ```

   



### black(code formatter)

- Black은 Python 재단에서 2018년에 시작한 **Python Code Formmater**

```powershell
# 1. 가상환경 실행
.\venv\Scripts\activate
#. venv/bin/activate

# 2. black 설치
pip3 install black
```





## 04 (선택) faker, pytest 설치

```powershell
# 1. 가상환경 실행
.\venv\Scripts\activate
#. venv/bin/activate

# 2. black 설치
pip3 install faker
pip3 install pytest
```

- 사용
  1. `_test.py`를 만들고
  2. `def test_` method를 + `assert 비교`구현하여 만든 뒤
  3. `pytest -vs [상대경로]`



## 05 (선택) dotenv 설치

```powershell
# 1. 가상환경 실행
.\venv\Scripts\activate
#. venv/bin/activate

# 2. python-dotenv 설치
pip3 install python-dotenv
```

3. **root에 `.env` 생성 후 `.gitignore`에 추가**



## 06 .gitignore 추가

- 기존에 추가한 `venv`, `.env`외에 
- **test용 DB, vscode cache파일들 추가**

```
venv
.env
storage.db
.pytest_cache
**/__pycache__
```





## 07 pre-commit 설치 및 세팅



```powershell
# 1. 가상환경 실행
.\venv\Scripts\activate
#. venv/bin/activate

# 2. pre-commit 설치
pip3 install pre-commit
```

3. **root에 `.pre-commit-config.yaml` 생성**



### flake8, black hook 설정

1. `.pre-commit-config.yaml`를 열고

2. **python버전을 기록하고**

3. 2개 패키지의 정보를 기입한다

   - **rev는 태그버전인데 `stable`**을 기록해놓고 차후에 `pre-commit autoupdate`을 시행하면 자동으로 버전이 기록된다.

   ```yaml
   default_language_version:
     python: python3.10
   repos:
   -   repo: https://github.com/ambv/black
       rev: 22.10.0
       hooks:
       - id: black
         stages: [commit]
   -   repo: https://gitlab.com/pycqa/flake8
       rev: 3.9.2
       hooks:
       - id: flake8
         stages: [commit]
   ```

   

### requirements.txt 자동업데이트 설정

1. `.pre-commit-scripts`폴더 생성

2. `requirements.py` 생성

3. 아래 스크립트 복붙

   ```python
   #!/usr/bin/env python
   
   from subprocess import call  # nosec
   from sys import platform
   
   
   def main():
       file_name = "./requirements.txt"
   
       if platform == "win32":
           with open(file_name, "w") as file_:
               call(["./venv/Scripts/pip3", "freeze"], stdout=file_)
       else:
           with open(file_name, "w") as file_:
               call(["./venv/bin/pip3", "freeze"], stdout=file_)
       call(f"git add {file_name}")  # nosec
   
   
   if __name__ == "__main__":
       exit(main())
   ```

4. `.pre-commit-config.yaml`에 hook 추가

   ```yaml
   -   repo: local
       hooks:
         - id: requirements
           name: requirements
           entry: ./.pre-commit-scripts/requirements.py
           language: system
           pass_filenames: false
           stages: [commit]
   ```





### (선택) pytest hook 추가

```yaml
-   repo: local
    hooks:
    - id: pytest
      name: pytest
      language: system
      entry: pytest -v -s
      always_run: true
      pass_filenames: false
      stages: [commit]
```



### (선택) dotenv example generator hook 추가

1. `.pre-commit-scripts`폴더 생성

2. `dotenv_example_generator.py` 생성

3. 아래 스크립트 복붙

   ```yaml
   #!/usr/bin/env python
   
   import os
   import re
   from subprocess import call  # nosec
   
   
   def main():
   
       dotenv = "./.env"
       dotenv_example = "./.env.example"
   
       if not os.path.exists(dotenv):
           exit()
   
       # (Get-Content .\.env) -replace('=.*', '=') | Set-Content .env.example
       # sed 's/=.*/=/' .env > .env.example
       with open(dotenv, "r", encoding="utf8") as file_:
           lines = file_.readlines()
           with open(dotenv_example, "w") as file__:
               for line in lines:
                   if line[0] == "#":
                       file__.write(line)
                       continue
                   file__.write(re.sub("=.*", "=", line))
   
       call(f"git add {dotenv_example}")  # nosec
   
   
   if __name__ == "__main__":
       exit(main())
   ```

4. ``.pre-commit-config.yaml`에 hook 추가

   ```yaml
   -   repo: local
       hooks:
         - id: .env.example
           name: .env.example
           entry: ./.pre-commit-scripts/dotenv_example_generator.py
           language: system
           pass_filenames: false
           stages: [commit]
   ```

   

### pre-commit install 

- 아래 명령어로 **.git/hooks에 세팅**을 해준다.

```
pre-commit install
```

- rev들의 버전을 맞추고 싶다면

```
pre-commit autoupdate
```
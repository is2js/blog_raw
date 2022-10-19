---
toc: true
layout: post
title: 클린아키텍쳐 08 Json api와 vscode flask debugging setting
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---


# 클린 아키텍쳐 8장



## 1 jsonapi 문서 확인 및 vscode flask 디버깅 설정

### 01 jsonapi 사이트에서 example에서 구조 살펴보기

#### document structure > top level 

- https://jsonapi.org/format/#document-top-level

  ![image-20221008002649574](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008002649574.png)

  ![image-20221008002000797](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008002000797.png)



#### Resources > Creating > response

- https://jsonapi.org/format/#crud-creating-responses

  ![image-20221008002249959](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008002249959.png)





### 02 vscode에서 flask 디버깅을 위한, launch.json 설정(python 자체는 vscode로 실행안한다?!)

#### vscode 디버깅시  route return breakpoint에서 F5(계속)을 안눌러주면 멈쳐있다. 결과를 확인할 수 있다.

1. vscode 좌측에 `실행 및 디버그`버튼을 클릭한다.

   ![image-20221008003008516](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008003008516.png)

2. **launch.json file**을 누르고 -> python -> python file을 선택해서 **local에 `.vscode > lauch.json`을 생성한다**

   ![image-20221008003110391](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008003110391.png)
   ![image-20221008003249127](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008003249127.png)

3. vscode 문서 > python > debugging > [flask debugging](https://code.visualstudio.com/docs/python/debugging#_flask-debugging)을 찾아가서 설정을 복사해온다

   ```json
   {
       "name": "Python: Flask (development mode)",
       "type": "python",
       "request": "launch",
       "module": "flask",
       "env": {
           "FLASK_APP": "app.py",
           "FLASK_ENV": "development"
       },
       "args": [
           "run"
       ],
       "jinja": true
   },
   
   ```

4. **launch.json 내용 중 `configurations` 내부 list value 중 1개로 dict를 복붙한다.**

   - python파일은 터미널에서 실행하면 되니 **기존 python은 지운다. F5누르면 flask만 실행되게 하자.**

   ![image-20221008003916716](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008003916716.png)

5. **FLASK_APP에 파일명이 아닌 py확장자까지 다 적어줘야한다. app객체를 import하고 있는 `run.py`를 명시해주자**

   ![image-20221008004012125](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004012125.png)

6. 다시한번 run and debug 탭을 누르고 실행시킨다.

   - F5가 단축키, shift + F5 종료, ctrl+shift+F5 재실행

   ![image-20221008004106945](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004106945.png)

   ![image-20221008004303020](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004303020.png)

7. F9로 route의 return부분을 break point를 찍고 -> F5로 실행상태에서 -> POSTMAN으로 입력을 준 뒤 ->break point확인 후 확인이 끝나면 -> continue(F5) 해보자.

   - create(insert, POST) 테스트이므로 unique key는 바꿔서 send

   ![image-20221008004612274](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004612274.png)

   ![image-20221008004640215](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004640215.png)
   ![image-20221008004756167](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004756167.png)

   ![image-20221008004809797](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004809797.png)

   ![image-20221008004838896](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004838896.png)

   ![image-20221008004934422](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008004934422.png)

   ![image-20221008005057791](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005057791.png)

   ![image-20221008005107132](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005107132.png)

8. 걸린 상태에서 **마우스를 갖다대면, 변수값들이 다 보임**

   ![image-20221008005506255](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005506255.png)

   ![image-20221008005532878](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005532878.png)
   ![image-20221008005612581](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005612581.png)



#### 실패하는경우 보내기(uniquekey 안바꾸고 다시 한번 send)를 break point를 잡으려면  code < 300 early return이 아닌 밑부분을 잡던가, 실제 검증로직이 있는 use cases의 validate_entry부분을 잡아야한다

![image-20221008005819378](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005819378.png)

​	![image-20221008005838955](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005838955.png)

![image-20221008005851899](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005851899.png)

![image-20221008005901303](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008005901303.png)

![image-20221008010003679](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008010003679.png)



### 03 framework 디버깅 설정 추가(.vscode) 도 ignore하지말고 commit에 추가해준다.

```
git commit -am "improve: enabling debugger"
```





## 참고

- [Robert C. Martin (Uncle Bob)의 Clean Code Blog](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
  - [PDF-free-Clean Architectures in Python](https://leanpub.com/clean-architectures-in-python)
- [용어 참고 블로그 1편](https://velog.io/@jahoy/Python%EC%9C%BC%EB%A1%9C-Clean-Architecture-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)
- [용어 참고 블로그 2편](https://velog.io/@jahoy/Python%EC%9C%BC%EB%A1%9C-%ED%81%B4%EB%A6%B0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B02)
- [fasapi vs flask](https://testdriven.io/blog/fastapi-crud/#postgres-setup)



#### gitignore

- git에 commit되었던 것을 ignore하고 싶을 때

  ```
  git rm [파일명] --cached
  ```
  
- git amend

  ```
  git commit --amend --no-edit
  ```

  

1. console 등에서 init import부터 생기는 pycache폴더
2. venv
3. 연습용 sqlite .db파일
4. pytest시 생기는 .pytest_cache 폴더

```
**/__pycache__
venv
storage.db
.pytest_cache
```



#### sqlalchemy패키지

- 일반 패키지: create_engine / 칼럼재료들
- .orm 패키지:   
  - relatioship: 1:M에서 1에 해당하는 table이 들고 있는 가짜칼럼
  - sessionmaker: handler class 속 enter에서 engine을 묶어서 session을 반환해주는 놈
- .exc 패키지:
  - IntegrityError: unique key 중복시 나는 에러



- 계층별 변수정리
  - id : sqlalchemy -> **repo부터는** fk_id도 있으니 `pk_id`
  - name: sqlalchemy->repo->usecase까지는 그냥 name (선택인자 개별메서드에by_name)-> **controller부터는 외부에선 다른(pet)_name도 있으니 `fk_name`**
    - **OneEntity의 경우 그냥 controller에서도 `name`만**



#### github에서 br따서 작업하기

##### [1-1] github에서 br생성 ~ clone

1. github에 들어가서 `feat/~`로 브랜치 생성

2. 해당 br만 clone

   ```
   git clone -b [branch] [git주소]
   ```





##### clone후 초기 작업 세팅

1. gitignore중에 db파일 있으면 옮겨주기
   - 없으면 config import해서 Base + handler -> get_enigne -> bind한 다음, create_all()해줘야할듯??

![image-20220929154256052](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220929154256052.png)

2. github에서 따온 빈 새br에 대해, venv를 만들고 활성화하고  vscode를 연다

```
python -m virtualenv -p python310 venv

.\venv\Scripts\activate
```



3. 최초 requirements.txt 직접 설치

```
pip3 install -r requirements.txt
```

4. 최초 pre-commit install 직접 세팅

```
pre-commit install
```

5. vscode 열고 interpreter venv로 지정

```
code .
```





##### [1-2] 작업끝난 br의 폴더에서 새 작업 br생성 ~ 분기게시 (초기세팅 또 안해도 되는 장점)

1. 현재 br를 확인한다.

   ```python
   git branch
   * feat/register_user_use_case
   ```

2. **checkout -b**로 새 br 생성하여 진입하기

   - 원래는 github에서 create branch했었음.

   ```
   git checkout -b feat/generic_use_cases
   
   git branch
   
   * feat/generic_use_cases
     feat/register_user_use_case
   ```

3. **git push -u origin [branch명]으로 `새로생성한 br를 push해서 github에 생성하기`**

   - vscode에서는 push -u origin [branch] 대신  **`분기게시`가 뜬다.**

4. github에서 **빈 분기가 생성되었는지 확인하기**

   - github에 merge되기 위해서는 **직전 작업이후 `github에 새 작업 br를 먼저 생성해놓고 작업`에 들어가야한다**




##### [1-3]  옛날에 따둔 br에서 작업시작할 땐, 최신 master를 pull -> rebase후에 작업시작하기

1. `git checkout master`를 통해 **안가져와서 안보이던 master**를 만든다

   - **-b의 새로만든 것이 아니므로** remote와 연결된 master가 된다.
   - `git branch`로 확인

   ```
   git checkout master
   
   git branch
   ```

2. `git pull`로 master에 최신 코드를 가져온다

   ```
   git pull
   ```

3. **rebase받을 업뎃안된 작업br로 넘어간 뒤, `matser의 내용을 rebase로 받아온다`**

   ```
   git checkout [작업br]
   
   git rebase master
   
   git log --oneline
   ```

   

##### [2] framework 있다면 venv에 환경변수 세팅 -> vscode 디버깅 세팅 

1. 등록: 

   ```powershell
   $env:FLASK_APP = "초기화된 app객체를 import한 python파일명"
   #export FLASK_APP=파일명
   ```

   - powershell은 파일명은 " " 확장자를 뺀 쌍따옴표로
   - **보통 app객체 import한 python파일은 `run`이라고 명명하며, main으로 app.run도 가지고 있다.**

   ```powershell
   $env:FLASK_RUN_PORT = 8000
   ```

2. 확인

   ```powershell
   Get-ChildItem Env:
   ```


3.  **cli로 framework실행**

   ```
   flask run
   ```

4. **vscode에 F5로 실행 및 디버깅 가능하게 세팅(.vscode/launch.json)**

   1. vscode 좌측에 `실행 및 디버그`버튼을 클릭한다.

   2. **launch.json file**을 누르고 -> python -> python file을 선택해서 **local에 `.vscode > launch.json`을 생성한다**

   3. vscode 문서 > python > debugging > [flask debugging](https://code.visualstudio.com/docs/python/debugging#_flask-debugging)을 찾아가서 설정을 복사해온다

      ```json
      {
          "name": "Python: Flask (development mode)",
          "type": "python",
          "request": "launch",
          "module": "flask",
          "env": {
              "FLASK_APP": "app.py",
              "FLASK_ENV": "development"
          },
          "args": [
              "run"
          ],
          "jinja": true
      },
      
      ```

   4. **launch.json 내용 중 `configurations` 내부 list value 중 1개로 dict를 복붙한다.**

   5. **FLASK_APP에 파일명이 아닌 py확장자까지 다 적어줘야한다. app객체를 import하고 있는 `run.py`를 명시해주자**

   6. 다시한번 run and debug 탭을 누르고 실행시킨다.
      - F5 실행, shift+F5 종료, ctrl + shilft + F5 재실행

   7. F9로 route의 return부분을 break point를 찍고 -> F5로 실행상태에서 -> POSTMAN으로 입력을 준 뒤 ->break point확인 후 확인이 끝나면 -> continue(F5) 해보자.

   8. git commit

      ```
      improve: enabling debugger
      ```

      

##### [3] 해당br 작업(테스트) 완료후 ~ merge까지 정리

1. status 확인후 add . 후  -am으로 커밋->실패->-am커밋

   - **add를 먼저 날려야 add된상태로 pytest하는 것 같다?!**

   ```
   git status
   
   git add .
   
   git commit -am "feat: implementing data interfaces"
   
   git commit -am "feat: implementing data interfaces"
   ```

2. **git push는 현재 로그인된 vscode에서 하고 있음.**

3. **github에 들어가보면, `Compare & pull request`가 떠있더라도 `PR탭 > new PR`로 들어가는 버릇을 들여보자.**
   
- Comparing change까지 들어가서 코드를 확인하고 `Able to merge` 및 `세부코드`를 확인하고 **직접 Create PR까지 가자**
  
4. **merge후 master에 반영되었는지까지 확인하자**





#### vscode 설정

- 캐쉬파일 안보이기

```json
"files.exclude": {
        "**/__pycache__": true,
        "**/.pytest_cache": true
    }

```

- tab시 space로 입력되기

  ![image-20221001015405760](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001015405760.png)

- 탐색기 열기 단축키 : `shift + alt + R`

  - 파일: 파일 탐색기에 표시



#### test정리

1. repo_insert_X

   1. id제외 faker + insert메서드 -> new_dto
   2. new_dto속 배정id + engine db select -> query_data -> engine db delete
   3. assert new_dto.필드 == query_data필드 비교

2. repo_select_X
   1. id포함 faker -> entity모델 객체
   2. id포함 재료들 + engine db insert -> db데이터만들어놓기
   3. id, fk재료들 + select메서드 -> List[entity모델객체들]
   4. engine db delete

   





#### status_code

1. `422`: validate_entry실패하는 query 속 param들 -> usecase메서드의 결과물 response의 ["Success"]가 False

   - **`필수param`(dict속 필드)이 빠졌을 경우(존재검증) or 필수param이 `validate_entry실패`할 경우 -> `response("Success"/"Data")의 "Success"가 False`**

   ```
   if reponse["Success"] is False:
   ```

   

2. `400`: query속 param들이 필요한 controller인데 http_request안에 아예 존재도 안했다면, 400 Bad Request에러

   - **http_request에 필수param을 포함한 .query or .body가 아예 날라오지도 않았을 경우**

   ```
   if http_request.query:
   	# if query
   # if not query
   else:
   ```

   ```
   if http_request.body:
   	# if body
   # if not body
   else:
   ```

3. `409`:  타겟 자원에 상태에 요청이 conflict를 유발한 경우

   - **register(insert) 요청에 대해 unique key(name)이 중복되어, 중복에러(IntegrityError)를 발생한 경우**

   ```
   POSTMAND에서 생성요청을 같은name를 가진 body로 2번 요청했을 때
   ```

   





#### powershell 환경변수 등록/확인

- python가상환경 venv에서 activated한 상태로 적용
  - `venv\Scripts\activate.ps1`에 저장된다.

1. 등록: 

   ```powershell
   $env:FLASK_APP = "hello"
   ```

   - 파일명은 " " 확장자를 뺀 쌍따옴표로

   ```powershell
   $env:FLASK_RUN_PORT = 8000
   ```

2. 확인

   ```powershell
   Get-ChildItem Env:
   ```

   ```powershell
   Get-ChildItem Env: | Format-Table -Wrap
   ```

   ```powershell
   ## 'LOGONSERVER' 환경변수를 찾고 싶다면,
   Get-ChildItem Env:LOGONSERVER
   
   ## 'on'이 포함된 단어, ex) logONserver, ONedrive...
   Get-ChildItem Env:*on*
   
   ## 'on'으로 시작하는 단어, ONedrive ...
   Get-ChildItem Env:on*
   
   ## 'on'으로 끝나는 단어, ex) processor_revisiON ...
   Get-ChildItem Env:*on
   ```


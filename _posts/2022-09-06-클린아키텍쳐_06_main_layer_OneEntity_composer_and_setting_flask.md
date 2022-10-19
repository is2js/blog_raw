---
toc: true
layout: post
title: 클린아키텍쳐 06 Main layer와 OneEntity Composer, Flask 세팅
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---

# 클린 아키텍쳐 6장


## 1 Main layer

### 01 src> main 폴더 및 init 생성

### 02 src>main> composer + adapter/configs/routes/interface 폴더 및 init생성

![image-20221006170716962](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006170716962.png)



## 2 interface> Controller의 Interface로서 RouteInterface 정의하기

![image-20221006170900078](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006170900078.png)

### 01 main>interface>route.py + init 생성하고 RouteInterface정의하기

#### Route는 controller인터페이스로서, HttpRequest Input -> HttpResponse Output의 route method로 정의한다.

```python
from abc import ABC, abstractmethod
from typing import Type
from src.presenters.helpers import HttpRequest, HttpResponse


class RouteInterface(ABC):
    """ Interface to Routes """

    @abstractmethod
    def route(self, http_request: Type[HttpRequest]) -> HttpResponse:
        """ Defining Route """

        raise Exception("Should implement method: route")

```



- init에 올리기

```python
from .route import RouteInterface
```



### 02 모든 Controller는 RouteInterface를 상속하고, controller의 메서드명 handle -> route로 변경해준다.

```python
#...
from src.main.interface import RouteInterface
#...


class FindUserController(RouteInterface):
    #...
    def route(self, http_request: Type[HttpRequest]) -> HttpResponse:
```



```python
from typing import Type
from src.main.interface import RouteInterface
#...

class RegisterPetController(RouteInterface):
    #...
    def route(self, http_request: Type[HttpRequest]) -> HttpResponse:
```



### 03 코드 수정후 확인은 pytest -vs 전역으로 해서 검사한다

```
pytest -vs
```



### 04 commit -> interface추가는 feat:보다는 improve:로??

```
git status
git add.
git commit -am "improve: Adding interfaces to controllers"
```



### 05 생략된 controller 및 Spy 추가

- [참고 github](https://github.com/programadorLhama/Backend-Python/tree/master/src/presenters/controllers)
  - register_user_controller.py 및 test
  - find_pet_controller.py 및 test 
    - src>presenters>controllers에 추가 및 **init import**
  - find_pet_spy.py
  - register_user_spy.py
    - src>data>test에 추가 및 **init import**

- 전역 `pytest -vs`하면서 수정
  - 원본 mock_users() -> 나는 pet처럼 mock_user()로 쓰고 있다.









## 3 composer> use_case별 Controller객체(route객체)를 반환해주는 composer()메서드 구현



### 01 OneEntity의 register Usecase composer method부터 구현

#### 인터페이스인 RouteInterface Type으로서 UseCaseController객체를 반환해주는 use_case_composer() method 구현



1. scr>main>composer > `register_user_composite.py` 생성

2. **controller객체 반환 Type을 위한 interface인 RouteInterface import후 `_composer()메서드` 구현 **

   ```python
   from src.main.interface import RouteInterface
   
   def register_user_composer() -> RouteInterface:
       """ Composing Register User Route
       :param - None
       :return - Object with Register User Route
       """
   ```

   

3. repo -> usecase -> controller 객체 순서대로 생성후, **controller객체 == route객체 반환**

   - usecase객체는 변수를 **중간변수로서 일반명사**인  `use_case`로 받자
   - controller객체는 **`개별use_case_route`로 받자**

   ```python
   from src.main.interface import RouteInterface
   from src.data.register_user import RegisterUser
   from src.infra.repo.user_repository import UserRepository
   from src.presenters.controllers import RegisterUserController
   
   
   def register_user_composer() -> RouteInterface:
       """ Composing Register User Route
       :param - None
       :return - Object with Register User Route
       """
   
       user_repository = UserRepository()
       use_case = RegisterUser(user_repository)
       register_user_route = RegisterUserController(use_case)
   
       return register_user_route
   
   ```

   

#### 생성된 usecase_controller(route)객체 반환하는 composer메서드를 init에 올리기

```python
from .register_user_composite import register_user_composer
```



## 4 routes 구현 전에 Framework 설치후 app객체 생성

### 01 frame work(Flask=1.1.2)부터 설치 (2.0아직 안넘어가기)

1. 가상환경 활성화 상태에서

   ```
   pip3 install flask==1.1.2
   ```

   

### 02 framework마다 extention으로 -Cors 설치

- Cross Origin Resource Sharing (CORS)

- 위와 같이 하면 전체 url 에 대해서 CORS 가 적용되어 **다른 도메인에서 해당 url 을 호출해서 사용하는데 문제가 없습니다**.

  ```
  pip3 install Flask-Cors
  ```

  

### 02 main>configs에 app.py 생성 후 app객체 생성후 CORS ext으로 초기화

```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app)
```



### 03 app객체(변수)를 configs의 init에 올리기

- my) class, method뿐만 아니라, `app`이라는 `변수(객체)`도 import할 수 있구나

```python
from .app import app
```











## 5 routes > api_route.py에서 route등록객체 생성



### 01 프레임워크(flask)의 route등록객체 모듈(Blueprint) + 우리의 route객체 반환메서드(composer) 를 import한다



#### 프레임웤의 route등록객체(Blueprint)를 생성한다. name은 "api_routes"로 준다. (prefix아님)

- fastapi는 Blueprint 대신 APIRouter를 쓴다.
  - [참고 사이트](https://testdriven.io/blog/moving-from-flask-to-fastapi/)

```python
from flask import Blueprint
from src.main.composer import register_user_composer

api_routes_bp = Blueprint("api_routes", __name__)

```



#### 프레임웤의 dict->json 역직렬화 모듈(jsonify)를 import하고 예제route를 route등록객체를 통해 생성한다

```python
from flask import Blueprint, jsonify
from src.main.composer import register_user_composer

api_routes_bp = Blueprint("api_routes", __name__)


@api_routes_bp.route("/api", methods=["GET"])
def something():
    """ test """
    
    return jsonify({"Programmer": "Dolbum"})
```



#### 예제 route가 작성된 route등록 객체를 init에 등록한다

- app객체, route등록객체 모두 **객체(변수)단위로 init에 올린다.**

```python
from .api_route import api_routes_bp
```





### 02 올려놓은 route등록객체를 main>configs>app.py에 있는 app객체에 등록한다. (모든 것은 app에 등록)

```python
from flask import Flask
from flask_cors import CORS
from src.main.routes import api_routes_bp

app = Flask(__name__)
CORS(app)

app.register_blueprint(api_routes_bp)
```



## 6 root에 run.py 정의 후 모든 것이 초기화된 app객체를 main or [환경변수+flask run]으로 실행



### 01 root에 run.py 생성 후 app객체 import하여 app.run

![image-20221006221607127](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006221607127.png)



```python
from src.main.configs import app

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```



### 02 terminal에서 run.py로 app.run시키기

```
python run.py
```



#### flask 1.1.2 버전문제 -> jin

- 에러로 인해 `flask 업데이트` or `jinja2<3.1.0 / werkzeug==2.0.3 / itsdangerous==2.0.1`으로 다운그레이드

  ```
  ImportError: cannot import name 'escape' from 'jinja2'
  ```

```
pip3 install --upgrade jinja2<3.1.0

pip3 install --upgrade werkzeug==2.0.3

pip3 install --upgrade itsdangerous==2.0.1
```



#### Flask app객체를 src.main.configs.app에 있는 것을 lazy loading한다고 뜬다.



![image-20221006222859554](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006222859554.png)



### 03 환경변수에 FLASK_APP= 에 [app객체 import한 python파일명]을 명시하면 lazy loading을 제거한다. 

#### python run.py 대신 환경변수 FLASK_APP=[app객체가 import되어있는 python파일]을 명시하면, main으로 인한 실행 && lazyloading없이 app discovery에 의해 빠르게 로딩된다.

- **venv 가상환경에 의해 환경변수가 저장**된다.
  - **Windows Powershell, `venv\Scripts\activate.ps1`에 환경변수가 올라간다**
- **`python run.py`로 직접실행(lazy loading)시키려면 `__main__`이어하먄 했다**
  - **환경변수에 의한 `flask run`실행은 app객체를 import해놓으면 알아서 탐지해서 실행된다.** 
  - 환경변수에 의한 실행은 `__main__ -> app.run(host=,port=)`가 필요없다

- **venv에 환경변수 등록해놓으면 deactivate했다 들어와도 살아있다.**



```powershell
$env:FLASK_APP = "run"
#export FLASK_APP=run

$env:FLASK_DEBUG = "1"
#export FLASK_DEBUG=1

Get-ChildItem Env:
#export

flask run
```

![image-20221006230145174](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006230145174.png)



## 7 local run api를 POSTMAN으로 테스트

### 01 실행중인 url을 붙여넣고, api uri는 src>main>routes>api_route.py의 route등록객체를 보고 확인해서 접속한다

![image-20221006230833856](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006230833856.png)



![image-20221006230842123](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006230842123.png)

![image-20221006230852374](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006230852374.png)

![image-20221006230929493](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006230929493.png)

### 02 여기까지를 first composer 1개 + flask setting으로 commit한다

```
git commit -am "feat: Implementing first composer and setting Flask"
```





## 참고

- [Robert C. Martin (Uncle Bob)의 Clean Code Blog](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [용어 참고 블로그 1편](https://velog.io/@jahoy/Python%EC%9C%BC%EB%A1%9C-Clean-Architecture-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)
- [용어 참고 블로그 2편](https://velog.io/@jahoy/Python%EC%9C%BC%EB%A1%9C-%ED%81%B4%EB%A6%B0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B02)

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



- 계층별 변수정리
  - id : sqlalchemy -> **repo부터는** fk_id도 있으니 `pk_id`
  - name: sqlalchemy->repo->usecase까지는 그냥 name (선택인자 개별메서드에by_name)-> **controller부터는 외부에선 다른(pet)_name도 있으니 `pk_name`**



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




##### [1-3]  옛날에 따둔 br에서 작업시작할 땐, 최신 master를 rebase후에 작업시작하기

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

   



##### [2] 해당br 작업(테스트) 완료후 ~ merge까지 정리

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

   - **필수param(dict속 필드)이 빠졌을 경우(존재검증) or 필수param이 validate_entry실패할 경우**

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

   





#### powershell 환경변수 등록/확인

- python가상환경 venv에서 activated한 상태로 적용
  - `venv\Scripts\activate.ps1`에 저장된다.

1. 등록: 

   ```powershell
   $env:FLASK_APP = "hello"
   
   $env:FLASK_DEBUG = "1"
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


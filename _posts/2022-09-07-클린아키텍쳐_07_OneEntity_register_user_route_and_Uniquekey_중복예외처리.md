---
toc: true
layout: post
title: 클린아키텍쳐 07 OneEntity Register Route와 UniqueKey 중복예외 처리
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---


# 클린 아키텍쳐 7장



## 1 POSTMAN -> route + request모듈 -> POSTMAN으로 요청+응답 확인하기

### 01 flask의 request모듈로 route별 요청정보를 한번에 확인할 수 있다.

#### route에서 request모듈.args.to_dict()를 jsonfy에서 key와 함께 반환해주면, 어떤 정보를이 들어오는지 확인가능하다

1. request 모듈을 import하고 
2. `request.args.to_dict()`를 route에서 직렬화해서 반환해주도록 정의하고
3. `flask run`이후에
4. POSTMAN에서
   - Rquest- Params에서 query param을 입력해서 넘겨주면
   - Response - Body에서 resonse된 정보를 확인할 수 있다.

- **src>main>routes> api_route.py**

```python
from flask import Blueprint, jsonify, request
# from src.main.composer import register_user_composer

api_routes_bp = Blueprint("api_routes", __name__)


@api_routes_bp.route("/api", methods=["GET"])
def something():
    """test"""

    # return jsonify({"Programmer": "Dolbum"})
    return jsonify({"message": request.args.to_dict()})

```

```
flask run
```

![image-20221006235742915](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006235742915.png)

![image-20221006235802628](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006235802628.png)

![image-20221006235904614](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221006235904614.png)





## 2 usecase_controller별 route 추가

### 01 register user Route 추가

#### 001 함수명에 use_case를 작성해놓고 -> api명은 "/api/entity" 만 +  methods로 행위(register->POST) 나타내서 작성하기

```python
@api_routes_bp.route("/api/users", methods=["POST"])
def register_user():
    """ register user route """

    return 
```



#### 002 adapter 작성(flask request모듈 + composite route객체 만나서 결과반환 method)



##### flask request -> [ HttpRequest -> composite route객체(repo+usecase+controller를 조합 완성객체) -> HttpResponse ] -> return HttpResonse해주는 method

- adapter pattern to Flask는
  - **`프레임워크의 request`를 `바로 조합route객체의 input`로 받게 하는게 아니라**
  - **완충역할을 해줄 `커스텀 HttpRequest`로 변환해서 사용하는 과정**
    - **Composite route객체: 커스텀 HttpRequest를 input으로 가져서 `어느 프레임웤의 request든 HttpRequest로 변환하면 받아준다`**

##### src>main>adapter에 api_adapter.py 생성

![image-20221007001802260](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007001802260.png)



#### 003 flask_adapter 메서드 signatue 작성: [flask의 request]를 받아 -> HttpRequest로 변환하고 -> [composite route객체] 사용하도록 [2개의 직접적인 연결을 막는 장소이면서, 2개를 인자로 받는 장소]

1. `flask의 request`를 any type으로 + `usecase_route객체` 2개를 처리하기 위한 method로서 인자로 2개를 받는다.

   ```python
   from typing import Type
   from src.main.interface import RouteInterface
   
   
   def flask_adapter(request: any, api_route: Type[RouteInterface]) -> any:
       """ Adapter pattern to Flask
       :param - Flask Request
       :api_route: Composite Routes
       """
       return 
   ```

   

2. **Route`인터페이스는 Type용으로만 쓴다면` 편하게 `인터페이스는 삭제하고 as Route`로 변경해서 쓴다.**

   ```python
   from typing import Type
   from src.main.interface import RouteInterface as Route
   
   
   def flask_adapter(request: any, api_route: Type[Route]) -> any:
       """ Adapter pattern to Flask
       :param - Flask Request
       :api_route: Composite Routes
       """
       return 
   ```

   

#### 004 flask_adapter 메서드 구현부 작성

##### POST요청일 때의 flask의 request.json 확인해보기

1. flask의 `request`모듈의 **`request.json`은 POST요청 속 body를 dict로 자동으로 만들어준다.**

   - cf) GET요청 속 params들은 `request.args.to_dict()`해야 dict로 만들어줬었다.

   ![image-20221007005634213](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007005634213.png)



###### POSTMAN의 POST요청은 절대조건1) headear에 Content-Type: application/json 명시 + 2) body는 raw로 작성하기

![image-20221007010147636](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007010147636.png)

![image-20221007010158453](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007010158453.png)

###### post route추가하고 request.json 자체가 dict임을 인지할 것

![image-20221007010234258](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007010234258.png)

![image-20221007010250973](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007010250973.png)

##### request.json(raw Body -> Dict)를 HttpRequest의 body=인자로 넣어주면 객체 생성하여 사용하기

- HttpRequest의 3개 필드들은 Dict Type을 인자로 받는다.

  ![image-20221007010544424](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007010544424.png)

```python
from typing import Type
from src.main.interface import RouteInterface as Route
from src.presenters.helpers import HttpRequest, HttpResponse


def flask_adapter(request: any, api_route: Type[Route]) -> any:
    """ Adapter pattern to Flask
    :param - Flask Request
    :api_route: Composite Routes
    """
    http_request = HttpRequest(body=request.json)
    
    return

```

##### httprequest객체로 composite route객체 사용하여  response return

```python
from typing import Type
from src.main.interface import RouteInterface as Route
from src.presenters.helpers import HttpRequest, HttpResponse


def flask_adapter(request: any, api_route: Type[Route]) -> any:
    """ Adapter pattern to Flask
    :param - Flask Request
    :api_route: Composite Routes
    """
    http_request = HttpRequest(body=request.json)
    response = api_route.route(http_request)

    return response

```

- route객체 == controller객체 -> .route메서드로 

  - "Success"와 "Data"를 key로 가지는 HttpResponse객체를 반환한다.

  ![image-20221007014616163](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007014616163.png)





- 작성완료된 adapter method를 init에 올린다.

```python
from .api_adapter import flask_adapter
```



#### 005 api_route.py에서 작성완료된 flask adapter메서드와 해당usecase의 composer 메서드를 import해서 사용한다.



##### flask.request + composite.route객체를 연결해주는 adapter메서드와 해당usecase의 composite route객체를 반환해주는 composer method를 import해서 사용한다.

- composer는 **repo + usecase + controller객체를 다 생성후 합성해서 반환해줘야하는 `method()`이다.**

```python
#...
from src.main.adapter import flask_adapter
from src.main.composer import register_user_composer

api_routes_bp = Blueprint("api_routes", __name__)

#...

@api_routes_bp.route("/api/users", methods=["POST"])
def register_user():    
    """ register user route """

    message = None
    response = flask_adapter(request=request, api_route=register_user_composer())

    return 
```



##### flask_adapter가 반환해주는 HttpResponse객체를 controller에서 다시 한번 살펴보기

- HttpRequest는 POST용-body필드 / GET용-query필드를 각각 뒤지지만

- **HttpResponse는 `어떤 method든`결국 `body=`에 use_case결과의 `"Data"`가 들어간다.**

  - find

    ![image-20221007015904879](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007015904879.png)

  - register

    ![image-20221007015934723](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007015934723.png)

- fail시에는 **body=에 message가 들어간다.**

  ![image-20221007020005317](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007020005317.png)





#### 006 composite route객체의 결과물에는 status_code/body필드를 가지며, 성공과 실패가 있으니 => router에서는 성공과 실패를 구분해서 message로 반환한다

##### route는 = None초기화가 아니라, 빈 dict {}로 초기화해놓고, composite route객체 성공시에 채운다.

```python
@api_routes_bp.route("/api/users", methods=["POST"])
def register_user():    
    """ register user route """

    # message = None
    message = {}
    response = flask_adapter(request=request, api_route=register_user_composer())
```



##### 일단 성공시 message 재할당해서 채울 데이터를 만든다. 

1. **message에는 `"Type" : "entity(소문자)"`가 추가된다.**

2. **생성성공시 id부터 생성된 객체필드들을 `response.body`에서 뽑아쓰면 된다. `response(body=에 "Data"정보)`가 들어가 있기 때문이다.**

   - **adapter메서드는 inputType(여기서만Flaks request), outputType(여기서만 HttpResponse? 항상일 것 같지만?)의 Type이 안정해진 any로 사용하기 때문에, 결과물에 대한 필드 접근을 직접해야한다.**

3. **POST후 생성된 객체정보를 알고 싶으면 `src>models>`에서 DTO객체(namedTuple)의 필드명을 본다.**

   - password는 제외하고 응답보낼 것이다.

   ![image-20221007020732432](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007020732432.png)

4. **POST(register)후 `새롭게 배정된 id`는 따로 필드로 배정하지만, `post요청시 body에 들어온 정보는 attributes`로 한번 더 싸서 보낸다?!**



```python
@api_routes_bp.route("/api/users", methods=["POST"])
def register_user():
    """ register user route """

    # message = None
    message = {}
    response = flask_adapter(request=request, api_route=register_user_composer())

    message = {
        "Type": "users",
        "id": response.body.id,
        "attributes": {
            "name": response.body.name
            }
    }
```



##### 다 만들어진 message는 `jsonify`시 `소문자 "data"`를 key로 해서 value에 줘서 반환한다. 이 때, 튜플로서 response.status_code도 같이 보낸다.

```python
@api_routes_bp.route("/api/users", methods=["POST"])
def register_user():
    """ register user route """

    # message = None
    message = {}
    response = flask_adapter(request=request, api_route=register_user_composer())

    message = {
        "Type": "users",
        "id": response.body.id,
        "attributes": {
            "name": response.body.name
            }
    }

    return jsonify({"data": message}), response.status_code

```



#### 007 성공시 message만 먼저 작성완료했으면, POSTMAN으로 확인한다. -> status_code는 body우측상단에 따로확인  -> DB도 확인한다

1. POST로 수정

2. url 확인 `/api/users` 로 **행위없이 entity로만 끝내기**

   ![image-20221007021609472](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007021609472.png)

3. **POST로서 Headers에 컨텐츠 타입 작성**

   ![image-20221007021400718](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007021400718.png)

4. **Body는 raw로 json으로 작성**

   ![image-20221007021430123](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007021430123.png)

5. send

   ![image-20221007021928933](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007021928933.png)

6. **튜플로 보낸 status 코드는 POSTMAN body 우측상단에 보인다.**

   ![image-20221007022229156](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007022229156.png)

7. **DB도 확인한다.**

   ![image-20221007022056791](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007022056791.png)



#### 008 unique key인 name를 바꿔서 send보내면 성공하지만.. 다시보내면 예외가 터진다. 이미 가진 name에 대해  UNIQUE constraint failed -> IntegrityError의 예외가 터진다.

- jjs123 -> jjs1234

  ![image-20221007022146088](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007022146088.png)

  ![image-20221007022310742](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007022310742.png)

- 똑같은  name으로 한번더 SEND

  ![image-20221007022437167](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007022437167.png)

  ![image-20221007022558718](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007022558718.png)

  ![image-20221007022535965](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007022535965.png)





### 02 route에서 터지는 DB에러는 adapter_method에서 composite route객체.route메서드에서 잡는다.

#### 001 register(insert)에서는 unique key 필드를 SEND 2번으로 중복에러(존재검증)에러를 터트려서 잡는다.

#### 002 adapter method에서, terminal의 SQLAlchemy 에러를 import한다.

```
sqlalchemy.exc.IntegrityError: (sqlite3.IntegrityError) UNIQUE constraint failed: users.name
```

```python
#...
from sqlalchemy.exc import IntegrityError
#...

def flask_adapter(request: any, api_route: Type[Route]) -> any:
    #...
```

#### 003 route객체.route() 호출부를 [try ~except 해당에러]로 잡는다.

```python
def flask_adapter(request: any, api_route: Type[Route]) -> any:
    #...
    http_request = HttpRequest(body=request.json)
    try:
        response = api_route.route(http_request)
    except IntegrityError:
        pass

    return response
```



#### 004 except로 잡은 에러에 대해 HttpError를 인터넷에서 검색후 message를 가져오고, 해당 에러를 에러매핑 HttpErrors클래스의 static메서드로 추가한다.

- **중복에러는 [409 Conflict](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/409)에 해당한다**
- src>presenter>errors>http_errors.py의 HttpErrors class에 **매핑 static 클래스로 추가한다.**

```python
class HttpErrors:
    #...
    @staticmethod
    def error_409():
        """HTTP 409"""

        return {"status_code": 409, "body": {"error": "Conflict"}}
```



#### 005 except Error:부분에서 HttpErros 409를 뽑아내 -> 성공 결과물과 같은 Type의 HttpResponse객체를 만들고 early return해준다.

```python
def flask_adapter(request: any, api_route: Type[Route]) -> any:
    """ Adapter pattern to Flask
    :param - Flask Request
    :api_route: Composite Routes
    """
    http_request = HttpRequest(body=request.json)
    try:
        response = api_route.route(http_request)
    except IntegrityError:
        http_error = HttpErrors.error_409()
        return HttpResponse(
            status_code=http_error["status_code"],
            body=http_error["body"]
        )

    return response

```

- 참고 **HttpErros의 "body"에는 {"error" : "error_message"}의 dict가 들어가있음.**

  ![image-20221007163935039](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007163935039.png)





### 03 [등록 route]에서는 [예외처리 완료된 adapter method]결과인 [HttpResponse의 status_code를 따라] message를 처리



#### 001 status_code < 300미만이라면, 정상 response를 받는 것이다. 성공시 message를 그대로 사용

```python
@api_routes_bp.route("/api/users", methods=["POST"])
def register_user():
    """ register user route """

    message = {}
    response = flask_adapter(request=request, api_route=register_user_composer())

    if response.status_code < 300:
        message = {
            "Type": "users",
            "id": response.body.id,
            "attributes": {
                "name": response.body.name
                }
        }
        return jsonify({"data": message}), response.status_code
        
    # Handing Errors
    return 
```



#### 002  200번대가 아니라면 jsonify하는 key value가 달라진다( 기존: "data": message  -> "error" :{ "statue":status_code, "title":error_message}

```python

@api_routes_bp.route("/api/users", methods=["POST"])
def register_user():
    """ register user route """

    message = {}
    response = flask_adapter(request=request, api_route=register_user_composer())

    if response.status_code < 300:
        message = {
            "Type": "users",
            "id": response.body.id,
            "attributes": {
                "name": response.body.name
            }
        }
        return jsonify({"data": message}), response.status_code

    # Handing Errors
    return jsonify({
        "error": {
            "status": response.status_code,
            "title": response.body["error"],
        }
    }), response.status_code
```

![image-20221007165408175](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007165408175.png)



#### 003 router의 <300 처리는 Headers 없는 POST요청(no body)도 400 error로 내려준다

- postman no headers ->

- flask request.json -> None

- **usecase controller class**의  route method -> 

  - no body(reqeuest.json)로 인한 HttpErros 400 생성

    3. httpresponse {'status_code': 400, 'body': {'error': 'Bad request'}}

    ![image-20221007170747881](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007170747881.png)

  3. cf) route객체인 composer는 객체 조합해서 생성 후 객체 반환하는 메서드라 로직이 없음.

3. route 300미만시 error로 처리

![image-20221007170605687](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007170605687.png)



#### 004 route < 300처리는 필수param 없는 요청(or validate_entry통과못하면)도 422 에러로 내려준다.

- headers는 주고(nobody 400 해결), **unique key이자 필수 param인 name없이 줘보자.**

  ![image-20221007171152068](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007171152068.png)

  ![image-20221007171203026](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007171203026.png)

- **`useacse controller route메서드`에서, `필수 param 존재검증(or validate_entry)`시 통과못하면 422에러를 냈다.**

  ![image-20221007171351206](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007171351206.png)

  ![image-20221007171241972](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221007171241972.png)

  





### commit

```
git commit -am "feat: implementing register_use Route with flask"
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


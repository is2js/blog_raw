---
toc: true
layout: post
title: 클린아키텍쳐 10 Register를 통한 Find route구현 -> OneEntity의 Route들 구현
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---

# 클린 아키텍쳐 9장



## 1 Find OneEntity/ManyEntity 동시에 route 추가 - find user

### 01 composer 메서드부터 작성

- framework adapter메서드가 작성된 상태에서 필요한 것은 usecase별 route(controller)객체 
- **find MayEntity는 딱히 fk_repo 및 fk_usecase는 필요없다**



1. src>main> composer > `find_user_composite.py` 생성

2. controller객체(route객체)완성까지 필요한 것들  import

   ```python
   from src.infra.repo.user_repository import UserRepository
   from src.data.find_user import FindUser
   from src.main.interface.route import RouteInterface
   from src.presenters.controllers import FindUserController
   
   def find_user_composer() -> RouteInterface:
       """Composing Find User Route
       :param - None
       :return - Object with Find User Route
       """
   
       repository = UserRepository()
       use_case = FindUser(repository)
       find_user_route = FindUserController(use_case)
   
       return find_user_route
   
   ```

3. src>main> composer > `find_pet_composite.py` 생성후 **위에 작성한 것 복붙하여 수정**하여 똑같이 작업

   ```python
   from src.infra.repo.pet_repository import PetRepository
   from src.data.find_pet import FindPet
   from src.main.interface.route import RouteInterface
   from src.presenters.controllers import FindPetController
   
   
   def find_pet_composer() -> RouteInterface:
       """Composing Find Pet Route
       :param - None
       :return - Object with Find Pet Route
       """
   
       repository = PetRepository()
       use_case = FindPet(repository)
       find_pet_route = FindPetController(use_case)
   
       return find_pet_route
   
   ```

   

4. 둘다 init에 올리기

   ```python
   #...
   from .find_user_composite import find_user_composer
   from .find_pet_composite import find_pet_composer
   ```

   

### 02 route 작성

#### find pet route부터 작성 -> methods 변경에 따른 adapter request처리 필요

1. **find route도 기존route를 복사해서 작성한다**

2. 함수명에 usecase  / url에는 entity만 / methods에 행위만

   ```python
   @api_routes_bp.route("/api/pets", methods=["GET"])
   def find_pet():
       """find pet route"""
   ```

3. composer 메서드 import하여 adapter 입력해주기

   ```python
   @api_routes_bp.route("/api/pets", methods=["GET"])
   def find_pet():
       """find pet route"""
   
       message = {}
       response = flask_adapter(request=request, api_route=find_pet_composer())
   ```



#### 현재 flask_adapter 메서드는 POST(register) request만 처리 중

![image-20221008232106781](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008232106781.png)



## 2 methods추가에 따른 adapter method 구현부 수정

### 01 register를 위한 POST request(.json)뿐만 아니라 find를 위한 GET 방식입력시 request.args.to_dict() 처리

#### 001 일단, POST로 보내면 request.args는 비어있고, request.json만 차있다.

![image-20221009000743835](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009000743835.png)

![image-20221009000819974](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009000819974.png)

#### 002 POST일때도 request.args.to_dict()나, to_dict()한 것을 params로 받아 .keys()를 해도 비어있는 dict로 상관없다

![image-20221009001116249](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009001116249.png)



#### 002 GET방식에 대비하여 request.args.to_dict()를 query_string_params로 받아놓고, 모든 entity_id를 존재를 확인하여 존재시 int로 convert한다(혹시나 find시 id를 문자열로 입력한 것에 대비한다)

```python
def flask_adapter(request: any, api_route: Type[Route]) -> any:
    """Adapter pattern to Flask
    :param - Flask Request
    :api_route: Composite Routes
    """

    query_string_params = request.args.to_dict()

    if "pet_id" in query_string_params.keys():
        query_string_params["pet_id"] = int(query_string_params["pet_id"])

    if "user_id" in query_string_params.keys():
        query_string_params["user_id"] = int(query_string_params["user_id"])

    http_request = HttpRequest(body=request.json)
    
    try:
        response = api_route.route(http_request)
    except IntegrityError:
        http_error = HttpErrors.error_409()
        return HttpResponse(
            status_code=http_error["status_code"], body=http_error["body"]
        )

    return response
```



#### 003 route를 요청하지 않은 상태지만. [try] 필수param의 id속성의 형변환이 에러가 난다면, [except] 400에러를 내서 early return한다

```python
def flask_adapter(request: any, api_route: Type[Route]) -> any:
    """Adapter pattern to Flask
    :param - Flask Request
    :api_route: Composite Routes
    """
    try:
        query_string_params = request.args.to_dict()

        if "pet_id" in query_string_params.keys():
            query_string_params["pet_id"] = int(query_string_params["pet_id"])

        if "user_id" in query_string_params.keys():
            query_string_params["user_id"] = int(query_string_params["user_id"])
    except:
        http_error = HttpErrors.error_400()
        return HttpResponse(
            status_code=http_error["status_code"], body=http_error["body"]
        )
```



- 구체적인 except를 지정하지 않아서 경고가 뜨는데, 이것은 실제 요청하면서 발견하여 넣어줄 예정이다.

  ![image-20221009004135197](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009004135197.png)



#### 004 HttpRequest에는 POST든, GET이든 일단 선택형인자로서 body=, query=가 둘다 들어간다. 비어있어도 상관없다. 또한 flask request.headers => header=인자에 넣어주면 된다.

```python
def flask_adapter(request: any, api_route: Type[Route]) -> any:
    #...
    try:
        query_string_params = request.args.to_dict()
		#...

	http_request = HttpRequest(header=request.headers, body=request.json, query=query_string_params)

```



## 3 GET방식 find Route의 message는 POST방식 register message의 복수형이다

### 01 register의 response.body에는 controller 결과 나온 DTO 1개만 "Data"로 들어갔지만, find의 reponse에는 List[EntityModel]의 "Data"가 "body"로 들어갔다. 

- jsonapi에서도 **여러개를 반환할 때는, "data"에  message value를 넣을 때 list로 각 entity결과물을 건네준다.**

  ![image-20221009005946581](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009005946581.png)

#### 001 GET route의 route객체 reposonse.body는 LIST이므로, <300 성공시, response.body를 순회하며 message = []에 여러개의 정보를 append해야한다.

![image-20221009005224105](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009005224105.png)

#### 002 POST의 message 내용만 잘라내서, list(response body) 순회하며 append할 준비를 한다.

![image-20221009005315694](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009005315694.png)

![image-20221009005437698](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009005437698.png)

```python
@api_routes_bp.route("/api/pets", methods=["GET"])
def find_pet():
    """find pet route"""

    message = {}
    response = flask_adapter(request=request, api_route=find_pet_composer())

    if response.status_code < 300:
        # in GET(find) response.body is List[DTO]
        message = []
        for element in response.body:
            message.append(
                {
                    "type": "pets",
                    "id": element.id,
                    "attributes": {
                        "name": element.name,
                        "specie": element.specie,
                        "age": element.age,
                    },
                    "relationships": {"owner": {"type": "users", "id": element.user_id}},
                }
            )

        return jsonify({"data": message}), response.status_code

    # Handing Errors
    return (
        jsonify(
            {
                "error": {
                    "status": response.status_code,
                    "title": response.body["error"],
                }
            }
        ),
        response.status_code,
    )
```



### 02 route return "data"에 list를 반환하는지 POSTMAN으로 확인하기

1. GET방식이므로 headers를 지우고, Params에 조회할 id를 입력한다.

   - 외부이므로  `entity_id`로 입력

   - db에서 확인

     ![image-20221009010446941](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009010446941.png)

   ![image-20221009010945333](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009010945333.png)

   ![image-20221009011716642](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009011716642.png)

### 03 Enum에 의한 직렬화 에러

![image-20221009011718106](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009011718106.png)

#### register시에는 repo에서 [enum.value]의 값으로 만든 DTO를 반환해주지만, find시에는 [순수enum]을 가진 EntityModel객체를 반환해주는 괴리

- register시 작동하는 repo.insert -> **enum 속 값을 .value로 뽑아서 보내준다.**

  ![image-20221009011307170](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009011307170.png)

- find시 작동하는 repo.select -> **enum 그대로를 가진 model객체를 보내준다.**

  ![image-20221009011400620](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009011400620.png)



#### register시 반환은 말그대로 등록완료를 보여주기 위한 DTO를 반환하지만, find는 use_case들에서 내부로직이 작동할 수 있어서, DTO로 반환하면 안된다.



### 04 find시 response.body에 반환되는 것들은 List[EntityModel]이니, route에서 직접 enum.value로 뽑아주자.(규칙)

![image-20221009012457418](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009012457418.png)

```python
@api_routes_bp.route("/api/pets", methods=["GET"])
def find_pet():
    #...

    if response.status_code < 300:
        # in GET(find) response.body is List[EntityModel] -> use enum.value 
        message = []
        for element in response.body:
            message.append(
                {
                    "type": "pets",
                    "id": element.id,
                    "attributes": {
                        "name": element.name,
                        "specie": element.specie.value,
                        "age": element.age,
                    },
                    "relationships": {"owner": {"type": "users", "id": element.user_id}},
                }
            )

        return jsonify({"data": message}), response.status_code

   #...
```





#### pk인 pet_id로  1개 조회 -> list로 반환

![image-20221009012616442](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009012616442.png)





#### fk인 user_id로 여러개 조회 -> list로 반환

![image-20221009012946912](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009012946912.png)



![image-20221009013007868](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009013007868.png)



#### fk에 대해서는 없는 데이터에 대해 -> 존재검증 없이 빈list 반환 by .all()



- fk조회시 데이터 없으면 빈list 반환

  ![image-20221009013143835](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009013143835.png)

#### pk에 대해서는 .one() 로 조회하며, 없는 데이터에 대해 NoResultFound에러가 난다.

![image-20221009014058912](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009014058912.png)



#### pk에 대해서 .first()로 없는 데이터를 조회하면? -> 에러없이 None으로반환되어 진행되며 필드(.id)를 뽑을 때 attributeError가 난다

![image-20221009014242418](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009014242418.png)



## 4 find route에 대해 repo의 select시 pk가 껴있는 로직은 .one()시 NoResultFound 예외처리해야한다.

### 01 repo들의 select시 pk로 조회로직은 복수를 기대하여 .all()로 조회하며, 데이터가 없어도 에러가 안나고 빈list가 내려오지만, pk가 포함된 조회는 .one()으로 조회하며 NoResultFound 에러가 난다.

![image-20221009013914987](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009013914987.png)

```python
sqlalchemy.exc.NoResultFound: No row was found when one was required
```



### 02 repo들마다 select_xxx()메서드에 NoResultFound예외처리하자

#### 나머지 except은 rollback하도록 그대로 두고, except NoResultFound만 빈 []를 반환하도록 한다.

1. repo마다 `.orm.exc`패키지에서 `NoResultFound` import

   ```python
   from sqlalchemy.orm.exc import NoResultFound
   ```

   

2. select메서드로 가서 **except구문을 추가한 뒤 NoResultFound를 잡아서 빈 list `[]`를 반환하자**

   ```python
   except NoResultFound:
       return []
   
   except:
       db_connection.session.rollback()
       raise
       finally:
           db_connection.session.close()
   ```

   



#### 이제 다시 없는 pk로 find해보자( select -> .one() -> NoResultFound -> return [])

![image-20221009023436515](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009023436515.png)

## 5 find_pet route를 복사해서 find_user  route 만들기

### 단일Entity Find시에는 relationships 속성을 지우고 message를 만든다.

![image-20221009023721569](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009023721569.png)

```python
@api_routes_bp.route("/api/users", methods=["GET"])
def find_user():
    """find user route"""

    response = flask_adapter(request=request, api_route=find_user_composer())

    if response.status_code < 300:
        # in GET(find) response.body is List[EntityModel] -> use enum.value
        message = []
        for element in response.body:
            message.append(
                {
                    "type": "users",
                    "id": element.id,
                    "attributes": {
                        "name": element.name,
                    }
                }
            )

        return jsonify({"data": message}), response.status_code

    # Handing Errors
    return (
        jsonify(
            {
                "error": {
                    "status": response.status_code,
                    "title": response.body["error"],
                }
            }
        ),
        response.status_code,
    )
```

### postman으로 조회

#### pk로 조회



![image-20221009023913795](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009023913795.png)



#### unique key인 name으로 조회 -> 실패 -> 필드명과 달라도 user_name으로 조회

![image-20221009024000315](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009024000315.png)

![image-20221009024032089](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009024032089.png)



##### 필드명은 name이지만, 다른entity도 nam을 가질 경우, controller에서 entity_name으로 처리했을 것임

- **id는 repo인자부터** fk때문에 `entity_id`
- **name등 고유명사는 controller인자부터**  외부인자랑 겹치므로 `entity_name`

![image-20221009024210204](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009024210204.png)





#### 없는 pk로 조회 -> one -> 빈 list가 올 것임

![image-20221009024605907](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009024605907.png)



## 6 adapter에서 발견하지 못한 에러들에 대해 except 500 처리

### adapter에서 잡는 에러는 현재 GET시 id형변환 실패에 대한 400에러 +  POST시 route메서드 호출결과 uniquekey(name)중복에 대한 409에러 등 request에 대한 사용자에러 밖에 없다

![image-20221009025030110](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009025030110.png)

### adapter에서 route객체의 .route method호출 후 409(중복검증)외에 모든 실패를 500으로 잡도록 except를 추가한다.

![image-20221009025224172](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009025224172.png)



```python

def flask_adapter(request: any, api_route: Type[Route]) -> any:
    #...

    try:
        response = api_route.route(http_request)
    except IntegrityError:
        http_error = HttpErrors.error_409()
        return HttpResponse(
            status_code=http_error["status_code"], body=http_error["body"]
        )
    except :
        http_error = HttpErrors.error_500()
        return HttpResponse(
            status_code=http_error["status_code"], body=http_error["body"]
        )
    return response
```



### HttpErrors에 500을 추가한다

```python
    @staticmethod
    def error_500():
        """HTTP 500"""

        return {"status_code": 500, "body": {"error": "Internal Server Error"}}

```



### adapter에서 예기치 못한 500에러에 대한 출력을 추가한다

```python
    except Exception as exc:
        print(exc)
        http_error = HttpErrors.error_500()
        return HttpResponse(
            status_code=http_error["status_code"], body=http_error["body"]
        )
```





### commit -> push

```
git commit -am "feat: implementing find_pet and find_user Routes"
```







## 참고

- [Robert C. Martin (Uncle Bob)의 Clean Code Blog](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
  - [PDF-free-Clean Architectures in Python](https://leanpub.com/clean-architectures-in-python)
- [용어 참고 블로그 1편](https://velog.io/@jahoy/Python%EC%9C%BC%EB%A1%9C-Clean-Architecture-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)
- [용어 참고 블로그 2편](https://velog.io/@jahoy/Python%EC%9C%BC%EB%A1%9C-%ED%81%B4%EB%A6%B0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B02)
- [fasapi vs flask](https://testdriven.io/blog/fastapi-crud/#postgres-setup)

- jsonapi 예제문서
  - https://jsonapi.org/format/#document-top-level
  - https://jsonapi.org/format/#crud-creating-responses
- vscode flask 디버깅 세팅 문서
  - https://code.visualstudio.com/docs/python/debugging#_flask-debugging





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
  
  - .orm.exc 패키지
  
    - **NoResultFound**
  
      - **repo에서** pk로 select시 .one()으로 1개만 찾을 때, 없으면 발생하는 예외
  
      - 따로 except를 잡아서 빈list를 반환(select는 원래 List반환)
  
        ![image-20221009023057244](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009023057244.png)
- .exc 패키지(exception):
  
  - **IntegrityError**: 
  
    - **adapter에서 처리**하며 register시 unique key 중복시 나는 에러
  
      - repo에서는 그냥 raise만 올려놓고, adapter에서 잡더라
  
        ![image-20221009014640656](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009014640656.png)



- 계층별 변수정리
  - id : sqlalchemy -> **repo 인자부터는** fk_id도 있으니 `pk_id`
  - name: sqlalchemy->repo->usecase까지는 그냥 name (선택인자 개별메서드에by_name)-> **controller 인자부터는 외부에선 다른(pet)_name도 있으니 `fk_name`**
    
    - **OneEntity의 경우 그냥 controller에서도 `name`만**
    
    
  
- **route 조심해야할 것**

  - register route는 response.body에 controller response["Data"]에 있던 **DTO가 반환**

  - **find route**는 response.body에 controller response["Data"]에 있던 List**EntityModel객체 반환**되니 **message 작성시 enum필드에 대해 .value까지 해서 보내기**

    

- repo 조심해야할 것

  - **select repo**는 
    - fk에 대해서는 항상 **.all()로 조회**해서 **없어도 빈list반환하며 에러안남**
    - **pk or unique key에 대해서는 `.one()`으로 조회하므로 `없을 때는 NoResultFound`가 나니 예외처리 필수**



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

     - use_case_controller

       ```python
       if reponse["Success"] is False:
       ```

       

2. `400`: query속 param들이 필요한 controller인데 http_request안에 아예 존재도 안했다면, 400 Bad Request에러 + 필수param(id)의 형변환 실패시

   - **http_request에 필수param을 포함한 .query or .body가 아예 날라오지도 않았을 경우**

     - use_case_controller

       ```python
       if http_request.query:
       	# if query
       # if not query
       else:
       ```

       ```python
       if http_request.body:
       	# if body
       # if not body
       else:
       ```

   - **필수param(id)의 형변환을 실패했을 때**

     - flask_adapter

       ```python
       try:
           query_string_params = request.args.to_dict()
       
           if "pet_id" in query_string_params.keys():
               query_string_params["pet_id"] = int(query_string_params["pet_id"])
       
           if "user_id" in query_string_params.keys():
               query_string_params["user_id"] = int(query_string_params["user_id"])
       except:
           http_error = HttpErrors.error_400()
           return HttpResponse(
               status_code=http_error["status_code"], body=http_error["body"]
           )
       
       ```

       

3. `409`:  타겟 자원에 상태에 요청이 conflict를 유발한 경우

   - **register(insert) POST 요청에 대해 unique key(name)이 중복된 send가 날아와, db에서 unique constraint == 중복에러(IntegrityError)를 발생한 경우**

     ```
     POSTMAND에서 생성요청을 같은name를 가진 body로 2번 요청했을 때
     ```

     - flask_adapter

       ```python
       try:
           response = api_route.route(http_request)
           except IntegrityError:
               http_error = HttpErrors.error_409()
               return HttpResponse(
                   status_code=http_error["status_code"], body=http_error["body"]
               )
       ```

       

   ```
   
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


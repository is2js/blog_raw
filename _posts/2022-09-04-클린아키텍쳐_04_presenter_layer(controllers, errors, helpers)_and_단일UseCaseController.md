---
toc: true
layout: post
title: 클린아키텍쳐 04 presenter layer와 OneEntity UseCaseController
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---


# 클린 아키텍쳐 4장



## 1 presenters layer에서 usecase controller 만들기

### 01 이번부터는 master에서 바로작업(-b없이 checkout후 git pull)

```
git branch
* feat/registry_pet

git checkout master
Switched to a new branch 'master'
Branch 'master' set up to track remote branch 'master' from 'origin'.

git branch
  feat/registry_pet
* master

git pull
Already up to date.
```



### 02 controller도입시부터 src>presenters(폴더+init)을 먼저 만들고, 내부에 controllers, helpers, erros + 각각의 init을 만든다.

![image-20221004165242326](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221004165242326.png)



### 03 helpers >  http_models.py를 먼저 만든다.

#### HttpRequest class

##### 생성자에 header Dict, body Dict, query Dict가 **생성자의 선택형kwargs인자**로  오며 필드로 가진다. repr도 같이 정의해준다.

```python
from typing import Dict


class HttpRequest:
    """ Class to http_request representation """

    def __init__(self, header: Dict = None, body: Dict = None, query: Dict = None) -> None:
        self.header = header
        self.body = body
        self.query = query

    def __repr__(self):
        return f"HttpRequest (header={self.header}, body={self.body}, query={self.query})"

```



#### HttpResponse class

##### 생성자에 status_code int, body any가 필수args로 오며 필드로 가진다.

```python
class HttpResponse:
    """ Class to http_response representation """

    def __init__(self, status_code: int, body: any) -> None:
        self.status_code = status_code
        self.body = body

    def __repr__(self):
        return f"HttpResponse (status_code={self.status_code}, body={self.body})"

```





#### init에 2개 class를 올려준다.

```python
from .http_models import HttpRequest, HttpResponse
```





### 04 controllers > use_case_controller.py를 만든다.

#### use_case별 controller.py -> UseCaseController class를 만든다.

##### usecase별controller는 usecase객체를 생성자인자로 받는다

1. `find_user_controller.py` 생성

2. **usecase class 객체를 생성자를 인자로 받는다**

   1. **usecase class 는 repo까지** 다 먹고 있는 service계층이다.
   2. **usecase인자의 Type[]에 interface를 src.domain.usecase에서 찾아서 주면된다.**
      - interface를 원래 class명과 같이 정의한데는 인자의 Type으로 주는데 있다.
      - **대신 실제 usecase class 정의시에만 as -Interface 로 import했었다.**

   ```python
   from typing import Type
   
   from src.domain.use_cases import FindUser
   
   
   class FindUserController:
       """ Class to define controller to find_user use case"""
   
       def __init__(self, find_user_use_case: Type[FindUser]) -> None:
           self.find_user_use_case = find_user_use_case
   ```

   

#### usecaseControllerr객체는 handle메서드를 가지며, http_request를 인자로 받고 http_response를 반환한다.

```python
from typing import Type

from src.domain.use_cases import FindUser
from src.presenters.helpers import HttpRequest, HttpResponse


class FindUserController:
    """ Class to define controller to find_user use case"""

    def __init__(self, find_user_use_case: Type[FindUser]) -> None:
        self.find_user_use_case = find_user_use_case

    def handle(self, http_request: Type[HttpRequest]) -> HttpResponse:
        """ Method to call use case """

```



#### handle메서드 인자 http_request 속 query(queryparam)에 따라 usecase객체의 호출method 경우의수가 달라진다. 

- 이것 때문에.. 더 상위에서도 query param이 갈라지기 때문에, use case에서도 나눠놨구나..
- sqlalchemy query.filter -> 선택형인자  1개 메서드 -> keyword별로 줘서 처리
  - repo select -> 선택형인자  1개메서드 -> if로 나눠서 하위1개메서드로 처리 (상위가 선택형인자 1개메서드면, 내부에서 if로 나누되, 하위도 선택형인자면, if안에 1개 메서드로 처리 된다.)
    - use case find -> **인자별로 method처리 -> repo는 내부에서 선택형인자로 if로 나누되, 1개 메서드로 처리**
      - use case find controller -> **사실상 quer param dict에서** 하나씩 확인해야하는 keys() 확인후 처리하는 **선택형인자** -> **if로 나눠서 처리** ->
        -  **use case find는 if상위 인자별로 개별 method 호출로서 -> 상위 if경우의수마다 정해진메서드 여러개로 개별처리**
          - 
        - 만약 use case find도 선택형인자로 1개메서드 처리했다면? -> 상위 if 경우의수마다 1개메서드로 처리
          - **use case는 `하위 repo메서드가 선택형인 것을 이용`해서, `내부 if를 나누지 않고 개별 메서드를 여러개 정의함`으로서 `상위가 if를 나누어서 편하게 쓰기`개별 정의된 메서드만 호출할 수 있게 편의를 제공했다.**
- my) select는 어쩔수 없이 repo->usecase->controller상위로 갈때까지 param 경우의 수를 계속 확인해야한다.
  - 만약, 중간의  usecase를 선택형인자 메서드1개로 처리하지 않고, 미리 경우의 수별 method를 정의해놓는다면,  usecase의 상위는 param경우의 수마다 개별메서드를 호출할 수 있다.
  - 만약, 선택형인자 메서드1개로 처리했다면, param경우의 수마다 선택형메서드 1개를 넣어서 호출하면 된다.
  - **왜 repo는 선택형인자 1개메소드로 처리하면서, usecase는 경우의수별 메서드를 따로 정의해놓을까???**
    - **sqlalchemy -> repo까지는 선택형으로 처리하더라도 `usecase는 controller에 사용될 service로서, 편의성 제공`을 해야할 것 같다.**

##### 의문점 해결: 선택형인자들이 들어와서 내려가도, 중간service usecase는 경우의 수별 메서드로 편의제공



##### HttpRequest속 query(param)에는 repo->sqlalchemy에 사용될 선택형인자가 담겨있다. [여기서는 select_all을 애초에 정의하지 않았으므로 메서드로 처리는 안했지만, query param없는 find도 있을 것이다.]

1. use case처럼 response = None으로 시작한다
   - **성공시만 해당하면 False시작(flag) or None시작(data)이다.**
   - 만약, 탈락조건 해당시만 처리되면 **True시작**이다.
2. query가 존재한다면, 선택형인자를 경우의수별로 확인하기 위해 query param dict를 기준으로 확인한다.
3. **DTO, entity Model만`sqlalchemy에 넣기 위한 id`라고 사용하고, `repo의 메서드`부터는 인자에 `user_id`를 사용한다.**
   - **`name의 일반명사는, sqlalchemy, repo, usecase의 메서드까지는 개별메서드구성을 위해 name`을 사용하되, `controller에서는 외부의 다른name도 있으니, user_name`으로 사용한다.**
   - 변수정리
     - id : sqlalchemy -> **repo부터는** fk_id도 있으니 `pk_id`
     - name: sqlalchemy->repo->usecase(선택인자개별메서드에by_name)-> **controller부터는 외부에선 다른(pet)_name도 있으니 `pk_name`**

3. controller로 오는 http_request.query 속 keys 경우의수별로 **service로서 인자 경우의수별로 개별제공되는 usecase의 method를 호출하여 response 재할당**

```python
class FindUserController:
    """ Class to define controller to find_user use case"""

    def __init__(self, find_user_use_case: Type[FindUser]) -> None:
        self.find_user_use_case = find_user_use_case

    def handle(self, http_request: Type[HttpRequest]) -> HttpResponse:
        """ Method to call use case """

        response = None

        if http_request.query:
            # If query

            query_string_params = http_request.query.keys()

            if "user_id" in query_string_params and "user_name" in query_string_params:
                user_id = http_request.query["user_id"]
                user_name = http_request.query["user_name"]
                response = self.find_user_use_case.by_id_and_name(
                    user_id=user_id, name=user_name
                )

            elif "user_id" not in query_string_params and "user_name" in query_string_params:
                user_name = http_request.query["user_name"]
                response = self.find_user_use_case.by_name(
                    name=user_name
                )

            elif "user_id" in query_string_params and "user_name" not in query_string_params:
                user_id = http_request.query["user_id"]
                response = self.find_user_use_case.by_id(
                    user_id=user_id
                )

            else:
                response = {'Success': False, "Data": None}

```



#### Find usecase의 결과response의  if response["Success"]가 is False인 경우 -> validate_entry False라면, controller에서는 status_code 422로 응답해줘야한다.

- http `422`: 이 응답은 서버가 요청을 이해하고 요청 문법도 올바르지만 요청된 지시를 따를 수 없음을 나타냅니다.

  - **파라미터 type에러에 걸릴 경우 validate_entry가 False가 되므로 이 경우 `422`로 응답한다.**

  ```python
  class FindUserController:
      #...
          if http_request.query:
              # If query
  
              query_string_params = http_request.query.keys()
  
              #...
  
              elif "user_id" in query_string_params and "user_name" not in query_string_params:
                  user_id = http_request.query["user_id"]
                  response = self.find_user_use_case.by_id(
                      user_id=user_id
                  )
  
              else:
                  response = {'Success': False, "Data": None}
  
              
              if response["Success"] is False:
                  # validate_entry False -> 422
  ```

  

### 05 errors > http_erros.py로 [error별 statue_code + body데이터 dict 반환] HttpErrors (static) class 정의하기

#### use_case_controller handle메서드 내 response "Success"가 False인 경우를 처리하기 위한 개별 HttpResponse를 만들어줘야한다. 그 내용물로 들어갈 [error용 status_code + body]를 dict로 묶어놓은 HttpErrors (static) class를 정의한다.

- my) `정해진 dict들` 매핑을 `errors.py`안에 `static메서드별로 매핑`해놓고, `Class.static메서드를 호출로 매핑dict를 반환`해서 꺼내쓰네?!



1. src> presenter > errors > `http_erros.py`를 생성
2. 각 에러별로 `@staticmethod`로 **객체 없이 클래스로 바로 호출하여 ` (error용 status_code, (error용)body를 dict`로 묶어서 반환해줄 method를 만든다.**
3. **body역시 개별dict이며, "error" key안에 "code별 message" 를 담는다.**

```python
class HttpErrors:
    """ Class to define errors in http """

    @staticmethod
    def error_422():
        """ HTTP 422 """

        return {"status_code": 422, "body": {"error": "Unprocessable Entity"}}

```

- init에 올려서 contoller들이 import해서 사용하기

```python
#...
from .http_errors import HttpErrors
```





- **cf) `목객체`도 정해진 형태의 데이터반환**이지만, **`1개의 랜덤데이터 반환`이므로 Static(method) Class로 정의하지 않고, 그냥 `메서드 1개 `로 **
  - **http erros는 여러 종류의 데이터들 반환 -> class로 묶어서 statiac 메서드별로 1개의 데이터씩 반환**





### 06 다시 handle controller로 복귀

#### if http_request.query가 있어서 usecase 메서드별 처리하고 response결과를 받았는데, response["Success"]가 valid인 경우 HttpErros를 이용해 HttpResponse객체 만들어서 early return

1. usecase 메서드의 반환이 Success가 False라면 validate_entry가 실패한 것-> 422 에러를 HttpErrors 스태틱 클래스에서 뽑아서 **HttpResoponse객체를 만들어 반환해준다.**

```python
#...
from src.presenters.errors import HttpErrors

class FindUserController:
    #...

    def handle(self, http_request: Type[HttpRequest]) -> HttpResponse:
        #...

        if http_request.query:
            #...

            elif "user_id" in query_string_params and "user_name" not in query_string_params:
                user_id = http_request.query["user_id"]
                response = self.find_user_use_case.by_id(
                    user_id=user_id
                )

            else:
                response = {'Success': False, "Data": None}

            if response["Success"] is False:
                http_error = HttpErrors.error_422()
                return HttpResponse(
                    status_code=http_error["status_code"], body=http_error["body"]
                )

```



#### if http_request.query가 있고,  response["Success"]가 False아니라면, [200 성공code + 개별 응답Data를 반환한다]

##### usecase는 validate_entry + 응답Data  -> controller는 status_code200 + 응답Data

- 아... usecase의 response["Success"]는 controller의 status_code=200이 아닌 422 상황 처리를 위함이었구나.
- 아... usecase의 **response["Data"]**야 말로, controller의 응답**body**가 되는 구나

```python
class FindUserController:
    #...
    def handle(self, http_request: Type[HttpRequest]) -> HttpResponse:
        #...
        response = None

        if http_request.query:
            #...

            elif "user_id" in query_string_params and "user_name" not in query_string_params:
                user_id = http_request.query["user_id"]
                response = self.find_user_use_case.by_id(
                    user_id=user_id
                )

            else:
                response = {'Success': False, "Data": None}

            if response["Success"] is False:
                http_error = HttpErrors.error_422()
                return HttpResponse(
                    status_code=http_error["status_code"], body=http_error["body"]
                )

            return HttpResponse(
                status_code=200, body=response["Data"]
            )
```





#### 적어도 select용 param이 있어야 하는 find_user controller에서 만큼은, http_request에  query가 없다면, 사실상 400에러

1. HttpErros static class에 400 에러 정의해주기

   - **query속 param들이 필요한 controller인데 아예 존재도 안했다면, 400 Bad Request에러**

   ```python
   class HttpErrors:
       #...
   
       @staticmethod
       def error_400():
           """ HTTP 400 """
   
           return {"status_code": 400, "body": {"error": "Bad request"}}
   ```



2. **if http.query -> ealry return 아래 -> `if not http.query 부분`**

   ```python
   class FindUserController:
       #...
       def handle(self, http_request: Type[HttpRequest]) -> HttpResponse:
           #...
           response = None
   
           if http_request.query:
               # if query
               if response["Success"] is False:
                   http_error = HttpErrors.error_422()
                   return HttpResponse(
                       status_code=http_error["status_code"], body=http_error["body"]
                   )
   
               return HttpResponse(
                   status_code=200, body=response["Data"]
               )
           
   
           # If no query in http request
           http_error = HttpErrors.error_400()
           return HttpResponse(
               status_code=http_error["status_code"], body=http_error["body"]
           )
   
   ```





#### 완성된 find_user_controller는 init 에  올려준다.

```python
from .find_user_controller import FindUserController
```





## 2  usecase controller test

1. src>presenter>controllers>`find_user_controller_test.py` 생성

2. test할 controller  + faker import

   ```python
   from faker import Faker
   from .find_user_controller import FindUserController
   ```

   

### 01 usecase Controller는 usecase를 인자로 받으니, test 사용할  usecaseSpy( 호출감지 = args보관+반환은mock )를 먼저 만들어야한다.

- 이전에는 **ManyEntity Register usecase**에서 fk정보에 대한 Fkentity에 존재하는지 유무를 usecase레벨에서 확인하기 위해 **FindFK UseCase를 인자**로 받아야해서 **FindFK(User)Spy**를 개발했었따.
  - **여기서는, usecase controller라면, 해당usecase를 인자로 받아야하기 때문에 UseCaseSpy를 생성한다**

- RegisterPet usecase를 test하기 위해 FK인 **FindUserSpy**를 이미 구현했으므로 import해서 사용한다
  - src>data>test>`find_user_spy`.py

```python
from faker import Faker
from .find_user_controller import FindUserController
from src.data.test import FindUserSpy

faker = Faker()

def test_handler():
    """ Testing Handle method """
    
```



### 02 UseCase Controller는 repo를 품고 있는 UseCase객체를 인자로 받는다.

#### 그러므로 repoSpy객체를 품는 UseCaseSpy객체를 생성해서 test할 Controller에 넣어준다.

- 참고: **ManyEntity의 Find는 FK_id value로 검색만 하지만, `ManyEntity Register`의 경우만, Fk객체를 UseCase FindFk를 써야하기 때문에 `자신useacse품는 Repo` + `FKusecase를 쓰기 위한, 품어야하는 Fk Repo` 2개가 필요하다**

  ![image-20221004234048437](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221004234048437.png)



1. usecase가 품을 repo spy 객체 -> controller가 품을 usecase Spy 객체를 생성하여 테스트할 controller 객체를 만든다.

   ```python
   def test_handler():
       """ Testing Handle method """
   
       find_user_use_case = FindUserSpy(UserRepositorySpy())
       find_user_controller = FindUserController(find_user_use_case)
       
   ```

   



### 03 Controller의 handle method 테스트는 HttpRequest의 선택형인자(headers, body, query) 중 query만 필요한 param을 넣어서한다.

#### usecase는 attributes dict -> method에 1개씩 뽑아서 할당해줬었다. controller부터는 method input이 [httprequest객체 by  query 속 필요한 param]들이다.

```python

def test_handler():
    """ Testing Handle method """

    find_user_use_case = FindUserSpy(UserRepositorySpy())
    find_user_controller = FindUserController(find_user_use_case)

    http_request = HttpRequest(
        query={"user_id": faker.random_number(), "user_name": faker.word()}
        )

    response = find_user_controller.handle(http_request)
```



### 04 input Test -> usecase Spy객체가 품고 있는 param_dict test

#### UseCaseSpy 역시 RepoSpy처럼, 개별메서드마다 param dict에 품고 있으니, input test는 UseCaseSpy객체.param_dict == http_request.query로 한다

![image-20221005001633572](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005001633572.png)



```python
def test_handler():
    """ Testing Handle method """

    find_user_use_case = FindUserSpy(UserRepositorySpy())
    find_user_controller = FindUserController(find_user_use_case)

    http_request = HttpRequest(
        query={"user_id": faker.random_number(), "user_name": faker.word()}
    )

    response = find_user_controller.handle(http_request)

    print(response)
    
    # Testing Inputs
    assert find_user_use_case.by_id_and_name_param["user_id"] == http_request.query["user_id"]
    assert find_user_use_case.by_id_and_name_param["user_name"] == http_request.query["user_name"]

```





#### input test만 끝나도 print(reponse)와 함께 일단 pytest -vs한다

```
pytest src\presenters\controllers -vs
```

![image-20221005002714887](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005002714887.png)

![image-20221005002637412](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005002637412.png)



##### input test 실패 분석

1. **controller가 UseCaseSpy를 품고 있지만, `Spy는 mock_dto()`를 통해 목객체를 반환하므로, `print상 찍힌 response속 Data: List[DTO]`는 잘 찍힌다.**

   ![image-20221005002912279](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005002912279.png)



2. **key_error인데, `UseCaseSpy는 name등의 id제외 일반명사는 그냥 인자로 사용(not user_name)`하고 있고, `Controller의 HttpRequest는 외부에서 user_name으로 일반명사에 entity명을 붙여서 사용`하고 있다.**

   - UseCaseSpy의 param dict는 일반명사 `name`을 key로 저장하기 때문에 바꿔준다.

   ```python
   # Testing Inputs
   assert find_user_use_case.by_id_and_name_param["user_id"] == http_request.query["user_id"]
   # user_name -> name in use case
   assert find_user_use_case.by_id_and_name_param["name"] == http_request.query["user_name"]
   
   ```

   ![image-20221005003829152](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005003829152.png)





### 05 OutputTest -> 

#### useCase의 output은 Dict[bool, List[Dto] or Dto] => Ouput test는 "Success" True와 "Data" is not None이었다.

![image-20221005004143865](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005004143865.png)

#### Controller output은 실패만 안한다면 HttpRequest(status_code=200, body=usecase의Resonse["Data"])

![image-20221005004439758](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005004439758.png)

##### Contoller Output test는 controller.handle()의 결과인 response(http_response)객체의 .status_code ==200, .body is Not None이다.

- usecase는 dict로 담아서 반환.
- controller는 HttpResponse객체에 필드로 담아서 반환

```python
# Testing Outputs
assert response.status_code == 200
assert response.body is not None
```

##### output test도 끝나면 pytest

```python
pytest src\presenters\controllers -vs
```



## 3 usecase controller fail test (no query param in HttpRequest)

### 01 no query param fail test는 성공test에서 HttpRequest객체 인자만 삭제하고 print(response)를 찍은 상태에서 테스트를 먼저 돌려보자.

1. 성공 test를 복사해서 **def** test_handle_no_query_param 만든다.

2.  HttpRequest의 query인자를 삭제하고 빈 객체를 만든다

3. **`print(response)를 포함한 상태로 pytest`를 돌려 에러를 내본다.**

   ```python
   def test_handler_no_query_param():
       """ Testing Handle method """
   
       find_user_use_case = FindUserSpy(UserRepositorySpy())
       find_user_controller = FindUserController(find_user_use_case)
   
       http_request = HttpRequest()
   ```

   ![image-20221005013332634](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005013332634.png)

   ![image-20221005013340723](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221005013340723.png)

#### response를 찍어서, 어떤 status_code를 확인해야할지 숙련안되었으면 확인해야한다



### 02 not query param Input test: spy속 모든 param dict == {} (빈 dict)

- 빈 dict는 비교되나보다.

  - **어떤 기본 자료구조든 객체없이 값으로만 구성되고 (seq는 순서까지), 비교된다.**

    ```python
    >>> {} == {}
    True
    
    >>> {1:"abc"} == {1:"abc"}
    True
    
     {1:"abc", 2:2} == {2:2, 1:"abc"}
    True
    ```

    

```python
def test_handler_no_query_param():
    """ Testing Handle method """

    find_user_use_case = FindUserSpy(UserRepositorySpy())
    find_user_controller = FindUserController(find_user_use_case)

    http_request = HttpRequest()

    response = find_user_controller.handle(http_request)

    print(response)

    # Testing Inputs
    assert find_user_use_case.by_id_param == {}
    assert find_user_use_case.by_name_param == {}
    assert find_user_use_case.by_id_and_name_param == {}
```



### 03 not query param Output test: .status_code == 400, .body -> code별 에러메세지가 "error" key에 있으니 key검사만 한다!

```python
    # Testing Outputs
    assert response.status_code == 400
    assert "error" in response.body
```



#### 참고로 validate_entry 실패는 status_code 422





#### pytest 돌리고 통과했으면 확인용 print(response) 삭제후 커밋

```
pytest src\presenters\controllers -vs
```

```
git status
git add .


git commit -am "feat: Implementing FindUserController"
git commit -am "feat: Implementing FindUserController"
```



#### master는 push후 merge안해줘도 된다.







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
   
   git add 
   
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

   ```
   if reponse["Success"] is False:
   ```

   

2. `400`: query속 param들이 필요한 controller인데 http_request안에 아예 존재도 안했다면, 400 Bad Request에러

   ```
   if http_request.query:
   	# if query
   # if not query
   ```

   
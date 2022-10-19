---
toc: true
layout: post
title: 클린아키텍쳐 12 jwt auth token handler와 decorator처리, dotenv 적용
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---

# 클린 아키텍쳐 12장



## 1 auth_jwt 패키지 및 token_handler모듈 속 TokenCreator Class 생성

### 01 src > auth_jwt 폴더 + init생성

![image-20221010152849836](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010152849836.png)

### 02 src > auth_jwt > token_handler 폴더 + init + token_creator.py 생성

![image-20221010153105055](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010153105055.png)



### 03 TokenCreator class정의

#### 001 token_key, exp_time_min,  refresh_time_min을 private필드로 받는다

- server만 알아야하는 정보는 클래스가 `self.__private`필드로 저장한다.

  - infra> config> db_hanlder.py > DBConnectionHanlder의 `self.__connection_string`

    ![image-20221010153504230](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010153504230.png)

```python
class TokenCreator:

    def __init__(self, token_key: str, exp_time_min: int, refresh_time_min: int):
        self.__TOKEN_KEY = token_key
        self.__EXP_TIME_MIN = exp_time_min
        self.__REFRES_TIME_MIN = refresh_time_min

```





## 2 TokenCreator에서 토큰 발급 대체



### 01 TokenCreator가 token발급하는 POST route의 jwt.encode로직을 대체

- private method로 서버작업 -> public method로 결과물만 공개

#### 001 server용 작업(token발급)만 수행하는 객체의 Class속 method는 필드뿐만 아니라 method도 인자(발급시 필요한 것은 현재사용자의 uid)는 받되 `__`private method로 정의한다.

- **발급시 외부에서 들어오는 인자는 현재사용자의 uid 뿐이다**

```python
class TokenCreator:

    def __init__(self, token_key: str, exp_time_min: int, refresh_time_min: int):
        self.__TOKEN_KEY = token_key
        self.__EXP_TIME_MIN = exp_time_min
        self.__REFRES_TIME_MIN = refresh_time_min

    def __encode_token(self, uid: int):
        pass

```



#### 002 서버작업인 token발급 POST route의 encode한 token결과물을 받는 과정을 TokenCreator의 `__encode_token`메서드에 위임한다

![image-20221010154421435](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010154421435.png)

```python
import jwt
from datetime import datetime, timedelta


class TokenCreator:

    def __init__(self, token_key: str, exp_time_min: int, refresh_time_min: int):
        self.__TOKEN_KEY = token_key
        self.__EXP_TIME_MIN = exp_time_min
        self.__REFRES_TIME_MIN = refresh_time_min

    def __encode_token(self, uid: int):
        token = jwt.encode({
            'exp': datetime.utcnow() + timedelta(minutes=30),
            'uid': uid
        }, key='1234', algorithm='HS256')

        return token
```



### 02 서버작업인 token발급을 private method로 했으면, [외부에서 결과물만 부를 수 있는 public method]도 정의해준다.

- **`비밀작업 -> 비밀결과물 반환 private메서드 정의`** 이후에는 **`비밀 결과물을 공개하는 public method`**정의가 순서이다



#### 001 private결과물을 받기 위한 public method(helper method?)는 인자는 동일하다

```python
class TokenCreator:

    def __init__(self, token_key: str, exp_time_min: int, refresh_time_min: int):
        self.__TOKEN_KEY = token_key
        self.__EXP_TIME_MIN = exp_time_min
        self.__REFRES_TIME_MIN = refresh_time_min

    def create(self, uid: int):
        return self.__encode_token(uid)

    def __encode_token(self, uid: int):
        token = jwt.encode({
            'exp': datetime.utcnow() + timedelta(minutes=30),
            'uid': uid
        }, key='1234', algorithm='HS256')

        return token

```





## 3 TokenCreator에서 토큰 refresh 추가

### 참고) datetime + timedelta로 token 'exp'를 채워 encode했지만 => decode하면 time.time()과 같은 float로 받아온다 => token 속 'exp'는 시간을 float로 형변환한 상태로 간주하고 datetime이 아닌 time.time()으로 처리한다.

- token 생성시

  ![image-20221010163030521](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010163030521.png)

- token 확인시

  ![image-20221010163007225](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010163007225.png)

![image-20221010162931495](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010162931495.png)



### REFRESH 개념: 만료기간이 더 짧은 기간으로 구성되며,  아직 만료되지 않은 token에 대해 [남은기간(만료시간-현재시간) < REFRESH기간]일 경우, 현재시간으로부터 다시 만료기간만큼, 만료시간을 연장해준다



### 01 refresh는 정제된 token을 인자로 받아 jwt.decode부터 시작한다

#### 001 GET token확인 route에서 decode후 uid를 가져오는 로직까지 복사해서 가져온다



![image-20221010165124392](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010165124392.png)

```python
class TokenCreator:

    def __init__(self, token_key: str, exp_time_min: int, refresh_time_min: int):
        self.__TOKEN_KEY = token_key
        self.__EXP_TIME_MIN = exp_time_min
        self.__REFRES_TIME_MIN = refresh_time_min


    def create(self, uid: int):
        return self.__encode_token(uid)

    def refresh(self, token: int):
        token_information = jwt.decode(token, key='1234', algorithms='HS256')
        token_uid = token_information["uid"]
        
```



#### 002 decode한 token에서 'exp'를 가져오되 datetime type이 아닌, float숫자 시간인 seconds로 인지하고, time.time()과 비교하여 [남은 MIN]을 구하고, [REFRESH_TIME_MIN]보다 더 짧게 남아있으면 REFRESH 해준다. 

- 만료시간 - 현재시간 차이를 `/ 60`을 통해 **`분단위로 변환한 남은시간`**이  **private REFSH_TIME_MIN 과 비교해서 `더 짧은시간이 남아있으면  REFRESH 대상 토큰`이다.**

```python
class TokenCreator:

    #...

    def refresh(self, token: str):
        token_information = jwt.decode(token, key='1234', algorithms='HS256')
        token_uid = token_information["uid"]
        exp_time = token_information["exp"]

        if (exp_time - time.time()) / 60 < self.__REFRES_TIME_MIN:
            return self.__encode_token(token_uid)
```



#### 003 REFRESH는 token_uid를 가지고, private encode메서드로 새로운 token(uid유지, exp만 현재시간+EXP_TIME_MIN만큼 연장)을 생성하여 반환한다. Refresh 대상이 아닌 토큰은 그냥 반환해주면 된다. =>  refresh 함수자체는 input이 token이고, output도 token이 결과값이다.

```python
def refresh(self, token: str):
    token_information = jwt.decode(token, key='1234', algorithms='HS256')
    token_uid = token_information["uid"]
    exp_time = token_information["exp"]

    if (exp_time - time.time()) / 60 < self.__REFRES_TIME_MIN:
        return self.__encode_token(token_uid)

    return token
```



#### 004 token 테스트에 썼던 변수 jwt.encode(key=) ,  'exp': ~ + timedelta(minutes=) 를 private상수로 변경해준다

![image-20221010170711674](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010170711674.png)

![image-20221010170740334](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010170740334.png)



```python
class TokenCreator:

    def __init__(self, token_key: str, exp_time_min: int, refresh_time_min: int):
        self.__TOKEN_KEY = token_key
        self.__EXP_TIME_MIN = exp_time_min
        self.__REFRESH_TIME_MIN = refresh_time_min


    def create(self, uid: int):
        return self.__encode_token(uid)

    def refresh(self, token: str):
        token_information = jwt.decode(token, key=self.__TOKEN_KEY, algorithms='HS256')
        token_uid = token_information["uid"]
        exp_time = token_information["exp"]

        if (exp_time - time.time()) / 60 < self.__REFRESH_TIME_MIN:
            return self.__encode_token(token_uid)
        
        return token

    def __encode_token(self, uid: int):
        token = jwt.encode({
            'exp': datetime.utcnow() + timedelta(minutes=self.__EXP_TIME_MIN),
            'uid': uid
        }, key=self.__TOKEN_KEY, algorithm='HS256')

        return token

```





## 4 TokenCreator를 싱글톤으로 사용 like app객체

- **서버마다 환경변수에 따라 다르게 입력되는**  server정보(private, env정보)를 가지고  +  **사용자에 따라 다른 정보를 인자**로 받는 **method로 서버켜질때 실시간 생성해서 작동**해야하므로 객체지만,
  - **`하지만, 서버 가동후에는 정보의 종류가 1개로 일정한 상수들로 1번만 생성하면, 이후 가동동안은 재활용 대상`**이 되므로 **싱글톤으로 생성한다**



### 01 [싱글톤 생성]: 싱글톤대상class의 py와 같은 level에 _singleton.py 생성해서 그곳에서 객체 생성해놓기 like app객체

#### 싱글톤 생성 방법은, 따로.py를 파서 거기에 객체를 1개 미리 생성해놓는 것

![image-20221010172325034](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010172325034.png)



```python
from .token_creator import TokenCreator

token_creator = TokenCreator(
    token_key='1234',
    exp_time_min=30,
    refresh_time_min=30,
)
```





### 02 [싱글톤 모듈화]: 미리 생성된 객체를 import하는 것이 싱글톤 but 다른 패키지에서도 사용하려면 패키지의 init에 한번더 미리 생성된 객체 import

#### token_handler의 init.py에 미리 생성된 TokenCreator객체를 import해놓고, 다른 패키지에서 부를 수 있게 한다

```python
from .token_singleton import token_creator
```





### 03[싱글톤 사용]:  token_creator라는 미리 생성된 싱글톤 객체를 [POST token생성 route]에서 import해서 사용

#### 001 api_route.py에서 싱글톤객체 token_creator import

- **기존 token 발급 및 확인을 위한 import를 삭제**하고 token_creator만 import한다

  ![image-20221010173907334](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010173907334.png)

```python
from flask import Blueprint, jsonify, request
from src.auth_jwt.token_handler import token_creator
#...


@api_routes_bp.route('/auth', methods=["POST"])
def authorize_user():

    token = token_creator.create(uid=12)

    return jsonify({
        'token': token
    }), 200

```

![image-20221011014852276](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011014852276.png)



## 5 token_verifier.py 데코레이터

### 01 Route마다 매번 [token 인증 -> 실패시 예외]의 검증하는 로직을 route에 반복 기술하는 대신 데코레이터로 만들기

- [참고 내 블로그](https://blog.chojaeseong.com/python/algorithm/pycharm/decorator/2022/07/20/python_%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0%EB%A5%BC-%EC%9E%91%EC%84%B1%ED%95%98%EB%8A%94-%EC%88%9C%EC%84%9C.html)

  ```
  기본 데코레이터 만들기
  01 기능을 입힐 base함수부터 작성한다. 이왕이면 print가 아니라 return하는 결과물이 있는 함수를 작성하여, 반환값에 대한 장식도 가능하게 한다.
  02 base함수를 (호출부없는)함수객체를 인자로 받는 클로져 함수를 사용-생성한다. 여기까지는 지역상태(메서드내부 인자 등)를 기억하는 함수객체를 return한 뒤, 외부에서 실제 인자를 받는 클로져가 된다.
  03 클로져 상태에서, base함수를 호출한 결과값을 장식해야한다면? 반환은 값이 되므로 함수객체를 반환하지 않게 되어, 클로져도 아니고 데코레이터도 아니게 된다. 그냥 값이 반환된 상태라서 외부에서 ()호출없이 끝난다.
  04 base결과물 장식 후, 클로져 상태(함수객체 반환)를 유지하기 위한 inner메서드 wrapper로 감싼 뒤, return wrapper함수객체 -> 외부에서 호출될 함수로서, 함부러 파라미터를 내부context라고 파라미터로 만들면 안된다. -> base함수의 인자에 맞춰야한다.
  05 완성된 데코는, base함수에 @달아서 사용해주기
  06 다중데코레이터는, 외부에서 한번더 감싸서 만들고, 달 때는 아래->위 순서대로 단다.
  함수객체 name에 base함수가 찍히도록 디버깅되는 데코레이터로 바꾸기 by wrapper메서드에 @functools.wraps(func) 데코 달아주기
  base함수호출시 필요한 특정 인자를 외부에서 조달하려면, 외부에 return되서 ()가 붙는 함수객체 wrapper에 *args, **kwargs로 파라미터를 정의하여 어떤 인자든 들어오게 한다.
  ```

  

#### 001 src > auth_jwt > token_verifier.py 생성

![image-20221010211709522](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010211709522.png)



### 02 token인증(실패시 예외)을 데코레이터 만드는 방법(바깥에서부터)

- **route의 메서드**가, 데코레이터의 decorator -> warpper -> base_func 중 **basefunc을 차지하게 됨.**

#### 001 decorator는  ()호출 전의 base_func객체를 인자로 받아서 내부에서 호출하고, return은 func객체를 호출하고 실제 deco하는 inner method wrapper를 호출하지 않은체 반환하므로 [input:func(base_func) -> output func(wrapper)]이다.

- 참고

  ![](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220720183920956.png)

```python
def token_verify(function: callable) -> callable:
```





#### 002 inner method wrapper메서드는 [결과값을 deco당하거나 or 호출전후로 작업을 하고 싶은 base_func()]의 decorating 과정에서 -> 외부 인자가요구하는 순간, wrapper method도 인자를 `*args, **kwargs`로 받는다. inner method인 wrapper는 decorator 맨 마지막에 호출하지 않은 체 반환되는 closure다

- 참고

  ![](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220720193114348.png)



```python
def token_verify(function: callable) -> callable:

    def decorated(*args, **kwargs):
        ## pre decoration

        # result = function()

        ## post decoration
        # post_decorated_result = result + @
        # return decorated_result
        
    return decorated
```



#### 003 GET route에서 token인증을 위한 decode + 예외처리 했던 로직을 inner method warpper(여기선 decorated) 내부로 가져와서, [base_func (여기서는 route함수) 호출전 (1)TOKEN인증(==실패시 예외) + (2) TOKEN REFRESH 작업]을 항상 진행하게 해준다.

- get route

  ![image-20221010214759256](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010214759256.png)

- 이동

  ![image-20221010214905612](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010214905612.png)



#### 004 필요한 것들을 import하고, 수정해나간다

- auth_jwt > token_verifier.py에 
  - flaks관련 모듈이 import된다
  - jwt가 import된다
  - 싱글톤 token_creator도 import된다
    - **인증 및 예외발생 route에는 없던 refresh를 해주기 위해**

```python
import jwt
from flask import request, jsonify


def token_verify(function: callable) -> callable:

    def decorated(*args, **kwargs):
        raw_token = request.headers.get("Authorization")
        uid = request.headers.get("uid")

        # if not raw_token:
        if not raw_token or not uid:
            return jsonify({
                "error": "Bad Request"
            }), 400

        try:
            token = raw_token.split()[1]
            token_information = jwt.decode(token, key='1234', algorithms='HS256')
            token_uid = token_information["uid"]

        except jwt.InvalidSignatureError:
            return jsonify({
                "error": "Invalid Token"
            }), 498

        except jwt.ExpiredSignatureError:
            return jsonify({
                "error": "Token expried"
            }), 401
            
        except KeyError as e:
            return jsonify({
                "error": "Invalid Token2"
            }), 401

        if int(token_uid) != int(uid):
            return jsonify({
                "error": "User not permission"
            }), 400

    return decorated

```



#### 005 base_func인 function(route함수)는 wrapper(decorated)에서 [token인증 및 예외처리] 끝내고, 자신만 호출되면 되나? 인증안되면 예외발생하고, 인증되면 route 자신의 역할만 하면 끝??

- 시나리오

  - **route함수가, 인증 및 예외처리만 할 경우 -> deco내wrapper내 token처리 끝나고 base_func()호출시 특별한 인자 없이 `base_func(\*args, \**kwargs)`로 return하고 끝낸다**

    ![image-20221010220636213](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010220636213.png)

    ![image-20221010220852840](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010220852840.png)

  - **route함수가, 인증 및 예외처리하고 `REFRESH한 token이 새로 발급`되어서, `이것을 인자로 받아갈 때`**

    ![image-20221010220940515](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010220940515.png)

    ![image-20221010221134178](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010221134178.png)



#### 006 route함수가 decorator로 token인증을 거치는 경우, 인증성공시의 refresh된 token을 route함수 인자로 받아야하며, decorator작성시, base_func(route_func)은 \*args\*\*kargs 앞에 가장 첫 인자로 next_token을 추가해야한다

![image-20221011014624097](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011014624097.png)

```python
import jwt
from flask import request, jsonify
from .token_handler import token_creator


def token_verify(function: callable) -> callable:

    def decorated(*args, **kwargs):
        raw_token = request.headers.get("Authorization")
        uid = request.headers.get("uid")

        # if not raw_token:
        if not raw_token or not uid:
            return jsonify({
                "error": "Bad Request"
            }), 400

        try:
            token = raw_token.split()[1]
            token_information = jwt.decode(token, key='1234', algorithms='HS256')
            token_uid = token_information["uid"]

        except jwt.InvalidSignatureError:
            return jsonify({
                "error": "Invalid Token"
            }), 498

        except jwt.ExpiredSignatureError:
            return jsonify({
                "error": "Token expried"
            }), 401

        except KeyError as e:
            return jsonify({
                "error": "Invalid Token2"
            }), 401

        if int(token_uid) != int(uid):
            return jsonify({
                "error": "User not permission"
            }), 400

        next_token = token_creator.refresh(token)
        # route function
        # return function(*args, **kwargs)
        return function(next_token, *args, **kwargs)

    return decorated
```

#### 007 inner method wrapper메서드에 @functools.wraps( base_func )을 달아주면, 디버깅시 함수name이 같이 찍힌다

```python
from functools import wraps
#...
def token_verify(function: callable) -> callable:
    
    @wraps(function)
    def decorated(*args, **kwargs):
```

#### 008 auth_jwt init에 올리기

```python
from .token_handler import token_creator
from .token_verifier import token_verify
```





### 03 token 인증필요 route(GET)에서 jwt 인증  decorator사용하기

#### 001 jwt 인증 데코레이터는 base_func이 인자로 next_token을 받는 것을 가정했으니 -> route함수는 token을 인자로 받아야한다 -> return에도 받은 next_token을 반환해보자.

- api_route.py

```python
#...
from src.auth_jwt import token_creator, token_verifier
#...

@api_routes_bp.route("/secret", methods=["GET"])
@token_verifier
def secret_route(token):

    return jsonify({
        'data': token
    }), 200
```



#### 002 POSTMAN에서 토큰생성 (POST) -> 토큰 인증(GET) 해보기

##### token 생성은 생성요청자 uid를 넣어줘야하는데, 여기선 하드코딩으로 넣어준다

![image-20221011015852728](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011015852728.png)

![image-20221011015644925](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011015644925.png)

##### token 인증요청자는 token생성한uid와 동일해야하는데, (10Headers에 하드코딩으로 POST에 썼던 uid를 하드코딩해서 넣어주고 요청한다. 또한, (2) Authrization탭에 POST시 발급받은 token을 똑같이 직접 넣어줘야한다.

![image-20221011015731021](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011015731021.png)

![image-20221011015706030](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011015706030.png)



#### 003 인증요청자가 발급자와 다른uid를 가지고 있다면? @token_verify가 인증실패 -> 예외처리를 잘해준다.  (400, Bad Request인데, -> 400, User not permission)

![image-20221011020126251](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011020126251.png)

![image-20221011020306881](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011020306881.png)

![image-20221011021008053](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011021008053.png)

##### uid를 입력안해도 (or 토큰이 없어도)예외처리를 잘해준다.(400, Bad Request인데 ->  400에 No Authorized)

![image-20221011020237204](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011020237204.png)

![image-20221011020246890](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011020246890.png)

![image-20221011020826150](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011020826150.png)

##### token 맨끝에 1~2글자 지워도 잘 에러 내준다.(498, Invalid Token, -> 401 Invalid Token)

![image-20221011020415029](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011020415029.png)

![image-20221011020629922](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011020629922.png)

![image-20221011020939299](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011020939299.png)







## 5 dotenv 적용하기

### 01 서버 가동에 필요한 상수들은, 환경변수들을 .env에 기록한다

#### 서버가동 설정은, 외부공개 code들이 아닌(github),  파일/환경변수(.gitignore)로부터  받아서 최초 1번 싱글톤 객체들의 private필드에 입력되어 재활용된다.



#### 001 root에 .env파일을 만들자마자 .gitignore에 추가한다

![image-20221011021644687](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011021644687.png)

![image-20221011021633709](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011021633709.png)



#### 002 .env에 정의한 내용을, python가동환경의 환경변수로 올려주는 `python-dotenv`패키지를 설치한다.

```
pip3 install python-dotenv
```



#### 003 src > config 폴더 + init을 만들고 내부 jwt_config_file.py를 만든다

##### 각 singleton객체가 환경변수 정보가 필요한 곳마다 src>config>xxx_config_file.py를 만들고 init에 올려서 쓴다

![image-20221011023115059](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011023115059.png)

#### 004 각 xxx_config_file.py는  os모듈과 from dotenv의 load_dotenv()를 import한 뒤, load_dotenv()로 .env 속 환경변수값들을 올렸다고 가정(싱글톤 CLASS들의 private필드들 참고)하고 xxx_config = {} 매핑해서 모은다.

![image-20221011030532298](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011030532298.png)

![image-20221011033203731](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011033203731.png)

1. xxx_config_file.py에서는 **xxx_config = {}의 dict를 만들고, os.getenv("key")로 뽑은 환경변수의 값들을 dict를 매핑해놓는다.**

   - **`환경변수 뽑을 때, 숫자는 int()형변환 꼭 하자!`**

   ```python
   import os
   from dotenv import load_dotenv
   
   load_dotenv()
   
   jwt_config = {
       "TOKEN_KEY": os.getenv("TOKEN_KEY"),
       "EXP_TIME_MIN": int(os.getenv("EXP_TIME_MIN")),
       "REFRESH_TIME_MIN": int(os.getenv("REFRESH_TIME_MIN")),
   }
   ```

2. **.env -> 환경변수에 올라갔다고 가정한 상수들을 `.env`KEY로 주면서 직접 값을 작성한다**

   ![image-20221011030736642](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011030736642.png)

   ```
   TOKEN_KEY=1234
   EXP_TIME_MIN=30
   REFRESH_TIME_MIN=15
   ```

   

3. **flask run 등 작동했을 때는 jwt_config이 채워진 상태이므로, `객체를 올리더라도 app, token_creator등의 싱글톤 정도만 init`에 올린다. `dict의 단순자료구조이므로 init에 올리지 않고, file까지 타고가서 import해서 사용`한다**

   - token_singleton.py에서 **싱글톤 객체 생성시 private필드값으로 `src.config.xxx_config_file import xxx_config` dict를 import해서 사용한다.**

   - 거진 **`싱글톤 객체app, TokenCreator 등이 생성되는 곳에 import`되서 사용될 것이다.**

   ```python
   #...
   from src.config.jwt_config_file import jwt_config
   
   token_creator = TokenCreator(
       token_key=jwt_config["TOKEN_KEY"],
       exp_time_min=jwt_config["EXP_TIME_MIN"],
       refresh_time_min=jwt_config["REFRESH_TIME_MIN"],
   )
   ```



#### 005 다른 곳에 미리 사용한 env변수 들 찾아서 import해서 사용하기

- **TOKEN_KEY는 jwt.encode, decode 메서드 사용시 key=로 들어갔으니 `kargs  key=`로 찾아본다.**

  - **혹은 `환경변수를 소문자 변환=`한 것으로 찾아본다**
  - **싱글톤 객체의 key=self.__private 변수 from 환경변수**면 통과다
  - key="하드코딩"이면 통과아님. **싱글톤객체의 메서드내부로 로직을 옮겨서 은폐해야함.**

  ![image-20221011035232574](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011035232574.png)

- **뭐야.. 토큰인증 데코레이터 속 decode과정이 없네??**

  ![image-20221011035634777](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011035634777.png)

- **환경변수들을 가지고 있는 TokenCreator가 있는데 굳이 jwt_config를 import해서 써야하나?**

  - **뭐야.. refresh할때느 decode과정이 포함되어 있잖아?**
    - private encode처럼 decode도 만들어야할 듯

  ![image-20221011035552122](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011035552122.png)



- **`__encode_token`처럼 create시 내부호출만 하는 것이 아니라, 외부 데코레이터에서도 사용되어야하니 `decode_token`의 public으로 만듦..**

  - TokenCreator의 이름이 바뀌어야하나...

  ![image-20221011040602953](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011040602953.png)

  ![image-20221011040511450](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011040511450.png)



### my) .env.example 를 만들어주는 precommit 스크립트 

```powershell
(Get-Content .\.env) -replace('=.*', '=') | Set-Content .env.example

# sed 's/=.*/=/' .env > .env.example
```

- generate_env_example.py

  ```python
  #!/usr/bin/env python
  
  import os
  import re
  from subprocess import call  # nosec
  
  
  def main():
  
      dotenv = "./.env"
      dotenv_example = "./.env.example"
  
      if not os.path.exists(dotenv):
          print(f"there is no {dotenv}")
          exit()
  
      # (Get-Content .\.env) -replace('=.*', '=') | Set-Content .env.example
      # sed 's/=.*/=/' .env > .env.example
      with open(dotenv, "r") as file_:
          lines = file_.readlines()
          with open(dotenv_example, "w") as file__:
              for line in lines:
                  file__.write(re.sub("=.*", "=", line))
  
      call(f"git add {dotenv_example}")  # nosec
  
  
  if __name__ == "__main__":
      exit(main())
  ```

  



### 02 TEST

1. flask run

2. token 생성 route(POST)

   ![image-20221011033343197](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011033343197.png)

3. token 인증 데코레이터가 달린 + route함수에 next_token을 인자로 받는 route(GET)

   ![image-20221011033548230](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011033548230.png)



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
5. 환경변수파일 .env 

```
**/__pycache__
venv
storage.db
.pytest_cache
.env
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

1. gitignore중에 `sqlite db파일` or `.env` 있으면 옮겨주기
   - 없으면 config import해서 Base + handler -> get_enigne -> bind한 다음, create_all()해줘야할듯??

![image-20220929154256052](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220929154256052.png)

![image-20221011214909442](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221011214909442.png)

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

   - **GET요청에 대해 http_request에 필수param을 포함한 .query or .body가 아예 날라오지도 않았을 경우**

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

   - **GET요청(request.args.to_dict())에 대해 필수param(id)의 형변환을 실패했을 때**

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

   - **token확인시 request.headers의 "Authorization" or "uid"가 안들어가있을 때**

     - token_veirifier.py

       ```python
       def token_verify(function: callable) -> callable:
           #...
       
           @wraps(function)
           def decorated(*args, **kwargs):
               raw_token = request.headers.get("Authorization")
               uid = request.headers.get("uid")
       
               # if not raw_token:
               if not raw_token or not uid:
                   return jsonify({"error": "Bad Request"}), 400
       ```

3. `401`: **token확인시 `토큰 만기` or `토큰 invalid` or `uid`가 다른 `Unauthorized`**

   ```python
   try:
       token = raw_token.split()[1]
       # token_information = jwt.decode(token, key='1234', algorithms='HS256')
       token_information = token_creator.decode_token(token)
       token_uid = token_information["uid"]
   
       except jwt.InvalidSignatureError:
           return jsonify({"error": "Invalid Token"}), 498
   
       except jwt.ExpiredSignatureError:
           return jsonify({"error": "Token is expired"}), 401
   
       except KeyError:
           return jsonify({"error": "Token is invalid"}), 401
   
       if int(token_uid) != int(uid):
   ```

   

4. `409`:  타겟 자원에 상태에 요청이 conflict를 유발한 경우

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

5. `500`: **adapter**에서 알고 있는 예외 다 처리하고 난 다음 맨 마지막에 **어떤 에러인지 내부에서 출력해놓기**

   - flask_adatper

     ```python
         http_request = HttpRequest(
             header=request.headers, body=request.json, query=query_string_params
         )
     
         try:
             response = api_route.route(http_request)
         except IntegrityError:
             http_error = HttpErrors.error_409()
             return HttpResponse(
                 status_code=http_error["status_code"], body=http_error["body"]
             )
         except Exception as exc:
             print(exc)
             http_error = HttpErrors.error_500()
             return HttpResponse(
                 status_code=http_error["status_code"], body=http_error["body"]
             )
         return response
     
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


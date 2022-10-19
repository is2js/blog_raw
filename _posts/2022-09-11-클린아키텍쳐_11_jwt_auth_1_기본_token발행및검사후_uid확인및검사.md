---
toc: true
layout: post
title: 클린아키텍쳐 11 jwt auth token 발행과 검사 및 uid 검사
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---

# 클린 아키텍쳐 11장


## 1 세팅

### 01 첫 프로젝트 구조  (not clean architecture)

#### 001 src> route.py

##### flask Blueprint, jsonify로 /secret route 구성

- clean architecture라면 `src> main > routes > api_routes.py`

![image-20221009214051476](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009214051476.png)

### 

#### 002 src> server.py

##### 작성된 .route의 route등록객체 + flask Flask + flask_cors CORS -> app객체 생성후 초기화

- clean architecture라면 `src> main > configs > app.py`



![image-20221009214205278](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009214205278.png)



#### 003 root > run.py

##### app객체 import 및 name main시 실행코드

- clean architecture라면 `root > run.py`

![image-20221009214159517](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009214159517.png)



### 02 clean architecture

#### 001 src> main> routes > api_routes.py에 /secret [GET] route 추가

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():
    
    return jsonify({
        'data' : 'Message secret'
    }), 200
```





### 03 POSTMAN으로 /secret GET route TEST

- jsonify한 메세지가 잘 들어오나 확인한다.





## 2 Flask-login 대신 PyJWT

### 01 PyJWT 설치

```
pip3 install PyJWT
```



### 02 jwt, datetime-datetime/timedelta import 하고 /auth [POST] route 추가

```python
import jwt
from datetime import datetime, timedelta
#...

@api_routes_bp.route('/auth', methods=["POST"])
def authorize_user():
    pass
```



### 03 /auth POST route에서 token 발급 -> jwt.encode({'exp': dattime }, key=, algorithm=)로 token 생성

#### route내에서 jwt모듈은 'exp(만료기간)'을 포함한 dict를 key=와 algorithm=을 지정하여 .encode() 메서드로 token을 만든다.

```python
@api_routes_bp.route('/auth', methods=["POST"])
def authorize_user():
    
    token = jwt.encode({
        'exp' : datetime.utcnow() + timedelta(minutes=30)
    }, key='1234', algorithm='HS256')

    return jsonify({
        'token': token
    }), 200
```



#### RS256과 HS256 둘다 SHA-256암호화 알고리즘이지만, HS가 secret key를 가지고 암호화해서 더 안전하다고 한다

![image-20221009220316821](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009220316821.png)





#### 환경변수 지정해서, flask run -> POSTMAN으로 확인하기

```
export FLASK_APP=run.py
export FLASK_ENV=development
flask run
```

- **POST는 headers가 필수며 **

  - Content-Type: application/json

- **body는 raw로 내용물을 넣어서 보낸다.**

  - 여기선 route에서 아직 안받으므로 아무거나 보낸다
    - username: 
    - password:

  ![image-20221009221118022](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009221118022.png)

  ![image-20221009221200419](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009221200419.png)



#### 생성된 token을 버리지 말고 가지고 있는다.

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2NjUzMjI3MTl9.km9cSKTBbOLbnHnsH3zq3dEfswdP57pizIFEpyf5_xY
```





## 3 기본token 발급 및 decode시 발생하는 예외 처리

### 01 token decode 검사 in Headers.get("Authorization") in GET route

#### 001 [ERROR] /secret GET route에서 request.headers 속 "Authorization" key를 확인하고 token자체가 없으면 token문제가 아니라 400 Bad Request다

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():
    token = request.headers.get("Authorization")

    if not token:
        return jsonify({
            "error": "Bad Request"
        }), 400

    return jsonify({
        'data' : 'Message secret'
    }), 200
```





#### 002 POSTMAN에서는 GET요청시 Authorization TYPE을 Bearer  Token을 선택하고, jwt.encode로 만든 토큰을 입력한다.

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2NjUzMjI3MTl9.km9cSKTBbOLbnHnsH3zq3dEfswdP57pizIFEpyf5_xY
```



![image-20221009224324071](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009224324071.png)

- 현재 토큰의 존재검사는 통과하니 data가 잘 나온다.

  ![image-20221009224642197](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009224642197.png)

- token을 삭제하고 send보내면 401에러가 뜨는지 확인한다.

  ![image-20221009224707102](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009224707102.png)



#### 003 받은 token을 출력해본다.

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():
    token = request.headers.get("Authorization")

    if not token:
        return jsonify({
            "error": "Bad Request"
        }), 400

    print(token)

    return jsonify({
        'data' : 'Message secret'
    }), 200

```

![image-20221009224837323](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009224837323.png)



#### 004 token은 사실 앞에 `Bearer[ ] `가 붙은 raw_token으로 간주하고 split해서 token만 취한 뒤 , jwt.decode(,key=, algorhitm=)으로 암호화시 사용한 동일 key와 algo로 암호화해독한 token_information을 출력해본다.

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():
    raw_token = request.headers.get("Authorization")

    if not raw_token:
        return jsonify({
            "error": "Bad Request"
        }), 400

    print(raw_token)
    token = raw_token.split()[1]
    token_information = jwt.decode(token, key='1234', algorithms='HS256')
    print(token_information)


    return jsonify({
        'data' : 'Message secret'
    }), 200
```



##### 나는 한참있다가 했더니.. POST route에서 발급한 token expire => jwt.exceptions.ExpiredSignatureEror가 난다

![image-20221009230510842](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009230510842.png)



##### 다시 /auth POST route에서 토큰을 발급(생성)하고, /secret GET route에서 토큰 검사를 해보자.

1. POSTMAN에 다른 탭을 쓴 덕택에 post코드가 남아있다.

   ![image-20221009230653220](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009230653220.png)

   ```
   eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2NjUzMjYxOTZ9.skUZSXEso8BILbzGWFk2BVWyEMhetZGSKLnOdaEmtvg
   ```

2. 해당토큰을 다시 GET에서 사용자가 headers에 담아서 보냈다고 가정하자.

   ![image-20221009230738952](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009230738952.png)

3. 잘들어오니 -> **decode한 token 출력을 확인해보자**

   ![image-20221009230803647](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009230803647.png)

##### decode한 token은 encode할때의 dict('exp'포함)가 그대로 출력된다

![image-20221009230839458](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009230839458.png)





### 02 invalid한 token(막토큰, 1~2개 지운 토큰)해서 GET요청해보기

####  001 [ERROR] 막token은 검사시 jwt.exceptions.DecodeError가 난다 -> 근데 막토큰을 올릴이 없다?

![image-20221009230941059](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009230941059.png)



#### 002 [ERROR] 원래 토큰에서 1~2글자를 지우면, jwt.exceptions.InvalidSignatureError가 난다

![image-20221009231649576](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009231649576.png)

##### [ERROR] 막token말고 1~2글자빠진 token(Invalid token)을 if not token과 동일한 498로 잡자

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():
    raw_token = request.headers.get("Authorization")

    if not raw_token:
        return jsonify({
            "error": "Bad Request"
        }), 400

    try:
        token = raw_token.split()[1]
        token_information = jwt.decode(token, key='1234', algorithms='HS256')
    except jwt.InvalidSignatureError:
        return jsonify({
            "error": "Invalid Token"
        }), 498

```

![image-20221009232258804](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009232258804.png)





### 03 token 발급 /auth POST route에서 발급(encode)시 exp를 1초로해서.. Expire관련 에러 잡기



#### 001 POST 발급route에서 encode시 'exp'의 값을 현재시간 + timedelta(seconds=1)로 줘서 무조건 만료된 상태를 만단다.

```python
@api_routes_bp.route('/auth', methods=["POST"])
def authorize_user():

    token = jwt.encode({
        'exp': datetime.utcnow() + timedelta(seconds=1)
    }, key='1234', algorithm='HS256')

    return jsonify({
        'token': token
    }), 200

```



#### 002 [ERROR] GET 확인 route에서 jwt.ExpiredSignatureError를 토큰이 존재하지 않을 때와 동일한 401(Unauthorized)로 잡는다.

- 굳이 내용물을 출력해서 확인할 필요없다
  - POST요청시 받아서 복사만 해둔다.
  - GET에서 출력했떤 것은 headers 속 raw_token -> token으로 split이 필요했기 때문에

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():
    raw_token = request.headers.get("Authorization")

    if not raw_token:
        return jsonify({
            "error": "Bad Request"
        }), 400

    try:
        token = raw_token.split()[1]
        token_information = jwt.decode(token, key='1234', algorithms='HS256')
        
    except jwt.InvalidSignatureError:
        return jsonify({
            "error": "Invalid Token"
        }), 498

    except jwt.ExpiredSignatureError:
        return jsonify({
            "error": "Unauthorized"
        }), 401

    return jsonify({
        'data': 'Message secret'
    }), 200
```



#### 003 POSTMAN에서 POST 1초짜리 발급 ->  GET ExpiredToken401을 확인한다.

![image-20221009234345130](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009234345130.png)



![image-20221009234406851](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009234406851.png)

#### expiredtoken 에럴 잡았으면 다시 exp를 30분으로 돌려놓자

![image-20221009234718731](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009234718731.png)



#### 참고)  expired token 이외에 + 498 invalid token도 401으로 잡아도 된다.

![image-20221009234821737](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009234821737.png)





## 4 Agent의 token외 user의 id (uid)도 추가

![image-20221009235215344](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221009235215344.png)



### 01 token발급 route에서 exp외에 [발급요청자의 uid]를 포함해서 발급

```python
@api_routes_bp.route('/auth', methods=["POST"])
def authorize_user():

    token = jwt.encode({
        'exp': datetime.utcnow() + timedelta(minutes=30),
        'uid': 12
    }, key='1234', algorithm='HS256')

    return jsonify({
        'token': token
    }), 200
```



### 02 token확인 route에서, headers에 "Authorization"이외에 현재user의 [token확인요청자의 "uid"]도 꺼내서 [token발급자의 uid]와 비교

#### 001 [ERROR] 현재 요청사용자의 uid를 headers에서 꺼낸다. token이 없는것 외에 uid가 없어도 400에러다

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():

    raw_token = request.headers.get("Authorization")
    uid = request.headers.get("uid")

    # if not raw_token:
    if not raw_token or not uid :
        return jsonify({
            "error": "Bad Request"
        }), 400
```



#### 002 발급시 token에 포함된 uid를  decode된 token_information에서 꺼내 변수로 받아놓는다.

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():

    raw_token = request.headers.get("Authorization")
    uid = request.headers.get("uid")

    # if not raw_token:
    if not raw_token or not uid :
        return jsonify({
            "error": "Bad Request"
        }), 400

    try:
        token = raw_token.split()[1]
        token_information = jwt.decode(token, key='1234', algorithms='HS256')
        token_uid = token_information["uid"]
```



#### 003 token decode과정에서 발생하는 예외통과시, token_uid vs 현재uid를 비교하여 다를시, 400으로 예외를 처리한다(형변환해서 확인)

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():

    #...
    try:
        token = raw_token.split()[1]
        token_information = jwt.decode(token, key='1234', algorithms='HS256')
        token_uid = token_information["uid"]

    #...
    except jwt.ExpiredSignatureError:
        return jsonify({
            "error": "Unauthorized"
        }), 401

    print(token_uid, type(token_uid), uid, type(uid))
    if int(token_uid) != int(uid):
        return jsonify({
            "error": "User not permissin"
        }), 400

    return jsonify({
        'data': 'Message secret'
    }), 200
```

#### token관련 테스트는 항상 POST로 새로 발급 -> 복사 -> GET으로 인증요청하자 => 엥? not uid에 걸려 401 에러가 난다

![image-20221010012011997](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010012011997.png)



### 03 uid는 Authorization과 달리, POSTMAN GET요청시, Headers에 직접 줘야한다

#### 001 GET요청시 Headers를 따로 준 적이 없었는데 uid테스트가 처음이다

![image-20221010012140462](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010012140462.png)

![image-20221010012149063](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010012149063.png)





#### 002 확인해보니, headers 속  uid는 string으로, token속 발급시 지정해준 uid는 int타입이었다.

```python
print(token_uid, type(token_uid), uid, type(uid))
# 12 <class 'int'> 12 <class 'str'>
```



#### 003 발급시와 다른 uid를 headers에 박아서 token확인요청 한다면?  token_uid vs uid 비교시 내는 User not permission이 나와야한다

![image-20221010012438559](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010012438559.png)





### 04 또다른 uid관련 에러 처리 -> token생성시 필수 dictk-key인 uid를,  없이 생성한 경우

#### 001 headers 속 uid는 .get()으로 꺼내고 없으면 400에러였다

![image-20221010012726664](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010012726664.png)



#### 002 그러나 token은  발급(POST)시 무조건 uid를 넣은 상태로 encoding해야하며, 확인요청(GET)시 token_info["uid"]로 강제로 key를 꺼낸다. 

![image-20221010012831765](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010012831765.png)



#### 003 [ERROR] 필수적으로 있어야할 dict key( not .get())인 uid가 token에 없으면 KeyError로 잡고 401 에러를 낸다. 임의로 "Invalid Token2"로 메세지를 잡는다

```python
@api_routes_bp.route("/secret", methods=["GET"])
def secret_route():

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

```

##### uid없이 토큰발급 Error를 내보기 위해,  POST route에서 임시로 uid key-value를 삭제하고 테스트

![image-20221010013228176](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010013228176.png)

- POST

  ![image-20221010013246974](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010013246974.png)

- GET

  ​	![image-20221010013310646](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010013310646.png)



##### 다시 발급시 uid 넣어주자

![image-20221010013340328](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221010013340328.png)





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


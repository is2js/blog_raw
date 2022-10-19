---
toc: true
layout: post
title: 클린아키텍쳐 09 ManyEntity Route부터 구현(OneEntity보다 먼저)
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---


# 클린 아키텍쳐 9장



## 1 ManyEntity register Route 추가 - register pet

### 01 framework adapter method가 있는 상태에서 route추가하려면, 가장 먼저 composite route객체(조합 controller객체)를 반환해줄 [composer method부터]

#### src>main>composer>register_pet_composite.py에 composer method 작성하기

1. repo부터 use case, controller까지 모든 객체가 다 필요하므로 import한다

   - **ManyEntity Composite route객체까지는 `FK_repo -> FindFK_usecase까지 추가`로 import해야한다.**

     - **ManyEntity_Controller부터는** FKrepo와 FindFKusecase가 통합되서 **1개 Entity로 처리**된다.

   - **repo들은 `객체 생성없이 사용하는 @classmethod`로서 `init`에 안올리고, `직접 py까지 from으로 잡고 import class`해서 쓴다.**

     ![image-20221008212652620](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008212652620.png)

     ![image-20221008212938458](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008212938458.png)

   ```python
   from src.infra.repo.pet_repository import PetRepository
   from src.infra.repo.user_repository import UserRepository
   from src.data.register_pet import RegisterPet
   from src.data.find_user import FindUser
   from src.presenters.caontrollers import RegisterPetController
   ```

2. 조합된 controller객체는 returnType이 RouteInterface로 통일이다.

   - 둘다 route함수를 쓰며 HttpRequest -> HttpResponse

   ```python
   from src.infra.repo.pet_repository import PetRepository
   from src.infra.repo.user_repository import UserRepository
   from src.data.register_pet import RegisterPet
   from src.data.find_user import FindUser
   from src.main.interface.route import RouteInterface
   from src.presenters.controllers import RegisterPetController
   
   def register_pet_composer() -> RouteInterface:
       """Composing Register Pet Route
       :param - None
       :return - Object with Register Pet Route
       """
   
   ```

3. **import한 재료들로 composite route객체(조합된 controller)를 만들어서 반환한다.**

   ```python
   #....
   
   def register_pet_composer() -> RouteInterface:
       """Composing Register Pet Route
       :param - None
       :return - Object with Register Pet Route
       """
   
       pet_repository = PetRepository()
       find_user_use_case = FindUser(UserRepository())
       use_case = RegisterPet(pet_repository, find_user_use_case)
   
       register_pet_route = RegisterPetController(use_case)
   
       return register_pet_route
   
   ```

4. composer에 init에 올린다.

   ```python
   from .register_user_composite import register_user_composer
   from .register_pet_composite import register_pet_composer
   
   ```

   



### 02 src>main>routes>api_route.py에  route 추가하기

#### 기존 register oneEntity를 복사해서 수정한다.

1. route함수명부터 usecase명으로 수정한다

2. 데코의 url에는 entity(pets)를 methods에는 행위(register->POST)를 표시한다

   ```python
   @api_routes_bp.route("/api/pets", methods=["POST"])
   def register_pet():
       """register pet route"""
   ```

   

3. 작성한 route객체 반환 compsoer method를 import한 뒤 **adapter method를 통해  `fw request`와 `composite route객체`를 인자로 `response`를 얻어내 메세지를 작성할 준비를 한다.**

   ```python
   #...
   from src.main.composer import register_user_composer, register_pet_composer
   #...
   
   @api_routes_bp.route("/api/pets", methods=["POST"])
   def register_pet():
       """register pet route"""
   
       message = {}
       response = flask_adapter(request=request, api_route=register_pet_composer())
   ```

   - controller객체부터는 response가 "status_code" 와  "body"이며, body에는 usecase의 "Data"가 들어가있다.



#### route객체 + flask request -> adapter를 통한 reponse로 message를 작성할 때는, type/배정id 외에 attributes를 뿌려줄 땐, domain>models의 각 entity필드를 참고한다

- users는 ~~`id`~~ + `name` + ~~password~~ 라서 `name`만 attributes에 뿌려줬다.

- domain>models>pets.py에서 필드들을 참고하자

  ![image-20221008215631341](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008215631341.png)



```python
@api_routes_bp.route("/api/pets", methods=["POST"])
def register_pet():
    """register pet route"""

    message = {}
    response = flask_adapter(request=request, api_route=register_pet_composer())

    if response.status_code < 300:
        message = {
            "Type": "pets",
            "id": response.body.id,
            "attributes": {
                "name": response.body.name,
                "specie": response.body.specie,
                "age": response.body.age
                }
        }
        return jsonify({"data": message}), response.status_code
```



#### jsonapi에 따라 user_id(FK) 필드는  id/ attributes 외에 relationships이라는 key로 value에는 FkEntity의 entity[정의시의 관계명?backref명?]으로 다시 싸서 user_id를 포장해서 따로 뿌려주기

![image-20221008220350922](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008220350922.png)

- 원래는 **Users**에  Pets이 가진 FK로서 User는 Pets에게 relationship을 줄 때,  관계명을 `owner`라고 할 것 같은데 여기선 그냥 id_pet의 변수밖에 없다?

  ![image-20221008221132671](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008221132671.png)





![image-20221008221223396](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008221223396.png)

```python
@api_routes_bp.route("/api/pets", methods=["POST"])
def register_pet():
    """register pet route"""

    message = {}
    response = flask_adapter(request=request, api_route=register_pet_composer())

    if response.status_code < 300:
        message = {
            "Type": "pets",
            "id": response.body.id,
            "attributes": {
                "name": response.body.name,
                "specie": response.body.specie,
                "age": response.body.age
            },
            "relationships": {
                "owner": {
                    "type": "users",
                    "id": response.body.user_id
                }

            }
        }
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



### 03 작성된 route를 POSTMAN으로 테스트

1. `flask run`

   - 디버깅할때만 vscode에서 F9 -> F5

2. post니까 Headers 확인

3. body는 raw로 작성

   - **Find_fk정보는 `fk_information`으로 지정해놨었다.**

   ![image-20221008223856902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008223856902.png)

4. 결과

   - **response시 fk에 대한 정보는 `relationships` 에 `관계명`에 쌓여서 온다**

   ![image-20221008223948758](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221008223948758.png)

- Type 대문자를 소문자로 고침



#### 커밋

```
git commit -am "feat: implementing register_pet Route"
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

   - **register(insert) 요청에 대해 unique key(name)이 중복된 send가 날아와, db에서 unique constraint == 중복에러(IntegrityError)를 발생한 경우**

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


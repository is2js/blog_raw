---
toc: true
layout: post
title: 클린아키텍쳐 02 usecase와 test를 위한 RepoSpy, MockDto method
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---


# 클린 아키텍쳐 2장



## 1 use cases (register user)


### 01 br따서 작업 세팅하기

1. github에서 작업br 생성

   ![image-20221001002856072](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001002856072.png)

2. clone주소 복사 후 -> local에 new폴더 생성후 -> clone -b [branch명] [clone주소]

   - branch명이 복잡하면, view에 들어가서 복사할 수 있다.

   ![image-20221001003032736](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001003032736.png)

   ![image-20221001003424553](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001003424553.png)

   ```
   wt -d .
   ```

   ```
   git clone -b feat/register_user_use_case https://github.com/is2js/backend-python.git
   ```

   ![image-20221001003645458](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001003645458.png)

3. **이후는 이전에 작업한 것처럼 gitignore상 백업db있으면 복붙부터.. 시작한다**

   

### 02 scr>domain> use_cases폴더 및 init생성

- use_cases들은 실제로 사용되는 로직이기 때문에 models(DTO)처럼 domain에 배속된다.



### 03 domain layer register_user인터페이스 -> data layer에서 객체 class를 구현한다

### register user Use Case 만들기

#### domain layer에 interface로 설계한다.

1. src>domain>use_cases> `register_user.py`을 생성한다.

2. **data layer에 속한 interfaces도 아닌데, interface로 구현하기 위해**

   - abc의 모듈들을 import한다.

   - DTO Users도 import한다.
   - **use_case 영역은 앞서구현한 infra의 entites들을 포함하고 있는 더 큰 개념이다?!**

   ![image-20221001004910220](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001004910220.png)

   ![image-20221001005153934](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001005153934.png)

   

#### RegisterUser 의 반환타입은 Dict[bool, DTO]형태이다.

```python
from abc import ABC, abstractclassmethod
from src.domain.models import Users
from typing import Dict


class RegisterUser(ABC):
    """ Interface to RegisterUser use case """

    @abstractclassmethod
    def register(cls, name: str, password: str) -> Dict[bool, Users]:
        """ Case """

        raise Exception("Should implement method: register")

```

- **여기서 cls를 사용하는데, 직전에는 self로 줬기 때문에, 수정해야할 것 같다**

- **생성된 인터페이스를 init에 올려준다.**

  ```python
  from .register_user import RegisterUser
  ```

  



#### data layer에서 개별 use_case마다 만들어줄  class폴더+init을 만들고, domain layer에 만든 use_cases  속 인터페이스들을 import한다.

1. data layer에 `register_user`폴더와 `init`을 + `register.py`를 만든다.

   ![image-20221001010236852](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001010236852.png)

2. data layer의 `개별_usecase폴더`의 py들은 domain layer의 `use_case`폴더 속 인터페이스를 as Interface로 import해서 사용한다.

   ```python
   from src.domain.use_cases import RegisterUser as RegisterUserInterface
   ```

3. 인터페이스를 상속해서 실제 use_case내용을 정의한다.

   ```python
   from src.domain.use_cases import RegisterUser as RegisterUserInterface
   
   class RegisterUser(RegisterUserInterface):
   	""" class to define usecase: Register User"""
   
   ```



#### data layer의 개별use case  Class들은 repository를 생성자에서 인자로 받아 객체로 생성된다(객체 static인 clasmethod X) -> typing을 위해 UserRepository Interface를 구현했던 것이다!

1. data layer의 use case class는 **필요한 repository를 인자로 받는다.**

   - 인자로 받는 것은 import가 필요없으나, **인자의 Type때문에 그것의 interface를 import해야한다**

   ```python
   from src.domain.use_cases import RegisterUser as RegisterUserInterface
   
   class RegisterUser(RegisterUserInterface):
   	""" class to define usecase: Register User"""
   
   	def __init__(self, user_repository):
   		self.user_repository = user_repository
   ```

2. **user_repository 파라미터의 type을 정해주기 위해서 `user_repository interface in data layer` 와  typing패키지의 `Type`모듈을 import해서 사용한다.**

   - **interface를 type으로 쓸려면 `as `에 interface를 제외하고 달아준다.**

   ```python
   from typing import Type
   #...
   from src.data.interfaces import UserRepositoryInterface as UserRepository
   
   class RegisterUser(RegisterUserInterface):
   	""" class to define usecase: Register User"""
   
   	def __init__(self, user_repository: Type[UserRepository]):
   		self.user_repository = user_repository
   ```





#### data layer의 개별 use case Class들은 객체로 생성될 것이며, repository를 필드로 보유하고, use case Class이름과 유사하게 인스턴스 메서드를 만들어 repository필드와 같이 작동한다.

1. register 메서드를 만들 것이므로 **인터페이스(`domain 속 register_user.py`)에서 시그니쳐를 복사해온다.**

   - cls -> self로 바꿔준다.

   ```python
   class RegisterUser(RegisterUserInterface):
   	""" class to define usecase: Register User"""
   
   	def __init__(self, user_repository: Type[UserRepository]):
   		self.user_repository = user_repository
   	
   	def register(self, name: str, password: str) -> Dict[bool, Users]:
   		"""REgister user use case
   		:param - name: person name
   		       - password: password of the person
   		:return - Dictionary with informations of the process
   		"""
   ```

2. 그에 따른 Dict 모듈, Users모듈을 import한다.

   ```python
   from typing import Dict, Type
   #...
   from src.domain.models import Users
   ```

   



##### 인스턴스 메서드는 일단, 파라미터들의 type 유효성부터 검사한다.(validate_entry)

```python
def register(self, name: str, password: str) -> Dict[bool, Users]:
		"""REgister user use case
		:param - name: person name
		       - password: password of the person
		:return - Dictionary with informations of the process
		"""

		validate_entry = isinstance(name, str) and isinstance(password, str)
```



##### if validate_entry 성공시에만 repository.insert_user를 호출하여 가변변수 respoonse = None에 결과값(DTO)를 할당해준다. reigster메서드의 반환타입은 {"Success", "data"}이다

```python

class RegisterUser(RegisterUserInterface):
    """ class to define usecase: Register User"""

    def __init__(self, user_repository: Type[UserRepository]):
        self.user_repository = user_repository

    def register(self, name: str, password: str) -> Dict[bool, Users]:
        """REgister user use case
        :param - name: person name
               - password: password of the person
        :return - Dictionary with informations of the process
        """
        response = None
        validate_entry = isinstance(name, str) and isinstance(password, str)

        if validate_entry:
            response = self.user_repository.insert_user(name, password)

        return {"Success": validate_entry, "Data": response}

```



### 04 use case  Class test (register user test)

#### data layer의 개별 usecase Class in 개별 usecase 폴더에서 _test.py도 만들어준다.

1. src>data>register_user > 에 `reigster_test.py`를 만든다.

   ![image-20221001015820100](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001015820100.png)

2. faker와 테스트할 use case CLass를 import

   ```python
   from faker import Faker
   from .register import RegisterUser
   
   faker = Faker()
   
   def test_register():
       """ Testing registry method"""
       
   ```



3. **use case class는 repository를 인자로 받는데,  사실상 repository는 test로 DB생성후 삭제하며 테스트가 끝난 상황이다**

   - **repo를 이용한 useCase의 test는 무엇을 하는 것일까?**

   - **내부 repository는 검증이 끝났으니 `validate_entry 검증` + `response가 제대로 들어오는지만` 된다.**

     ![image-20221001021341888](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001021341888.png)

     

### 05 use case test는 mock_repo [Repository Spy] 객체가 필요하다(DB관련된 infra의 repo를 테스트할 없음. input/output 테스트를 위한 목repo)

#### src>infra 속>  test폴더 + init 만든다.

![image-20221001021719040](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001021719040.png)





#### 특정repo에 대한 repository_spy.py를 만든다.

![image-20221001021844812](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001021844812.png)





1. 

#### [input spy 처리] repo spy는 repo의 메서드들의 signature에 따른 인자들을 받았을 때, 실제로 메서드 호출 대신 메서드별 배정된 param dict 필드속에 저장 파라미터들을 저장해놓는다.

- spy repository의 메서드들은 random한 input->output만 만들어내며 DB에는 접근하지 않도록 한다

1. repoSpy class를 만든다.

   ```python
   class UserRepositorySpy:
       """ Spy to User Repository """
       
   ```

   ​	

2. **원본repo의 메서드 시그니처를 그대로 가져오되, `메서드별 param들을 저장할 필드`를 `메서드 갯수대로` 만든다.**

   - repo의 객체static메서드인 cls -> self로 바꿔주기

   ```python
   class UserRepositorySpy:
       """ Spy to User Repository """
   
   
       def insert_user(self, name: str, password: str) -> Users:
           pass 
   
       def select_user(self, user_id: int = None, name: str = None) -> List[Users]:
           pass
   
   ```

3. 메서드 갯수대로 **생성자에 param저장용 dict필드 만들기**

   - 메서드명 뒤에 _param을 붙여서  = {} 선언해주기

   ```python
   class UserRepositorySpy:
       """ Spy to User Repository """
   
       def __init__(self) -> None:
           self.insert_user_param = {}
           self.select_user_param = {}
           
       
       def insert_user(self, name: str, password: str) -> Users:
           pass 
   
       def select_user(self, user_id: int = None, name: str = None) -> List[Users]:
           pass
   
   ```

4. **repo 메서드들의 return type은 거의 DTO라고 보면된다. `models`에서 import해주자**

   ```python
   from typing import List
   from src.domain.models.users import Users
   ```

   

5. **spy repo의 메서드로 들어오는 인자들을, 각 메서드당 배정된 param필드에 입력해준다.**

   ```python
   class UserRepositorySpy:
       """ Spy to User Repository """
   
       def __init__(self) -> None:
           self.insert_user_param = {}
           self.select_user_param = {}
           
       
       def insert_user(self, name: str, password: str) -> Users:
           """ Spy to all the attributes """
   
           self.insert_user_param['name'] = name
           self.insert_user_param['password'] = password
   
           pass 
   
       def select_user(self, user_id: int = None, name: str = None) -> List[Users]:
           """ Spy to all the attributes """
   
           self.select_user_param['user_id'] = user_id
           self.select_user_param['name'] = name
           
           pass
   
   ```

   

6. **이제 각 method별로 return type에 맞는 mock_user()가 필요하다**

   ```python
   class UserRepositorySpy:
       #...
       
       def insert_user(self, name: str, password: str) -> Users:
           """ Spy to all the attributes """
   
           self.insert_user_param['name'] = name
           self.insert_user_param['password'] = password
   
           # return Users
           return mock_users()
   
       def select_user(self, user_id: int = None, name: str = None) -> List[Users]:
           #...
   
           return [mock_users()]
   
   ```





### 06 Repo Spy의 return에 사용될 Mock DTO 반환 mock_method 만들기

#### [output 목처리] repo메서드별 return type에 맞는 실제DTO객체(X) 목객체 처리할 수 있또록 return DTO반환 mock_method()가 필요하다

##### return type인 DTO의 목객체는 DTO가 있는 domain에 test폴더+ init을 만든다.

![image-20221001031333044](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001031333044.png)

##### mock method의 이름은 mock_xxx.py로 만든다.

1. `mock_users.py`생성





##### repo spy클래스와 달리, 실제DTO class + faker로 mock DTO반환 mock method를 만든다.

1. 실제 DTO class `Users`와 `faker`를 import

   ```python
   from faker import Faker
   from src.domain.models import Users
   
   faker = Faker()
   
   def mock_users() -> Users:
       """ Mocking Users """
   ```

   

2. **내용물은 딱히 없고, faker로 만들 재료들로, DTO 객체를 만들어서 반환해주면 된다.**

   ```python
   from faker import Faker
   from src.domain.models import Users
   
   faker = Faker()
   
   def mock_users() -> Users:
       """ Mocking Users """
   
       return Users(
           id=faker.random_number(digits=5),
           name=faker.name(),
           password=faker.name()
       )
   ```

   

##### mock method가 만들어지면, init에 올린다.

```python
from .mock_users import mock_users
```



#### Repo Spy에서 mock_method를 import해서 mock DTO를 반환받도록 사용한다.

- user_repsitory_spy

  ```python
  #...
  from src.domain.test import mock_users
  
  class UserRepositorySpy:
      """ Spy to User Repository """
  
      def __init__(self) -> None:
          self.insert_user_param = {}
          self.select_user_param = {}
          
      
      def insert_user(self, name: str, password: str) -> Users:
          #...
          return mock_users()
  
      def select_user(self, user_id: int = None, name: str = None) -> List[Users]:
          #...
          return [mock_users()]
  
  ```

  



#### repo spy가 완성되었으면, infra.test의 init에 올린다.

```python
from .user_repository_spy import UserRepositorySpy
```



### 07 use case test(register_test)마무리



#### 01 usecase Class 에서는, 인자 repository 대신, repository spy객체를 import해서 사용한다

1. `register_test.py`에서 repo의 spy객체인 infra>test의 user repository spy를 import하고 use case class의 인자로 spy객체를 생성해서 넣어준다.

   ```python
   #...
   from src.infra.test import UserRepositorySpy
   
   faker = Faker()
   
   def test_register():
       """ Testing registry method"""
   
       user_repo = UserRepositorySpy()
       register_user = RegisterUser(user_repo)
   ```

   

#### 02 use case의 input test는 javascript모양으로 dict를 구성하여 use class method에 던져준다.

```python
from faker import Faker
from .register import RegisterUser
from src.infra.test import UserRepositorySpy

faker = Faker()

def test_register():
    """ Testing registry method"""

    user_repo = UserRepositorySpy()
    register_user = RegisterUser(user_repo)

    attributes = {
        "name": faker.name(),
        "password": faker.name(),
    }

    response = register_user.register(name=attributes["name"], password=attributes["password"])
    
```



##### repo spy를 가진 usecase class는, 내부에서 repo method호출시, method별 param dict에 소유만 하고있고, mock_method()로 DTO목객체를 response에 건네줄 것이다.



#### 03 use case class test는 input과 output을 따로 테스팅한다



##### use case Input Test는 input이 usecase객체를 투입된 뒤, 사용된 repo spy 속 method별 param dict에 잘 들어갔는지 까지 검사

- response와 무관하게, 넣어준 input vs repo spy에 들어간 param dict 속 input을 비교한다

  ```python
  def test_register():
      """ Testing registry method"""
  
      user_repo = UserRepositorySpy()
      register_user = RegisterUser(user_repo)
  
      attributes = {
          "name": faker.name(),
          "password": faker.name(),
      }
  
      response = register_user.register(name=attributes["name"], password=attributes["password"])
  
      # Testing inputs
      assert user_repo.insert_user_param["name"] == attributes["name"]
      assert user_repo.insert_user_param["password"] == attributes["password"]
  ```

  

##### use case Output Test는 repo spy는 mock_users()를 return하지만 input과 별개이므로, [input이 제대로 안들어갔다면, False / None]으로 반환될 [validate_entry와 response존재유무]만 검사한다.



![image-20221001145410425](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001145410425.png)

```python
def test_register():
    """ Testing registry method"""

    user_repo = UserRepositorySpy()
    register_user = RegisterUser(user_repo)

    attributes = {
        "name": faker.name(),
        "password": faker.name(),
    }

    response = register_user.register(name=attributes["name"], password=attributes["password"])

    # Testing inputs
    assert user_repo.insert_user_param["name"] == attributes["name"]
    assert user_repo.insert_user_param["password"] == attributes["password"]

    # Tesing outputs
    assert response["Success"] is True
    assert response["Data"]
```



##### pytest -vs를 돌리되, 검증안하는 response의 실제내용(spy repo가 faker mock_users()를 통해 반환한 데이터)를 확인하면서 돌려보자.

```
pytest src\data\register_user\register_test.py -vs
```

![image-20221001145810603](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001145810603.png)



#### 04 use case Class는 input에 대한 fail test도 필요하다

##### 성공test를 복사해서 _fail 테스트 메서드를 만든다.

```python
def test_register_fail():
    """ Testing registry method in fail"""

```



##### js input attributes에 invalid type을 입력

- name은 str타입인데 int를 넣어준다.

```python
def test_register_fail():
    """ Testing registry method in fail"""

    user_repo = UserRepositorySpy()
    register_user = RegisterUser(user_repo)

    attributes = {
        # "name": faker.name(),
        "name": faker.random_number(digits=2),
        "password": faker.name(),
    }
```

##### input이 잘못되면, usecase -> repo spy의 메서드로 진입도 못하므로, assert시 repo spy의 param dict는 비어있어야한다.

![image-20221001155815817](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001155815817.png)

```python
# Testing inputs
# assert user_repo.insert_user_param["name"] == attributes["name"]
# assert user_repo.insert_user_param["password"] == attributes["password"]
assert user_repo.insert_user_param == {}
```



##### ouput test에서는 validate_entry여부인 Success는 False / return의 Data는 response = None 초기화상태를 응답하게 된다.

```python
# Tesing outputs
assert response["Success"] is False
assert response["Data"] is None
```



##### 참고) pytest 특정경로는 폴더까지만으로 줘도 된다.

```
pytest src\data\register_user\ -vs
```

![image-20221001160341867](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001160341867.png)



##### test완료된 git 처리

```
git status
git add .
git commit -m "feat: Creating register user use case and testing it"
```



- push후 PR까지 완료하기





## use cases 2 FindUser

![image-20221001204534126](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001204534126.png)

![image-20221001204542131](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001204542131.png)

![image-20221001204550933](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001204550933.png)

![image-20221001204612237](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001204612237.png)



### 작업 끝난 br의 local 폴더에서 새 br파서 작업 이어서 하기

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

     ![image-20221001205639510](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001205639510.png)

4. github에서 **빈 분기가 생성되었는지 확인하기**

   ![image-20221001205751422](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001205751422.png)





### 01 use case Class는 domain > use_cases 에 [Interface형태 Class]부터 정의하더라

#### as -Interface를 붙여쓰므로, class명에 직접 interface를 붙여쓰지 않으므로,  data>interfaces에 속하진 않는다.

1. src>domain>use_cases> `find_user.py`를 만든다

   ```python
   from abc import ABC, abstractclassmethod
   
   class FindUser(ABC):
       """ Interface to FindUser use case """
   
   ```



#### FindUser는 select_user를 쓸 것인데, id/unique_col/fk 에 따라 경우의 수가 있으므로, 그만큼 `by_xx`메서드를 나눠서 만든다. use_case Class의 메서드 returnType은 Dict[bool, DTO] 아니라면 Dict[bool, List[DTO]] 고정이다.

- by_id로 1개만 select한다고 하더라도, select메서드의 return type은 고정적으로 List[DTO]이다.

1. user_id, name 2개를 검색id로 사용할 수 있는데, 일단  `by_id`부터 정의한다.

   ```python
   from abc import ABC, abstractclassmethod
   from typing import Dict, List
   
   from src.domain.models import Users
   
   class FindUser(ABC):
       """ Interface to FindUser use case """
   
       @abstractclassmethod
       def by_id(cls, user_id: int) -> Dict[bool, List[Users]]:
           """ Specific Case """
   
           raise Exception("Should implement method: by_id")
   ```

   

2. 복사해서 `by_name` 메서드도 만든다.

   ```python
   @abstractclassmethod
   def by_name(cls, name: str) -> Dict[bool, List[Users]]:
       """ Specific Case """
   
       raise Exception("Should implement method: by_name")
   ```

3. 복사해서 `by_id_and_name`을 만든다.

   ```python
   @abstractclassmethod
   def by_id_and_name(cls, user_id: int, name: str) -> Dict[bool, List[Users]]:
       """ Specific Case """
   
       raise Exception("Should implement method: by_id_and_name")
   ```

4. 인터페이스 정의가 끝나면, use_cases의 init에 올려준다.

   ```python
   from .register_user import RegisterUser
   from .find_user import FindUser
   ```

   

### 02 data >  개별use_case폴더명 + init 생성후, 개별use_case동사명.py에다가 usecase Class를 정의한다.

#### domain>use_cases>의 py명과, data>개별use_case명은  동사_목적어지만, 개별use_case폴더 속 py파일은 동사로만 구성한다.

![image-20221001213404812](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001213404812.png)

1. src>data>`find_user`폴더 + init을 만들고, **`find.py`를 생성한다**





#### domain>use_cases에서 정의한 interface를 as -Interface로 import 해서 실제 Usecase Class(FindUser)를 구현한다.

```python
from src.domain.use_cases import FindUser as FindUserInterface

class FindUser(FindUserInterface):
    """ Class to define use case Find User """
```



#### usecase Class는 repository를 인자로 받는데, 이 때, data>interfaces에 정의한 repo interface를 as repo로 import해서 준다.

```python
from typing import Type
from src.domain.use_cases import FindUser as FindUserInterface
from src.data.interfaces import UserRepositoryInterface as UserRepository


class FindUser(FindUserInterface):
    """ Class to define use case Find User """

    def __init__(self, user_repository: Type[UserRepository]):
        self.user_repository = user_repository
```



#### usecase class method의 signature는 domain>use_cases에 정의해둔 interface의 signature를 복사해서 수정한다.

```python
class FindUser(FindUserInterface):
    """ Class to define use case Find User """

    def __init__(self, user_repository: Type[UserRepository]):
        self.user_repository = user_repository

    def by_id(self, user_id: int) -> Dict[bool, List[Users]]:
        """ Select User By id
        :param - user_i: id of the user
        :return - Dictionary with informations of the process 
        """
```



#### usecase method의 구현부는 response=None초기화, vaildate_entry로 시작하고, 응답형은 각각을 채운 dict로 한다.

1. entry에서 형 검사시 통과못하면, repository를 이용못하고 response가 None으로 반환된다.
2. entry 형검사 통과시, response에 인자로 받은 repository를 이용해 DTO를 이용한 결과가 들어간다
3. return dict로 validate_entry결과 및 response결과를 key에 넣어 반환한다.

```python

    def by_id(self, user_id: int) -> Dict[bool, List[Users]]:
        """ Select User By id
        :param - user_i: id of the user
        :return - Dictionary with informations of the process 
        """

        response = None
        validate_entry = isinstance(user_id, int)

        if validate_entry:
            response = self.user_repository.select_user(user_id=user_id)
        
        return {"Success": validate_entry, "Data": response}

```



4. **나머지 인자 경우의 수에 따른 select method들을 다 복사해서 구현해준다.**

```python
def by_name(self, name: str) -> Dict[bool, List[Users]]:
    """ Select User By name
        :param - name: name of the user
        :return - Dictionary with informations of the process 
        """

    response = None
    validate_entry = isinstance(name, str)

    if validate_entry:
        response = self.user_repository.select_user(name=name)

        return {"Success": validate_entry, "Data": response}

    def by_id_and_name(self, user_id: int, name: str) -> Dict[bool, List[Users]]:
        """ Select User By id and name
        :param - user_id: id of the user
        :return - Dictionary with informations of the process 
        """

        response = None
        validate_entry = isinstance(user_id, int) and isinstance(name, str)

        if validate_entry:
            response = self.user_repository.select_user(user_id=user_id, name=name)

            return {"Success": validate_entry, "Data": response}
```





### 03 usecase Class test(find_test.py)

#### test는 동일폴더 속 모듈 + faker를 기본적으로 import

1. src>data>find_user> `find_test.py`를 만든다.

2. test는 기본적으로 faker를 import한다.

3. test는 기본적으로 **동일폴더에 있는 테스트할 모듈을 `.모듈명`import한다**

   ```python
   from faker import Faker
   from .find import FindUser
   
   faker = Faker()
   ```





#### repo인자로 사용하는 usecase Class test는 infra>repo동일선상 test폴더의 repospy를 import해서 인자로 사용한다.

- repo spy: repo와 동일하게 생겼으나 내부에서 CRUD없이 method별 dict에 param들을 모아두고, 확인용으로 쓴다.

```python
from faker import Faker
from .find import FindUser
from src.infra.test import UserRepositorySpy

faker = Faker()


def test_by_id():
    """ Testing by_id method """

    user_repo = UserRepositorySpy()
    find_user = FindUser(user_repo)
```



#### usecase는 input을 js타입의 attr dict를 faker로 만들고, usecase method에 넣어주면, 내부 repo spy가 param dict에 물고 있을 것이다.

- attributes에는 "id"로 입력했지만, param dict는 **param명으로 입력되기 때문에, user_id=에 넣어줬으면 user_id로 저장시켰을 것이다.**

```python
def test_by_id():
    """ Testing by_id method """

    user_repo = UserRepositorySpy()
    find_user = FindUser(user_repo)

    attributes = {
        "id": faker.random_number(digits=2),
    }

    response = find_user.by_id(user_id=attributes["id"])

    # Testing Inputs
    assert user_repo.select_user_param["user_id"] == attributes["id"]
```





#### usecase method test는 output(dict)를 Success(validate_entry TorF )와 Data(None or not None) 2개를 검사하면 된다

```python
# Testing Inputs
assert user_repo.select_user_param["user_id"] == attributes["id"]

# Testing outputs
assert response["Success"] is True
assert response["Data"] is not None
```



#### test메서드1개마다 pytest를 돌린다. 탐색기폴더에 우클릭 상대경로 복사해서 pytest -vs를 돌리면 된다.

![image-20221001224900780](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001224900780.png)

```
pytest src\data\find_user -vs
```



#### spy repo는 내부에서 faker로 실시간으로 만드는 mock_entity()로 객체를 만들어 반환해주므로, print()로 pytest -vs에서 내용물을 확인할 수 있지만, 랜덤객체고 Type만 DTO다

![image-20221001225054616](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221001225054616.png)

```python
response = find_user.by_id(user_id=attributes["id"])

print(response)

# Testing Inputs
assert user_repo.select_user_param["user_id"] == attributes["id"]

# Testing outputs
assert response["Success"] is True
assert response["Data"] is not None
```

#### test까지 끝났으면 모듈을 init에 올려주기

```python
from .find import FindUser
```



#### by_id 이외에 메서드test는 생략하고 (need test)를 달아주고 commit

```
git commit -am "feat: implementing find user (need test)"
```



## use cases 3 FindPet

### 01 src>domain> use_cases > find_pet.py (interface) 정의하기

- use case  메서드들은 모두 Dict[bool, DTO or List[DTO]]를 반환한다
- interface는 Use case가 객체생성class가 될 것이므로 @abstractmethod로 정의한다.

```python
from abc import ABC, abstractmethod
from typing import Dict, List
from src.domain.models import Pets


class FindPet(ABC):
    """ Interface to FindPet use case """

    @abstractmethod
    def by_pet_id(self, pet_id: int) -> Dict[bool, List[Pets]]:
        """ Specific case """

        raise Exception("Should implement method: by_pet_id")

    @abstractmethod
    def by_user_id(self, user_id: int) -> Dict[bool, List[Pets]]:
        """ Specific case """

        raise Exception("Should implement method: by_user_id")

    @abstractmethod
    def by_pet_id_and_user_id(self, pet_id: int, user_id: int) -> Dict[bool, List[Pets]]:
        """ Specific case """

        raise Exception("Should implement method: by_pet_id_and_user_id")

```



- 완성되면 init.py에 올려준다.

  ```python
  from .register_user import RegisterUser
  from .find_user import FindUser
  from .find_pet import FindPet
  ```





### 02 src>data>find_pet폴더>init + find.py (usecase Class) 정의하기

- src>domian>use_cases>find_pet.py에 정의한 interface를 바탕으로 usecase class 정의하기
  - repo를 인자로 작동하는 usecase Class
  - 메서드는 주로 reponse=None초기화, validate_entry로 시작한다.
    - entry가 참일 때, response에 repository의 메서드 호출 결과값을 재할당해준다. 아니면 None으로 반환된다.
  - repo select메서드의 반환타입은 무조건 복수 DTO List[DTO]이다.
  - usecase select메서드(find)의 반환타입은 무조건 Dict[bool, DTO or List[DTO]]이다.

  ```python
  from typing import Dict, List, Type
  from src.data.interfaces import PetRepositoryInterface as PetRepository
  from src.domain.use_cases import FindPet as FindPetInterface
  from src.domain.models import Pets
  
  
  class FindPet(FindPetInterface):
  
      def __init__(self, pet_repository: Type[PetRepository]) -> None:
          self.pet_repository = pet_repository
  
      def by_pet_id(self, pet_id: int) -> Dict[bool, List[Pets]]:
          """ Select Pet By pet_id 
          :param - pet_id: id of the pet
          :return - Dictionary with informations of the process
          """
  
          response = None
          validate_entry = isinstance(pet_id, int)
  
          if validate_entry:
              response = self.pet_repository.select_pet(pet_id=pet_id)
  
          return {"Success": validate_entry, "Data": response}
  
      def by_user_id(self, user_id: int) -> Dict[bool, List[Pets]]:
          """ Select Pet By user_id 
          :param - user_id: id of the user
          :return - Dictionary with informations of the process
          """
  
          response = None
          validate_entry = isinstance(user_id, int)
  
          if validate_entry:
              response = self.pet_repository.select_pet(user_id=user_id)
  
          return {"Success": validate_entry, "Data": response}
  
      def by_pet_id_and_user_id(self, pet_id: int, user_id: int) -> Dict[bool, List[Pets]]:
          """ Select Pet By pet_id 
          :param - pet_id: id of the pet
          :return - Dictionary with informations of the process
          """
  
          response = None
          validate_entry = isinstance(pet_id, int) and isinstance(user_id, int)
  
          if validate_entry:
              response = self.pet_repository.select_pet(pet_id=pet_id, user_id=user_id)
  
          return {"Success": validate_entry, "Data": response}
  
  ```

- 작성된 class는 init에 올려준다.

  ```python
  from .find import FindPet
  ```

  

### 03 src>data>find_pet폴더> find_test.py (usecase Class test) 정의하기

#### 01 usecase test는 인자로 들어가는 repo에 대한 repo spy가 필요 -> repo spy의 반환 값인 mock_DTO 반환 [메서드] mock_pet()가 먼저 필요

#### 02 mock_pet(DTO) 반환 moc_method() 부터 만들기

- faker + DTO import로 아무거나 생성해서 반환해주면 된다. **mock객체는 반환되는 필드값이 중요하지 않다.**
  - enum에 대해서는 string값을 넣어준다. DB든 dto든 string으로 들어가있다?!
- src>domain>models에 속하므로 models의 같은 선상인 **src>domain>test에 mock_pet.py에 메서드를 만들어준다.** 
  - **실시간 객체가 필요한데, class가 아니라 `목객체 생성 mock_method`로 만들어주면 된다.**
- mock_users.py -> 나중에 mock_user.py로 변경해야할 것 같다.

```python
from faker import Faker
from src.domain.models import Pets

faker = Faker()

def mock_pet() -> Pets:
    """ Mocking Pet 
    :param - None
    :return - Fake Pet registry
    """

    return Pets(
        id=faker.random_number(digits=5),
        name=faker.name(),
        specie="dog",
        age=faker.random_number(digits=1),
        user_id=faker.random_number(digits=5)
    )
```

- 만들어졌으면 init에 올려준다.

  ```python
  from .mock_users import mock_users
  from .mock_pet import mock_pet
  ```



#### 03 src>infra>test에 pet_repository_spy.py를 정의한다.

- repo폴더 같은 선상의 test폴더더에 `pet_repository_spy.py`생성

- repo spy는 **class에 원본repo method별  -> init에 param_dict를 만들어, input을 사용하지 않고 모아둔다.**

  - **output도 mock_dto()메서드로 생성해서 반환해준다.** 
    - insert처럼 단수일 경우 mock_pet()
    - select처럼 복수일 경우 [ mock_pet() ]

  ```python
  from typing import List
  from src.domain.test import mock_pet
  from src.domain.models import Pets
  
  class PetRepositorySpy:
      """ Spy to Pet Repository """
  
      def __init__(self) -> None:
          self.insert_pet_param = {}
          self.select_pet_param = {}
  
      def insert_pet(self, name: str, specie: str, age: int, user_id: int) -> Pets:
          """ Spy all the attributes """
  
          self.insert_pet_param['name'] = name
          self.insert_pet_param['specie'] = specie
          self.insert_pet_param['age'] = age
          self.insert_pet_param['user_id'] = user_id
  
          return mock_pet()
  
      def select_pet(self, pet_id: int = None, user_id: int = None) -> List[Pets]:
          """ Spy all the attributes """
  
          self.select_pet_param['pet_id'] = pet_id
          self.select_pet_param['user_id'] = user_id
  
          return [mock_pet()]
  
  ```





- 완성됬으면 init에 올려준다.

  ```python
  from .user_repository_spy import UserRepositorySpy
  from .pet_repository_spy import PetRepositorySpy
  ```

  

#### 04 드디어 use_case_test(find_test.py)에 spy repo 사용해서 정의하기

- repo spy를 import하고, 원본 usecase Class를 import해서 정의해서 **usecase class의 method들을 test_한다**

  ```python
  from faker import Faker
  from src.infra.test import PetRepositorySpy
  from .find import FindPet
  
  faker = Faker()
  
  def test_by_pet_id():
      """ Testing by_pet_id method in FindPet """
  ```

- usecase 객체를 생성시 repo spy를 넣어서 생성한다.

- usecase 객체는 **request input을 받으니,  dict로 input을 정의하고 method에 하나씩 뽑아서 넣어준다.**

  ```python
  def test_by_pet_id():
      """ Testing by_pet_id method in FindPet """
  
      pet_repo = PetRepositorySpy()
      find_pet = FindPet(pet_repo)
  
      attributes = {"pet_id": faker.random_number(digits=2)}
      response = find_pet.by_pet_id(pet_id=attributes["pet_id"])
  ```





##### usecase method의 test 중 input test는 repo spy가 먹은 input vs attributes로 만든 input을 비교한다.

```python
def test_by_pet_id():
    """ Testing by_pet_id method in FindPet """

    pet_repo = PetRepositorySpy()
    find_pet = FindPet(pet_repo)

    attributes = {"pet_id": faker.random_number(digits=2)}
    response = find_pet.by_pet_id(pet_id=attributes["pet_id"])

    # Testing Input
    assert pet_repo.select_pet_param["pet_id"] == attributes["pet_id"]
```





##### usecase method의 test 중 output test는 repo spy가 반환하는 목객체가 중요한게 아니라 usecase반환 dict의 success T/F유무 + data 존재유무를 확인한다.

```python
# Testing Output
assert response["Success"] is True
assert response["Data"] is not None
```



#### 05 1개 메서드 완성시마다 pytest를 돌린다.

```
 pytest [파일이 속한 폴더 상대경로 복사] -vs
```



### 04 나머지 method 생략 -> (need test)로 커밋하기

```
git status

git add .

git commit -am "feat: implementing FindUser use case (need test)"

git commit -am "feat: implementing FindUser use case (need test)"

git log --oneline
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

   4. repo_select_X
   5. id포함 faker -> entity모델 객체
   6. id포함 재료들 + engine db insert -> db데이터만들어놓기
   7. id, fk재료들 + select메서드 -> List[entity모델객체들]
   8. engine db delete

   
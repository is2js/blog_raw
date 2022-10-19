---
toc: true
layout: post
title: 클린아키텍쳐 03 OneEntity UseCase구현과 test를 위한 UseCaseSpy
description: python clean architecture

categories: [python, flask, cleanarchitecture]
image: "images/posts/python.png"
---

# 클린 아키텍쳐 3장


- 클래스 관계 다이어그램에서 리포지토리와 직접 연결되는 사용 사례를 넣었습니다. 올바른 것은 각 저장소의 인터페이스와 직접 연결되는 것입니다.

- user의 경우 register -> find 순으로 진행되었지만
- fk를 들고 있는 Many의 pet의 경우 find -> register로 진행된다?!
  - 등록도 없는데 find부터 해도된다? use case는 이미 repo의 insert가 개발된 상황에서 test해서 상관 없다.
  - **pet은 register작업시, user를 find해서 pk가 존재하는지부터 검사해야하기 때문에, fk의 register는 나중에(pk/one table register- > find 까지 끝난 후) 작업한다.**



## 1 ManyEntity Register use case - register_pet

### 01 github에서 branch 따서 새작업br 시작하기(했던 것)

1. 기존 작업 push이후 merge까지 끝내기
2. github에서 `feat/registry_pet` 이름의 **br 딴 후에 clone 준비하기**
3. local에서 `registryPet`폴더 만들고 여기다가 -b clone 받기
4. local 폴더에 **백업해둔 .db를 복붙해서 가져다놓기**
5. 터미널에서
   1. clone 코드폴더로 cd 들어가기
   2. ls로 파일 확인
   3. venv 생성 및 활성화
   4. venv 진입후 requirements.txt 설치, pre-commit install
   5. vscode로 진입해서 venv select



### 02 src>domain>use_cases>에서 register_pet.py 인터페이스 만들기

#### 관계 Register usecase Method느 fk(id)대신 fk의 Dict정보를 인자로 받는다.

1. **interface는 사실상 객체context를 사용안하므로 굳이 메서드를 self의 abstractmethod로 안만들어도 된다고 한다.@classmethod만 달아서 cls 다 달아서 cls로 인터페이스 메서드를 사용한다.**

2. **pet의 age는 user_id보다 더 자신 것 같지만 사실상 아니다. `age는 없어도 되는 nullable`이라면 -> kargs로 주기 위해, `fk에 해당하는 user_information보다 더 뒤쪽에 놓고  = None으로 초기화`한다**

3. **use case에서는 `user_id를 직접사용하는 repo`와 달리 `user_id대신 user_information dict`를 인자로 받는다. type은 id와 age의 int, name/password의 str으로 Dict type을 콤마로 연결해서 준다.**

4. use case 반환타입은 Dict[bool, DTO or List[DTO]] 고정이다.

   ```python
   from abc import ABC, abstractclassmethod
   from typing import Dict
   from src.domain.models import Pets
   
   
   class RegisterPet(ABC):
       """ Interface to RegisterPet use case """
   
       @classmethod
       def register(cls, name: str, specie: str, user_information: Dict[int, str], age: int = None) -> Dict[bool, Pets]:
           """ use case """
   
           raise Exception("Should implement method: register")
   ```

5. init에 올려준다.

   ```python
   from .register_user import RegisterUser
   from .find_user import FindUser
   from .find_pet import FindPet
   from .register_pet import RegisterPet
   ```

### 03 src>data>(개별UC)register_pet >(동사)register.py + init.py을 정의한다.

1. 만든 usecase Interface import 

2. usecase Class는 repo를 인자로 받는다. 생성자 정의시, repo Interface를 import해서 지정

   ```python
   from typing import Type
   from src.data.interfaces import PetRepositoryInterface as PetRepository
   from src.domain.use_cases import RegisterPet as RegisterPetInterface
   
   class RegisterPet(RegisterPetInterface):
       """ Class to define use case: Register Pet"""
   
       def __init__(self, pet_repository: Type[PetRepository]) -> None:
           pass
   ```



### 04 RegisterManyEntity usecase Class는 단독 usecase와 달리, repo외 [FindFkEntity_usecase_Class]를 생성자의 인자로 추가로 받는다.

1. import해서 인자의 Type으로 써야한다.

   ```python
   from typing import Type
   from src.data.find_user import FindUser
   from src.data.interfaces import PetRepositoryInterface as PetRepository
   from src.domain.use_cases import RegisterPet as RegisterPetInterface
   
   class RegisterPet(RegisterPetInterface):
       """ Class to define use case: Register Pet"""
   
       def __init__(self, pet_repository: Type[PetRepository], find_user: Type[FindUser]) -> None:
           self.pet_repository = pet_repository
           self.find_user = find_user
   ```

   

2. interface의 메서드를 구현한다.

   - FK register 메서드는 fk(int)값 대신, fk_information dict가 인자로 들어가있다.

   ```python
   from typing import Dict, Type
   from src.data.find_user import FindUser
   from src.data.interfaces import PetRepositoryInterface as PetRepository
   from src.domain.models import Users
   from src.domain.models import Pets
   from src.domain.use_cases import RegisterPet as RegisterPetInterface
   
   class RegisterPet(RegisterPetInterface):
       """ Class to define use case: Register Pet"""
   
       def __init__(self, pet_repository: Type[PetRepository], find_user: Type[FindUser]) -> None:
           self.pet_repository = pet_repository
           self.find_user = find_user
   
       def register(self, name: str, specie: str, user_information: Dict[int, str], age: int = None) -> Dict[bool, Pets]:
           """Register Pet use case
           :param - name: pet name
                  - specie: specie of the pet
                  - age: age of the pet
                  - user_information: Dictionary with user_id and/or user_name
           :return - Dictionary with informations of the process
           """
   ```

3. 구현부는 `repsone = None` 와  `validate_entry` 검사로 시작은 동일하지만, **`user_information -> find_user` 과정이 더 추가**된다.

   ```python
   class RegisterPet(RegisterPetInterface):
       #...
   
       def register(self, name: str, specie: str, user_information: Dict[int, str], age: int = None) -> Dict[bool, Pets]:
           #...
           
           response = None
   
           # Validate entry and tring to find an user
           validate_entry = isinstance(name, str) and isinstance(specie, str)
   ```

   

#### RegisterManyEntity Class의 register메서드 내부에서는 [private emthod]를 추가해서, 생성자인풋 [self.FindFK_usecase_class]로 method input인 [fk_information]을 검증한다.

- **my) `미리 소유한 self.객체필드`로, public method의 외부input을 다룰 땐, `__private method로 정의`해서 다룬다.**
- findFK_usecase_class의 결과는 Dict[bool, List[DTO]]다

```python
class RegisterPet(RegisterPetInterface):
    #...

    def __init__(self, pet_repository: Type[PetRepository], find_user: Type[FindUser]) -> None:
        self.pet_repository = pet_repository
        self.find_user = find_user

    def register(self, name: str, specie: str, user_information: Dict[int, str], age: int = None) -> Dict[bool, Pets]:
        #...

    def __find_user_information(self, user_information: Dict[int, str]) -> Dict[bool, List[Users]]:
        """ Check user infos and select user
        :param - user_information: Dictionary with user_id and/or usre_name
        :return - Dictionary with the response of find_use use case
        """
        pass

```



##### find_user 역시 select라서 id and/or unique key의 경우의 수에 따라서 결과물을 반환하게 한다. 실패가능성이 있는 호출은 가변변수 None으로 초기화해놓고 시작한다.

- **usecase method는 `input 검증통과시만 그외 실패` or `경우의 수에 따라서 실패가능`에 대해 `결과물을 받아주는 가변변수를 실패로 초기화(response = None)`해놓고 `if성공시`의 패턴을 가진다.**
- 여기서는 `fk_founded = None`으로 초기화해놓고, **input 변수 경우의수에 따라 나눠서 하되, `실패 input에 대해서, 성공결과와 똑같은 형태의 Dict[bool, None]`형태로 반환하게 한다**
- **input이 dict일 때, `keys()`를 통해 `경우의 수`를 나눈다.**



##### repo select: input을 keyword=None으로 주고, 경우의수별 if문으로 한방에 처리한다

- **repo:  select `input을 keyword로 만들고 -> 경우의 수별 if문`으로 처리한다.**

  ![image-20221003163626647](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221003163626647.png)



##### usecase select(find): input이 js -> dict에 담겨서 들어오며, select용 input의 경우의 수 마다 args로 정의한 method를 각각 만들어놓는다.

![image-20221003163933181](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221003163933181.png)

​	![image-20221003164344873](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221003164344873.png)

##### manyentity register usecase 속 FKentity FindFk -> 자신처럼 js의 dict로 담겨들어오긴 하나, 확정된 정보가 아니라 dict.keys()로 빼놓고, repo kargs처럼 keys에 담긴 경우의 수별 if문을 사용해 한방에 처리하되, args 정의한 method를 호출하게 한다.

- 따지자면, repo처럼 usecase도 keyword 인자로 만들어놓고,  if문으로 한방에 처리하면 안되려나..**어차피 다른 usecase register에서 인자의 경우의수를 따져야하는데?**

- **경우의 수별 아예  user_information dict에 `없는, 실패 경우의 수도 early return`  Dict[bool, List[DTO]]로 처리한다.**
  - 인자가 있는 경우는 가변변수 None을 채워서 맨 끝에서 반환
  - 중간에 실패한 경우 early return 반환

```python
def __find_user_information(self, user_information: Dict[int, str]) -> Dict[bool, List[Users]]:
    """ Check user infos and select user
        :param - user_information: Dictionary with user_id and/or usre_name
        :return - Dictionary with the response of find_use use case
        """

    user_founded = None
    user_params = user_information.keys()

    if "user_id" in user_params and "user_name" in user_params:
        user_founded = self.find_user.by_id_and_name(user_information["user_id"], user_information["user_name"])

        elif "user_id" not in user_params and "user_name" in user_params:
            user_founded = self.find_user.by_name(user_information["user_name"])

            elif "user_id" in user_params and "user_name" not in user_params:
                user_founded = self.find_user.by_id(user_information["user_id"])

                else:
                    return {"Success": False, "Data": None}


                return user_founded
```



#### 다시  user_information method input을 \_\_private 메서드로 결과물을 받아, entry와 동시에 \_\_private 메서드의 결과물 중 "Success"여부만으로 같이 checker의 검증 조건으로 준다.

- private메서드지만, FindUser의 response로서 결과물 검증은 Success(T or F)로 해버린다.

```python

    def register(self, name: str, specie: str, user_information: Dict[int, str], age: int = None) -> Dict[bool, Pets]:
        #...
        response = None

        # Validate entry and tring to find an user
        validate_entry = isinstance(name, str) and isinstance(specie, str)
        user = self.__find_user_information(user_information)
        checker = validate_entry and user["Success"]
```



#### validate_entry and FindFK결과물["Success"] 둘다 참인 경우(if check:)에만 response에 repository의 insert(register) method 호출의 결과물을 담는다.(검증 성공시만 None에 재할당해서 담기)

- **단독 usecase: `response = None  -> if validate_entry`**
- **관계FK가진 register usecase: **
  - **`resonse = None`** 
  - **`if checker`**
    - **validate_entry** and
    - FK_information -> FindFK.find~ by private메서드
      - 결과: **Dict[bool, List[DTO] or None**
        - **`FK_Dict["Success"]`**
- **user_information + FindUser의 검증시, 딱히 결과물보다는 Dict의 Success여부만 중요하며, 검증후 호출시 들어갈 것은 user_information의 "user_id"값 밖에 없다**

- 해당 method의 결과물 Success에 들어가는 것은, validate_entry가 아닌 checker가 들어간다.

```python
def register(self, name: str, specie: str, user_information: Dict[int, str], age: int = None) -> Dict[bool, Pets]:
    #...
    response = None

    # Validate entry and tring to find an user
    validate_entry = isinstance(name, str) and isinstance(specie, str)
    user = self.__find_user_information(user_information)
    checker = validate_entry and user["Success"]

    if checker:
        response = self.pet_repository.insert_pet(name, specie, age, user_information["user_id"])
        
    return {"Success": checker, "Data": response}

        def __find_user_information(self, user_information: Dict[int, str]) -> Dict[bool, List[Users]]:
	        #...
```

- UseCase는 아직 상위에서 import해갈 일이 없어서 **init에 안올린다.**
- 대신 **test시에는 `현재폴더의 모듈import`로서  from `.use_case동사` import method**형태로 사용하고 있다.



## 2 ManyEntity Register use case Test - register_test.py

1. `resigster_test.py`를 만든다.



### 01 repo spy와 더불어, ManyEntity의 register use case는 추가된 인자에 대한 [FindFK spy]도 만들어야한다.

#### 개별usecase 와 동일한 선상의 src>data에 test폴더를 만들고 find_user_spy.py와 init을 만든다.

![image-20221003233517745](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221003233517745.png)

#### spy는 input을 self.필드에 머금고, output은 mock객체로 내어야하니, Users(DTO)와 mock_users()를 import한다

```python
from src.domain.models import Users
from src.domain.test import mock_users
```



#### usecase 는 원래 생성자에 repo를 인자로 받았지만,  UsecaseSpy는 실제 repo가 필요없이 RepoSpy가 들어갈 것이므로 Type을 any로 준다!!!! 

![image-20221003234225144](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221003234225144.png)

```python
class FindUserSpy:
    """ class to define use case: Select User """

    def __init__(self, user_repository: any) -> None:
        self.user_repository = user_repository
        
```



- FindUser에서 method들을 확인하고, 그 이름별로 _param을 붙인 dict를 갯수대로 생성자에서 초기화하여, 추후 input들을 들고 있을 수 있게 한다.

![image-20221003234318140](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221003234318140.png)

```python
class FindUserSpy:
    """ class to define use case: Select User """

    def __init__(self, user_repository: any) -> None:
        self.user_repository = user_repository
        self.by_id_param = {}
        self.by_name_param = {}
        self.by_id_and_name_param = {}
```



#### 원본 usecase의 메서드들 signature를 복사해서 가져오되,  구현부에는 다들 자기 param dict에 할당해준다.

![image-20221003234549687](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221003234549687.png)

```python

class FindUserSpy:
    """ class to define use case: Select User """

    def __init__(self, user_repository: any) -> None:
        self.user_repository = user_repository
        self.by_id_param = {}
        self.by_name_param = {}
        self.by_id_and_name_param = {}

    def by_id(self, user_id: int) -> Dict[bool, List[Users]]:
        """ Select User by id """
        self.by_id_param["user_id"] = user_id

    def by_name(self, name: str) -> Dict[bool, List[Users]]:
        """ Select User by name """
        self.by_name_param["name"] = name


    def by_id_and_name(self, user_id: int, name: str) -> Dict[bool, List[Users]]:
        """ Select User by id and name """
        self.by_id_and_name_param["user_id"] = user_id
        self.by_id_and_name_param["name"] = name
        
```



#### repo spy와 또다른 차이점은, method별 param dict에 input 저장외, [use case 종특, return {"Success", "Data"}]를 위해 resonse/validate_entry 처리를 해줘야한다. -> repomethod호출이 아닌 mock_method() + [] 를 활용해서 Data(response, DTO or List[DTO])를 채워준다.

![image-20221003235723271](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221003235723271.png)



```python
class FindUserSpy:
    #...
    def by_id(self, user_id: int) -> Dict[bool, List[Users]]:
        """ Select User by id """
        self.by_id_param["user_id"] = user_id

        response = None
        validate_entry = isinstance(user_id, int)

        if validate_entry:
            response = [mock_users()]

        return {"Success": validate_entry, "Data": response}
```

- 나머지 2개 메서드도 다 채워준다.
  - **실제repo의 메서드호출 대신, mock_method로 객체를 생성해서 반환해준다.**

```python
    def by_name(self, name: str) -> Dict[bool, List[Users]]:
        """ Select User by name 
        :param - name: name of the user
        :return  - Dictionary with informations of the process
        """
        self.by_name_param["name"] = name

        response = None
        validate_entry = isinstance(name, str)

        if validate_entry:
            response = [mock_users()]

        return {"Success": validate_entry, "Data": response}


    def by_id_and_name(self, user_id: int, name: str) -> Dict[bool, List[Users]]:
        """ Select User by id and name
        :param - user_id: id of the user
               -  name: name of the user
        :return  - Dictionary with informations of the process
        """
        self.by_id_and_name_param["user_id"] = user_id
        self.by_id_and_name_param["name"] = name

        response = None
        validate_entry = isinstance(user_id, int) and isinstance(name, str)

        if validate_entry:
            response = [mock_users()]
        
        return {"Success": validate_entry, "Data": response}
```



- 갖다쓰기 위해서  init에 올려준다.

```python
from .find_user_spy import FindUserSpy
```





### 02 register_(ManyEntity)test.py 작성하기

#### import할 것들 정리

- test니까, **faker**를
- UseCase의 인자로 들어가는 것들의 spy들을
  - **PetRepositorySpy**
  - **FindUserSpy**(ManyEntity Find Spy)
    - usecase로서 **UserRepositorySpy**
- test할 UseCase인 **RegisterPet**

```python
from faker import Faker
from src.infra.test import PetRepositorySpy, UserRepositorySpy
from src.data.test import FindUserSpy


faker = Faker()
```



#### pet_repoSpy, find_user with user_repoSpy for FindPet

```python
def test_register():
    """ Testing Register method in RegisterPet"""

    pet_repo = PetRepositorySpy()
    find_user = FindUserSpy(UserRepositorySpy())

    register_pet = RegisterPet(pet_repo, find_user)
```



#### usecase test라면 input은 attributes dict지만, 내부 fk필드는 id int값이 아닌, fk_information = {}으로! 내부 정보는 user전체정보가 아닌 find==select시 필요한 필드들(user_id and/or user_name)으로 구성한다

```python
def test_register():
    """ Testing Register method in RegisterPet"""

    pet_repo = PetRepositorySpy()
    find_user = FindUserSpy(UserRepositorySpy())

    register_pet = RegisterPet(pet_repo, find_user)

    attributes {
        "name": faker.name(),
        "specie": faker.name(),
        "age": faker.random_number(digits=1),
        "user_information": {
            "user_id": faker.random_number(digits=5),
            "user_name": faker.name(),
        },
    }

```



#### usecase method test는 attributes -> usecase객체.method를 호출하여 얻는 response -> Dict[bool, DTO or List[DTO]]를 output으로 test한다. 이 때, method input에 fk_id 대신 fk_information with select args로 구성되는 것이 차이점이다.

```python
def test_register():
    #...
    attributes = {
        "name": faker.name(),
        "specie": faker.name(),
        "age": faker.random_number(digits=1),
        "user_information": {
            "user_id": faker.random_number(digits=5),
            "user_name": faker.name(),
        },
    }

    response = register_pet.register(
        name=attributes["name"],
        specie=attributes["specie"],
        age=attributes["age"],
        user_information=attributes["user_information"],
    )
```



#### input test에서, fk_information은 pk(Pet)을 register하는데, only id만 필요하므로, fk의 id만 Pet repo인자/ Pet repoSpy의 param dict로 들어간다. usecase Input test는 pet_repoSpy의 param dict를 검사하므로 검사하려면 fk_information정보 중에 id만 검사한다.

- fk_information dict는, usecase input으로 들어가지만, repo에는 id만 들어간다.
  - **fk_information dict**는 **private메서드에서  fk자신의 usecase 중 Find method에 사용**되어, 있으면 **Dict[bool, List[DTO]]**를 없으면  None이다**. 결과변수 user에는 Success, Data가 T/F, List[DTO] or None**으로 있을 것이다.

![image-20221004012819609](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221004012819609.png)

```python
def test_register():
    """ Testing Register method in RegisterPet"""

    pet_repo = PetRepositorySpy()
    find_user = FindUserSpy(UserRepositorySpy())

    register_pet = RegisterPet(pet_repo, find_user)

    attributes = {
        "name": faker.name(),
        "specie": faker.name(),
        "age": faker.random_number(digits=1),
        "user_information": {
            "user_id": faker.random_number(digits=5),
            "user_name": faker.name(),
        },
    }

    response = register_pet.register(
        name=attributes["name"],
        specie=attributes["specie"],
        age=attributes["age"],
        user_information=attributes["user_information"],
    )

    # Testing Inputs
    assert pet_repo.insert_pet_param["name"] == attributes["name"]
    assert pet_repo.insert_pet_param["specie"] == attributes["specie"]
    assert pet_repo.insert_pet_param["age"] == attributes["age"]
    assert pet_repo.insert_pet_param["user_id"] == attributes["user_information"]["user_id"]

    print(pet_repo.insert_pet_param)
```



#### pet repo spy의 param dict를 print하며 pytest

```
pytest src\data\register_pet -vs
```

![image-20221004015421420](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221004015421420.png)



#### FindFkSpy()로 들어간 fk_information input은 따로 검사한다. 

- 아래부분을 제거하고 FindUserSpy 객체가 어떻게 머금고 있는지 확인해보자.

  ```python
  assert pet_repo.insert_pet_param["user_id"] == attributes["user_information"]["user_id"]
  ```

- **일반적으로 repo spy가 method별 param_dict를 필드로 머금고 있다.**

  - user_repo_spy

    ![image-20221004015854620](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221004015854620.png)

- **그 상위단계인 UseCase의 Spy는 repo spy(any) + UseCase메서드 param_dict를 머금고 있다.**

  - find_user_spy

  ![image-20221004020007284](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221004020007284.png)



#### repo spy(CRUD 인자별 경우의 수 by if)처럼, UseCase Spy(인자별 각 method)도 같은 repo와 같은 input을 받으며, 경우의수별 method들에 대한 param_dict를 가지고 있으니,  usecase_spy로 fk_information input정보를 param_dict로 test한다

```python
# Testing Inputs
assert pet_repo.insert_pet_param["name"] == attributes["name"]
assert pet_repo.insert_pet_param["specie"] == attributes["specie"]
assert pet_repo.insert_pet_param["age"] == attributes["age"]

# Testing FindUser Inputs
assert find_user.by_id_and_name_param["user_id"] == attributes["user_information"]["user_id"]
assert find_user.by_id_and_name_param["name"] == attributes["user_information"]["user_name"]
```



```python
pytest src\data\register_pet -vs
```



#### usecase Output 검사는 고정이다. usecase.method의 결과response는 Dict[bool, List[DTO] or DTO]

```python
# Testing Outputs
assert response["Success"] is True
assert response["Data"] is not None
```



#### commit -> branch  확인후 -> push -> merge

```
git status
git add .
git commit -am "feat: Implementng register_pet use case"
git commit -am "feat: Implementng register_pet use case"
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

   
---
toc: true
layout: post
title: sqlalchemy으로 DB 생성  alembic으로 DB 수정
description: ORM으로 DB를 생성부터 수정/삭제까지 

categories: [python, database, sqlalchemy, alembic, orm]
image: "images/posts/python.png"
---

### sqlalchemy ORM -> DB 생성 및 alembic을 통한 ORM -> DB 수정



#### 01 PyConsole로 ORM -> DB create(only)

##### 전체 Entity 한번에 생성(Base + engine + Entities)

1. **python console**을 열고

2. **Base**객체와 engine객체 생성을 위한 **DBConnnectionHandler** import

3. **Entity들을 메모리에 올리기**위한 entity import

4. **Base.metadata.create_all( engnine )**명령어로 생성

   ```python
   from infra.config.base import Base
   from infra.config.connection import DBConnectionHandler
   
   from infra.entities import *
   
   #with DBConnectionHandler() as db:
   #    Base.metadata.create_all(db.get_engine())
   engine = DBConnectionHandler().get_engine()
   Base.metadata.create_all(engine)
   ```

   

   

##### 특정 Entity만 + engine으로 생성

1. **python console**을 열고

2. engine객체 생성을 위한 **DBConnnectionHandler** import

3. **특정 entity** import

4. Entity.`__table__`.create( **engine** )

   ```python
   from infra.config.connection import DBConnectionHandler
   
   from infra.entities import Entity
   
   
   Entity.__table__.create(engine)
   ```



#### 02 DB 생성 로직화 with .env and fake data

##### 구조 설정

1. **python-dotenv 환경설정: 블로그 참고**

   ![image-20221016235532505](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221016235532505.png)

2. src > infra > config > **`connection.py`의 DBConnectionHandler**의 

   ![image-20221016235601151](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221016235601151.png)

   1. connection_string을 **settings.DATABASE_URL에서 가져온다.**

      ```python
      from sqlalchemy import create_engine
      from sqlalchemy.orm import sessionmaker
      from src.config.settings import settings
      
      
      class DBConnectionHandler:
          def __init__(self) -> None:
              # self.__connection_string = "mysql+pymysql://root:564123@localhost:3306/cinema"
              self.__connection_string = settings.DATABASE_URL
              self.__engine = self.__create_database_engine()
              self.session = None
      ```

   2. **contextManager에서 새로 engine연결하면, `sqlite-memory DB`는 손실되니, `get_current_sesion`으로 `현재 생성된 conn객체(engine을 가진)로 Session클래스`를 반환해주는 메서드를 만든다.**

      ```python
      def get_current_session(self):
          Session = sessionmaker(bind=self.__engine)
          return Session
      ```

3. src > infra > config > **`db_creator.py`를 만들고 `Session클래스`와 `Base + engine의 create_db 메서드`를 구현한다.**

   ![image-20221016235616216](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221016235616216.png)

   - **Session 클래스를 만들어놓고, main이든 어디든 session=Session()객체를 만들어, 연결된 engine으로 db에 쿼리를 날릴 수 있다.**
   - **`Base.metadata.create_all( engine )`시 `entityModel들이 메모리에 떠있어야`하는데, `main에서 실행시킬 것이므로 그 전까지 떠있기만 하면 된다`**

   ```python
   from .base import Base
   from .connection import DBConnectionHandler
   
   conn = DBConnectionHandler()
   engine = conn.get_engine()
   
   Session = conn.get_current_session()
   
   
   def create_db():
       Base.metadata.create_all(engine)
   ```



4. **`일회성`으로 실행시킬 script이며, `entities정보 + configs정보`를 통하하여 사용할 `. > create_database.py`를 생성한다**

   ![image-20221017000354366](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221017000354366.png)

   - **src>infra>config>`db_creator.py`의 `Session`클래스 + `create_db()`메서드를 import해서 사용한다**

   - **src>infra>entities > `*` 전체를 init에서 올려서 `db생성시 entityModel들 메모리에 import`시킨다**

   - 메모리상 entity들 + create_db()를 통해 **db생성후 `Session()`객체로 `load_fake_data`를 구현하여 샘플 데이터를 집어넣는다.**

     ```python
     from src.infra.config.db_creator import create_db, Session
     
     from src.infra.models import *
     
     
     def _load_fake_data(session):
         group1 = Groups(group_name='1-MDA-7')
         group2 = Groups(group_name='1-MDA-9')
         session.add(group1)
         session.add(group2)
         session.commit()
         session.close()
     
     
     def create_database(load_fake_data: bool = False):
         create_db()
     
         if load_fake_data:
             _load_fake_data(Session())
     ```



###### 다른 entity그룹을 생성 01: : 같은Base를 공통으로 상속해서 사용하되, .create_base.py의 생성대상entity 메모리에 다른 entity그룹을 import하기 -> load fake data를 수정해야하니 비추

```python
from src.infra.config.db_creator import create_db, Session

# 변경
#from src.infra.models import *
from src.infra.entities import *

def _load_fake_data(session):
	...

def create_database(load_fake_data: bool = False):
    create_db()
```

###### 다른 entity그룹을 생성 02:  .create_base2.py를 만들어서 다른 entity메모리 +다른 load_fake_data까지 구현하여 main에 import해서 사용하기

```python
from src.infra.config.db_creator import create_db, Session

#from src.infra.models import *
from src.infra.entities import *


def _load_fake_data(session):
    # 다르게 구현


def create_database(load_fake_data: bool = False):
    create_db()

    if load_fake_data:
        _load_fake_data(Session())
```



```python
#from create_database import *
from create_database2 import *


if __name__ == '__main__':
    create_database(load_fake_data=False)
    session = Session()

    for it in session.query(Filmes):
        print(it)

```





5. **`.>main.py`에서 `.>create_database.py`를 통채로 `*` import하여** 

   - db_creator.py 속에 있던 **`Session`**
   - create_datbase.py에 있던 `*(entityes)` + **`create_database(load_fake_data=)`**
     - 모두를 `*` import해서 사용한다

   ```python
   from create_database import *
   
   if __name__ == '__main__':
       create_database(load_fake_data=True)
       session = Session()
   
       for it in session.query(Groups):
           print(it)
   ```

   

##### _load_fake_data 메서드 구현 예시

```python
from faker import Faker

from src.infra.config.db_creator import create_db, Session

from src.infra.models import *


def _load_fake_data(session: Session):
    ## 1) 1:M 에서 [M의 fk]로 쓰일 [1의 객체]부터 먼저 생성한다.
    ## 2) N:M 에서 relationship칼럼이 없는 Entity부터 먼저 생성한다.
    # -> lessons.relationship칼럼.append( groups )
    group1 = Groups(group_name='1-MDA-7')
    group2 = Groups(group_name='1-MDA-9')
    session.add(group1)
    session.add(group2)

    ## 3) N:M에서 종류가 제한적인 것을 미리 만들어놓고
    ##    반복문을 돌면서, 생성하여, 반대entity는 append로 등록해준다.
    lesson_names = ['수학', '프로그래밍', '철학', '알고리즘 및 데이터 구조', '선형대수학', '통계', '체육']
    for key, it in enumerate(lesson_names):
        lesson = Lessons(lesson_title=it)
        lesson.groups.append(group1)
        if key % 2 == 0:
            lesson.groups.append(group2)
        session.add(lesson)

    faker = Faker('ko_KR')
    ## 4) 랜덤으로 택1시키려면, list로 미리 쏴두고 -> faker.random.choice
    group_list = [group1, group2]

    ## 중간에 끊겨도 되는 지점을 commit해준다.
    session.commit()

    ## 5) student를 50개 만든다. fk에 해당하는 group.id를 group_list에서 택1한 뒤 id만 뽑아쓴다.
    for _ in range(50):
        full_name = faker.name().split(' ')
        age = faker.random.randint(16, 25)
        address = faker.address()
        group = faker.random.choice(group_list)  #

        student = Students(full_name, age, address, group.id)
        session.add(student)

    session.commit()

    session.close()
    
    
def create_database(load_fake_data: bool = True):
    create_db()

    if load_fake_data:
        _load_fake_data(Session())

```











#### 03 alembic을 이용한 ORM -> DB 수정

1. **terminal**을 열고

2. alembic 설치 및 alembic 초기화

   ```shell
   pip3 install alembic
   
   alembic init alembic
   ```

3. alembic 설정

   1. ./**alembic.init 내부 sqlalchemy.url 주석** 처리

   2. ./**alembic/versions/env.py 설정**

      1. **config객체에 sqlalchemy.url** main_option 추가

         ```python
         config = context.config        
         
         # set sqlalchemy.url
         db_url = "mysql+pymysql://root:564123@localhost:3306/cinema"
         if not config.get_main_option('sqlalchemy.url'):
             config.set_main_option('sqlalchemy.url', db_url)
         ```

      2. **target_metadata**에 **Base의 .metadata** 할당 및 **`entity들 메모리에 띄워두기`(필수)**

         ```python
         # target_metadata = None
         
         from src.infra.config.base import Base
         from src.infra.entities import *
         
         target_metadata = Base.metadata
         ```

      3. **run_migrations_online**메서드에 **칼럼Type까지 비교**하도록 **context.configure에 `compare_type=True` 추가하기**

         ```python
         def run_migrations_online() -> None:
             #...
             with connectable.connect() as connection:
                 context.configure(
                     #...
                     compare_type=True
                 )
         
         ```

4. **DB생성해놓은 상태에서 revision최초 하나 만들어주기**

   ```powershell
   alembic revision -m "init entites"
   alembic upgrade head
   ```

5. **Entity모델들을 수정한 뒤, alembic으로 DB 업데이트하기**

   - **절대 DB를 수작업으로 건들면 안된다.**
     - ORM수정에 따른 01 ORM으로 DB생성은 무시하면서 인식유지되더라
   - **`Entity .py를 삭제`할 땐, `init에 걸어둔 것도 같이삭제`하자**

   ```powershell
   # entity 수정후
   alembic revision --autogenerate -m "add column"
   alembic upgrade head
   ```

   

   

   






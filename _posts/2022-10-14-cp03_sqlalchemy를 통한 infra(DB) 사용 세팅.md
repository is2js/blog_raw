---
toc: true
layout: post
title: CleanPython 03 sqlalchemy를 통한 infra(DB) 사용 세팅
description: infra layer에서 DATABASE 사용하도로고 세팅하기

categories: [python, cleanpython, project, infra, orm, database, entities, models]
image: "images/posts/python.png"
---

## sqlalchemy를 통한 infra(DB) 사용 세팅





### 01 src>infra> config + entities(models) 모듈 생성

![image-20221024001913826](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024001913826.png)





### 02 src> infra> config 모듈 정의

- init.py 외에 **`base.py`, `connection.py`, `db_create.py`**를 생성한다

  ![image-20221024002046593](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024002046593.png)



#### src> infra> config> base.py 정의

- base객체를 만든다.

  ```python
  from sqlalchemy.ext.declarative import declarative_base
  
  Base = declarative_base()
  ```

  - 각 EntityModel class들이 상속하여 정의한다
  - .metadata필드를 이용해서 DB를 생성한다



#### src> infra> config> connection.py 정의

1. **DBConnectionHandler** class를 정의한다.

   - 내부 DATABASE_URL (or connection_string)을 private필드로 보유하고
   - 객체 생성과 동시에 db_url로 **engine**객체를 private메서드로 생성해서 필드로 보유하고
     - public get_engine 메서드 호출시 반환해준다.(engine.execute("sql") 가능)
   - None으로 비어있는 session필드를 가지고 있다가
     - **context manager로서 작동할 때, 해당필드를 이용해서 session을 채워서 썼다가 다시 지우는 행위를 반복한다.** 
   - 임의로 내가 get_current_session 메서드도 만들어주었다.
     - context manager아닐 때도, session을 사용하기 위함인데, 직접 close해줘야할 것이다.

   ```python
   from sqlalchemy import create_engine
   from sqlalchemy.orm import sessionmaker
   from src.config import db
   
   
   class DBConnectionHandler:
       def __init__(self, echo: bool = False) -> None:
           # self.__connection_string = "mysql+pymysql://root:564123@localhost:3306/cinema"
           self.__connection_string = db.DATABASE_URL
           self.__engine = self.__create_database_engine()
           self.session = None
   
       def __create_database_engine(self):
           engine = create_engine(self.__connection_string)
           return engine
   
       def get_engine(self):
           return self.__engine
   
       def get_current_session(self):
           Session = sessionmaker(bind=self.__engine)
           return Session
   
       def __enter__(self):
           Session = sessionmaker(bind=self.__engine)
           self.session = Session()
           return self
   
       def __exit__(self, exc_type, exc_val, exc_tb):
           self.session.close()
   
   ```



#### src> infra> config> db_creator.py 정의

- **EntityModel객체로 -> DB의 table들을 생성할 때 필요한 `Base, Engine`객체들을 import해서 db생성 명렁어를 `메서드 create_db`로 매핑해놓은 곳이다.**

  ```python
  from .base import Base
  from .connection import DBConnectionHandler
  
  conn = DBConnectionHandler()
  engine = conn.get_engine()
  
  Session = conn.get_current_session()
  
  
  def create_db():
      Base.metadata.create_all(engine)
  ```

- **sqlite의 경우, `memory db`를 사용할 때, `생성당시의 session이 필요`하기 때문에, Session클래스까지 생성해놓았다.**



### 03 src> infra> entities 모듈 정의

- entities말고 models 등의 이름을 써도 된다.

  

1. 각각의 table에 대응하는 `EntityModel.py`를 정의하고 **내부에서는 src>config> init에 떠있는 base객체를 import**하여 상속해서 EntityModel class를 정의해주면 된다.

   ![image-20221024003525061](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024003525061.png)

   ```python
   from src.infra.config.base import Base
   from sqlalchemy import Column, String, Integer, ForeignKey
   
   
   class Artists(Base):
       __tablename__ = 'artists'
   
       id = Column(Integer, primary_key=True)
       name = Column(String(50), nullable=False)
       title_filmes = Column(String(50), ForeignKey("filmes.title")) # FK의 tablename.pkColumn
   
       def __repr__(self):
           return f"Artists (name={self.name}, filme={self.title_filmes})"
   
   ```

   

2. **완성된 `각각의 entityClass들`을 `init`에 import해놓아야지 `db 생성 명령어 호출시  메모리에 해당Entity들이 import 되어있어야함`을 만족할 수 있다.**

   ![image-20221024003646435](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024003646435.png)

   ```python
   from .artists import Artists
   from .filmes import Filmes
   
   ```



### 04 root의 create_database.py  + main.py 정의후 table생성하여 query 날리기



#### 1 root(.) > create_dabase.py 정의

##### 1) init data없을 때 기본 생성

- domain 및 src정보와 무관하게 **실행에 필요한 파일이며 외부 library와도 접촉지점이므로 `create_database.py`를 `root`에서 생성한다.**

  ![image-20221024004356041](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024004356041.png)



1. 기본적으로 src>infra>config>`db_creator .py`에 정의해놓은 **create_db**메서드를 import하고, **그 메서드를 호출하는 메서드 create_database를 정의한다.**

   - main.py에서 바로 create_db만 호출하면 될 것 같지만, **db생성후 Faker등으로 `init data insert`까지 하는 역할을 하기 위함이다.**

   ```python
   #.>create_database.py
   from src.infra.config.db_creator import create_db
   
   def create_database(load_fake_data: bool = True):
       create_db()
   ```

2. **초기데이터 insert없이 DB - table만 생성할거면 여기서 `from src.infra.entities import * `로 메모리에 띄워놓고 종료하면 된다.**

   ```python
   from src.infra.config.db_creator import create_db
   from src.infra.entities import *
   
   def create_database(load_fake_data: bool = True):
       create_db()
   ```

   



##### 2) init data insert를 위해 create_db 메서드호출 직후 load_fake_data()까지 구현

- 이 작업을 하기 위해 main으로 create_database.py를 따로 빼놓은 것이다.
  - init 데이터가 없으면, main.py에서 create_db메서드만 import해서 model들 import해서 db생성하면 된다.



1. **init data를 넣어주려면, `sqlite memory같은 경우 필수로, 생성당시 engine이 bind된 Session`이 필요하므로 src>infra>config>db_creator.py 내부에서 만들어놓은 Session클래스 객체도 import한 뒤, 인자로 넣어준다.**

   ![image-20221024013613581](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024013613581.png)

   ```python
   #.>create_database.py
   from src.infra.config.db_creator import create_db, Session
   
   def create_database(load_fake_data: bool = True):
       create_db()
   
       if load_fake_data:
           _load_fake_data(Session())
   ```

   - `load_fake_data` 대신 `load_init_data`를 통해 Faker 라이브러리 없이 구현해도 된다.



2. **먼저, `insert구현시 사용`될 EntityModel들이 필요하며,  `db생성 호출시 메모리에 띄워둬야 할 main.py`가 `from create_database import *`로 메서드 와 entitymodel을 한번에 import할 수 있게  `from src.infra.entities import *`로 전체 Entity를 메모리에 띄워준다.**

   ```python
   #.>create_database.py
   from src.infra.config.db_creator import create_db, Session
   from src.infra.entities import *
   
   def create_database(load_fake_data: bool = True):
       create_db()
   
       if load_fake_data:
           _load_fake_data(Session())
   ```

   

3. **private `_load_fake_data`메서드를 구현해준다.**

   - faker데이터가 필요하면 import해서 구현하면 된다.
   - **추가적인 faker 사용법은 이 [문서를 참고](https://github.com/is2js/2022_sqlalchemy/blob/master/docs/02_faker.md)하자.**
   - **session을 직접 사용하는 것이므로 `session.close()`를 명시하자**

   ```python
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
   ```





#### 2 root(.) > main.py 정의 (db생성 및 생성당시 session으로 query날리기)

1. `create_databse 메서드 `(with load init data), `Session클래스`, `모든 entities class`들을 메모리에 띄워놓은 **from create_database.py를 import *로 가져온다 **

   ```python
   #.> main.py
   from create_database import *
   ```

2. **main 실행 부에서 2가지를 수행한다**

   1. create_database() 메서드 호출하여 테이블 생성하기
   2. Session클래스로 db생성당시 조건의 session객체 생성하기

   ```python
   #.> main.py
   from create_database import *
   
   if __name__ == '__main__':
       create_database()
       session = Session()
   ```

3. **이후 session객체로 queryf를 날리면 된다. **

   - `from create_database import *`에는 entityModel들도 같이 import되어있어서 자유롭게 사용할 수 있다.

   ```python
   #.> main.py
   from create_database import *
   
   if __name__ == '__main__':
       create_database()
       session = Session()
       
       # select
   	stmt = (
           select(User)
           .options(selectinload(User.addresses))
       )
       print(stmt)
       for it in session.execute(stmt).scalars():
           print(it.addresses)
       print('*' * 30)
   ```





### 05 학습용 DB 및 모델 추가

#### 01 .env에서 DB설정 변경

1. `.env`의 dialect + **db_name을 바꿔준다.**

   - root에 새롭게 저장될 db

     ![image-20221024175436534](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024175436534.png)

#### 02  새 Entitiy 패키지 만들기



2. **src> infra> 에 `새로운 entity` 패키지를 만들어준다.**

   ![image-20221024175529595](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024175529595.png)

3. src>infra>새로운entitiy패키지에 **table별 EntityModel class를 정의하고, init에 올려준다.**

   ![image-20221024182553837](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024182553837.png)

   - init.py

     ```python
     from .users import User
     from .addresses import Addresses
     ```



#### 03 새 create_database_xxx.py 만들기

- create_database.py는
  - **`.env`에 따라 설정이 달라지는 고정된 메서드와 객체  `src.infra.config.db_creator의 create_db, Session`을 import한다**
  - **`init data를 insert`용 or `main에서 메모리에 띄워야할 생성EntityModle들을 *로 import`를 해야한다**

1. **기존 `create_database.py를 복사`해서  `create_database_xxxx.py`를 만든다.**

   ![image-20221024183057771](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024183057771.png)

2. **`새롭게 만든 entity로 import * 하도록 수정`한다**

   ![image-20221024183148169](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024183148169.png)

   ```python
   from src.infra.config.db_creator import create_db, Session
   from src.infra.tutorial2 import *
   
   
   def _load_fake_data(session: Session):
       ...
   
   
   def create_database(load_fake_data: bool = True):
       create_db()
   
       if load_fake_data:
           _load_fake_data(Session())
   
   ```

   



#### 04 main.py 에서 from 새로운 create_database_xx import *해서 query날리기

![image-20221024183248152](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024183248152.png)

```python
from create_database_tutorial2 import *

if __name__ == '__main__':
    create_database()
    session = Session()
```

![image-20221024183321517](https://raw.githubusercontent.com/is3js/screenshots/main/image-20221024183321517.png)



### (번외) 모듈없이 Python Console에서 ORM -> DB table 생성하는 방법(sqlite memory 불가)



#### 1 전체 Entity 한번에 생성(Base + engine + Entities)

1. **python console**을 열고

2. **Base**객체와 engine객체 생성을 위한 **DBConnnectionHandler** import

3. **Entity들을 메모리에 올리기**위한 entity import

4. **Base.metadata.create_all( engnine )**명령어로 생성

   ```python
   from src.infra.config.base import Base
   from src.infra.config.connection import DBConnectionHandler
   
   from src.infra.entities import *
   
   with DBConnectionHandler() as db:
       Base.metadata.create_all(db.get_engine())
   #engine = DBConnectionHandler().get_engine()
   #Base.metadata.create_all(engine)
   ```

   

   

#### 2 특정 Entity만 + engine으로 생성

1. **python console**을 열고

2. engine객체 생성을 위한 **DBConnnectionHandler** import

3. **특정 entity** import

4. Entity.`__table__`.create( **engine** )

   ```python
   from infra.config.connection import DBConnectionHandler
   
   from infra.entities import Entity
   
   
   Entity.__table__.create(engine)
   ```












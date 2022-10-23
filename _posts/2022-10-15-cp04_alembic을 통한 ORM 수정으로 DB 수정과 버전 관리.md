---
toc: true
layout: post
title: CleanPython 04 alembic을 통한 ORM 수정으로 DB 수정과 버전 관리
description: alembic --autogenerate을 이용한 ORM 수정 -> DB 수정

categories: [python, cleanpython, project, infra, database, orm, alembic, autogenerate]
image: "images/posts/python.png"
---

### alembic을 통한 ORM 수정을 통한 DB 수정과 버전 관리



#### alembic --autogenerate을 이용한 ORM 수정 -> DB 수정

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

   

   

   






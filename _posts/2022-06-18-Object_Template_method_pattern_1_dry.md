---
toc: true
layout: post
title: Object)TemplateMethod for DRY(개발자의세계)
description: 구현체들의 중복되는 공통로직 제거를 위한 템플릿메소드 패턴

categories: [java, 우테코, oop, object, 개발자의세계, templatemethod, pattern, comparefiles]
image: "images/posts/java.png"
---

### 구현체에서 템플릿메소드 패턴으로 추상클래스 만들기

#### 맨땅에서 확인하기

- 카테고리로만 적용했던 마커인터페이스를 삭제한 상태이다
    - 공통로직이 발견되었고, 개별구현 로직을 포함한 상태이다.

1. 구현체에서 **미리 템플릿메소드패턴으로 구성**을 해놓아야한다.

    1. 한번에 일 처리해줄(인터페이스 or 추상클래스) && 자식은 물려받아 안보이면서 구현안해도 되는 `public (템플릿)메소드 1개`가 **전체로직의 내부에서 개별구현로직인 `private 훅메서드들(step메서드들을) 감싸고 있는 형태`**여야한다.
    2. **구상체별 개별구현 될 로직**(step메서드들, proteced abstact로 올라갈)들은 **public 템플릿메소드 `안`에서 `순서대로 private 훅메서드로 추출된 상태`**여야한다
        - **`private으로 추출된 메서드`가 있어야, @Override로 superclass 추출할 때, `abstract`를 선택하고, 그 선택이 일반부모class 추출 -> abstact class로 추출을 만든다.**
        - **또한 그 훅메서드들(템플릿메소드안에 step들을 가지며 개별구현 될)은 `메서드명이 통일`되어있어야한다.**

    

    

2. 현재 코드

    - BackEnd class와 FrontEnd class의 공통로직은 public 템플릿메소드 1개로, 개별구현 로직들은 private메소드로 만들어, `Programmer`라는 추상클래스로 추상화하고 싶다.

        - 백엔드 클래스

        ```java
        package developer.domain.progammer;
        
        public class BackEnd {
        
            private Server server;
            private Language language;
        
            public Program makeProgram(final Paper paper) {
                if (paper instanceof ServerClient) {
                    final ServerClient pa = (ServerClient) paper;
                    server = pa.getServer();
                    language = pa.getBackEndLanguage();
                }
        
                return makeBackEndProgram("백엔드 프로그램");
            }
        
            private Program makeBackEndProgram(final String name) {
                return new Program(name);
            }
        }
        
        ```

        - 프론트엔드 클래스

        ```java
        package developer.domain.progammer;
        
        public class FrontEnd {
        
            private Language language;
            private Library library;
        
            public Program makeProgram(final Paper paper) {
                if (paper instanceof Client) {
                    final Client pb = (Client) paper;
                    language = pb.getLanguage();
                    library = pb.getLibrary();
                }
        
                return makeFrontEndProgram("프론트 프로그램");
            }
        
            private Program makeFrontEndProgram(final String name) {
                return new Program(name);
            }
        }
        
        ```

        

3. 구상체들을 템플릿형태로 **변경해야할 부분**

    - 공통로직은 public 템플릿메소드 `1개`로 잘 나타나있다.

    - **개별 구현로직은**

        - 서로 다르게 구현되는 `if  ~ return`부분이 **private 훅메서드(step메서드들)로 추출이 안되어있는 상태**다.

            ```java
            // BackEnd.java
            if (paper instanceof ServerClient) {
                final ServerClient pa = (ServerClient) paper;
                server = pa.getServer();
                language = pa.getBackEndLanguage();
            }
            
            // FrontEnd.java
            if (paper instanceof Client) {
                final Client pb = (Client) paper;
                language = pb.getLanguage();
                library = pb.getLibrary();
            }
            ```

            

        - 이미 추출된 훅메서드가 **명칭이 통일이 안되어있는 상태**이다.

            ```java
            // BackEnd.java
            return makeBackEndProgram("백엔드 프로그램");
            
            // FrontEnd.java
            return makeFrontEndProgram("프론트 프로그램");
            ```

            

    

4. 변경해보기

    1. 이미 public 템플릿메소드가 존재하는 상황이다.(**전체로직 & 공통코드를** 담는 public 메서드)

    2. 개별구현로직도 순서대로 이름이 통일된 **private 훅메서드로 추출**한다.

        ![60dde2e3-a345-4be7-9393-b6c84b359bfb](https://raw.githubusercontent.com/is2js/screenshots/main/60dde2e3-a345-4be7-9393-b6c84b359bfb.gif)

    3. **구현체별로 이름이 다른 훅메서드들의 이름을 통일한다.**

        - 추상클래스를 추출할 클래스에서만, 메서드명을 추상화해서 통일하고
        - 나머지 구현체들은 그대로 두자. -> **이미 동일한 메서드를 가지고 있으면, `자동implements`가 인식안되서 직접 작성해줘야한다.**
            - 아래 영상에서는 FrontEnd의 메서드명도 수정하는데 그대로 두던지, `수정하고 주석처리`해놓자.

        ![71015c26-1fa5-4b14-8cf4-f2eacc1ca8c0](https://raw.githubusercontent.com/is2js/screenshots/main/71015c26-1fa5-4b14-8cf4-f2eacc1ca8c0.gif)

    4. **1개의 구현체**에서 템플릿메소드패턴을 적용한 추상클래스를 추출한다.

        - public 템플릿메소드를 추출하려고 하면, **private메서드들이 빨간색 뜨는데, 이놈들을 abstract로 데려가면, 구상체별 개별구현 훅메서드가 된다.** 

        ![59eeca1f-d228-4c1d-aacd-eddc152b6f2d](https://raw.githubusercontent.com/is2js/screenshots/main/59eeca1f-d228-4c1d-aacd-eddc152b6f2d.gif)

    5. **나머지 구현체들**은, 추상클래스를 상속한 뒤

        1. 공통로직을 담은 **public템플릿메소드는 제거하여 추상클래스로부터 물려받아 쓴다.**
        2. 개별구현로직의 **private훅메서드들은 주석처리해놓고 -> implements해서 구현한 뒤 -> 개별구현로직을 복붙**하자.
            - **메서드명이 추상메서드와 동일하면, implements에 안뜬다.**
            - 메서드명이 추상메서드와 동일하게 그대로 두면, 접근제어자에만 빨간색떠서 직접 수정해줘야한다.

        ![e8835bc1-377f-405a-a924-8b91297b6c17](https://raw.githubusercontent.com/is2js/screenshots/main/e8835bc1-377f-405a-a924-8b91297b6c17.gif)

        

#### Compare Files를 이용한 구상체클래스들 추상화



##### Compare Files

- **비슷한 파일 차이점 비교할 때 **
  - **ex> `구상체들의 공통로직(템플릿메소드) +개별로직(훅메서드로 추출)` 만들어서 템플릿 메소드로 올릴 때**
- 단축키 세팅
  - **keymap > `compare file` 검색 후 > `Ctrl+D`로 지정**    ![20220619131007](https://raw.githubusercontent.com/is3js/screenshots/main/20220619131007.png)





##### 템플릿 메소드 패턴 확인사항

- **`모든 구상체`에 대해 `구조는 다 수정`**

  1. `구조` 확인 

     1.  **공통 로직을 포함한 전체로직**이 **public** 예비템플릿메소드  **1개**에 담길 것

     2. **그 내부(1.)에서 개별로직들은** **private** 예비훅메서드들로 빠져있을 것

        - 학습중 발견: private 예비훅메서드들은 개별구현시 달라질 인자는  소유하고 있으면 안됨. 훅메서드는 그 내부에서 달라지는 내용들을 개별구현한다.

        ![8523ff92-1f01-4de3-812c-08519fc65da5](https://raw.githubusercontent.com/is3js/screenshots/main/8523ff92-1f01-4de3-812c-08519fc65da5.gif)

  2. **`구조` 확인 후 `수정`**

     1. public 예비템플릿 메서드 내에 모든 로직이 담겨있느냐
        - **private** 메서드(내부에서 개별로직이 훅메서드로 빠진 것)는 **보지말고**, **`public 예비템플릿메서드 1개만 있고` 모든 로직이 다 들어가도록하며, `없는 구상체는면 생성`해서 넣어준다.**
     2. 템플릿내부 개별로직이 private 훅메서드로 빠져있느냐
        - **달라서 `highlight뜬 line들이 public 예비템플릿메서드 내에 보이면 안된다.`**
        - **`템플릿메서드 내 hightlight(개별로직)` -> `private 예비훅메서드`로 `추출`해서 훅메서드안으로 보내야한다.**
          - **`예비훅메서드의 명`은 개별구상체들 다 쓸 것이니 `추상화해서 추출`**해서 짓자.

     ![6dfd3fef-c672-48c4-af07-586d291f94eb](https://raw.githubusercontent.com/is3js/screenshots/main/6dfd3fef-c672-48c4-af07-586d291f94eb.gif)

  3. `이름` 확인만

     1. 템플릿메소드 이름이 추상화되어 동일하느냐
     2. 훅메서드 이름 역시 추상화되어 동일하느냐

- **`올릴 구상체 1개만`에 대해 `추상화된 이름으로 수정`** 

  - **public 예비 템플릿메서드 `명`와  내부 private 예비훅메서드`명`들을 `모두 개별 구상체 통일 예정`이므로 `대표 구상체 1개는 추상화한 메서드명으로 수정해놓고 올리기`**

    - 구상체들이 미리 이름을 통일시켜놓으면, implements 목록에 안보일 수 있다.
      - 여기선 훅메서드와 템플릿메서드명이 겹쳐서 템플릿메소드명도 수정했다

    ![266bd29c-fd30-4ff7-86b3-9631a9f16a69](https://raw.githubusercontent.com/is3js/screenshots/main/266bd29c-fd30-4ff7-86b3-9631a9f16a69.gif)

- 올릴 구상체 1개로 추상체를 만든다.

  - public메서드를 public 템플릿메소드로 올리려고 체크하면

  - private메서드들이 빨간색표시되며 `abstract체크`를 통해  protected abstract 메서드(훅메서드)로 올릴 수 있다.

    - public -> 추상클래스에서 가져간다. (템플릿메소드 1개)
    - private -> 구상클래스에서 개별구현한다. (개별구현 훅메서드들)

    ![f6c4ef40-7354-4ad1-bb05-0781b2fd220c](https://raw.githubusercontent.com/is3js/screenshots/main/f6c4ef40-7354-4ad1-bb05-0781b2fd220c.gif)

- 추상체가 만들어지면, **`나머지 구상체들`은 `훅메서드들만 implements`해서  `개별로직으로 수정`**

  - implements목록에 안보이더라도  **`public 템플릿메소드는 공통로직이라 구상체들은 안보인상태로 물려받아 사용`** 한다.

    - `구상체는 추상클래스를 상속한다`
    - **`구상체의 public템플릿메소드는 삭제`한다**
    - **`구상체의 구현대상은 훅메서드들만` 하면 된다.**
      - 만약  추상층의 prot abst훅메서드 <-> 구상층의 private메서드의 이름이 같아서 implements 목록에 안뜨면, 접근제어자를 protected로 수정해주면 된다.
    - `구상체는 외부에서 public 템플릿메소드`만 사용하게 된다.

    ![8771603c-b2c3-4349-ab07-02b79eea6850](https://raw.githubusercontent.com/is3js/screenshots/main/8771603c-b2c3-4349-ab07-02b79eea6850.gif)





- 문제점 발견

  - 템플릿메소드내 **훅메서드들**은 내부에서 공통으로 호출 -> **외부에서 인자를 받지 않으면, 내부에서 주는 인자는 의미가 없다. -> `템플릿메소드가 받는 인자가 아니면, 인자 없이 훅메서드를 만들자. 개별구현을 숨기는 객체없는 내부메서드일 뿐!`**

    - 개별구현하고 싶으면, `훅메서드의 호출 인자`가 아니라, 훅메서드 `내부에 개별구현에서 다른 값을 사용`한다

    ![image-20220619142451534](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220619142451534.png)

    ![32e96307-3324-40b9-9a90-0475851b1e7d](https://raw.githubusercontent.com/is3js/screenshots/main/32e96307-3324-40b9-9a90-0475851b1e7d.gif)




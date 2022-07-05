---
toc: true
layout: post
title: OBJECT 15 PART1 전체 요약(코드스핏츠)
description: object 책을 강의한 코드스핏츠 유튜브 요약

categories: [object]
image: "images/posts/java.png"
---

- 참고 유튜브 : https://www.youtube.com/watch?v=navJTjZlUGk
- 정리본: https://github.com/LenKIM/object-book
- 코드: https://github.com/eternity-oop/object
- 책(목차) : https://wikibook.co.kr/object/


#### 01 기본 소양

1. 퀜트백의 4가지 원칙: 정해놔야 예외상황을 즉각 처리할 수 있어 비용을 아낀다.

   1. L: 로컬변수를 사용하자 (local consequences)

   2. M: 중복을 최소화하자 (minimize repetition)

   3. S: 짝을 맞춰 생성하자 (symmetry)

   4. C: 컨벤션을 지키자 (convention)

      

2. 패턴: 정해진 원칙하에 반복되서 나타아는 유형을 베스트로서 패턴화 시키면, 비용없이 즉각 적용 가능하다



3. 역할모델: (요구사항)변화에 따른 격리에 성공하는 유일한 방법
   - 역할모델별로 entity를 나누고 객체를 설계해야, 격리에 성공할 수 있다.
   - 객체지향 프로그래밍이 어려운 이유
     - 역할 모델을 사용하기 위해서는 `모든 추상화 기법`을 동원함.
     - 그 중에 `일반화`를 가장 대표적으로 씀(가장 머리가 많이 필요)
     - 수련하는데 시간이 오래걸려서 실패를 많이 한다.



4. 추상화: 역할모델을 만들기 위함.
   1. 데이터의 추상화
      1. modeling: 목표에 따라 기억해야할 것만 필드로 추리는 것
      2. categorization: 1개의 객체를 카테고리별로 맞게 작동시킨다. ex> 아빠이자~ 사장이자~
      3. grouping: 공통점 없어도 묶어둘 수 있다.
   2. 프로시져 추상화: 싸여진 함수에게 데이터 처리를 맡기는 행위
      - generalization: 일반화된 방법을 사용해서 함수를 적게 만든다.
      - capulization: 은닉화의 느낌보다는 복잡한 로직을 내부로 감추어, 사용자에게 간단한 인터페이스만 제공하는 것
   3. 객체지향적 추상화: UML에서 class간 연결방법
      1. Generalization:  일반화 - 인터페이스 or 추상클래스를 만듦
      2. Realization: 구상화 - 인터페이스 or 추상클래스에서부터 파생된 클래스
      3. Dependency: 함수의 인자를 받거나 부분적으로 참조해야할 때
      4. Association: 단짝, 나의 영혼에 각인된 친구, 필드에 정의된 dependency
      5. Directed Association
      6. Aggregation
      7. Composition

5. Program & Timing(프로그램 실행 시간)
   1. language code: 언어로 코드 작성하는 시간 with lint time
      - 컴파일 이전의 시간으로서 코드품질을 위해 `lint`라는 것을 이용한다. 프로그램 작성시 `lint time`을 이야기한다. 
        ex>그거 왜 lint time에 못골라냈어. lint를 바꿔봐.
   2. machine language: compile time
   3. file: 컴파일 후 결과물. 
      - 메모리 올리기 전에 파일로 만들어, 영구보존을 시도함
   4. load: 파일로 만든 뒤에야 메모리에 적재
   5. run: 메모리에 올라간 후 실행. runtime
      - load(적재) -> run(실행)이 반복된다.
   6. terminate : 종료



6. script program & Timing

   1. language code: lint time

   2. file

   3. load: 컴파일 전에 파일이 되고 메모리에 적재

   4. machine language: compile time

   5. run: runtime

   6. terminate

      

7. error의 종류
   1. context error: 미리 못 잡아낸다. ex> instanceof

   2. runtime error: 앵간하면 미리 못잡아낸다

      - 못잡아내는 것들을 lint time에 미리 잡아내고 싶다.

   3. compile error: 미리 빨간줄로 잡힌다.

      

8. runtime in java

   - file -> load -> run: 파일로 보존되는 코드들은 메모리에 적재되어 실행된다.

   1. load

      - 메모리에는 명령세트/값세트가 로딩된다.

        - 아직 cpu로 가진 않았다

          ![image-20220620170311005](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620170311005.png)

   2. insturction fetch & decoding

      - 명령(instructino)세트 중 1개를 가져와(fetch) cpu의 디코더(decoding)로 가서 디코딩 된다.

        ![image-20220121212930254](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121212930254.png)

   3. execution

      - 명령코드가 cpu의 디코더에서 디코딩 된 이후에는 연산 유닛으로 가서 명령을 실행한다

        ![image-20220121212948777](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121212948777.png)

      - 명령이 실행될 때, cpu의 데이터유닛이 명령에 필요한 값세트들을 가져와서 받아온다.

        ![image-20220121213047105](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121213047105.png)

      - 명령+값으로 실행된 결과를 다시 연산유닛으로 보낸 뒤, 결과물을 데이터유닛에 반환해준다.

        - 여기까지를 execute라 한다.

        ![image-20220121213118329](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121213118329.png)
        ![image-20220121213210144](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121213210144.png)

      - cpu의 데이터유닛은 연산결과데이터를 메모리에 돌려준다.

        ![image-20220121213237415](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121213237415.png)

      - 이제 명령2를 가져와서 디코딩하고(instruction fetch & decoding)  연산후 처리(execution)후 메모리에 돌려주는 행위를 반복하는데, 이 시간을 runtime이라고 한다.

        ![image-20220121213400751](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121213400751.png)

   4. 메모리에 load된 순서대로 execution되는 과정을  sync(동기화)라고 부른다.

   5. 명령세트들이 다 소모되면 terminate한다

   6. runtime을 정리하면

      1. loading
      2. instruction fetch & decoding
      3. execution의 반복이라고할 수 있다.

   7. 하지만 runtime에는 추가 로직이 남아있다.

      1. `Essential definition loading`

         - 프로그램마다 다 가지는 최초 로딩 과정으로서, 메모리에 적재되는 순간 최초에 하는 행동은 `이 프로그램 구동을 위한 기초적인 정의부터 로딩` ex> 기저에 있는 print함수, console 등의 필수 정의들을 로딩.하도록 컴파일러안에 짜여져 있다.

      2. 그 다음으로`VTABLE MAPPING`을 한다.

         - 코딩: 우리는 text만 짰음
         - 컴파일러: file을 만드는 애 일 뿐이다.
         - 우리가 쓴 변수와 메모리공간은??? 
           - 이미 compile time에 변수마다 가상의 메모리가 있다고 가정하고, 컴파일 한다
           - 이미 변수-가상의 메모리가 컴파일된 상태에 맞춰서 `vtable mapping`을 표로 만들어, 진짜 메모리와 매핑을 시켜, 진짜 메모리주소로 바뀐다.
         - vtable mapping으로 **진짜 메모리를 얻은 상태이므로 실행가능해 진다.**

      3. 이제서야 `RUN`이 일어난다.

         ![image-20220121215911789](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121215911789.png)

         - 하지만 실행이후에 모든 랭기지는 끝나지 않는다.

      4. `RUNTIME DEFIFITION LOADING`을 통해 실행이후에도, class 및 함수 정의 등 많은 정의를 로딩한다.

         - 최초 프로그램 실행시만 class등이 로딩되는 것이 아니다.
         - java만 해도 실행도중 새로운 class들을 계속해서 로딩할 수 있다.
         - **java**가 대단한점 : 업계최초로 **runtime loading을 적용**한 것
           - jar파일이 1~2기가 되는데, 실행시 왜 안뻗을까?
             - 필요한 파일들은, 필요시 최초로 file에서부터 loading을 시작해서 ~!
             - c나 c++은 프로그램 시작시, 우리가 정의한 모든 class가 시작된다.
           - java는 사용자가 만든 것은 하나도 로딩X 필요시에 파일로부터 로딩

      5. `RUN`다시 로딩된, 새로운 class 등의 정의를 실행시킨다.

      6. 같은 runtime임에도 불구하고, essential definition loading과 vtable mapping은 한번만 일어나지만, **run -> runtime definition loading -> run 은 계속해서 일어난다.**



9. runtime in script program
   1. file -> load -> compile -> run ?
   2. 스크립트의 생명주기를 생각해보면, **컴파일되거나 최초로딩(**essential definition loading과 vtable mapping)**없이 실행부터 된다.**
      ![image-20220121220531132](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121220531132.png)
   3. **일단 실행된 이후 내가 만든 class와 함수가 loading**이 된다.
      - 예를 들어, 브라우저 시작 -> run -> jquery를 로딩 -> jquery를 쓰는 코드 실행
      - 같은 runtime이지만, jquery쓰는 코드(run time)에 대해 상대적으로 jquery 정의 코드(static time)를 정적타임으로 보여진다.
        ![image-20220121220741081](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121220741081.png)
      - jquery외에 다른 class 등을 사용할때도 상대주의적으로 나뉜다.
        ![image-20220121220818848](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121220818848.png)
      - **runtime은 정의되어있거나 확정적인 것이 아니라고 생각**해야한다.
        - runtime안에서도 runtime이 쪼개지며, 각 코드에 대해서 확정타임이 상대적으로 정해진다.

   4. **runtime 요약 : runtime은 확정적인 무엇이라고 가정하지 말아라**
      - 유연하며, 언어에 따라 애매할 수도 있음.





10. Pointer of Pointer(간접참조)

    1. 어떤 값을 얻기 위해서 특정 포인트를 직접 가르키지 않고  포인트를 찾은 뒤  `.`쩜을 통해 다시 포인트를 찾아서 얻겠다.

    2. 변수에는 값과  \&메모리주소값 2가지 속성이 있다.

       - 값도 메모리격자판에 / \&메모리주소값(포인터)도 메모리격자판에 박히게 된다.
       - 포인터(\&메모리주소값)는 값의 시작메모리번호(첫번재 값의 위치)만 가리킨다.

    3. **타 `원시변수(A)`의 메모리주소값`만` 값으로 가지는 변수를 `직접 참조 포인터(Bad)`라고 한다. 반면, `원시변수의 메모리주소를 한번 감싸서 가지고 있는` = `쩜을 이용해 원시변수에 접근할(가리킬) 수 있는`포인터를 `간접참조(Good)`라고 한다.**

       - 직접 참조 포인터의 예
         - B = \&A
       - 간접 참조 포인터의 예
         - B = { key: \&A}
           - B`.`KEY로 A에 접근
         - class B { int A }
           - B`.`getA()로 A에 접근

    4. 문제는 **직접 참조 포인터(원시변수의 메모리주소를 값으로 바로 가짐)**를 가 A의 메모리주소값을 가지고 있다고생각하여, **B를 할당하는 사용하는 C, D에게 `참조의 전파`를 실패하게 만든다**
       ![image-20220121225221773](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121225221773.png)

       - 처음 코드를 작성할 때, C와 D는 B가 가진 A의메모리주소값을 가지고 있다

       - 하지만 runtime시 B가 다른놈을 가리키게 된다면?

         - C와 D에는 여전히 \&A만 존재하고
         - B는 다른 변수인 K의 메모리주소값 \&K를 가리키기 바꼈다면?
           - B = \&A
           - C = B, D = B
           - B = \&K
             - B는 K를
             - C,D는 A를 여전히 가리키고 있다.
         - 이후 C와 D를 사용할 때, K의 값을 가리키는 것처럼 보이지만, **중간에 setter에 의해 B가 K를 가리키게 바뀌고, C와 D는 여전히 A를 가리키게 된다**

         ![image-20220121224807662](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121224807662.png)
         ![image-20220121225102913](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121225102913.png)
         ![image-20220121225116935](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220121225116935.png)

    5. **A의 포인터변수 B는 `포인터로서 가리키는 놈이 바뀔 수 있다`고 항상 생각하고, 그 C와 D는 B를 가리키는 놈으로서 값이 아니면 절대 할당X 포인터(B)의 포인터를 가리키게 해야한다.**

       - B = { VALUE: \&A }
       - C = B, D = B
         - C와 D는 B를 할당해서 사용한다.
       - **B`.`VALUE = \&K**
         - B가 간접적으로 한번 감싸서 K를 가리키도록 변화시킴에 따라, C와 D도 B.VALUE를 통해 똑같이 결국 K를 가리키게 된다.

    6. **외부에 노출된 참조 = 원시변수의 메모리주소 값으로만 가지는 `직접 참조`를 지양하고, `타변수의 메모리변수는 한번 감싸서 가지고 있음(B with map or class)으로써, 이를 사용하는 변수들(C, D)도 변화에 대응되도록 하자`**

       - **참조가 바뀔 가능성이 있는 B같은 변수들은 한번 상대방을 한번 감싸서 가지고 있는 간참조를 이용하자.**

    7. 나의 정리

       1. my) runtime시 레퍼런스(한 변수가 참조하는 주소)가 바뀌는 경우가 있고
          - 그럴경우, 일반 변수로 받아보던 또다른 참조변수들은 업데이트가 안되니
          - 바뀔 예정이 있는 애들은 map안에 변수가 포인터가 되도록하고
            - 받아보던 또다른참조변수들은 map을 할당받고
            - 바뀌는 것은 map안의 .변수가 reference를 할당받으면서 변경가능하게 하자
       2. **직접 메모리 참조시, 걔를 참조하는 애들은, 업데이트가 자동반영안되 문제가 된다.**
       3. **참조하는 애들은 껍데기를 참조하도록하고, 변경가능한 놈은 내부에서 바뀌게 한다.**
          - **대신 참조하는 애들은 껍데기-> .점을 통해 -> 업데이트되는 내부에 접근하게 한다**
       4. **이 원리(참조에 참조, <런타임시 가능한> 점을 통한 다시 한번 참조 찾기)는 객체지향에서**
          - 직접참조X **위(바깥) 참조를 통해 아래(내부)로 내려오는 구조**로서
          - 인터페이스를 정의시, 추상클래스 메소드호출 -> 그 함수가 구상클래스 메소드를 호출할 수 있는 원리이며
          - 링크드리스트의 원리
          - 데코레이션 패턴의 원리
       5. 책에서는 `동적 바인딩`이라고 표현한다.
          - 우리는 직접참조는 지양하고, 참조에 참조를 이용해야, 런타임변경에 안전해진다.
          - 그래서 **객체지향에서는 값을 이용하지 않고, 모든 곳에 참조를 이용**한다.






#### 02 식별자와 다형성

1. 객체는 값이 아니라 **식별자(메모리주소)**로 식별

   - 식별자(identifier): 객체를 구분 지음. runtime시 load된 `메모리주소`
     - 값이 같아도 같은 객체가 아님. 
     - 객체가 같다 = 메모리주소가 같다 = 식별자가 같다
       - 만약, 객체가 아닌 값만 사용하는 context(원시변수 등의 `값 contetxt`)가 있다면, 값이 같다 = 같은 것이다
     - java에서 equals를 사용하는 이유: java에서는 `==`비교는 **default로 무조건 식별자(메모리주소)를 비교**한다.
       - 문자열 비교시 식별자(메모리주소)대신 비교하라고 equals
       - 객체 비교시 식별자(메모리주소) 대신 비교하라고 equals/hashCode 재정의

   
   
2. 다형성(polymorphism)이 지원되어야 진짜 객체지향

   1. 다형성의 2가지 요소

      1. substitution(대체가능성)
      2. internal identity(내적 동질성)

   2. 대체가능성: 구상형 객체 생성시, 변수를 추상형 or 구상형 선택해서 받을 수 있다.

      - 다 미리 생성되지만, 하나의 형을 변수로서 선택했다면,  해당형에 포인터를 배정하며 동적바인딩된다.

      ![image-20220122195245713](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122195245713.png)

      ![image-20220122195422209](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122195422209.png)

      - 추상형(Runnable)에 정의된 기능만 사용할 수 밖에 없다.
      - 하지만 포인터(Runnable, 추상형)의  포인터(Worker)를 통한 .run()의 실행이라 구상형이 구현된 로직을 실행한다.
        - 한번 거쳐서 구상층의 내용이 실행되므로 비용이 크다.
      - 자세히 보면, Woker()객체 생성시
        - 추상형 Runnable객체가 먼저 생성되고
        - 구상형 Worker의 객체가 생성 미리 생성된다.
        - 어떤 변수를 쓰냐에 따라서 2개 중 1개의 형에 포인터를 배정한다.
      - 추상형인 Runnable변수에 배정했다면, run()실행시
        - Runnable부터 찾아 들어간다음
        - run(추상메서드)를 찾아간다음
        - 실제구현부인 Worker.run()으로 들어가서 실행된다.
      - 이 모든 것은 runtime에 시행되어 동적바인딩이라 부른다.

   3. 내적 동질성: 추상형으로 받았어도, 구상형 생성자로 객체 생성했다면, 구상형(출신지)의 메소드가 호출된다.
      ![image-20220122211317113](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122211317113.png)

      - 메서드 참조는 출신지가 제일 중요하다
        - 추상체의 worker.print()를 호출했지만, 객체 생성자가 호출된 HardWorker()가 물려받은 print()가 HardWorker내부에서 호출된다.
      - 변수선택으로 인해 형은 달라지더라도, 내적동질성 = 출신지메소드가 호출된다.



3. object(객체)가 갖춰야할 2가지
   1. 기능의 캡슐화(Encapsulation of functionality)
      - 복잡한 기능, 상세기능은 객체 내부의 메서드로 가져 사용자에게 쉬운 인터페이스를 제공하지만
      - **궁극적인 목표는 수정/추가시 발생하는 변화에 따른 여파를 격리**
        - 쉬운기능 노출 = 복잡한 기능 내부에 갖힘 -> **캡슐화 = 로직의 격리 -> 외부에 영향 줄어듦**
   2. 상태 관리(Maintenance of State)
      - **관리의 대표가 은닉화**다.
      - 상태값들을 감춰서 내부에서 관리해라는 뜻.
        - 변화에 대한 여파가 오지못한다.





#### 03 theater(객체의 설계)

- 상호의존표

  - 책(error 빨간색): 잘못된 객체지향 설계

    ![image-20220122214003688](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122214003688.png)

  - 수정본

    ![image-20220122214556326](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122214556326.png)
    ![image-20220621120453611](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621120453611.png)

    - 받아오되, **필드객체로 값으로 저장**하고 있어야 의존성이 보인다.
      - 메서드인자로 받아오되, 그 class를 필드값으로 저장없이 다른 것을 위해 사용만 한다면 의존성은 보이지 않는다.

##### Theater

- **theater(시작점)**는
  ![image-20220122223342652](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122223342652.png)

  - <-> 자신이 가진 가격**정보를 넘겨주어**, **만들어 파는(**찍어낼) ticket을 **서로 안다**

    - 내부 발행(생성)객체는, **반드시 this로 정보를 줘서**, **현재 발행 주체를 객체=식별자로 알려주면서**가 정보를 제공하여 생성하게 한다. 

      - **theater.fee의 필요한 값만 넘겨주어선 안된다. 객체는 식별자로 이야기한다.**
      - class내 생성이므로, 여러 인스턴스가 각자의 정보를 가지고 있기 때문에, 인스턴스 theater마다 서로 다른 정보를 가지고 있고, 서로 다른 정보의 Ticket이 발행된다.

    - 내부 메서드안에서 new Ticket( this:현재인스턴스의 정보 )로 바로 생성할 재료객체를 앎.

      - **정보를 this로 현재instance의 정보를 제공하여 내부 생성객체는 서로 안다고 표현**한다.

        ![image-20220620220809177](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620220809177.png)

    - 자신이 만든 ticket을 office에게 맡기니

      - office는 -> ticket을 안다
        - 재료객체를 받은 것은 안다고 표현

    - office는 받은 ticket을 seller에게 표 팔기 위해 

      - seller -> ticker을 앎
        - 받은 것을 안다고 표현

  - -> 내가 **`내부`에서 만든** ticke **재료객체를 `발행과 동시에 받아갈`(줄)** N개의 `office`(들)을 **안다**

    - **내부 생성객체를 받아갈 객체(들)**는 **외부에서 인자로 들어온** 뒤, **자기가 add메서드기능 제공해서 받아가야하므로 안다고 표현**한다.

      - **뭔가를 받는 놈도, 받을 준비가 되어있어야한다( 받는놈.add( ), 받는놈.set( ))**
      - 정해진 수를 받아갈 땐, 받을놈 + 그 갯수를 함께 인자로 받는다.
      - 안 상태 = 인자로 받아온 상태에서, 들어온 객체의 기능을 통해,  내가 가진 재료객체를 건네줄 수 있다.
        - 받아온 객체.add( 재료객체 ) 형식으로 객체에게 건네준다.
      - 받아온 객체 목록은 List필드값으로 저장해서, 확인용으로 쓸 수 있다.

      ![image-20220620221101632](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620221101632.png)

  - -> 자기가 발행하는 내부(발행)생성객체 invitation을 서로 안다

    - 내부 발행객체는, 현재instance의 정보를 받아가므로this를 건네준다.

      - 여러 개를 받아가면, add기능을 제공받고
      - 1개만 받아가면, set기능을 제공받자

    - 내부 생성객체는, **메서드인자를 통해 들어와** 자기의 기능으로 받아가야하므로,  안다고 표현

      ![image-20220620223020232](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620223020232.png)

  - -> 자신이 발행하는 invitation을 **발행과 동시에 받아갈** audience를 안다

    - 내부 생성객체를 넘겨받을 객체는, **인자로 들어와서** 받을준비=지 메서드로 받아갸므로,  또한 안다고 표현

      ![image-20220620222121228](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620222121228.png)

  - ->  자신의 기능(.enter( ))의 대상인 audience를 안다

    - **(여기선 검증) 기능의 대상 객체를 메서드의 인자로 받아와야하기** 때문에 안다고 표현

      - theater.enter(  audience )
      - **Ticket자체로도 .isValid()를 확인하는 기능을 할 수 있지만(객체세상)**
        - 하지만, **theater 인스턴스마다 서로 다른 정보로 발행을 하니, 발행한 theater정보가 필요하므로, 발행시 정보를 준 this가 필요하다**

      ![image-20220620222944995](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620222944995.png)



##### Ticket

- **ticket**(시작점theater이 아는 3 중 1)은
  ![image-20220123210927966](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123210927966.png)

  - <-> theater가 ticket을 발행(안다)할 때, **현재 theater instance(발행주체)로부터 정보를 받아서 생성**하므로 theater를 서로 안다

    - **생성자에서 외부 인자로 받아올 때** 안다고 한다.

      - **특히, 발행주체는 fee정보 대신 불변하도록 final field로 가지고 있는다.**

      ![image-20220620230053646](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620230053646.png)

    - 추가

      - **getFee**(): 정보를 **객체로 받아왔다면, 객체내부정보를 getter를 통해 중개해서 제공**한다.

        - final로 불변이지만, 발행주체의 정보(원시값  fee)가 변하더라도, 원시값(fee)을 포장한 (theater)를 통해 정보를 가져온다
        -   `.`을 통한 포인터의 포인터 정보는 **해당 값이 바뀌어도, 실시간 변화된 정보를 받아온다.**
        - **값의 변화가 반영되려면, 참조의 참조로 얻어와야한다! 그래야 참조의참조 업데이트가 runtime에 반영(이후에도 반영)** 
          - **내 고유정보가 아니면, 값으로 받아오지 말자!**
          - **포인터의 포인터를 비용으로 치루더라도 -> 변화가 바로 반영되도록 하는 원리를 가지게 한다.**

        ![image-20220620230537223](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620230537223.png)

      - **isValid**(Theater theater): 

        - **발행되는 `소모되는 재료객체`로서 `있다/없다 존재 확인이 필요할 때 null객체`**를 가지고 있는다.

        - **이미 쓴 표**인지 검증할 때, flag변수를 상태값으로 가지고 있는다.

        - 그외에, **발행주체**에 대한 검증 + **잘못 생성**된 null객체인지도 추가한다.

          - **발행되는 재료객체로서 null객체를 가진다.**

          ![image-20220620231924384](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620231924384.png)



##### Invitation

- **Invitation**(시작점theater이 아는 3 중 2)은
  ![image-20220123213008079](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123213008079.png)

  - <-> theater가 invitation을 발행(안다)할 때, **현재 theater instance(발행주체)로부터 정보를 받아서 생성**하므로 theater를 서로 안다

    - **생성자에서 외부 인자로 받아올 때** 안다고 한다.
      - **특히, 발행주체는 fee정보 대신 불변하도록 final field로 가지고 있는다.**

    ![image-20220620232447385](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620232447385.png)

  - 추가

    - **발행되는 `소모되는 재료객체`로서 `있다/없다 존재 확인이 필요할 때 null객체`**를 가지고 있는다.



##### TicketOffice

- **office**(시작점theater이 아는 3 중 3)는
  ![image-20220123214826683](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123214826683.png)

  - -> **받는** ticket을 안다

    - **메서드의 인자로 받는 재료객체**를 (저장도 해야하니) 안다고 표현

      - 받을 때는, **주는 객체한테 들어가서  받는 기능을 제공해야만 한다. 이 때, 재료객체를 인자**로 받는다.

      ![image-20220620233026335](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620233026335.png)

  - (심화) 의외로  **주는 theater를** **모른다.**
    ![image-20220620233817178](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620233817178.png)
    ![image-20220621002935330](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621002935330.png)

    - office는 **주는 주체는 모른 상태에서** 받는 기능만 제공한다.
      - my) **주는 주체 내부로 들어가 받는 기능만 제공할 땐, 주는 쪽을 몰라도 된다.**
        - 넘어가 기능 제공하는 쪽은 몰라도 된다.
    - office는 theate에게 **주는 것이 없기 때문에, 인자로 받아올 필요가 없다**.
      - 화살표 방향: 주는쪽이, **줄때는 받는 쪽을 알고서 인자로 받아와, 자기 기능으로 받아가게 하여**, 준다.
      - **이 때, 받는 쪽이, 들어와서 자기 기능을 open(제공)해 줄때만 주는 것이 가능하다**
        - **화살표 나가는 쪽이 많이 알고 있고, 줄 수 도 있지만, 받는 쪽이 기능 제공(오픈)해줄 때만 줄 수 있다.**
          - 거만한 것은 화살표를 받는 office다. 주는데도 안받을 수 있으니
      - 화살표 나간다 = 알고 있다 = 줄 수 있다?(X) = 알고서 인자로 받아왔지만, 받은 객체 =  **`화살표 받는 쪽의 받기 기능`을 제공한다면, 이용할 뿐이다.**
      - 화살표 받는다 = **화살표 쏜놈이 나를 받아or생성해서 쓰려고하여, `화살표 받는 쪽으로서 기능을 제공`한다.**

  - <- 가지고 있는 ticket을 팔라고  **seller에게 `주는 데도, 역으로 안다.`(= 주는 객체들이 이미 완성된 것이니, 니 상황에 맞게 빼가라고 기능 제공할테니, 나를 알아서 가져가, 기능 이용해)**

    - **준다고해서, 화살표가 나가면서 알아야하는 것은 아닌가보다. **

      - **줄 것이`내부 생성해야하는 객체라, 내부에 와서 받아감`으로 줘야할 때는 화살표가 나가서 알고 (Theater -> Office)**
      - **줄 것이 `이미 생성된 객체라,  외부에서 빼갈 때`는, 화살표를 반대로 받아서  `상대방이 알고서 받아가 빼가는 제공 기능을 이용하도록` 빼가는 기느응ㄹ 제공해서 준다.(Office <- Seller)**
        - theater는 -> office에게  **내부발행해야하는** ticket을 **받아가라고 할** 때, 화살표가 나가면서 알고 있어야만 = 받아와야만 했다.

    - **office는 -> seller에게 ticket을 줄 때(이미 완성된 객체라 seller상황에 맞게 빼갈 때)** 

      - seller를 받아와, seller의 받는 기능을 이용해서 주는게 아니라
      - seller가 **이미 완성되어 가지고 있는** tickets를 **알아서 빼갈 때**는 seller가 office를 받아가서(알고서), 내부 재료객체(tickets)를 `빼가는 기능`을 **seller내부 상황에 맞게 빼간다.**
        - seller상황에 맞게 빼가는 기능이니, **빼가는 놈의 상황에 맞는 여러 빼가는 기능을 제공**할 수 도 있다.
          1. 초대권을 가진 audience을 만난 seller의 상황: 무료로 빼가게 한다.
          2. 일반 audience를 만난 seller의 상황: 돈 주고 빼가게 한다.
        - 빼가는 기능을 상황별 제공할 때, invitation여부를 몰라도 되나??
          - invitation를 받아가는 audience도 알아야한다.
          - **상황별 빼가는 기능만 제공하고, 상황 판단은 빼가는 놈인 seller에서 분기별로 알아서 결정해서 기능을 쓰게 한다.**

      ![image-20220621002330351](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621002330351.png)

    - my) 정리
      ![image-20220621002926657](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621002926657.png)

      - 발행주체가 내부 생성 재료객체를, 받아가라고 할 땐, 화살표가 나간다
        - 받아가는 기능을 제공하는 놈을 알고서, 메서드 인자로 받는다.
      - 갖다 쓴놈이, 창고에서 완성된 재료객체를, 빼갈 때도, 화살표가 나간다
        - 상황에 맞는 **빼가는 기능을 제공하는놈을 알고서**, 메서드 인자로 받는다.
        - **결과적으로 빼가는 기능을 제공하는 office는 seller의 소속기관으로서 seller의 `setter의 메서드 인자로 받아서 알게 된다.`**
          - **즉, 빼가는 기능을 이용하는 상황의 메서드에서 인자로 받는게 아니라, 미리 알게 된다.**
        - **소속기관이면, 생성시부터 정해지니, `생성자의 인자 -> final로 박아`두면 안될까 싶지만, `소속기관은 불변하지 않고 바뀔 수 있어서, setter -> 정보field로 박아둔다.`**

  - 추가

    - ticket과 다르게, **생성재료 정보를 가진, 발행주체 객체(Theater)가 따로 있는게 아니라면, 생성시 필요한 재료를 `값`으로 받아 필드로 가지고 있는다.**

      ![image-20220620234825699](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620234825699.png)

    - 받는 기능 제공시, 여러 번 받아와 저장한다면, 저장필드로 빈 리스트 필드를 가진다.

      ![image-20220620233051642](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620233051642.png)

      

    - 외부에서 재료객체(ticket)을 빼가기도 하는데(By seller), 빼가기 전에 그 객체의 정보를 먼저 조회하기도 한다

      ![image-20220621011903391](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621011903391.png)



##### TicketSeller

- **TicketSeller**는
  ![image-20220124012340287](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124012340287.png)
  ![image-20220621003304340](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621003304340.png)

  - -> **office에서** 돈을주고 **빼온(사온) 재료객체** ticket을 안다(모르고 팔 순 없다)

    - **알고서, 창고객체(office)의 기능을 이용해 `빼온 재료객체`를 return값으로서**를 안다고 표현

  - -> office에게 **고용되는 소속기관 = 상위도메인으로서** 안다. **+ 재료객체를 빼오는 기능제공 대상을** 안다

    - **`프리랜서 소속기관으로서 setter의 인자로 받아와 정보필드에 박아둘 프리랜서 소속기관, 상위도메인 객체`**를  안다고 표현

      - **`소속`을 `setter로 받을 것인지 / 생성자에서 받을 것인지`는 신중하게 생각해야한다.**
        - **태어나서 `불변하는 발행주체, 소속기관` -> `생성자에서` 태어날때부터 받기**
        - **`중간에 계속해서 바뀔 수 있는 프리랜서의 소속기관` -> `setter`로 받기**

      ![image-20220621005830062](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621005830062.png)

    - **(창고로부터)` 빼오는 기능을 제공하는 놈을 받아와, 상황별로 빼오는 기능을 이용`해야해서 안다고 표현**

  - -> **재료객체** ticket을 **거래할(받아갈) 거래대상** audience(고객)을 **서로 알아야한다.**

    - 서로알기

      -  발행주체(정보보유, theater) <-> 발행재료객체(Ticket)
      - 거래대상: seller <-> audience
        - **seller: 빼가는 기능 제공시, `audience를 알고서 거래가능한지  검증 -> 물건양호시 사는놈의 돈 차감`까지 해야한다**
        - **audience: 들어가서 검증을 당하기도 하지만, 최종적으로 seller를 알고 seller가 거래물건을 주는 기능을 이용한다.**

    - 메서드명만 getTicket(거래 대상)이지, 거래메서드다.

    - 받는 놈기만 하는 놈(theater-office)은 갑으로서 받는 기능을 제공하기도 하지만, **거래에서는 사는 객체(audience)는 파는 객체(seller)에게 메소드 인자로 들어와, `거래가능한지 검증 -> 물건양호시 돈 차감까지`당해야해서** 알아야한다.

      - **거래를 하는 상대객체**를 서로 안다고 표현(**트랜잭션 관계**)

      ![image-20220621012307689](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621012307689.png)

  - (심화) theater-office의 관계에서 **받기 기능을 제공하는 받아가는 office가 갑**이었다면

    - **여기(seller - audience) 거래관계에서는 `받기기능이 없이, 검증당하고, 돈차감당하는 audience`보다, `최종적으로 getter의 return으로 거래재료객체를으로 건네주는 기능을 가진 seller가 갑`이다.**

      - 최종 기능을 제공하는 쪽이 갑이다. 
      - audience는 거래정보를 검증당하고, 돈을 차감당하는 기능만 제공하는데, **최종적으로 중요한 기능은 seller의 거래물건 제공기능이며, 그 안에서 audience가 들어와 검증/차감 당한다.**
      - 파는놈 seller는, 사는 놈인 audience를 메소드 인자로 받아서 **자기 기능으로 audience에게 거래 검증/돈차감기능을 활용하여  return으로 거래객체를 줄지/안줄지를 결정하는 기능을 제공해서 갑이다.**

    - my) **거래관계에서 Seller와 Audience는 서로 알지만, 파는놈 Seller가 갑으로서 `사는 놈.buy기능( 파는놈 )`의 인자로 들어가지만, seller에게 제공하는 기능이 아니며, 그 내부에서는 `사는놈(을)이 seller(갑)의 .getTicket( 파는놈this )의 거래메소드의 인자`로 들어가서, 돈검증/물건양호시 돈차감까지 당한다.**

      - 아래서 할 내용

        ![image-20220621014243063](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621014243063.png)

##### Audience

- audience는
  ![image-20220124223050202](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124223050202.png)
  ![image-20220621105608176](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621105608176.png)

  - -> **거래하여 받아와 나한테 저장하는 재료객체** ticket의 존재를 미리 안다

    - 거래후 받아와 **내 상태(field)로 저장(setter)해야한다면, 객체필드로서 미리 알아야한다.**

    ![image-20220621110334766](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621110334766.png)

    - (심화) 소모성 재료객체를 상태(객체필드)로 가질 땐, **setter전 default상태를** null객체를 사용한다.

  - -> 상대방이 줘서 상태로 저장하는 객체 Invitation은 미리안다.

    - **setter** 역시 **받기기능으로, 받기후 내 객체필드로 저장하려면, 미리 알아야**한다.

    - (심화) **받기기능으로 받을 준비가 되어있는 객체는, 주는 놈이 일방적으로 알기만 하면 된다.**

      - my) **받기기능 제공 -> 주는놈이 setter or add로 일방적으로 알아서 꽂아주기만 한다.(소통은X)**
      - theater -> ticketoffice 
      - theater -> audience

      ![image-20220621111556554](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621111556554.png)

  - -> ticket을 **판매하는 주체이자 거래대상은** seller를 **서로안다**

    - 파는놈이 갑으로서 **팔기 기능을 제공**하니, **알고서 받아와 기능을 이용**해야한다.
    - (심화)**사는놈은, 스스로 구매 기능을 가지나(제공하는게 아님), 파는놈을 인자로 받아와, 파는놈의 기능을 이용해서 물건을 받아와 저장한다**
      - 파는놈의 기능 내부에는, 사는놈을 검증/돈차감 해야하기 때문에 인자로 받는다.

    ![image-20220621111753408](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621111753408.png)

    - 거래해서 받아오는 객체(Ticket)뿐만 아니라 파는놈(seller) 역시 알아서 그 기능을 이용해야한다.

  - 추가

    - 고객(사는놈)은 태어날때부터 값 amount(돈)를 자신의 상태로서 가지고  태어난다.

      ![image-20220621105403648](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621105403648.png)

    - 물건을 사는 놈은, 파는 놈이 알고서 이용할 기능 2개를 제공해야한다.

      1. 거래가능한지 검증당하기 hasAmount
      2. 물건양호시 돈차감당하고 성공여부 minusAmount

      ![image-20220621114252207](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621114252207.png)

      - (심화) seller(갑)에 의해, 검증/돈차감에 대한 최소한의 노출(boolean)만 해준다.
        - getAmount로 내 정보를 직접 가져가서 하는 것이 최고의 노출이며 안좋은 것이다.

    - (심화)**갑이 파는놈(seller)가 요구하는, `검증`(거래가능한지 검증, 초대권 잇는지 검증) 및`차감후 성공여부`(돈 차감, 초대권 차감)**메서드들은 **파는놈(seller, 갑)이  사는놈(audience, 을)에게 요구하는 스펙들(조건들)이며 `거래(트랜잭션)을 위한 조건들`이다..**

      - **hasAmount**(거래가능한지 물어봄), **getInvitation**(직접 정보가져가서 검증)
      - **minusAmount**(돈차감후 성공여부), **removeInvitation**(성공여부 없이 바로 초대권 차감만)
        - **`트랜잭션 조건들`만, `인터페이스(카테고리)로 빼서 책임을 분리`할 수 도 있다.**
        - TransactionCondition
          - 어렵다..

    - 상대방이 나에게 시키지 않고, 내 정보를 물어보고 내 외부에 직접 판단/검증할 때 getter주기기능을 제공한다. 

      - ~~최고수준의 노출이라 딱히 좋다고 할 수 없다.~~
      - **my) 내생각에는 `NULL객체`로 바로 확인 가능할 경우, 직접 가져가서  != Null객체 으로 바로 확인가능하니까, 시키지 않고 받아가는 것 같다.**

      ![image-20220621113614284](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621113614284.png)

    - (심화) 사는놈은 **스스로 사용하는 기능은 buyTicket밖에 없고**

      - 다른 메서드들은 전부, 다른 클래스들을 접대(기능제공)해주는 메서드들 밖이다.
        - setter도 **상대방이 일방적으로 알고서 꽂아주는 것**이기 때문에, 상대방용이다.  상대방이 안다 = **상대방 내부에서 사용**한다.
        - getter도 **상대방이 정보를 직접 받아가 상대방 내부에서 사용**한다.
        - 4개의 트렌잭션 스펙들은 **seller가 알고서, 자기내부에서 이용**하는 것이다.

      ![image-20220621115106476](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621115106476.png)

      - 알고서 필드를 가져오되, 상태값(필드)로 해당class 객체를 저장하지 않으면, 의존성은 안보인다.

        ![image-20220621120809980](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621120809980.png)

##### Main

- main은

  ![image-20220621124740309](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621124740309.png)

  1. **원래 Main부터 짜야한다. 절대 class -> Main 순으로 짤 순 없다.**
  2. 내 도메인에 맞는 **시나리오부터 main에서 짜면서**
     1. **객체들의 협력상황을 보면서**
     2. **class들을 만들어나간다.**
  3. 테스트코드부터 설계에 반영하려면, **테스트코드가 실제 설계에 사용되는 client코드와 닮아있어야**하는데 
     - **클래스를 완성되었다고 가정**하고, **클래스를 객체로 어떻게 사용할 건지 먼저 짜보는게** 훨씬 낫다
     - **상상속으로  class를 짜봤자, 어떻게 사용되는지는 모른다.**



#### 04 Type, condition, 책임기발 개발

- type
  - **변수명 = 메모리주소의 별명** = my) 1개 class라면, 개별 인스턴스명
    - `a = 3;` : a라는 메모리주소에 a을 할당
  - **data type** = 변수명이 가리키는 메모리주소를 시작으로 **차지하는 데이터의 길이**
    - java의 data type = 데이터의 길이
  - **객체지향의 형(type)** : data type(원시형)이 뿐만 아니라 **모든 것을 type(형)으로 생각**





- 객체지향의 Type:  4가지 개념을 구현 가능한 유일한 것
  1. role : **역할을** type(형)으로  묘사
     - 역할 interface
     - 역할 class
     - 역할 enum
  2. responsibility: 로직(**책임**)을 type(형)으로 표현
     - ~할 수 있음 : ex> Reservationable의 인터페이스의 형
     - 람다식도 형(function형)
  3. message: 특정 역할들끼리 or 특정 책임들끼리의 **협력을 위한 메세지**도 type(형)으로 되어있음
     - string, integer로 넘기면 형이 아닌 값이다.
       - 값: 불변하며, 보낼 땐 복사해서 간다.
         - 협력하거나 동등한 대상이 아니다.
         - 상태관리 책임이 없음.  책임위임이 안됨.
         - 캡슐라이즈가 안되서 객체지향에서는 못쓴다. 
       - 형: 객체의 주소(변수)을 보낸다. ex> list는 복사안되고 참조주소가 넘어간다.
  4. 프로토콜: 마커 인터페이스. 합의된 공통요소도 형(type). 몸체가 없어도 형을 가짐.





- jvm에서 동원 가능한 3가지 context type

  1. static: **단 1개의 인스턴스를 생성시** but 동시성 문제는 따로 해결해야 함.

     - jvm에는 동시성 보장 장치가 없으므로 싱글톤을 보장하는 코드로 직접 짜야한다.
     - 책에서 static context를 완전히 버리길 권유
       - 왠만하면 instance context만 사용하길 권유

  2. enum : **제한된 여러 개의 인스턴스 묶음이 필요시** + 제네릭 사용 불가

     - static하지만, 생성에 대한 동시성 문제가 없음. static보다 더 빨리 만듦.

     - enum으로 만드는 게 일반 instance보다 유리
     - 많은 경우, 무제한 인스턴스(by class)가 아닌 경우가 많아서  class를 enum으로 대체할 수 있는 경우가 많다.
     - **but 제네릭 사용이 불가하여, 형 대체 = `여러형을 알고 시을 때 or 추상층에 T로 구상층이 특정형만 알고 싶을 때` 사용이 불가능하다.**
       - **제네릭 사용불가의 문제 때문에, enum 대신 마커인터페이스 + class 조합을 쓴다.**

  3. class: **무제한 인스턴스가 필요시** 변수로 받아 만들어냄

  4. 정리

     1. 동시성 문제(static)가 없고 제네릭(enum X)이 사용가능한 class + 인터페이스를 주력 형으로 사용하게 된다.
     2. 확장없이 제한된 instance는 enum으로 대체하자
     3. static은 적이다. 쓰지말자. 
        1. **사용한다면 `Factory`클래스( 생성만 해주고, 사용 은 안하니  상태값 X -> 동시성 문제 없음 )**
        2. **사용한다면 `Utility`함수(원래 사용시,  변할 수 있는 상태값 X)**

- class 속 인스턴스메소드 vs 유틸리티 함수
  1. `this`나 `this.`의 내부 상태값을 쓰지않는다? -> **class에서 빼내야하는 유틸리티 함수**
     - 메서드파리미터 + 지역변수 + 전역context변수만 가지고 정의되는 메서드
  2. this나 this.필드의 **내부 상태값을 쓴다 -> 인스턴스 메서드**







- condition
  - 조건 분기: 특정 변수나 필드의 **변하는 상태값에 대한 연산**을 처리하기 위해 생기는 것
    - **상태값(필드, 변수) = runtime이 바뀔 가능성을 가진 것** -> 분기를 만들게 된다.
  - 조건 분기에 대한 전략 2가지
    1. ~~**내부에 if를 나열하는 `내부 응집성`으로 모아두기**~~
       - switch문을 이용하는 것
       - if와 마찬가지로, 조건 분기 증가시  -> 내부 코드 전체를 변경(추가)해야함
    2. **외부에 위임하여  `if수만큼 외부처리기`를 생성하고, 외부에서 분기를 선택하여 입력해주는 방식 **
       1. 조건 분기위 위치를 내부 -> 외부로 옮기는 방식이다.
       2. 외부에서는 1개 분기에 대한 처리기만 넣어준다.
       3. 대부분의 패턴이 이 방식을 이용
          - 전략패턴: if 5개 -> 추상체 + 5개의 전략객체(외부 처리기) 생성





- 내부 응집성(Cohesion) 있는 코드: 한 눈에 보이는 코드 but **안좋은 말**

  ![image-20220125220548356](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220125220548356.png)

- 분기를 외부에 위임하여, 외부에서 선택한 분기를 주입하는(Injection) 코드: 내부에서는 처리기만 실행해준다.

  ![image-20220125220802586](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220125220802586.png)

  - 내부에서는 처리기 실행안한 체로 받아와서, 실행만 해준다.
  - 외부에서는 그만큼 형(익명클래스 구현도 형)이 생긴다.
  - **내부 응집성을 줄인** 코드 장점
    1. 내부 코드 수정이 없어진다. : 변화가능성은 전부 외부로 위임한다.
       - **내부 코드 수정 없다 = 재사용 가능한 코드가 된다.**
         - **변화율이 큰 코드(if유발 = 확장/변화 되는 코드)는 외부로**
         - **변화율 적은 코드(처리기 실행등 사용만하느 코드)는 내부로**







- 책임기반 개발 - > 추상화해서 역할이 나온다.

  - 사용자 기능 = 가치 = 시스템의 책임

  - 책임 = 가치 있는 기능 = 구상클래스의 메서드

    - 인터페이스 등의 껍데기가 아닌, 실제 로직을 구현하는 메소드가 책임이다.

  - 책임인 **메서드를 더 작은 단위의 책임으로 분할**하자

    - 예매 기능 = 너무 복잡하다
      - `예매`가 잘되어야한다 -> `방대한 책임`이 물려서 더 쪼갠 뒤, 개선해야한다.
        - ex>  depth 2이상의 if -> 분리

    - 응집성이 높으면서도 (한눈에 보이면서도)
    - 결합도가 낮은 (의존성이 낮음) 코드로 쪼갤 수 있는 것이 실력

  - **쪼개진 책임(메서드들)들을 추상화한 것이 `역할`이라고 정의한다.**

    - **`역할`이라는 것은 여러개의 책임(메서드)들을 말하는 게 아니라, `책임(메서드)들을 추상화`한 것**
    - 쪼개는 과정에서 공통점 / 공통의존기능이 발견된다.
    - 이것으로 추상화에 성공했다면, 또다른 쪼개진 책임 추가도 쉽게 된다.
      - **1개의 책임만 있어도, 더 높은 수준으로 추상화해서 `역할`로 승격**해놓고 만들어놔야,  이후 또다른 책임의 확장이 쉬워진다.
      - 연역법 성공 -> 귀납법으로 다른 사례를 만들 수 있음.

  - 책임과 역할을 따로 생각하긴 어렵다

    - 다양한 책임들(메서드들) -> 공통코드 or 공통 의존기능을 뽑아서 -> 역할로 추상화 하는 것이 굉장히 어렵다.

  - my) 전략패턴이나 추상클래스 -> 익명클래스 분신술 구현의 **전략메서드/훅메서드는 `공통점을 찾기 어려운 분기속 개별구상체들의 코드들을 -> 구상체만의 로직들만 모아 1개의 책임(메서드)으로 묶고 ->  명칭을 추상화하여 -> 인터페이스로 역할을 만든 것`**이라고 보면 될 것 같다.

    - **`역할` = `추상체`가 가진 `전략메서드, 훅메서드`**
    - **`책임들` = `구상체들`의 `개별 구현 전략메서드/템플릿메서드` or `익명클래스`의 `실시간 개별구현 전략/훅메서드` **
    - 만약, **전략메서드/훅메서드가 인자를 받는 경우라면?** 내부 라이브러리 레이어들도 다 바뀌어야한다.
      - **추상화 능력이 떨어지면**, 수시로 라이브러리가 업데이트 된다.



- 객체간 협력은 **구상체의 책임(메서드)들이 아니라 추상체의 역할(전략메서드, 템플릿메소드)로 해야한다.**
  - 협력 구축은 **보다 높은 단계인 역할**단계(추상층)에서 협력해야한다.
  - 책임단계(구상층)에서의 협력은, 책임(메서드)의 추가/변경시마다 협력시스템에 위험이 전재힌다.
    - 책임 추가 -> 협력도 깨진다.



- 정리
  - 값(전달시 불변값이 복사, 실시간 변동 반영안됨)이 아니라 형Type(참조를 건네줘서 연결, 변화가 반영되는 포인터의 포인터)을 써야한다.
  - 조건 분기를 제거하는 방법은 2가지인데 내부 응집도(한 눈)보다는 외부로 분기를 보내야한다.
    - 그러려면 책임들 -> 역할로 추상화한다.





#### 05 theater with reservation and discount policy(객체의 설계2)

- [최종코드](https://github.com/LenKIM/object-book/tree/master/object2/src)

- step1의 모델

  ![image-20191124163632027](https://raw.githubusercontent.com/is3js/screenshots/main/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f30303679386d4e366779316739393633666b337a766a333131713069676d79372e6a7067)


  ![image-20220623170546098](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623170546098.png)

  ![image-20220623170509341](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623170509341.png)

  - theater가 재료객체 **ticket, invitation을 내부에서 발행(생성)**하였는데,  그 이유는 **발행시 theater를 생성자의 인자로 받아가 정보를 필드에 저장**하도록 내부에서 넘겨줬기 때문이다.
  - theater가 발행한 **ticket 판매의 복잡한 책임**은, 책임(메서드)이 커지기 때문에 직접 하지 않고, **ticketOffice에게 가져가게 위임**했었다.
    - ticketOffice에게 와서 받아가라고 하여, 발행과 동시에 넘겨줬다.
    - ticketOffice는 ticketSeller를 통해 판만큼의 가진 티켓은 차감 + 그만큼 Fee를 얻었다.
  - ticketOffice는 티켓 창고역할만 하고, 실제 판매는 프리랜서인 ticketSeller를 고용(하위도메인으로서 소속기관Office를 필드로 가짐)하여, Seller가 판매를 한다.
    
- step2의 새로운 모델(책)
  ![image-20220126224317186](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220126224317186.png)

  - **Fee <-> Amount의 판매(물물교환)로직이 사라진다.**
    - invitation도 사라짐
    - 대신 reservation을 하게 한다.
  - **책**에서는 Screening이라는 상영정보 객체가 `.reserveSeat()`을 예약 책임을 가지고 있다.
    - `정보전문가 패턴`에 의해 정보를 가장 많이 가진 객체가 책임을 지는 구조이다 -> **말이 안된다.**
    - 상영정보는 real domain에서는 자리번호/순서/시간을 가진 단순 시간표정보일 뿐이다.
    - 게다가 현실에서는 1개의 상영정보(1 Screening)는 여러 영화(N Movie) 가지는 구조를 가진다.
      - 10시 - 스타워즈
      - 10시 - 인디아나존스
      - 10시 - 아이언맨
      - **영화들은 1개의 상영정보만 알아서 fk로 참조하면 되는 것이다.**
      - **N을 1의 필드로 넣으면 복잡해진다.**
        - 상영정보에 movie제목을 넣지말자.
        - 일부만 다른 값을 가져 중복 문제가 발생할 때, 다른 값을 가지는 그 필드(영화제목)은 정규화 대상으로서 N table로 옮겨야함.
    - **그렇다면, 상영정보는 movie 제목도 없어야하고, 시간만 가지고 있어야한다 -> 예약을 스스로 할 수 없다.**
      - 현실에서는 여러개의 극장 : 극장마다 여러개의 영화 :그 영화마다 여러개의 상영정보 형태를 가짐.
  - **책**에서는 할인 정책도 소개한다.
    - **어떻게** 할인하는 가 -> Discount**Policy**
      - AmountDiscountPolicy: 돈을 깍아서
      - PercentDiscountPolicy: 퍼센트로 깍아서
    - **언제** 할인하는 가 -> Discount**Condition**
      - TimeDiscountCondition: 시간이 언제인지 기준으로 할인
      - SequenceDiscountCondition: 몇번째 상영인지를 기준으로 할인
    - 책에서 소개한 것 처럼 **연관관계가 수직관계로 할인정책이 나오진 않는다.**
      - 각 정책들은 교차조건이다. 서로간의 부모 자식간은 상관없다.



##### Main

- 객체 설계시 Clinet코드부터 시나리오에 맞춰 짜야한다.

  ![image-20220127212251961](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127212251961.png)
  ![image-20220127220145163](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127220145163.png)



1. client에서 극장객체를 만드는데, Theater은 Ticket 발행에 필요한 정보인 fee를 Long 값 -> 값 객체 Money를 사용하게 한다. (책에서 요구함)

   - 값 객체는 일반 참조형과는 다른 성질을 가지고 있다.
     ![image-20220621182630624](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621182630624.png)

2. client에서 영화객체를 만드는데,

   - 제네릭으로 upperbound T형(추상형)의 구상형들 중 1개 구상형인 특정할인정책 아는 상태의 영화이다.

     - **왜??**
       ![image-20220621204507969](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621204507969.png)
     - **책에서 `영화`는 `무조건` DiscountPolicy 2개(Percent or Amount DiscountPolicy) 중에서 `1개의 policy는 movie에 필수 정책 적용`해야하는 `제약조건`을 주었다.**
       - **`1:1제약조건`의 적용은 값으로 적용X /  `생성자에서 객체로 주입하여 적용 X` / `제네릭`을 통해,  추상체로 정의된 정책을 `<형>으로 1:1 제약조건을 적용한 SubType`을 만들어낼 수 있기 때문이다.**
         - 참고) `FrontEnd <Client> ` `FrontEnd<ServerClient`>의 경우
           - 1:1 제약조건이 아니라 **들어오는 구상체마다 구현이 다른데, 여러 구상체를 받아야할 경우 추클->익클 분신술 SubType으로 `1개의 구상체가 여러 특정형을 상황에 맞게 SubType을 만들어내야하는 상황`이였음.** 
           - **근데, 현재 구상체Movie에 타 추상체(DiscountPolicy)의 구상체들을  택 1일해서 적용하려면, 인자로 받더라도, 그 구현체에 따라 다르게 작동한다. 더이상 구상화할 수 없는 Movie에 대해 N개의 추상체가 택1로 들어와야한다면 instanceof로 확인해서 쓸 수 밖에 없을 것이다.**
           - **`my) 구상불가능한 최하위 구상체` vs `N개의 구상체 택1`의 `1:1매칭`은 `instanceof`와 마찬가지로서, 제네릭으로 적용한다.**
             - 여러 구상체가 매번 인자로 들어와도, 추상체로 들어올거라, instanceof 써서 그에 맞게 적용하는 수 밖에 없을 것이다.
       - 이 때, Movie는 더이상 구상형Movie가 아니라 **Movie를 추상층으로 한 `Movie<특정형>`의 `Movie의  Subtype Movie`이며 Movie보다 더 구상형이 된다.**
       - Movie는 SubType을 가질 수 있는 Class라고 인식해야한다.

   - Movie의 제네릭을 통해 `1:1 제약조건 적용된 SubType`이 정해졌으면, 4개의 정보를 받아서 생성한다.

     - 3) 실제로 영화가격은, 바로 안정해지고, 상영정보(Screening)에 있는 상영시간에 의해서 차후 정해진다.
     - 4) **AmountDiscount의 `1:1 필수 제약조건의 policy`가 적용된 SubType Moive 상태에서, SequenceAmountDiscount라는 더 구체적인 when?의  `condition policy의 추가 정책`은 `먼저 지정된 제약조건policy Type에 맞게 + 생성자 인자에 객체로 주입해서 정책 적용`하였다.**
       - PercentDiscount
         - ??
         - ??
       - AmountDiscount
         - **Sequnece**AmountDiscount
         - ???AmountDiscount
       - condition policy(when)의 적용을 인자로 하려면, **`제네릭`으로 먼저 적용된 `제약조건 policy`에  맞게, 객체로 적용한다.**

   - 생성된 영화는 theater가 받기기능으로 받아가 저장한다.

     - **각 극장(1)마다 걸릴 영화(N)에 대해 `매칭을 받기기능으로 1쪽에 저장`하였다.** 
       - **각 영화(1)마다 걸릴 상영정보(N)또한 `극장 안에다가 매칭`시켜줘야한다.**

     ![image-20220621182334082](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621182334082.png)

3. theater에 생성된 영화에 딸린 `상영정보들(Screening)`도 넣어 저장한다.

   - 하위도메인으로서, 상위도메인인 영화_id(fk) 대신 영화객체를 매번 넣어준다.

   - 상영정보에 예매제한이 있어야하는데, **원래는 상영관을 사용하나, 상영관 대신 가용좌석수를 통해 무한예매를 방지**한다.

     ![image-20220621203224750](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621203224750.png)

   - 여러개를 넣을 것이므로 변하는 변수들은 변수로 지정한 뒤,  밖에서 반복문을 돌린다.

     - seq,  hour은 같이 같은 제한갯수로 도니까, 3시간마다 돌리면서 24시전까지의 제한이 있는 hour에만 제한을 주고, 안쪽에서 같이 돌려준다
     - 매번 하루의 제한이 끝날때마다 day를 바깥에서 돌려준다. 8일부터 31일까지 돌릴 예정이다.

     ![439fc601-00e5-4049-9657-825a7c602880](https://raw.githubusercontent.com/is3js/screenshots/main/439fc601-00e5-4049-9657-825a7c602880.gif)

4. theater와 협력할  ticketoffice와 **계약**을 맺되, **office에게 떼줄 수수료(fee)인 계약조건도 같이 인자로 받아서계약한다.**

   - **기존**에는 티켓을 팔아줄 **협력 ticketOffice들을 받아와서 setter로 나에게 꽂아넣어서 리스트만 저장했었다**

     ```java
     theater.setTicketOffices(ticketOffice);
     ```

   - **지금은 서로 알아서 계약을 한다.**

     - **계약시 들어가는 수수료10.0도 사실 `객체로 메세지`를 보내야한다. 인자에 값을 쓰면 객체지향 위반이다.**

     ![image-20220621210008036](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621210008036.png)

5. seller는 main코드 변화가 없다.

   ![image-20220621210742699](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621210742699.png)

6. audience - buyTicket 대신 **Customer가 상태값(변하는 변수)으로 자본금을 가지고 태어나, `reserveTicket()`의 예매를 하도록 바뀌었다.**

   ![image-20220621211156036](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621211156036.png)

7. theater가 client코드 -> 외부 -> 나중에는 UI로 **저장하고 있는 영화 / 상영정보드을 보여줘야할 책임이 있기 때문에 Main에서 getter(주기)기능을 제공한다.**

   - for + break;로 첫번째 것만 선택했다고 가정하고 한다.

   - **Customer는 스스로 예매`.reserve()`를 하지만,  사는 놈은 을의 위치이다. 가지고 있어 파는 놈(`seller`)와 산 것을 검증하는 `theater`의 을 인자로 받아서 내부에서는 그들의 기능을 이용한다.**

     - 구매할 때, Ticket 1개만 구매하는 것이 아니라, **어떤 영화의 어떤 상영정보를 골랐는지 `살 재료객체`도 같이 인자로 받는다. 추가로 `제한을 확인해야하는 구매 수량`도 인자로 받는다.**
     - reverse(): 예매
       - 특정 seller에게
       - 이 theater의
       - 특정 movie를
       - screening일 때
       - 2명이서
         - 보고 싶다고 예약을 한다.

   - Customer는 **내부에서 파는놈/산것 검증하는 놈을 `인자로 받아서 이용하는 이유`는 `산 것을 반환받아 내부 필드에 꽂아넣기 위해` + `산 것을 검증하기 위해`서 갑들을 인자로 받는다. 그외에 `구매수량`도 들어간다.**

     - **내부에서 갑인 thater가** theater.enter ( customer )로 **예매 성공한 customer의 예매 정보를 검증한다**
     - **예매좌석수 2도 객체지향 위반이다. 실제론 `SeatCount.of(2)`가 들어가야할 듯**
     - **isOk의 불린형도 원시값이라 객체지향 위반이다. 객체로return되어야한다.**
       - 위에 반복문으로 돌악는 seq도 **`SequenceNumber.of(seq)`의 객체로 들어가야한다.**
       - **영화제목도 `Title.of()`로 객체로 들어가야한다.**

     ![image-20220621215701031](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621215701031.png)



8. 원시값을 메세지로 넘기면 안된다. **`객체`만이 역할을 수행한다. `Type`만이 책임을 수행한다.**
   - 가능좌석수를 100으로 확신하고 썼어도... 확장가능성이 없다고 확신하면 안된다. 중간에 바뀔 수 있으면, 다른데 전파를 제대로 하기 위해서는 `객체껍데기`로 한번 쏴서 **포인터(변수)의 포인터(갖다 쓴놈)이 대응을 할 수 있다.**



##### DiscountCondition

- 일단 책에서 나오는 수직구조의 policy연관관계는 틀렸다고 한다.

  ![image-20220129001023447](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220129001023447.png)



- **Condition Policy**는 **최소화된 2가지 `역할`을 `공통`적으로 가지는 인터페이스다.**

  - my) 추상메서드는 공통을 모은 것이므로 책임(메서드) -> `역할`(추상메서드)이라고 승격해서 불러야한다.
    - **아래 2가지 역할은 iterator패턴과 동일하다.**
      - hasNext -> next() 보다는 **2가지 역할이 중요**

  1. **조건을 만족하는지 `액션 발동조건 검증` 역할**
     - `isSatisfiedBy()` 여기선 상영정보객체  + 관객수를 받음
  2. **`조건 검증 통과시 할 정책적용 액션`역할**
     - 여기서는 `calculateFee( )`로  discount 적용 액션

  ![image-20220621224203970](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621224203970.png)



- **외부 조건에 의해 변화/발동하는 경우, 2가지 인터페이스를 가져야한다.**
  1. 발동조건 검증 메서드(trigger) : **is~ has~ (외부조건 파라미터)**
  2. 액션 메서드(action): **발동(외부조건 파라미터)**
     - `DiscountCondition`에서도 있고
       - **외부 조건에 따라 `정책 발동`**
     - `Screening`의 seat필드에 대해서도 사용례가 있음.
       - **외부 조건에 따라 `필드변화`**



- 인터페이스
  - 제일 좋은 것은 마커 인터페이스
  - **책임/역할이 1개인 인터페이스**가 2번째로 좋다.
  - 여기선 역할이 2개인데??  
    - 인터페이스가 충분히  추상화되지 않았다.
    - **2개 정도도 책에선 만족한다. 메서드를 1개로 하면, layer가 그만큼 깊어지고, 이해하기 힘들 수 있다. 너무 깊은 추상화는 팀원들의 화를 불어일으킬 수 있음.**

- 메세지
  - 인자가 없는 함수 = 프로시져 = 혼자서 스스로 책임 수행
    - 결합이 약해지는 단점.
    - 우리 수준에서 못짠다는 단점
  - **인자가 1개인 함수**
    - 2번째로 좋은 함수로서 우리가 짜야하는 함수
  - 어떻게 좋은 함수(인자 1개)를 짤 수 있을까?
    - 여러개의 인자들을 객체화 시켜서 **객체 1개로 전달한다. `언제나 옳은 객체지향 메세지`가 된다.**
  - 인자가 2개이상이다?
    - **메세지가 충분히 `추상화, 객체화, 형`이 되지 않았다.**



##### DiscountPolicy

- 책에서는 enum으로 적용하지만, **enum은 형Type이 될 수없다.** (제네릭도 적용 불가?!)

- **policy 자체는 `마커` 인터페이스이지만, 그 내부에 `policy종류별`로  `policy 마커 인터페이스를 상속한 인터페이스`로 정의**한다

  ![image-20220202213429164](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202213429164.png)

  - inner interface의 정의는.. 부모interface를 중괄호로 구현해서 상속시켜야한다.
  - policy 자식의 종류에는 NONE policy도 포함시킨다.

  ![image-20220621225931356](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621225931356.png)

  - 차후 자식 policy는 아래와 같이 적용할 예정이다.

    ![image-20220621232928479](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621232928479.png)



- **차후 상세 정책은**

  1. policy 자식 중 1개를 구현하면서

  2. condition policy(중간추상층, 1개의 메서드를 중간에 미리 구현)를 상속한

  3. 구상class를 만들어 쓸 예정이다.

     ![image-20220621233211776](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621233211776.png)



##### Condition -> Policy 순으로 정책 적용해보기(시행착오)

- DiscountCondition은 **어쩔 수 없이 2개의 메소드를 가진다.**
  - **구상체를 추상클래스(중간 추상층)로 만들고 `발동조건 메서드` 1개를 중간에서 막고, `액션`메서드는, 2번째 정책(마커인터페이스)를 구현하되, 개념을 액션메서드에 녹여서 구현한다.**
    - class는 내려온 2개의 추상메서드들을 반드시 개별구현해야하지만
    - abstract class는 **상위추상층의 추상메서드들을 `중간 추상층으로서 중간에 @Override개별구현하여 fix`해놓으면 최종 구상class들은 `안보이지만, 갖다쓰는 물려받은 메서드`가 된다. 많은 추상메서드들 중에 `선택해서 중간구현`할 수 있다.**
  - **my) 추상클래스 사용례**
    - 전략패턴: 구상체들의 개별구현을 자신의 분신술로 Main으로 끝까지 미룰 때
    - frontEnd: 최하위구상층이 여러특정형을 알아야하고 다 개별구현해야할 때 제네릭+분신술 쓸 때
    - movie : 여러특정형(policy)과 필수로 1:1매칭되어 적용되어야할 때
    - DiscountCondition:  인터페이스의 2개 메소드 중 1개를 중간추상층으로 구현해야할 때
- **2개의 메소드를 가진 Condition Policy 인터페이스 처리 전략**
  - 원래는 특정형의 구상체class가 2개의 메소드를 구현
  - **특정형 기반으로 제한(추상클래스로 받을 기존 구상형)**하여, **추상클래스 중간추상층으로 만들어, 1개의 메소드만 중간에서 선택구현하여, 자식은 안보이지만 물려받아 갖다쓰고, 떠내려간 1개의 추상메서드만 구현하게 한다.** 
    - **중 간추상층에서** 개별구현할 때는, **구현에 필요한 정보를 생추상클래스의 생성자로 받게 한 뒤, final로 불변화시켜, 자식은 못건들게 한다.**
- **2개 중 왜 발동조건 메서드를 중간층에 개별구현 미리 했을까?**
  - 액션을 미리 구현하면 안되나?
    - **액션과 나머지 2번째 적용 정책과 관련**이 있다.
    - 액션부터 구현하면 4 x 2 = 8개 다 만들어야한다.
  - **요구사항에서 `도메인과 1:1매칭을 이루는 기준 정책을 더 나중에 구상층에서 구현`하는 방향으로 설계**해본다???.
    - 조건을 만족해야 액션하므로, 조건부터 중간추상층에 구현한다?

###### SequenceDiscount(조건 정책) + AmountDiscount

1. Condition 정책 인터페이스의 2개 메서드 중 **중간 추상층에서 미리 구현할 메서드 1개를 선택한다.**

   - 발동조건 검증여부 역할(추상메서드)를 **개별구상체마다 막아놓을 것이다.**

     ![image-20220621235327034](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220621235327034.png)

2. condition policy정책인 `DiscountPolicy`를 구현한 구상체를 **일반class구상화 대신 추상클래스로 구상화하여 중간 추상층을** 만들고, **발동조건 메서드만** 개별 구현하되, **개별구현시 필요한 인자를 추상클래스의 생성자로 받아 final필드에 박아준다.**

   ![cb5d5790-4193-4ebe-9429-7b9979d50a60](https://raw.githubusercontent.com/is3js/screenshots/main/cb5d5790-4193-4ebe-9429-7b9979d50a60.gif)
   ![image-20220622000731229](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622000731229.png)

   - return 문 빠진 스샷

3. **이제 중간추상층에 한번 더  나머지 `액션 메소드 1개만 정의`하는 구상층을 낼 수 있는데, 이 때 `DiscoutPolicy의 자식policy 중 1개인 AMOUNT(Discount)를 구현하고,  그 2번재 정책의 내용은 액션메서드를 개별구현 할 때, 로직에 적용`한다**

   1. 중간추상층 SequenceDisCount(추상클래스)를 상속한 구상체를 만든다.

      - **`추상클래스가 생성자를 새롭게 정의`했다면(for 중간에 미리 개별구현을 위한 인자 받아오기)**
        - **자식의 생성자도 `부모의 생성자 인자를 똑같이 받는 생성자를 최초 재정의`해줘야하며, 받은 인자를 부모의 생성자가 쓸 수 있게`super()`에 넣어준다. 이로 인해 `부모 생성자처리 -> 부모 필드 채우기` 가 `부모에서 이루어진체로 내려오도록 위임`해야한다.**
        - 이후에는 **자식의 생성자에, 자식구현을 위해 필요한 정보를 생성자 인자로 받아올 수 있다. super()의 부모생성자호출에 필요한 것만 먼저  필수로 받아줄 뿐이다.**

      ![0dc514c6-7460-44b4-9444-26efe721e4f5](https://raw.githubusercontent.com/is3js/screenshots/main/0dc514c6-7460-44b4-9444-26efe721e4f5.gif)

   2. **`condition policy`의 추상메서드 중 1개 `액션`역할 의 구현**이 아직 안되어서 넘겨받아 구현해야한다. **여기에 `2번째 정책(마커인터페이스지만)의 개념을 구현과 동시에 첫번째 정책 개별구현 메소드 내부에 개념을 녹인다.`해버리면 된다.**

      - AmountDiscount정책을 적용할 것이므로, 요금계산은 원래요금 - amount만큼 뺀 것을 반환해주면 된다.
        - 값객체는 값을 상태값으로 가지고 있으며 **스스로 연산책임을 가진다.** **인자로 똑같은 값객체가 주어진다면, ** 내부에서 편하게 계산되며, **연산값으로 new 새로운 값객체를 생성해서 반환한다.**
      - **class 내부 개별 구현로직에 필요한 정보들은, 생성자를 통해 받아오게 한다.**

      ![eed99ef4-0a4f-43d5-8b8b-9e696a4127e7](https://raw.githubusercontent.com/is3js/screenshots/main/eed99ef4-0a4f-43d5-8b8b-9e696a4127e7.gif)



###### SequenceDiscount(조건 정책) + PercentDiscount

3. SequenceDiscount(조건 정책)  + AMOUNT(2번째 정책)이외에 **나머지 정책조합을 구현**해보자. -> **SeqeunceDiscount(조건정책) + PercentDiscount**

   1. 조건정책을 **발동조건메서드만 개별구현하는 추상클래스로 구상화**하면서 조건메서드만 중간추상층에서 구현한다.

      - 이미 구현된 SeqeuenceDiscount

        ![image-20220622130711116](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622130711116.png)

   2. **만들어진 조건 정책  추상클래스를 상속**하면서 && **2번째 정책 마커인터페이스**를 구현한 최종 구상체에서 **액션메서드에 2번째 정책을 반영**한다.

      - Percent로 할인되는 2번재 정책을 반영할 것이다.
        - 원래 요금 fee에  .minus를 할 것인데
        - 원래 요금 fee에 percent(double형)만큼 .multi한 가격을 .minus해야한다.

      ![ad8da5aa-c9e5-4270-9242-1301b450751d](https://raw.githubusercontent.com/is3js/screenshots/main/ad8da5aa-c9e5-4270-9242-1301b450751d.gif)





4. **먼저 적용되는 조건 정책을 구상층의 이름에 먼저** 나타나게 수정

   ![image-20220622132257591](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622132257591.png)
   ![image-20220622132324480](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622132324480.png)



5. **실패 돌아보기**

   1. Main에 있는 정책이 적용되는 도메인 Moive입장에서는 특정Discount만 알도록 설계되어있다. 

      ![image-20220622133329658](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622133329658.png)

      - movie입장에서는 **1:1매칭되어야하는 DiscountPolicy가 추상체로 묶였으면 좋겠다**

   2. 하지만, **DiscountCondition 정책을 먼저 적용하다보니**

      1. **발동조건1 Sequence or 발동조건2  ???**를 기준으로 묶이게 된다.
      2. **정책은 다시  Sequence 정책1, Sequnece 정책2 , 3, 4**로 분리되어버린다.
         1. **추상화가 잘못된 상태다.**

   3. **결과적으로 `도메인이 묶이길 원하는 인터페이스부터 중간추상층`으로 추상화하도록 2개의 인터페이스를 조합해야한다.**

      - 메서드 2개를 가진 인터페이스를 **중간추상층 화 해서 메서드 1개 막는 전략**을 썼더니,  **중간추상층화 한 인터페이스의 구상형 기준으로 나머지 인터페이스가 묶이게** 되며되.
      - **중간추상층의 구상형갯수2 x  같이구현할 구상형 갯수 4 = 8개의 모든 경우의수를 다 class형으로 만들어야한다.**



##### Policy -> Condition 순으로 정책 적용하기

- **2개 인터페이스 중 먼저 구현(중간추상층화)**한다 -> **그 인터페이스의 구상형들을 기준으로  최종정책들**이 묶인다.
  
  - **즉, 묶이길 원하는 인터페이스부터 먼저 구현(중간추상층)한다.**
- **`마커 정책의 구현`은 `조건 정책`의 `액션메서드`구현과 밀접하므로, 중간추상층에서는 액션메서드를 `먼저 구현`하여  `최종정책들은 1개의 발동조건 메서드만 구현`하게 한다.**

- **마커인터페이스와 조건정책인터페이스를 동시구현한다.**

  - 그래야 액션메서드를 개별구현하도록 내려온다.

  ​	![image-20220622144545612](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622144545612.png)

  ![image-20220622145356944](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622145356944.png)



###### AMOUNT + Sequence

1. 묶이길 원하는 **정책인** DiscountPolicy의 개별 **마커인터페이스부터** 구현하되, **액션메서드에 같이 구현**하기 위해, 정책조건 인터페이스도 **콤마로 같이 구현**한다
   ![f0f1be1e-56eb-4889-a8df-1fcb3f0a5e16](https://raw.githubusercontent.com/is3js/screenshots/main/f0f1be1e-56eb-4889-a8df-1fcb3f0a5e16.gif)

2. **아직 1개의 인터페이스만 구현한 중간추상층**으로서, **정책조건의 액션메서드에다가 정책(마커) 구상형의 실제 내용을 구현**한다.

   - Policy자체는 이제 추상화 된 것이다. AmountDiscount로 2번째 정책까지 구현한 최종 구상체를 묶을 수 있다. 

   - **액션메서드 1개만 구현하기 위해 abstact class(중간추상층)화 한다**
     - 발동조건 메서드는, 이후 구현될 최종구상체의 Sequnece/Time Discount의 영역으로 남겨둔다.
   - class내부 **개별구현을 위해 필요한 정보들은 생성자에서 조달**한다.

   ![eb75a240-3e0f-4aba-8c26-cd9896023b59](https://raw.githubusercontent.com/is3js/screenshots/main/eb75a240-3e0f-4aba-8c26-cd9896023b59.gif)

   

3. 이제 조건정책의 발동조건 메서드 1개를 구현하는 최종 구상체를 만들어보자.

   - 2개의 발동조건(Sequence/Time) 중 Sequence를 구현해본다.

   ![159c5b32-9c13-48c1-8824-ba8a168ffc11](https://raw.githubusercontent.com/is3js/screenshots/main/159c5b32-9c13-48c1-8824-ba8a168ffc11.gif)



###### AMOUNT + Time

4. 나머지 정책조건 중 1개인 Time으로도 구현해본다.

   ![40a5116d-7d86-4318-8316-354791822eb1](https://raw.githubusercontent.com/is3js/screenshots/main/40a5116d-7d86-4318-8316-354791822eb1.gif)





###### PERCENT + Sequence

5. 마커 정책 PERCENT를 구현 & 액션메서드 구현를 위해 DiscountCondition을 구현한 중간추상층(추상클래스)를 만들고, 

   - 다시 구상화하여 남은 발동조건메서드를 구현하며  SequnecePercenDiscount를 만든다.

   ![0fd0d766-9bf3-436d-9e28-7b3c9c981925](https://raw.githubusercontent.com/is3js/screenshots/main/0fd0d766-9bf3-436d-9e28-7b3c9c981925.gif)

   

###### PERCENT + Time

6. 마찬가지로 순서대로 구현

   ![8552b02f-c7e1-41a0-bbe1-c8e9c557ea2b](https://raw.githubusercontent.com/is3js/screenshots/main/8552b02f-c7e1-41a0-bbe1-c8e9c557ea2b.gif)





######  돌아보기

- 중간추상층은 **implments에 여러개 인터페이스를 구현** 할 수 있다.

  - implements `마커정책`, `조건정책 with 액션메서드+발동조건메서드`
  - **여러개 구현안됬으면, `중간추상층 1개 미리 메서드 구현`을 위해 무조건 조건 정책부터 구현했어야 한다.**

- 최종 구상층은 **extends에 1개의 부모만 상속**할 수 있다.

  - 중간추상층 1개만 필수로 상속할 수밖에 없다.
  - **만약, 구상층들간에 공통로직이 생겨도 `코드중복제거를 위한 추상클래스(템플릿메소드패턴) 추가`가 불가능하다**
  - 필수 추상클래스를 상속한 자식이라면, **`코드 중복`하려면 extends가 아닌 implments를 위해 전략패턴을 적용한 인터페이스로 해야한다**
    - 외부에 발생할 **별도의 전략객체들을 밖에서 받아와야한다**

- **왜 정책을 먼저 구현했냐?**

  - **도메인인 Movie에** condition이 아닌 **`policy구상형을 1:1매칭이 필수`였기 때문이다.**

    - **정책은 2가지가 반영되어야하니, 2번째 정책이 반영된 최종구상체들이 `policy를 추상층`으로 가져서 묶어야만 한다**

    ![image-20220622150349229](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622150349229.png)







##### Movie (제한된 정책 객체들만 받기 with 제네릭)

![image-20220203143256759](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203143256759.png)



1. Movie는 제약조건이 있다. **2종류의 할인정책(Amount, Percent) 중에 1가지와 1:1 매칭된 체로  생성되어야한다.**
   ![image-20220622151852444](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622151852444.png)
   - 하지만, 정책적용은 policy와 condition을 조합해서 class 생성하고, 그 객체를 받아들여서 사용한다.
   - condition 종류는 상관없이 policy기준으로 붙여야한다?
     1. policy를 정책조합의 중간추상층으로 조합하여, 정책들을 생성한다.
     2. movie는 특정형이 아니라 중간추상층 policy 2종류를 알아야한다.
        - **제네릭은 특정형을 알게 하지만, 최상위 추상층을 T의 제약으로 주, 특정추상층을 알게할 수 있다.**
          - 제네릭은 객체생성시 특정형을 알게하고, **class를 T로 정의**한다.
          - 제네릭을 익클-분신술구현시 특정형을 알게 구현한다면, 추상클래스를 T로 정의한다.
            - 익클말고, 추상클래스의 구상class도 특정형을 알게 구현할 수 있을 것임.
          - **제네릭을 객체생성시 추상형을 알게 하고, class를 T로 정의한 뒤, `메서드(생성자) 파라미터도 T로 정의`해서 쓰면, `객체 생성시 메서드(생성자) 인자에 구상형들을 받아들일 수` 있다.**
        - movie마다 중간추상층(추상체)을 알게끔 만들면, 그 구상체들이 건네오게 알게 만들 수 있다.
          - 구상체들이 들어와도, 공통메서드로 처리되는 구상체들을 가지고 있다면,  if나 istanceof가 없이 바로 처리될 수 잇을 것이다.



2. **Movie를 요구사항에 따라 정리하며 만들어본다.**

   1. **movie 생성시, `정책의 최종구상형객체를  생성자 인자`로 받아들여야한다.**
      ![image-20220622154125462](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622154125462.png)

   2. **movie 정의시**, **`생성자 파라미터 정의부에는 여러구상체들이 올 수 있도록 추상체가`가 와야한다.**

      - **추상체를 어느레벨로 정해야할지가 문제**다

        ![image-20220622153803953](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622153803953.png)

        - 중간추상층 AmountDiscount를 주면, PercentDiscount 구상체들이 못온다.
        - 중간추상층은 **상위추상층으로  Policy와 Condtion 2개를 다 구현**했기 때문에 1개만 선택을 못하는 것도 문제다.

   3. **`제약걸린 제네릭 T`을 통해 `객체 생성시에만 특정형을 알고 쓰세요`할 수 있다.**

      - **`제약조건upperbound를 상위 추상층`으로 주면, 어떤 형이든 아는 객체가 생성될 것이다.**

        - 2개 인터페이스를 2개의 상위추상층으로 보는 방법은 `&`를 써서 연결하면 된다.

        ![image-20220622154457138](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622154457138.png)

   4. **상위추상층을 upperbound로 하면 -> 객체 생성시 `특정 중간추상형을 알게 만들 수 있고` -> 구상층의 추상체로서  `생성자 인자에는 특정 중간추상형의 최종구상체들`을 넣을 수 있게 된다.**

      - **제네릭의 사용 = 특정형을 안다 = `파라미터나 변수, returntype의 T자리에 특정형으로 정의된 상태`의 객체가 생성된다.**

      ![image-20220622155258816](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622155258816.png)

      - **제네릭의에 넣은 `특정 중간추상형(파라미터,변수,returntype)`과 `사용되는 인자`가 매칭이 안되면 에러가 날 것이다.**

        ![image-20220622155514296](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622155514296.png)

   5. **이제 외부에서는 제네릭에 `요구사항에 따른  특정 추상형을 아는 Movie`객체들을 만든다.** 

      - 영상으로 보기

      ![c78f265d-f4b9-4c26-aa8b-565679c86827](https://raw.githubusercontent.com/is3js/screenshots/main/c78f265d-f4b9-4c26-aa8b-565679c86827.gif)

   6. 응용 생각해보기

      - **제네릭의 upperbound는 `if 분기를 처리하는 각 전략객체(구상체)들의 추상체(들)`가 대상이다.**
      - **if분기를 구성하는 조건이 2개다? -> 인터페이스 2개로 추출 -> 2개 인터페이스를  implements하는 추상클래스(중간추상층) 1개 생성** -> **제네릭으로 변경하여 특정중간추상층을 사용시, 상위추상층으로 uppperbound에 `A & B`**
        - 각 if를 구성하는 조건이 3개다? -> 인페 3개 추출(개별 전략객체생성) -> 3개를 imp하는 추상클래스(중간추상층) 1개 생성 -> 제네릭으로 변경사용시 upperbound에 상위추상층으로 `A & B & C`를 놓고 사용
      - if는 추상체 -> if 갯수만큼 구상체class  정의후, 외부에서 객체 처리기를 만들어서 주입
        - 그 객체class -> 객체 생성을 `형`으로 대신해주는 것이 `제네릭`







##### Movie 2(구성과 정책 적용)

1. **Movie가 제네릭을 쓰고 있는 이유는?**

   - 정책 구상체 객체를 받아가서 사용하면 안되나?
   - **정책 구상체가 2개 인터페이스 조합**인데, 요구사항에 **`movie마다 1개 인터페이스(policy)에 대한 1종류씩만 적용`하라는 제약이 있어서 `그 하위 구상체만하라는 제약`이 걸림.**
     1. 2개 인터페이스를 조합하되, 1종류 제약이 걸린 인터페이스를 **종류마다 중간추상층으로 구상화**한다.
     2. 제네릭이 upperbound는 2개인터페이스를 다 걸어놓되, **movie생성시, 특정 중간추상층의 하위 구상체들만 알도록 -> 인자로 받도록 제약을 준다.**

   - **제네릭을 사용할 때, (중간)추상체를 넣어서 추상체를 알도록 할 수도 있다!**

2. 제한된 1개종류 policy의 정책들도 **여러개가 들어올 수 있다고 가정하고, 1개 인자 -> `...`가변배열 인**자로 받아준다.
   ![b7b1a93f-b575-4b4f-80a4-0428078ed7bf](https://raw.githubusercontent.com/is3js/screenshots/main/b7b1a93f-b575-4b4f-80a4-0428078ed7bf.gif)

3. 여러개가 들어와도, **중복허용 없이 종류별 1개씩만 있으면 된다면, `생성자 내부에서 빈 hashSet필드에  배열-> list -> addAll기능`으로 `빈 Set<T> = new HashSet<>(); 필드`에 넣어준다.**
   - **영화별로, `적용된 할인정책을 1개씩만 소유하고 있으면, 꺼내서 돌면서 적용만` 하면 된다.**
   - **list는 index가 관여한다 -> 결국엔 값으로 식별된다.**
     - **객체지향에서는 list에서 필수인 index인 값context보다  `객체context = 식별자로 식별`해야한다**
   - **set은 index없이 `들어간 객체`로만 식별할 수 있다.**



4. movie는 정책을 적용할 수 있는 `할인정책 객체`와  `정책적용 대상`인 movie 1개의 요금 `fee`를 가지고 있기 때문에, **정보전문가패턴**에 의해서 정책을 적용할 수 있다.

   - my) **정책적용대상(fee)를 가진 곳에 정책객체를 생성시 넣어주는 것 같다.**

   - 정책의 액션메서드인 calculateFee가 똑같이 Movie에서도 동일한 네임으로 적용해보자.

   - **가지고 있는 할인정책은 종류별로 여러개이지만, `외부에서 받아온 해당영화의 상영정보(screening)`에 의해, 맞는 것 1개만 isSatisfiedBy 된다고 가정하고, ealry return시킨다.**

     - 순서를 고려하지 않는 set이기 때문에, 돌면서 해당하는 정책 객체는 1개라고 가정한다.

   - **돈계산시, 액션메서드가 Money값객체를 반환하므로, 메서드체이닝이 가능해진다.**

     - 할인된 요금에다가 `audienceCount`만큼 곱한 **해당 영화 상영 전체 금액**을 구한다? 
       - movie는 할인된 1개 영화가격이 아니라, **할인된 가격 x 전체 인원수의 총 매출을 계산하는 것으로 가정한다**

     ![b5a358ee-0463-4e4f-9c55-27e2a08c1774](https://raw.githubusercontent.com/is3js/screenshots/main/b5a358ee-0463-4e4f-9c55-27e2a08c1774.gif)

     - **그럼, 할인요금 적용 안될 때도, 인원수만큼 곱한 금액을 반환한다.**

       ![83bd9005-c9dc-44cc-9cc8-20ff947f5be1](https://raw.githubusercontent.com/is3js/screenshots/main/83bd9005-c9dc-44cc-9cc8-20ff947f5be1.gif)

     

   



##### Money(final 필드 1개의 데이터 객체 - 1개의 final 필드가 연산기능 제공으로 변할시 -> 새 객체로 반환하는 불변의 값객체)

![image-20220203154025289](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203154025289.png)

- 값에 class라는 껍데기를 씌우고, 상태값으로 값을 가진다.
- 상태값인 값이 자신의 메서드에 의해 연산되며, **return시 껍데기를 씌운 `새로운 객체로 를 생성해서 반환`**한다.
  - 연산 전 상태값+껍데기는 불변성이 유지된다.
    - **모든 필드는 final이다.**
    - 값객체에서 **상태값의 값 필드는 private으로 외부노출 안된다.**
  - 자신의 기능의 결과가 자기자신type이라면, **자신의 메서드들을 체이닝할 수 있다.**
- 재화 등 0이상이어야하는 상태값을 가진다면 **return 하한선을 확인한다.**

- 자신에 대한 **연산 기능(메서드)는 인자로 똑같은 값객체를 주는 것이 가장 좋**으나 값이 들어오는 경우도 있는데

  - 이는 **다른 데서 객체 대신 값을 써서 스노우볼이 굴러서 오는 것**

  - 어떤 객체context라도, 1개의 원시형(값)을 썼다면, 전역적으로 값context를 만든다.

    - 되도록이면 인자든 뭐든 싹다 객체를 써야한다.

    ![image-20220622201057708](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622201057708.png)
    ![image-20220622201123945](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622201123945.png)

  - **값들을 객체로 썼다면? 어느 순간 `abstract numbers`라는 추상화가 가능할지도 모른다.**

    - **숫자도 객체로 만들어야 추상화될 가능성**이 있다.
    - 객체로 만들어야 -> 구상체들의 2개이상의 책임 -> 추상체의 1개의 역할 -> 한번에 처리된다.
    - primitive가 온 순간 -> **확장 = 추상화**는 없어진다. 반드시 객체로 메세지를 주고 받아야한다

- 연산 뿐만 아니라 **비교까지 모두 값객체의 메서드로 하자.**

  ![image-20220622201430837](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622201430837.png)



- 장점 정리
  1. 번거로워도 값을 private + final 필드로 **데이터 은닉**화 -> **외부에서는 값을 제공된 기능으로만 변경할 수** 있다.
  2. 스스로 연산후 **새 객체로 return -> 기존의 상태값에 간섭못함 -> `동시성 문제가 없다`**
     - **확정되어있는 도메인이나 연산 -> 값객체로 바꿔서 동시성 문제를 없애자.**



- **값 객체를 필드로 가진 객체들은**

  - 그 필드가 자기 기능으로 변화하면, 새 객체를 반환하므로 **값객체 필드는 재할당 할 수 밖에 없어서 final이 불가능하다.**

    - 아래는Theater가 가진 Money amount 필드가 재할당 되는 것

      ![image-20220203202450918](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203202450918.png)

  - **`재할당되어 변하는 (no final)필드`(값객체 필드 포함)는 반드시 private으로 외부노출을 막아서, 동시성 문제를 없앤다.**

    - 포인터의 포인터 중, 포인터 자체가 바껴서.. 못찾아갈 수 있다.
    - **재할당되는 필드는 외부에서 아예 사용 못하게 막**아야한다.



##### Reservation(여러 final 필드의 순수 데이터객체 - 연산없는 값 객체)

![image-20220203162318753](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203162318753.png)

- Ticket을 대신하는 재료객체이다.
  - data-oriented class라고 볼 수 있다.
  - **정보의 묶음인 `재료객체, 데이터객체`는 `null 대신 public static final `로 상수로 선언한 `NULL객체`를 쓰자.** -> **데이터객체는 `생성/조회 전 검증 실패시`에 null대신 NULL객체를 반환해준다.**
    - **값객체 class내부에 선언**한다.
    - **public static** - 돌려쓴다.
    - **final** - 불변이다. 돌려쓰는 + 불변 -> 상수
    - **데이터객체는 반환되는 경우가 많아서, 검증 실패시 등에서 NULL객체를 만들어주는데** EMPTY or NONE이라는 변수명을 사용한다.
      - **`데이터객체Class.EMPTY` 혹은 `데이터객체Class.NONE`으로  static한 상수로 사용됨**
- **재료객체, 데이터객체는 모두 `순수한 데이터정보`로서  `final`필드를 사용한다.**
  1. 극장
  2. 영화
  3. 상영정보
  4. 예매한 인원수(count)



- 데이터 객체도 **값객체**다.

  - **`연산을 제공안하는` 값객체 = `재료객체, 데이터객체` with final**

  - **값객체는 NULL객체를 고려한다.**



- **`전체가 final필드`를 가진다면, 스레드 안전이다.**



##### Screening(final필드 + 외부조건에 따라 변하는 필드의 데이터객체 - 변하는 필드만 final떼고 trigger+action기능을 제공하여 새객체 반환없는 값객체)

![image-20220203164740902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203164740902.png)

- **대부분이 final필드로 박아지면, 데이터객체로서 값객체라 부를 수 있다.**

  - final필드라도, **외부 할인정책의 개별구현시 에서 인자로 받아 내부 sequence와 whenScreened를 getter없이 조회할 수 있게 `public final`이다.**

    ![image-20220622204229616](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622204229616.png)

- 여기서 `seat` 필드는 

  - **final이 아니다 -> 나중에 변한다.** by setter(받기기능) or 연산기능 등
  - **private이다. ->  변하더라도 `외부에서 제공된 기능을 통해서만 변한`다.**
    - 예매하는 사람이 예매를 했을 경우, **나 자신을 검증하고, 그만큼 가용좌석수가 까여야하기 때문**이다.
    - 예매한 좌석수: count(외부에서 들어옴)
    - 가용 좌석수: seat(검증되면, count만큼 까야됨)
    - **외부의 인자에 의해 검증시 사용되고(trigger)되고, 차감(action)되는 필드다.**
      - trigger : count이상 seat를 현재 가지고 있니?
      - action: trigger를 이용해 만족할경우, count만큼 좌석수를 seat에서 빼라.

- **seat가 변하는 것은 `외부(count-예매좌석수)`에 의해 변하는(`예매권 발행시 차감되어야하는`) 필드다.**

  - **seat의 변화(차감)는 `외부에서 예매를 발행(in theater)할 때` 작동하며, 현재 인스턴스에 반영되어야한다(차후 setter나 다른 기능으로) -> final이면 안된다.**

    - **trigger만족시, 예매권 발행전, 자리수 차감**

  - **`외부 인자`라는 `조건에 따라 변하는 필드`는, 그 내부에서 1) trigger(변화조건) + 2) action(변화) 메서드를 가져서 `외부에 제공(외부에서 예매발행시 작동할 것임)`한다.**

    1. `hasSeat(int count)` : **외부조건에 따라 변화조건 검증 기능**
    2. `reserveSeat(int count)` : **외부조건에 따라 변화** + **내부에서 trigger사용함**
       - 액션메서드는 항상 **`내부에서 if trigger메서드를 사용해 검증후 액션로직을 구현`한다.**
         - 내부에서 사용하지 못하는 경우(trigger가 나중에 작성될 경우)는 **외부에서** trigger -> action순으로 사용하면 된다.
           - ex> 정책객체는 적용객체 내부로 들어가서 외부에서 사용됨.
       - **`trigger 조건을 만족하지 못할경우, thr RuntimeException`을 내면 된다.**

    ![2c21447a-2b16-441e-ae45-d16b9fdcf5ea](https://raw.githubusercontent.com/is3js/screenshots/main/2c21447a-2b16-441e-ae45-d16b9fdcf5ea.gif)





##### Theater

![image-20220203165805132](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203165805132.png)

![image-20220203170438126](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203170438126.png)

- 일단 자본금이 값객체Money로 대체되었다.

- theater의 저장정보 추가

  - **모든 정보들이 theater`생성후 저장`되며 `여러개(Collection)으로 저장될 예정이면 빈컬렉션을 default값으로 초기화해서 저장`한다**
  - 기존에는 ticketOffices들만 저장
    - ticket을 받기기능으로 가져가던 ticketOffice들 목록저장을 `List`에서 하다가 **객체지향으로서 index = 값이 없는 `Set`으로 저장한다.**
  - 상영할 movie와 movie에 딸린 상영정보들을 Map으로 저장
    - movie만 저장한다면 set으로 했을 것인데
    - movie별 screening도 저장해야하므로 **상위1 당 하위도메인들N을 Map에 한번에 저장한다.**
      - 이 때, **하위도메인**상영정보**들**을 map의 **value자리에** List가 아닌 **Set에 중복을 허용없이 여러개를 저장**한다.
    - `Set<Screening>`은 map의 value값인데 없을 수도 있다. **value가 없을 수도 있는 key값을, `key값만 저장가능`하게 하기 위해 `value의 default NULL값`도 값객체처럼 static final로 만들어놓는다.**

  ![image-20220622213906070](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622213906070.png)

- 상위도메인movie를 key로 받기기능

  - **받기기능에 중복 확인시 false로 알려주기 위해 return이 boolean형이다.**
  - key등록시 value인 `Set<Screening>`은 빈 set로 넣어서 초기화한다.

  ![image-20220622214601165](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622214601165.png)

- 하위도메인screening을 value로 받기기능

  - **하위도메인의 CRUD는 항상 상위도메인과 같이 인자로 받는다.**
  - 상위도메인이 key로 등록안됬다면, false로 반환한다
  - 등록된 상위도메인key가 존재하는 상황이라면, **이미 default로 `빈 set을 value로 생성된 상태` 만들어놨기 때문에 `map.get(상위도메인)`으로 `꺼낸 상태에서 add만` 해주면 된다.**
    - put이 아니다. get한 뒤, 빈 셋에 add다.

  ![image-20220622214952589](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622214952589.png)

  

- client가 정해준 rate를 가지고,  ticketOffice와 계약 후, 성공시 저장목록에 추가

  - step1에서 기존에는, 일방적으로 theater만  -> ticketoffice을 알고 목록을 list에 저장만 했었다.
    - 내부생성되는 티켓도, 와서 받기기능으로 받아가기만 했다.
  - **지금은 쌍방 계약이라 서로 알아야한다.** 계약조건에 해당하는 외부인자 rate도, 계약할 ticketOffice와 같이 받는다.
    - ticketOffice도, this로 현재 theater 인스턴스를, **커미션율을 같이** 들고가서, 계약을 저장한다.
      - 차후 나오지만, 여러극장과 계약할 수 있기 때문에, theater별 rate를 따로 필드로 저장한다.
      - ticketOffice가 이미 계약한 theater일 경우 계약메서드 returndmf false를 반환해줘서 계약이 무산된다.
      - 계약 성공시 계약ticketoffices의 set에 추가해주고, 성공여부를 client에 알려준다.
        - **성공/실패가 있는 트랜잭션 메서드의 경우, boolean형으로 작성하자!**

  ![image-20220622220958815](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622220958815.png)



- 계약을 취소할 수 있다.

  - 이것도 ticketOffice가 허락을 t/f로 알려줘야 진행된다.
  - **my) 계약도 거래처럼, 계약을 해줄지말지 결정하는 사람(ticketOffice)가 갑이고, 을에서 인자로 받아서, 내부에선 this와 함께 갑의 기능에 계약 가능한지 물어본다.**
  - 저장목록에서 삭제와 마찬가지이므로, 존재여부부터 검사한다. + 갑이 계약취소를 해줘야한다.
    - 취소가능할 시, set에서 remove로 객체를 제거한다.

  ![image-20220622221703873](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622221703873.png)



- **딸린 하위도메인(Screening) 전체 조회**

  - 상위도메인 객체(fk대용) 로 전체 조회한다.
  - 상위도메인 존재여부 -> 해당도메인 존재여부를 확인부터 한다.
    - 없으면 빈값을 보내야하며, 자주 반환되니 매번 생성하지말고, **컬렉션의 NULL객체로서 EMTPY의** 상수로 만들어놓는다.

  ![image-20220622225228869](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622225228869.png)





- **유효한 screening인지 -> 상위도메인 존재 및 상-하위도메인 연결확인**

  - **아래, 예매권 검증에서 사용될 예정이다.**
  - 예매권의 영화, 상영정보가 제대로 된 정보인지.

  ![image-20220622225925643](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622225925643.png)





- **예매권 발행주체로서, 고객이 산 예매권을 검증하는 책임도 가진다.**

  - 예매권의 정보가, 발행한 theater가 맞는지부터 시작해서 내 정보들로 비교해서 반환한다.
  - 예약좌석수count는 theater가 아니라 client에서 지정해주기 때문에, 외부에서 사용했던 것을 그대로 받아온다.
  - 내부에 존재하는 상-하위도메인 정보는 위에서 정의한 메서드로 검증한다.

  ![image-20220622233040464](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220622233040464.png)





- **Customer와 거래는 TicketSeller가 하는 데, 예매(reserve)의 책임은 Seller -> Office -> Theater까지 요청이 들어온다.**

  ![image-20220623002927648](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623002927648.png)

  - 사는 놈 Customer가 스스로 예매하지만, 갑인 Seller의 예매(**거래**)기능 이용

    ![image-20220623003127672](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623003127672.png)

  - TicketSeller-> TicketOffice로 예매권 **생성** 요청
    ![image-20220623003209741](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623003209741.png)

  - TicketOffice는 -> Theater에게 예매 **생성** 요청

    ![image-20220623003551966](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623003551966.png)

  - Theater는 **발행정보를 통한 검증을 거쳐** 예매 **생성**하여 office -> seller -> customer에게 반환함

    ![image-20220623003852718](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623003852718.png)

- **예매발행 검증(trigger포함) 통과 <-> 예매권 발행 사이에서  `특정 이용가능좌석수가 차감 action`이 Screening 객체에 필드에 적용되어야한다.**

  - screening 객체 속에 이용가능자석수가 field로 담겨있으며, 거기에 trigger와 action메서드를 제공하니, 이용해서 차감시켜야한다.
  - 외부인자에 의해 액션이 들어가는 객체screening은 trigger와 action을 제공한다.
    - **trigger는 `if (! trigger성공) -> early return`형태로 사용하고, 이 조건문 아래에서는 trigger성공 상황으로서 action한다.**

  ![image-20220623004852900](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623004852900.png)



- 데이터객체의 발행은, 새객체를 생성해서 반환해준다.

  ![image-20220623005132425](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623005132425.png)







##### TicketOffice

![image-20220203205814111](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203205814111.png)

![image-20220203210611290](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203210611290.png)

- 자본금을 값객체로 가지고 태어난다.

- **Theater와 계약을 하며, 계약취소 기능도 있다.**

  - **트랜잭션 관계라서 서로 안다.**

    - 여기선 office를 거래의 갑으로 보고 **먼저 처리하고, 성공여부를 을(theater)에게 boolean 메서드로 t/f여부를 전달해줘야한다.**
      - 을(theater)는 갑의 성공여부를 확인한 뒤, 계약/계약취소를 한다.

  - 계약은, 계약조건(rate)와 함께, theater를 빈 값으로 초기화된 map field에 add(put)하는 것이다.

    - 1theater는 여러office와 거래하여, office목록을 set에 저장하지만,
      ![image-20220623121224734](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623121224734.png)
    - 1office도 여러theater와 거래 가능하여, **theater목록 + 거래 theater에 딸린 거래조건rate을 map**으로 저장한다.

  - 거래란, **서로의 컬렉션 field에 거래대상을 저장해놓는 것**이며, **거래조건을 제시하는 office가 갑으로서, 을theater에서는 office목록만 vs 갑office는 theater목록을 + 거래조건rate과 함께 저장한다.**

    ![image-20220623122149211](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623122149211.png)
    ![image-20220623122317916](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623122317916.png)

  - 을(theater)는 **갑의 계약/계약취소 성공시에만 진행된다. `if  !성공`으로 실패시, 을도 실패를 반환한다.**

    ![image-20220623122420396](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623122420396.png)

    

- 기존에는 **Theater에게 데이터객체(Ticket)을 받기기능(빈 컬렉션필드에 add)으로 가져와 창고처럼 list필드에 모으고** 있다가 **Seller가 소속기관으로 생각하고 필드로 받아와(setter)  -> seller 내부상황에 office에서 office의 주기기능(getter)을 이용해 받아왔지만 `창고 역할은 사라졌다`** 

  - 기존

    ![image-20220623120245528](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623120245528.png)


    ![image-20220623120613128](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623120613128.png)

  - **지금은, 발행주체인 Theater에게 받아와 -> Seller에게 반환해주는 기능을 제공해, `검증 후 생성된 것을 받아 전달`만 역할만 한다.**

    - 창고역할은 사라짐.

    ![image-20220623120807813](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623120807813.png)



- office는 창고역할은 사라지고, **seller가 예매발행 요청**을 **theater가 아닌 연결된 office에게 먼저 발행 요청**한다.
  ![image-20220203210611290](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203210611290.png)

  - **내부에서는 궁극적으로 실제 발행을 해주는 theater에게 발행요청을 또 보낸다.**

  - 하지만, **seller(판매자)에게 발행 요청을 받고나서, theater(생산자)에게 발행 요청하기 전에 office가 하는 일이 있다.**

    - **왜냐면, `customer가 사는 예매권`에 포함된 정보들이, 거래하는 seller의 소속기관인 office와 `계약된 theater가 발행가능한지`부터 시작해서, `사려고 선택한 정보들이 유효한지`를 확인해줘야한다**
    - 즉, office-seller의 **생산자(theater)로부터 판매자(seller)까지 `중개자 역할`을 하므로, `중개자와 계약된 생산자가 발행가능한, 데이터객체를 요청하는지`를 검증해줘야한다.**

  - 요약하면 **seller(판매자)가 생성요청하는 데이터객체(Reservation)의 정보**가

    1. 나와 계약된 생산자로 요청하는지 by theater 인자
    2. 중개자와 계약된 생산자가 만들 수 있는 것인지(유효한 정보인지) by movie, screening 인자
    3. 생산 가능한지(이용자석수 남아있는지) by screening 인자 
       - **중개자로서 검증후에, 생산자에게 생산 요청한다.**

    ![image-20220623125516698](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623125516698.png)

  - **또한, 중개자로서, `생산자theater가 올바르게  데이터객체(예매권)을 생성`했으면, `seller에게 넘겨주기 전에, 생산자와 계약한 거래조건으로 나의 commission을 챙기고, 나머지를 생산자에게 준다.`**

    - 중개자는 **판매금 계산 by 정책적용가능한 movie객체**

    - 중개자는 **거래조건대로 커미션 계산**
    - **커미션은 중개자의 자본금에 plus**
    - **판매금 - 커미션을, 생산자의 자본금에 plus**
      - 후에 seller에게 예매권을 넘겨준다.

    ![image-20220623125701706](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623125701706.png)

  



##### TicketSeller

![image-20220203211346707](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203211346707.png)

- Customer와의 **거래에 대한 갑**으로서
  - **판매할 물건을 NULL객체로 만들어놓고(변수재활용)**
  - **물건 가격을 계산한다**
    - **movie**가 정보전문가패턴으로서, 정책도 가지면서 태어나, **계산의 책임이 있었다.**
  - 상황이 **사는놈(Customer)가 돈을 가지고 있는지 검증**하여
    - **사는놈이 돈이 있으면, 중개자 office에게 예매권을 받아온다.**
      - 외부에서 받아온 객체는 **NULL객체인지 검사를 꼭 한다.**
        - **받아온 물건이 제대로 되었다면, 사는 놈(Customer)에게 돈을 차감한다.**
    - **if 돈 검증 -> if 물건 확인**의 과정에서 **애초에 돈부터 없으면**, NULL객체 그대로 반환하게 한다.
      - **early return이 아니라, 상황마다 return해야할 객체 값이 재할당 될 경우, 미리 제일 쉬운 상황인 NULL객체로 만들어놓는 것**

- **기존** Ticket을 팔 때도 마찬가지 로직이다.

  - **seller는 돈계산을 하지않고, 소속기관이자 중개자인 ticketoffice가 커미션 + 생산자에게 커미션 뺀 금액을 챙긴다.**

  ![image-20220623133845692](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623133845692.png)

- 현재는 Ticket대신 Reservation으로 바꼈다.
  ![image-20220623142554754](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623142554754.png)

  1. **거래는 `중첩if를 만족할때만 성공(특수)` & `(그외 대부분의 코드분기) 중첩된 if의 else마다 매번 실패값을 return`**해야하므로 **`애초에 실패값인 null객체를 반환 변수에 할당`해놓고 -> `<중첩if성공시에만 바뀌는 else없는 + earlyReturn없는 코드>를 짠다.`**한다.

     - **여러 조건을 통과(중첩if)해야 성공이고, 그외 실패(NULL객체반환)일 경우**

       - 중첩if의 else마다 발생할 **실패값를 미리 변수에 담아두고**
       - 중첩if의 성공만 **변수에 성공값 재할당**
         - **else는 코드를 안짜고 무시하면 ->  그대로 default 실패값**
       - 마지막에 **변수만 반환**하면
         - **else 실패 코드의 중복없이 성공만 고려하는 코드가 된다.** 

     - 만약, 중첩if의 else마다 실패코드를 작성한다면?

       ![image-20220623141344853](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623141344853.png)

       - **return 실패값이 if의 else마다 반복**된다.

     - 만약, 중첩if시 성공을 드모르간 법칙을 적용한 ealry return으로 짠다면?

       ![image-20220623141500910](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623141500910.png)

       - **not A시, not B시 `실패값을 return`하는 것이 `중복` 것은 마찬가지다.**

  2. 물건의 가격을 받아오고

  3. if 사는 놈이 그만큼 돈이 있다면

     1. 그때서야 외부에서 물건(Ticket)을 받아온다.

        - **돈 있는 것을 확인한 뒤에야, 중개자 office가 돈 계산하며 theater에게 만들라 시킨다.**

        - 바깥의  Ticket 변수에 채운다.

     2. if 물건이 제대로 되었으면(not null객체)

        - **외부 물건을 가져왔는데, 내부 사정에 의해(이용좌석수 제한 등) 생산이 안될 수 도 있다. -> 생산 및 내부 돈계산이 안됬을 것이니, 사는 놈의 돈 차감(피해) 전에 처리된다.**

        1. 사는놈의 돈을 차감한다
           - 가져온 물건이 제대로 되었을 때만, 돈을 차감함.

  4. 3에서 if 2개(돈있냐/물건제대로됬냐)를 다 통과했다면, not null ticket   vs  if 통과 못했다면 default null티켓을 받는다.

  

- my) else없는 중첩if문 -> **미리 else에 해당하는 코드가 위에 세팅되어있어야한다.**

- **`물건의 값`은 ticketOffice(중개자)가 물건 떼올 때 미리 커미션 -> 생산자(theater)는 커미션 뺀 금액을 함께 챙기고, `seller는 고객의 돈을 차감`만 시킨다.**

  ![image-20220623143046485](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623143046485.png)



##### Customer

![image-20220203212017919](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203212017919.png)

- 자본금을 값객체로 가지고 태어난다.

- 사서 받아올 물건을 저장할텐데, **태어날 때부터 없는 물건으로서, 거래후 void setter처럼 받아오니, 필드를 null대신 NULL객체로 초기화 해놓는다.**

  ![image-20220623163348741](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623163348741.png)

- 사는놈은 을로서, 갑인 파는놈에게 거래를 요청하길, **스스로 기능을 호출하되, `인자로 갑(파는놈)` + `사고 싶은 물건의 정보`을 데리고 와서 갑의 기능을 이용하여 `this`로 자신을 보낸다.**

  - **my)을인데, 자기가 안가고 스스로 기능 호출 해서`갑을 데리고 오는 이유`?**
    - **갑의 거래기능을 이용해 데이터객체를 받아서 저장해야하므로 시작을 먼저 자기가 스스로  void(setter)로 호출한다.**
  - seller, office, theater의 .reserve()와는 다르게, 누구에게 데이터객체를 반환해주는게 아니라, **받아서 자기가 가져야하니(필드에 박아야하니) void로 reserve()를 정의**한다.

  ![image-20220623163902430](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623163902430.png)



- 상태값으로 not final 값객체를 가졌다면, **외부에서 값객체를 변화시킬 api를 제공한다.**

  - 을로서, 파는놈에게 돈 가졌는지 검증 당한다.

    - **값객체끼리의 비교는 내부 기능으로 정의해서 처리한다**

    ![image-20220623164113484](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623164113484.png)

    ![image-20220623164235096](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623164235096.png)

  - 을로서, 돈 차감을 파는 놈에게 맡긴다.

    ![image-20220623164157721](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623164157721.png)





##### Main 다시보기

- Movie가 중간추상층을 특정형으로 알도로 제네릭을 사용

  - 그 특정형이 T대신 생성자/메서드 인자, 변수형으로 사용되었을 것이니 -> 추상층의 구상층들을 생성자의 인자로 주입 가능해졌다.

    ![image-20220623164753954](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623164753954.png)

  - AmoutDiscount냐 vs TimeDiscount냐의 if분기를 제네릭으로 제거한 것으로 볼 수 있다.

    - **2개의 정책이 적용된 최종 정책객체를 DI주입만 해주면 되는 것이다.**
      - 현재 사용된 정책: sequence가 1일 때, 1000원을 깍아주는 SequenceAmount정책

- Theater에 movie를 add한다.

  ![image-20220623165215363](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623165215363.png)

  - setter는 1개의 데이터를 필드에 바로 박도록 받기기능
  - add는 1개or여러개의 데이터를 **`빈 컬렉션`으로 초화된 필드**에 추가하도록 받기기능

- Theater에 screening을 add한다.

  ![image-20220623165249915](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623165249915.png)

  - screening은 movie에 딸린 하위도메인들이다.
    - fk로서 movie객체도 같이 넣어준다.
  - **상-하위도메인들을 연결하면서 받기기능은 내부 `빈 Map필드`에 put하는 것이다.**

- Theater는 ticketoffice와 계약한다.
  ![image-20220623165512630](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623165512630.png)

  - theater내부에 저장해야하므로 인자로 받아서 계약한다.
    - office도 계약한 theater을 내부 저장하기 위해, this로 받는 과정이 내부에 있다.

- TicketSeller는 ticketoffice를 소속기능으로서 받아서 내부에 저장한다.
  ![image-20220623165602104](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623165602104.png)

  - 계약관계가 아니라서 일방적으로 알아서 박기만 한다.?
  - seller 내부에 저장해야해서 seller가 office를 인자로 받아온다.

- customer가 seller에게 거래를 요청한다.

  - theater가 상영하는 것들 여러 상영정보중 첫번재 것을 선택했다고 가정하기 위해 for + break;
  - 을이지만, **seller에게 최종적으로 받은 데이터객체를 받아 저장해야하기 때문에 받기기능을 이용하기 위해, seller를 인자로 알고 데려온다**.

  ![image-20220623165759368](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623165759368.png)

- theater는 발행권자로서 정보가 많으므로, customer가 가진 데이터객체(예매권)을 검증해준다.





#### 06 객체간 통신

- 오브젝트 5장(1~4장 정리) 내용
  1. 객체간 책임을 주고 받을 때, 메세지로서 객체인자를 사용
  2. 책임은 누구에게?
     1. 유지보수하기 쉽게 설계한다.
     2. 구동코드를 배치한다.
  3. 설계 : 역할(추상메서드)책임(구상메서드) 모델을 통해  책임을 객체별로 나눠서 배치한다.
  4. 책임을 나눠가지려면, 메세지를 주고 받아야한다



- 객체간 통신

  - 메세지를 주고 받는 이유 : **객체별로 책임(메서드)을 나눠가지려고**
  - 객체망: 역할책임 모델기반의, 객체별로 코드가 잘 분리된 형태

- 객체는, **정보은닉을 위한 캡슐화**를 통해 내부사정은 내부에서 변경할테니, **외부에서는 제공되는 메서드로 소통만 해라.(시켜라)**
  ![image-20220204115202073](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204115202073.png)

  - 내장을 까지 않는 객체 -> public interface(메서드)로 메세지를 주고받는다. for 객체별 책임분리가 가능해지기 위해

- 현실은 다르다

  - 역할과 책임이 서로 다름

    - 객체의 책임: 수행하는업무 by 메서드
    - **객체의 역할: 당면한 어떠한 측면 by 인터페이스**

  - 같은 객체라도, **소속된 공간이나 측면에 따라 역할이 달라**지며  **역할에 따른 책임**이 외부에 노출되어 소통한다.

    - **같은 객체라도, 역할에 따라 책임을 가지니, 한 객체는 다양한 책임이 존재할 수 있다.**
      - 역할 = 인터페이스 = 객체가 당면한 어떤 측면

    ![image-20220204115805806](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204115805806.png)

  - 책임인 **메소드는 객체가 가진 `내부 데이터`에 따라 이미 정해진다.**
    - 그러나 역할인**인터페이스는, 데이터와 무관하게, 다양한 측면으로 여러가지를 구현되며 -> 다양한 인터페이스를 가진다면, 다양한 메서드를 가질 수 있게 된다.**
    - 예를 들면
      - 데이터 = 돈
      - 책임 = 돈을 지급 (데이터인 돈에 의해 정해짐)
      - 역할 = implements 사장, 아빠, 남자 등 여러가지 구현 가능
      - 역할이 생김에 따라  책임이 늘어난다. (역할이 없으면 데이터가 메서드를 한정해놓는다)
        - 책임1: 월급 지급
        - 책임2: 용돈 지급
        - 책임3: 데이터 비용 지급

  - 요점: **1개의 객체는, 어쩔 수 없이 여러 역할(인터페이스)를 implements해야하고, 그 역할에 따른 책임들(메서드들)을 수행하고 있다.**



- 객체간 통신이 어려운 이유

  - 객체 1개도 역할에 따라 다른 책임을 수행하고 있는데, 그러한 객체들이 여러개다.

    ![image-20220204121000002](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204121000002.png)

  - **다른 객체들도 객체망 내에서 나처럼, <역할에 따른 책임 수행>중이다.**

    ![image-20220204121114130](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204121114130.png)

  - **하지만, 객체간 통신은, 역할별로 주고 받는게 아니라 `1개 역할의 1개 책임` vs `다른 역할의 다른 책임` 형태로 책임 단위로 주고받아 복잡한 과정이다.**

    - 같은 역할의 책임만 통신하진 않는다.

    ![image-20220204134221271](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204134221271.png)

  - 데이터에 의해 책임이 나오고, 역할에 의해 다양한 책임으로 구현된다.

    - 책의 screening은 자기 가진 데이터 때문에 -> reserve()의 책임을 가지기도 했다. (정보전문가패턴: 데이터를 아는 놈이 책임 구현)
      - 인터페이스A: 상영정보 데이터를 가지는 책임(메서드X)
      - 인터페이스B: 상영정보를 바탕으로 예매를 하는 책임
    - **책대로라면, reserve()의 책임 -> reservator라는 역할 인터페이스를 imple후 구현해야한다.**
      - **메서드** = 책임 -> 역할안에서 나옴 -> **인터페이스도 만들어서 역할에 따른 책임으로 써야함.**
      - 한다면...
        - Screening implements Reservator
        - @Override reserve() { };
        - screening.reserve() ??
          - 상영정보가 예약을 하는 모순점이 그대로다..
          - 정보정문가패턴 + 역할책임모델이 모순
            - **나중에 해결됨.**
      - 원래는 new Reservator(screening)으로 데이터를 받은 뒤, reservator.reserve()를 해야한다.

- 통신망의 구성
  ![image-20220204140422832](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204140422832.png)

  - 객체는 통신하며, 다른 객체에게 도움을 받는다.
    - 2개 이상 도움을 요청했다면, 내부에서 1개로 도움을 합체시켜, 다른객체에게 다시 도움을 요청할 수 있다.
    - 비동기적인 도움 요청도 있기 때문에 더 복잡하다.
  - **도메인들을,  협력하는 객체망 + 메세지로 연결할 수 있어야한다.**
    - 코드를 분산시켜 객체를 만들고, 메세지로 연결만 할 수 있으면, 
    - **통신의 목적: 객체별로 책임을 나눠 가질 수 있게 된다.**

  - 잘못된 순환 통신망(오른쪽)

    ![image-20220204141012415](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204141012415.png)

    - 2객체 이상 통신하더라도, **서로 아는 구조 = 순환 참조만 없으면 된다.**
      - **순환구조 문제점: 의존성 제거가 안됨. 설계 자체가 안됨**
        - 단방향화(simplex) 시켜야한다.

- 객체 설계의 난점

  1. 인터페이스의 그룹화

     - 메서드 1개만 가진 인터페이스가 제일 좋다고 가정하자.

     - 인터페이스는 (구상체별) 책임들을 추상화한 역할이며 측면이다.

     - **1개 도메인이 가진 여러 측면 -> 여러 역할 -> 여러 책임**을 가진다.

     - **각 객체들은 자기만의 역할들(측면들)을 다 가지고 있을 것이다.**

       ![image-20220204144930849](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204144930849.png)

     - **객체망은 여러측면(역할)마다 발생하는 책임들끼리 소통한다**

       - 같은 역할(인터페이스)끼리의 소통도 있고
       - 다른 역할(인터페이스)끼리 소통하기도 한다.

       ![image-20220204145201844](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204145201844.png)

       - 다른 역할끼리 소통한다면, 인터페이스의 그룹화를 다시 변경해야한다. 계속 서로 상호작용해야한다.

         ![image-20220204145447399](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204145447399.png)

     

#### 07 SOLID 와 GRASP

##### SOLID

1. SOLID 5원칙

   1. SRP: 단일책임원칙
      - 책임: **코드를 수정하는 이유가 1개되는 객체가 되도록 짜라**
        - **`이 때` +  `이래서 변해`를 찾으면 분리해라**
        - seller vs office: **Theater가 다 하면 안되나?**
          - seller: 사는놈의 `돈을 차감`해주는 책임
            - 이 때는, 돈을 차감해야하는 놈이 따로 필요하다.
          - office: `돈을 정산`하는 책임
            - 이 때는, 돈을 정산해주는 놈이 따로 필요하다.
        - class를 변경하는 이유: 도메인에 관련된 이유 1개
          - 일반로직, 공통로직이 들어가서 이것으로 변경되면 안된다.
          - 공통로직은 유틸클래스로 빼자.
        - 만약, 코드 수정하는 이유 1개로 시작해서 class를 고치는데 다른클래스까지 타격 -> 잘못짠 설계로**산탄총 수술**
   2. OCP: 개방폐쇄원칙
      - open: 확장에는 열려있고 close:코드수정시에는 닫혀있다.
      - **다형성, 인터페이스 참조**로 생각한다.
        - 내부코드는 close되어 확장시 수정이 없도록 다형성(추상체)를 인자로 받고
        - 외부에서는 open되어 여러 구상체를 추가해도 된다.

   3. LSP: 리스코프 치환 원칙

      - **부모형이 자식형을 대체할 수 있다.**

      - 자식들에게서 추상메서드를 추출하여 부모가 일할 수 있게 한다.

      - **공통로직으로서 추상메서드를 추출할 때, `앞으로의 추가 구상층으로 확장도 가능`하도록 `교집합만 가지게` 짜야한다.**

        - 현재보이는 구상층만의 공통점으로 X  **추가될 구상층까지 다 고려해서 추상화시켜야해서 어렵다**

        ![image-20220204154807594](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204154807594.png)

      - **일부 구상층들만의 추상층은??**

        - 인터페이스로 만들어 빼고, **추가implements하면 된다.**

        ![image-20220204154932059](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204154932059.png)

   4. ISP: 인터페이스 분리 원칙(interface segregation)

      - **구상형 대신, 접근할 메서드를 가진 인터페이스를 만들어, `엉뚱한 권한접근(메서드호출)을 막는다`.**

      - **`접근할 메서드별로 모듈(class)`을 만든다면, `잘못된 것`이다.**

        - 객체 1개에 모든 메서드들이 모여있으면 엉뚱한 접근을 못막음.

        ![image-20220204190242478](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204190242478.png)

      - **인터페이스 모델방법:**

        - 인터페이스별로 메서드를 분리한다.
        - **모든 인터페이스를 객체가 구현**한다.
        - 자체 메소드는 객체내부에서 개별구현한다.
        - 필요에 따라 해당 인터페이스를 인자로 받는다?

        ![image-20220204190814982](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204190814982.png)

   5. DIP: 의존성 역전 원칙(Dependency inversion)

      - **추상화된 것에 의존해야한다. + 다운캐스팅 금지**
      - 구상체 = 저차원의 모듈, 추상체 = 고차원의 모듈
      - 구상체가 사용되었다면, 추상형으로 바꿔서 사용한다.
        - 그러려면 **ISP인터페이스분리원칙에** 의해 **인터페이스가 잘 나누어져있어야한다.**
        - 그러려면 **LSP(부모형이 자식형을 대체가능)하도록 추상메서드 추출해야한다.**



##### DDHL

- 머리나쁜 우리를 위해 만들어준 추가 4원칙

6. DI: 의존성 주입
   - 너흰 추론 불가능이니까, 받아서 써
7. DRY: don't repeat yourself
   - 너흰 까먹고 또 쓰네. 중복 하지마
8. Hollywoord principle: 의존성 부패 방지
   - 너흰 나한테 물어보지마 변질될 수도 있어.(getter) -> 잘 아는 놈에게 그냥 시켜
   - 물어보는 순간(getter) 일반적으로 부패된다.
     - 일반적으로 물어보면(getter)후  대답해야해서(return 응답객체) -> 새 의존성이 추가될 수 도 있다.(값getter가 아니라 객체getter면)
9. Law of demeter: 디미터 법칙 = 최소 지식만 요구한다.
   - classA 안에서 methodA가 가질 수 있는 최대지식은?
     - classA필드들 + 내부에서 만들어진 응답객체 / 인자로 넘어온 객체 / 지역변수 
     - **그외 새로운 것을 알면 안된다.**
       - 1차적으로 관계된 것들만 알자.
       - 그외에는 직거래를 유도하거나 래핑메소드를 만들어서 1차원적으로 만들자.
       - 타고가서 return응답객체를 받아오지말자.(간접거래)
         - 간접적으로 아는 것은 모르는 것이나 마찬가지다. 머리가 나빠서 기억 못함.
     - **열차전복사고(`.` 2개이상)을 방지하면 된다.**
       - 새로운 의존성(응답객체)이 올 수 있다.





##### 헐리웃원칙(의존성부패방지) && 디미터법칙(최소지식) -> 무책임? 모순?

- 헐리웃원칙 = 의존성 부패방지
  
  - getter로 물으면 응답객체가 와서 의존성생기고 부패되니 **시켜라**
- 디미터법칙 = 최소지식만 요구
  
- 1차원적인 관계만 유지하고, 열차전복사고를 방지해라
  
- 헐리웃원칙 + 디미터법칙

  - **1차원 관계만 유지(쩜1개) 하면서 시킨다.(no getter, no 응답객체)**
  - **메세지만 주고 받으면서 객체간 통신은 가능하다**

  ![image-20220204200739653](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204200739653.png)

- **시켰을 때, `내부 메서드가 제대로 작동했는지 / 현재 던진 것의 성공여부(boolean) 등`은 어떻게 알까? 시키기만 하면 되나?**

  - 무책임??
  - **안의 내용은 확인하지 말고 `메세지가 잘 수신되었는지를 하는 하는 코드`만 작성한다.**

- 메세지를 확인하는 코드는?

  - **원래 내장까지 까주는 객체들(`Mock` or `내부 log찍는 인터페이스를 구현한 객체`)로부터 메세지를 수신 확인한다**
  - 생략

  

- mock객체 활용 시키는 것을 검증

  - 단위테스트시 mock객체

    - **`실환경`**의 객체를 **대신하진 못한다**.

  - 우리는 객체망을 통해 문제해결 but 해당 **객체의 내부를 추론할 수 없**으므로 **`객체망검증`을 위한 mock객체가 필요하다** 

  - 모조객체(mockery) vs 목객체(mock)

    - 모조객체 : 목객체의 우두머리 = 테스트 관리 객체이자 context
    - 목객체: 테스트용 모의 객체

  - Mock객체를 통한 검증
    ![image-20220204210641461](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220204210641461.png)

    1. mockery(context)부터 배치
    2. mockery의 context를 안고서  mock객체 회/파/빨  생성
       - mock객체는, 객체망을 검증해주는, 내장까주는 주변객체
       - mock객체는 mockery객체로 보고하는 로직이 내부 구현
         ![image-20220205110407718](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205110407718.png)

    3. 실제 테스트 대상 객체 생성하여 객체망 구성

       - 보통 DI로 구성
         ![image-20220205110539358](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205110539358.png)

    4. trigger를 통해, 회색mock객체에 첫번째 메세지 통신
       ![image-20220205110656857](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205110656857.png)
       ![image-20220205110707290](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205110707290.png)

    5. 회색mock객체로부터 `목객체에서 대상으로 메세지 송신`을 mockery에 보고

    6. 파랑/빨mock객체로부터 `목객체에서 메세지 수신`을 mockery에 보고

       ![image-20220205110905643](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205110905643.png)

    7. 이 목 시스템 외에 `객체 메소드 단위테스트`로는 정상작동객체를 증명할 수 없다.

       - 객체는 오직 메세지를 주고-> 받고할 때 의미가 있으므로
       - 전/후의 주변객체(mock객체)들을 통해 
         - 적시에 그자리에 있는지
         - setter로 세팅한 것이 적시에 이루어지는지 확인할 수 없다.
       - 객체는 오직 객체 통신으로 책임이라는 메세지를 호출하는 것만으로 의미를 가지며, 목 테스트를 통한 방법의외에 검증할 방법이 없다.

##### GRASP의 9가지 패턴

- [참고블로그 - 한빛미디어-김대곤님](https://www.hanbit.co.kr/channel/category/category_view.html?cms_code=CMS8586826397)

- General Responsibility Assignment Software Patterns
  - **책임을 부여하는 원칙**들을 말하고 있는 **패턴**.
  - 구체적인 구조는 없지만, 철학을 배울 수 있다.
  - **총 9가지의 원칙을 가지고 있다**

- SOLID 5 + DDHL 4를 base로 하였지만, **더 민간용으로 쉽게 나온 설계요령**

  - 다른 관점에서 SOLID법칙, 헐리우드법칙, 디미터법칙을 인식하는 좋은 계기가 된다.
  - **거성들의 원칙(5+4)보다 객체지향 적용의 약한 레벨에 적용할 수 있게  좀 풀어놓은 버전**
  - **응용범위는 더 좁을 수 있다. 패턴은 특정상황에서 쓰는 것**

- 9가지 패턴

  ![image-20220205112944699](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205112944699.png)

1. 정보전문가 패턴

   - 객체지향의 은닉성을 의미하는 것
   - 데이터를 많이 아는 객체가 **책임(메서드)**도 가지는 것이 좋다.
     - 은닉성이 잘지켜지니까 그놈이 책임=메서드를 가지자

2. Creator 패턴

   - 은닉성과 연결되는 것
   - **객체의 생성**도 데이터를 많이 알고 있는 놈 or 친밀한 놈이 하는게 좋다.
     - 실제로 지키기 힘듬. 정보전문가패턴과 같을 때도/아닐 때도 있다.
   - reservation의 생성
     - 책: **screening이 많이 알고** 있어서 예매권 생성
     - (반례) 개선: 생성해서 **`검증에 이용(친밀한 놈?)`**할 theater가 예매권 생성
       - **`이용할 놈`이 생성도 담당하기 위해** 위해, **관련 정보를 theater에 모으는 사전 작업이필요했음.**

3. controller

   - 중개자가 직접 접촉 or 간접 접촉
   - **어댑터 패턴 + 미디에이터 패턴**을 동시에 적용하고 싶을 때, **컨트롤러 패턴으로 퉁친다.**
     1. 어댑터 패턴: (자주변하는 것은) 내가 직접아는 것보다, **중간에 어댑터를 두어, 간접적으로 아는 것이 훨씬 유리하다.**
        - model이 view를 직접아는 것보다 **controller(어댑터)**를 통해 간접적으로 아는 것이 더 좋다
     2. 미디에이터 패턴: 1객체가 다수의 객체를 알아야한다면, 객체망이 너무 복잡해지니, **중심에 미디에이터를 두고, 미디에이터가 다 알게 한다음, 객체는 미디에이터 1개만 알게 한다**
        - 사람 - 모든 case를 다 아는`법원` - 판례1, 판례2 ...
        - 어댑터가 여러 시스템을 알고 있다면, 미디에이터 패턴에 해당한다
          - model - controller - `view / console ... `
   - 나온이유: **외부에 대해선 어댑터역할을 하고, 내부에선 미디에이터 패턴이다.**

   

4. Low Coupling(낮은 결합도)

   - **결합도 낮다 = 알고 있는 객체가 적다 = 양방향 참조를 제거한다 = M:N을 제거하듯이 형1개가 추가되지만, 두려워하지마라**
   - 알아야만 하는 객체만 알고, **단방향 의존성**을 가지는 것이 목표
     - 양방향 순환참조 : 상태를 보고 버그확정이 불가능해진다.
       - 디버거들이 무력화
       - 주고 받기 때문에 값을 모른다. + 누구책임인지 모른다 + runtime에서 못잡는다.
       - 100% 제거해야함
       - RDB의 기술 MN -> 1M으로 바꾸는 것처럼 **중간테이블과 같은 `중간객체`가 들어와야지 제거가 가능하다.**
         - 형이 1개 늘어난다.
         - 어댑터 패턴에서도, 연결할 놈 1개가 추가되면, 어댑터인터페이스 + 어댑터구상체 set로 1개씩 늘어난다.



5. High cohesion(높은 응집도)
   - 방법이 이미 정해져있다.
   - SRP에서의 책임처럼이 때 + 이래서 변해의 **변화율에 따라 코드를 나누고, 변화시 다른 곳의 영향이 안가게 격리시키는 것**



6.  Protected Variations
   - 인터페이스 분리 원칙을 적용해라
   - 공통점을 찾아 인터페이스로 추상화해서 **가능한 추상체로 소통**
     - **공통점을 찾는 이유: 인터페이스로 노출해서 -> 책임을 할당한다**
7. Polymorphism
   - **전략패턴을 의미한다.**
   - 인터페이스로 추상화하 비슷한 얘기 같지만, **추상체를 인자로 받아들이고, 달라지는 부분은 구상체들이 나중에 구현**



8. Pure Fabrication
   - 공통된 기능/순수기능객체는 따로 모아서 작성한다
   - **수정의 여파가 다른 곳으로 가지 못하게, 공통기능은 따로 분리해서 처리한다.**
     - **추상층으로 따로 분리하라는 얘기** or 
     - **공통클래스로 빼라는 얘기**
   - **추상화가 끝난 객체 = `완성판 객체`는?**
     - 인터페이스(역할)을 구현한 추상클래스에 `공통기능`이 public `템플릿 메소드`로 정의
       - `개별구현`은 public 템플릿메소드 내에 protected abstract `훅메서드`로 구현
   - **전략패턴 <-> 템플릿메소드패턴**을 밥먹듯이 바꿀 수 있어야한다.
     - 템플릿메소드패턴: **`고정 구상층`을 가진 추상클래스**
     - 전략패턴: **`확장성`에 유리한 인터페이스**

9. indirection
   - 직접참조관계를 피하고, **중계객체를 이용하면, 개별객체의 충격을 흡수해준다.**
   - 포인터의 포인터
     - 다른 의미로는 인터페이스의 추상형 사용
     - 데코레이터패턴 or 어댑터 패턴으로 간접 명령
   - 간접참조를 하게 된다면, 직접참조의 대상(value)를 변화시 수동으로 바꿀필요없이 참조만 하면된다.





#### 08 Theater with SOLID(객체의 설계3)

- [최종코드](https://github.com/LenKIM/object-book/tree/master/object3/src)

![image-20220624005906639](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624005906639.png)



##### Movie

![image-20220205161147187](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205161147187.png)

- T형(Amount or PercentDiscount) 할인정책 객체 여러개를 받던 것을 1개만 받도록 설계됨.

- **정책조건 만족시, 총 요금을 `movie내부에서 fee`를 이용해 movie내부에서 계산 -> `정책 객체에게 movie의 fee를 넘겨서` 알아서 정책객체가 알아서 계산하도록 `계산 위임`**하도록 바뀜.

  ![image-20220623223839368](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623223839368.png)





##### DiscountPolicy

![image-20220205161328082](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205161328082.png)

- 기존에는 인터페이스내부에 policy종류별 마커인터페이스들 담고 있었으나, **caculateFee()에 대한 `템플릿메소드 패턴`이 적용된 `추상클래스`로 바꾸었다.**

  - 물려지는 public은 다 템플릿메소드라고 부른다.
    - addCondittion, copyCondition, calculateFee
  - 자식(구상클래스)에 위임된 protected abstract 메서드를 훅메서드라고 부른다.
    - calculateFee
  - **템플릿메소드패턴은 언제???**
    - 구상형의 수가 한정된 상태
    - **구현층에 공통 로직이나 공통 필드가 존재할 때, 중복제거**용으로
  - **전략패턴은 언제???**
    - 구상형이 확장가능성이 있으면서
    - **개별로직들이 공통점이 없어서 -> 1개 메소드로 인터페이스를 정할 때**
  - **만약, 공통로직이 존재하여 템메패턴써야하는데, 전략패턴을 써버린다면?**
    - **공통로직 -> 수정시 일일히 다 수정해야함.**
  - **한정된 추메패턴에서, 구상체가 추가되었는데, 공통로직이 안보인다? 전략패턴으로 싹다 다시 바꿔줘야한다.**

  ![image-20220623225344143](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623225344143.png)

- 또한, 내부에 **`정책조건을 빈컬렉션 필드에 모아서`, 할인 정책 적용시 사용한다.**

  - **생성자에서 받지 않고, 이후에 유연하게 받아들일 수 있게 setter처럼 받기기능을 제공**한다.

  ![image-20220623225407203](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623225407203.png)

- 특이하게 **같은 policy를 받아서, 내부 `정책조건들 필드`을 복사해서 가져오는 기능도 존재한다.**

  - **이는, movie에 적용될 policy가 다른 policy로 대체될 때, `기존 policy의 정책조건을 가져와서 물려받을 수 `있게 만든다.**

    - AmountPolicy에서 PercentAmount로 할인정책을 바꿀 때, 기존 AmountPolicy가 가진 conditions를 물려받는 기능

  - **다시 conditions객체를 만들어 넣으면 안되나?**

    - 객체는 식별자로 판단되기 때문에, 다시만들면 다른 객체가 된다.
    - 같은 종류의 객체비교는 내장을 깔 수 있기 때문에 쉽게 가능해진다.

    ![image-20220623225318562](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623225318562.png)

  - 이런 방식을 택한 이유는
    - **Movie의 지식을 줄이기 위해,** policy만 알고, policy가 conditions를 알게 하는 방식으로 변경
    - 기존: SequenceAmountPolicy로 둘다 적용된 조건+정책 구상체를 받아들였었다.
    - 원래는 Movie가 가졌던 condition들인데, policy가 가지게 하였다.
      - policy교체시 -> 딸린 conditions들도 옮겨줘야하는 책임을 가지게 된다.
      - **객체에서는 이렇게 소유권을 맘대로 옮겨선 안된다.**



##### AmountPolicy, PercentPolicy

![image-20220205164846747](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205164846747.png)

- 템플릿메소드패턴의 구상체들은, **훅메서드**만 개별구현해주면 된다.
  - 추상클래스에서 안보이지만 다 물려준다.
  - 훅메서드 구현시 혹시, 다른 정보가 필요하면?, 생성자에서 받아서 처리해주면 되는데, 부모 생성자도 super로 재정의해서, 부모레벨에서 다 처리되고 내려오게 하면 된다.
- 할인요금을 계산할 때, 넘어오는 fee에 대해, **얼만큼의 amount만큼 깎아줘야하는지 정보가 미리 태어날 때 정해져있어야한다.**
  - 값객체 amount를 생성자에서 받는다.

![image-20220205165027756](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205165027756.png)





##### DiscountCondition

![image-20220205170231321](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205170231321.png)

![image-20220623235407537](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220623235407537.png)

- 기존에 가지던 action메서드는 DiscountPolicy에게 위임하고 1개의 메서드를 갖는 인터페이스가 되었다. 전략패턴



##### Period(Time)Condition, SequenceCondition

![image-20220205170250233](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205170250233.png)
![image-20220205170320843](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205170320843.png)

- 정책조건의 전략객체로서, 개별적으로 조건을 만족하는지 구현한다.
  - 필요한 내부정보는 생성자에서 조달한다



##### TicketSeller

![image-20220205170435907](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205170435907.png)

- reserve()의 **인자(파라미터)가 5개**나 된다.

  - **객체를 메서드의 인자**로 받았다 -> 그 객체를 알고 의존하는 **dependency를 가지고 있다.**

    - 5개의 묶음을 형(type, class)로 강제하면 좋으나 **상위도메인이자 소속기관인 ticketOffice에게  customer를 제외하고 4개의 인자를 다시 넘겨줘야한다.**

      ![image-20220624000603189](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624000603189.png)

    - 또한 customer는 예매시, seller인 나에게 5개의 정보를 다 넘겨준다.

- **나는 비록  5개의 인자를 받았지만, 이 dependency 중 `theater, movie, screening의 객체`을 `나(seller)는 사용하면 안된다`**

  - **왜?**

    - **나는 ticketOffice에게 `정보 전달의 책임(의존성을 중개만)`만 있다.**

  - **원래 의존성을 가지던 ticketOffice만 사용해야한다.**

  - **`원래 의존성이 없었는데, 메서드의 인자로 넘어온 의존성`에 대해서는 `그 의존성을 사용하고 끝낼 것이 아니면 사용하면 안된다.`**

    - 보통 받으면 사용하면 되지만, **seller는 해당 `의존성의 전달 책임만 있고, 사용하고 끝낼 놈은 ticketOffice`다 **
    - **`내가 사용하고 끝낼려면, ticketOffice에게 전달하면 안된다.`**

  - **현재 코드에서 오류 잡아보기**

    - 인자로 받은 의존성을, 내가 직접 사용하는 `movie`

      ![image-20220624001326990](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624001326990.png)

    - **이미 사용했지만, 내가 의존하는 객체에 다시 넘겨주는 오류**

      ![image-20220624001414341](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624001414341.png)



- **이렇게 전달만 할 것인지 vs 직접 사용할 것인지를 선택하는 것은 `디미터원칙(최소지식 원칙)`을 지키려고 하는 것이다.**

  ![image-20220205171559206](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205171559206.png)

  - `빨간줄`이 디미터 원칙을 위배한다는 것을 알아야한다.
    - **ticketOffice에게 넘겨줘야할 것인데, 중간에 직접 사용하는 오류**
    - **내가 사용하지말고, 나와 직접적인 관계가 있는 1차원적 관계의 `ticketOffice에게 책임을 위임`한다.**
      - my) 그대로 놔두면, 나도 사용, office도 사용 -> **movie입장에서는 적어도 1가지는 디미터원칙 위반(건너고 건너는?)**
      - movie가 변하면 -> seller(나)도, office도 바뀐다 
  - **나와 1차원 관계가 있는, 직접적인 관계의 ticketOffice에게 movie를 사용하는 것을 위임하자**

- **`의존성(movie)의 변화는 한 곳`에서만 나타나도록 `1차원 적인 놈에게만 의존성 사용을 몰빵`하자. 여러 객체가 쓰지말자.**

  - **`메서드의 인자로 받아도, 직접 사용하지 않으면 의존관계가 없다`** 

  - **ticketOffice**는 **원래 movie를 전달받아 사용했었다.**

    - **이미 메서드인자로 전달받은 객체를 내부에서 사용** -> 직접적인 관계, 1차원적인 관계, **의존성 가진 상태**

    ![image-20220624002339096](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624002339096.png)

  - **이왕 받는 거, 미리 한번 더 받아서, 책임을 위임받는다.**

    - ticketSeller 코드변화

      - **직접의존성이 있는 객체가 대신 기능해주는 것을 wrapping method라 한다.**

      ![image-20220624002452842](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624002452842.png)

  

##### TicketOffice

![image-20220205172319775](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205172319775.png)

- seller가 직접 호출해서 의존성이 추가되는 것을 방지하기 위해 office는 **이미 직접 의존성을 가지므로, 대신 기능해주는 `wrapping method`를 제공했다. **

- **`내부에 코드(메서드)가 추가되었을 때, 항상 기존 코드와 비교해서 <메서드가 대신 차지할> 중복코드는 없는지 확인`해야한다.**

  - 빨간색의 코드가 중복이다. 추가된 메서드로 대체한다.

  ![image-20220624003633243](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624003633243.png)







##### Main

- 한번에 policy + condition 합쳐진 구상체에서 -> condtions를 채워서 가지고 있는 policy로 사용법 변화되었으니 코드 수정

  ![image-20220624005647656](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624005647656.png)







#### 09 LSP

- object 6장
  - 디미터의 원칙: 최조지식 요구 = 1차원적 관계, 아는놈이 직거래하도록 시키자
  - 헐리우드 원칙: **변하는 놈에게 물으면(getter) 나도 여파**가 생기니, 묻지말고 그냥 시켜라
- 객체지향 실패 이유
  - 변하는 놈에게 물어보기 때문에 실패한다 -> 시키자
    - getter = **값(변함), 응답객체(의존성 생김)를 `return하는 method를 만들지말자`**
    - 물어봐서 받아오는데, 변하는 놈이면? 나도 변화의 여파를 받는다.
      - 물어볼 시, 실시간 업데이트가 안됨.
      - **`객체는 계속 변하여, 내가 알고 있던 걔가 아니다.`**
- 디미터 + 헐리우드 원칙 2가지는 **객체지향보다 `수정에 강한 코드(변화의 여파가 없는 코드)`를 만든다.**
  - SOLID원칙으로 따지면, **LSP, OCP**가 변화여파가 없는 코드다.



##### 확장시, 스펙(공통메서드)가 줄어드는 경우

- 리스코프치환원칙(LSP): 부모형으로 자식을 안전하게 대치할 수 있다.

  1. 구상층의 공통메서드를 추상층 + 추상메서드로 뽑아낸다.

     ![image-20220205201824119](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205201824119.png)

     - **그러나 성급한 추상화**이다. **보이지 않는 확장 구상체의 메소드를 고려안함**
       ![image-20220205201953384](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205201953384.png)

     - **대부분의 개발자들은, 확장으로 인해 공통이 아닌 것으로 밝혀져도, fake c()를 만들어 에러를 내는 방식을 많이 취한다.**

       - 하지만 fake c()가 context버그를 만들어낸다. **공통도 아닌데, 추상메서드를 개별구현해버리는 경우가 `리스코프 치환원칙 위배`이다.**

       ![image-20220205202041293](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205202041293.png)

  2. 이 때, **공통점이 없는 놈에게 억지로 공통점을 만들어주는 것이 아니라, `100% 공통로직만으로 구성된 중간추상층`을 `구상층 가장 가까이 추가`하고, `일부 공통로직은,  일부구상체들만 더 위에서 상속한다`**

     - 중간추상층

       - 기존에는 **2개의 메서드 중 1개 메서드를 먼저 개별구현하기 위해 썼었다.**
         - 예) 정책조건 인터페이스 = 1trigger + 2action을 가지는데, 2action메서드만 중간에서 먼저 구현해서 막고 -> 개별구현체들은 1trigger만 구현
       - LSP에서는 구상체 확장했을 때, 공통메서드 일부를 안가지는 경우, **100% 공통메서드만을 위한 추상층을, 다같이 붙이게 만든다.**
         - **일부 공통메서드는 더 상위에서, 해당하는 것들만 상속한다.**

     - **공통의 범위(스펙)가 줄어드는 경우, `줄어든 공통메서드를 가지는 중간추상층을 만들어 구상층에 가까이 붙여준다.`**

       - **`일부 공통로직은, 그것만 모아서 추상층을 만들고, 해당하는 것들만 더 상위추상층에서 구현`해준다.**

         ![image-20220205202205658](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205202205658.png)

     





##### 확장시 스펙은 그대론데, 단독메서드가 생기는 경우(확장형 LSP)

![image-20220205202804665](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205202804665.png)



- 줄어들 때 처럼 
  - 공통메서드a,b,c만 추상화한 중간추상층이 구상층에 가장 가까이 붙어있다.
  - **그렇다면, d만 빼서 concrete3의 상위추상층으로 올리면 안되나?**
    - 내가 만든 예시
      ![image-20220624115642749](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624115642749.png)
    - **d는 일부 `공통도 아니기 때문`에, 중복코드가 아니면 추상화할 이유가 없다.** 
      - **추상화는 공통점만 하는 것이다. 일부라도 공통이어야 한다.**
  - **혼자만 가지는 메서드는 `instanceof`의 `다운캐스팅`을 유발하여 `LSP위반`하는 코드다**
    - **`LSP위반 2가지`**
      - **내가 가지지도 않는데 억지로 공통이라 올린 fake c()를 구현하는 경우**
      - **나만 가져서, 나만 확인해서 d()를 사용하는 경우**
- **추상체를 파라미터로 하여 부모형으로 대체해야하는데, 구현체 1개만 자신만의 메서드를 가져서 확인해야하는 경우, `다운캐스팅을 방지하는 제네릭`을 이용한다.**
  - **`제네릭 이용 2가지`**
    1. **특정형(중간추상층도 가능)을 1:1로 인식해야하는 경우**
       - 예) movie는 AmountPolicy, or PercentPolicy 둘 중에 1개형만 인식한다
    2. **구상층의 확장시, 단독메서드가 생겨 instance가 발해아는 경우**



- **추상체를 그대로 쓰면, d()를 인식할 방법이 없다**
  - **`제네릭`을 통해 특정형(d()를 가진 concrete3)알게 하면서, `익클을 통해서 외부에서 d()를 구현`하도록 하게 한다**
  - **즉, `제네릭을 통해 구현을 외부 클라이언트로 미루어`서, 와서 인식하지말고, `미리 인식한 상태로 구현으로 선택`해서 들어오도록**
    - 개발자의 세계 예시
      - **d()가 아니라, 해당 개별 구상체마다 `특정 구상체만 제공해주는 필드`로 인해 `instanceof`의 다운캐스팅을 해서 데이터를 받아온다.**





#### 10 개발자의 세계-문제점



##### Programmer

![image-20220205205215745](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205205215745.png)

- 추상층이며, **director가 개발자+들(추상층)을 도구로 보고, program을 제공해주라고 시킨다. 이 때, paper+들(추상층)을 재료로 던져준다.**
  - Program을 **return으로 ~(Director내부= 외부)에게 제공**해야하므로 **return값이 있는 오퍼레이터(추상메서드)를 제공**한다.
    ![image-20220624125637927](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624125637927.png)

##### Paper

![image-20220205204930129](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205204930129.png)

- paper는 외부에서 제공해달라고 시키거나(return제공기능), 외부에서 주거나(void받기기능)이 없는 마커인터페이스다.
  - 구상체들이 impl하면 범주만 나타내준다.

##### Client

![image-20220205205943360](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205205943360.png)

- void 메서드로 받기기능(외부에선 주는 기능)만 있다.
  - 외부에서 Programmer+들(추상체)를 주면, 내부에 상태값으로 저장한다.
  - 참고) void는 제공 기능9return)을 제외한 받기기능뿐만 아니라 처리기능(deploy, print) 등 다양한 곳에서 쓰인다.
  - 참고) return 받기기능도 성공여부를 전달해야한다면 boolean으로 받기기능을 정의하기도 한다.
- 마커인터페이스를 구현했으면, **내부에서 필드**(데이터, 상태값 저장) + **메소드**(기능, 책임)를 **모두 채워야한다.**
  - clinet나에게 필요한 데이터들을 필드로
    - 정해진 데이터는 초기화된 필드로
    - 나중에 받을 데이터는 setter(받기기능)로 채울 것이니 null로 초기화
  - 내가 제공해야하는 기능은 메서드로
    - **나는 기획서들 중 1개로서, 이후 나한테 맞는 개발자를 받아 저장(받기기능 for 변하는 필드값)한다.**



##### ServerClient

![image-20220205210600624](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205210600624.png)

- Client기획서와 다르게, 2개의 데이터를 받기기능을 외부에 제공한다.
  - 개발자 2명은 나중에 받는다. 그들이 사용할 language도 숫자에 맞춰 2개를 가진다.



##### FrontEnd(instanceof 등장)

![image-20220206004905381](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206004905381.png)

- 외부에서 시키는, paper를 받아 Program 제공 기능을 구현해야한다.

- 기획서를 받을 때, 
  - 나(FrontEnd, 특정구상체)에 맞는 특정Paper구상형을 인지 확인한다.
  - 재료로 들어오는 추상체의 구상체별로 **각각 다른 정보를 제공**하여 **확인 by 다운캐스팅(instanceof)**할 수 밖에 없다.
    - Paper에 추상층 정보가 작아서, 개별(Client vs ServerClient)로 확인해야한다
    - **Paper가 공통메서드(오퍼레이트)가 없는 `정보가 작은 마커인터페이스`이기 때문이다.**
      - 그렇다고 Client, ServerClient의 공통메서드는 찾을 수 없다.
      - **Clinet, ServerClient의 로직은 1개의 메서드가 아니라 `서로 다른 필드`로서 인`터페이스에 오퍼레이터로 추상화할 수 없`기 때문** 





##### BackEnd(instanceof)

![image-20220206010030897](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206010030897.png)

- 역시 마찬가지로, Program을 외부에 제공해주는 기능을 만들 때, Paper기획서들을 재료(인자)로 받지만, Paper의 정보가 적어서, **어떤 Paper인지 확인**해서 나에게 맞는 정보만 가져와야한다.



##### instanceof의 문제점

- Paper구상체마다 확인해서 사용한다. 구상체가 추가되면?  또 instanceof로 확인해야한다.
  - **다운캐스팅 = 확장에 막혀있다 = 구상체 추가시 내부코드가 추가된다(변한다)= `O`CP위반**
- if분기는 goto를 통해 runtime, compile error를 유발하지 않는다. -> 못찾아낸다.



##### Director

![image-20220206010649959](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206010649959.png)

- 여러 paper들을 소유하기 위해선 -> 데이터필드를 빈컬렉션 초기화 -> add기능으로 받기를 사용했다.
  - 특히, 상위도메인처럼, name과 매핑해서 저장하기 위해서, map을 이용했다.
    - 여러개를 저장하면 add(받기)외에 C(add)RU(edit)D(remove)를 다 구현할 수있는데 생략
- director내부에서 저장된 paper을 paper로 꺼내더라도
  - paper구상형마다 처리 로직이 다르므로 확인을 해야하는 문제점이 있다.
    - 추상화 후 개별구현 메서드 기능이 없다.
    - **마커인터페이스의 문제점: 한번에 처리할 수 있는 오퍼레이터가 없어서, 꺼낼때마다 확인할 수 밖에 없다.**
      - **인터페이스:  `인터페이스에 정의된 오퍼레이터 외의 기능`을 이용하려면, `다운캐스팅으로 확인해서 써야`한다.**
  - 구상형 종류마다 다 instanceof로 확인해야한다.



##### 문제점들

- 추상체(Programmer)가 추상체(Paper)를 넘겨받을 때,
  - **특정구상체(FrontEnd)는, 특정구상체(Client)만 알아야만 하는 상황이다.**
    - ServerClient를 받으면, 그 순간 필요한 정보(library 등)가 없어서 context에러가 난다.
- **인자로 받는 추상체 중 특정형만 알아야한다면? **
  1. **`제네릭`을 통해, 받는 놈이 `인자(재료)로 오는 추상체를 특정형`만 알게 한다.**
     - 조건: **받는 놈의 `추상층`이 제네릭 `T extends 재료의 추상형`으로 정의되어있어야한다.**



- **LSP문제로 보면?**	
  - abc -> d() 추가가 아니라
  - 마커인터페이스 0 -> d() 추가의 상황이다.



- **`추상층(부모형) 정의되지 않는 정보 d() `는  물어서 확인(instanceof)해서 써야하니**

  1. **확인 = 묻지말고 시켜라? -> 헐리웃원칙?(부모형에 적용)**

     - **1개 메서드로 묶어서 추상층에 올려 -> 추상층에 시킨다.**
       - **시킨다 = 상대방에게 물어보고나서 하던 일들을 메서드를 만들어 기능을 제공해줘야 시킨다.**
         - 엥? 확장성LSP의 자기만하는 메서드d()는 올리면 안될텐데??
         - **중복코드가 없은 것을 1개 메서드로 올릴 경우, 전략패턴**
       - 올릴 때는, 해당 구상체만의 로직을 찾아서 메서드로 묶고 올려야한다.
         - **만약, 물어보던 놈의 내부정보(필드등)이 사용되었다면, 반대로 시킬 때는 메서드의 this로 들어가게 된다.**
         - 물어보던놈이 추상체 중 1개였다면, this -> 파라미터로 받을 땐, 추상체로 올려야한다. 모든 것을 추상화

     ![image-20220206120118325](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206120118325.png)
     ![image-20220206120106161](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206120106161.png)

  2. (Paper말고 반대쪽으로 보면)backend도 마찬가지로 paper에게. setData(this)로 시키면, 공통 코드가 생긴다.

     - **공통코드가 생기면 Programmer인터페이스 -> 추상클래스 + 템플릿메소드 패턴으로 바꾼다.**

     ![image-20220206152809213](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206152809213.png)



 3. Paper에게 this -> 추상형 인자로 시켰더니, 거기서도 **확인해야하는 문제가 발생함.**

    - **시키기 전에 N(추상):M(추상)의 관계인지 확인해야한다.**
      ![image-20220206162318822](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206162318822.png)

    - **추상형(programmer)이 추상형(paper)을 받았을 때 생기는 instanceof의 문제**

      - **instanceof를 if처럼 `묻지말고 시키기 위해 로직을 모아서 -> 메서드 1개로 추상화`해서 제거할 수 없다.** 
        - 물어보고 사용될 때, **`현재 내부가 추상체의 구상체`라면, `this`로 넘길 때 `추상체`가 넘어가서 반대쪽에 `instanceof`가 생긴다**
      - 원래는 구상체들을 확인하는 class -> **if 제거 방법인 추상화로 의존성 역전을 쓴다(전략패턴)**
        1. 구상체를 물어 사용되는 내부 로직을 모아서 메서드로 만들어, 구상체.시키기()로 만들고
        2. 그 메서드를 추상체에 올린 뒤,
        3. 내부에 필요한 구상체를 외부에서 주입한다.
      - 여기서는 구상체확인하는 class가 **추상체**이며
        1. 시키기 위해 모을 때, class내부 field에 set하는 로직이 포함되므로 **this**로 **로직을 모은 메서드에 `인자 this`가 생기고**
        2. 그 **this는 추상체 중 1개의 구상체라, 추상체로 받고**
        3. 추상층에 올라간 메서드의 파라미터는 **추상체다.**
           - **메서드 1개로 모아서 시키는 `예비추상메서드의 인자`가 생길 수 밖에 없으며(호출부내에서 호출부 정보 이용-setter)**
           - **하필이면 그 this로 넘어간 인자가 추상체**여야만 한다.
      - pingpong으로 문제가 왔다갔다만 한다.
        - 참고) 2개이상의 오페레이터를 가진 추상층 -> 성급한 추상화
        - 참고) 개별로직 밖에 없는데 추상화 -> 성급한 추상화

    - **my) 메서드 추출시 내부context가 사용되어, this로 추출되었을 때, `그 context(class)가 어느 추상체의 구상체`라면, `넘어가서 this를 추상체인자로 받으니, instanceof가 발생할 각오`를 해야한다.** 

      ![image-20220624162208442](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624162208442.png)

    

	4. 정리

    - instanceof를 if제거 방법으로 제거하려면
      - 구상체에게 시키는 메서드를 만들고, 그 메서드를 추상화할텐데
      - **추상메서드의 인자에 this가 발생하고, 그 this역시 추상체라면, instanceof는 반대쪽으로 가서 생긴다.**

    

    

5. ServerClient는 Client와 다르게, this(programmer)자리에 2개의 구상층이 다 들어올 가능성이 있다.

   - **Paper에게 시켰더니, 구상체 갯수만큼 instanceof가 늘어난다.**

     ![image-20220206165844359](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206165844359.png)

   - **다시 반대로, BackEnd가 Paper에게 묻도록 롤백하면**, 다시 Paper중에 serverClient 만 처리하면 된다.

     - 1paper는 여러N Progammer를 가질 수 있고 in field
     - 1programmer는 1paper밖에 못가지므로,
       - **1에 해당하는 paper를 N에 넘겨서 처리하는 방향이 맞다. in 추상체vs 추상체**

6. **외부에서 받아온 인자에 대해, if를 제거해야하는 이유**
   - **`지금 보이는 if의 case가 다가 아니다. 보이는 것만 짜려고 해서 놓친다.`**
   - **if를 외부로 돌려서, 확장가능한 모든 case를 외부선택에 맡긴다.**
     - 내부는 코드변화가 없이 추상체로 대응한다.
   - **메서드로 추출하는 이유: 외부에서 시키는 것에 대하 기능제공을 해준다.**
     - 내부에서 if의 모든 case를 고려하지말고, 외부가 시키는대로만 하자.





#### 11 개발자의 세계-제네릭 적용

- `추상화` -> instanceof가 아니라 `if`를 제거한다.
  - 전략패턴: 추상체 인자를 유지한 체, **구상체는 `형으로 만들어서 외부에서 선택`**
- `제네릭` -> `instanceof` ( if와 호출처와 엮여 인자가 달린 추상메서드로 추출 )를 제거한다.
  - **외부 추상체 인자를 유지한 체, 구상체는 `형을 만들고, 외부에서 특정형 전용의 구상체를 만들도록 밀어낸다`.**
- 둘다 `if`를 통해 발생하는 runtime, context에러를  `형`을 통해 발생하는 compile에러로 옮긴다.





##### Paper와 Client

![image-20220206200620827](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206200620827.png)

1. 추상층은 **T형 + upperbound(업캐스팅)**의 제네릭을 주고, **추상체 인자(메서드/생성자/변수)를 T형으로 준다.**



2. 구상층은 T형대신 **알아야하는 특정형**을 제네릭으로 주고, 추상체 인자 대신 **특정형으로 정의**한다.

   - 구상층의 instanceof가 사라진다.

   - **구상층마다 특정형(FrontEnd or BackEnd)를 알게되어 -> `인자의 형들이 추상체가 아닌 특정형` 을 받으므로 instanceof 대신 사용할 수있게 된다.**

     - 추상형에 제네릭T를 쓰면, 구상형들의 T자리에 특정형으로 정의되도록 지원된다.

     ![image-20220624171207816](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624171207816.png)

     - 현재 추상체인자를 사용하는 클래스의 추상층에 제네릭으로서 upperbound로 T형을 꽂아주고, 추상메소드 정의시 T를 사용해서 정의
     - 구상시, 특정형의 부모를 상속/구현하면, T자리에 특정형으로 물려받음.

   ![image-20220206202245219](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206202245219.png)





##### ServerClient

- 그러나, instanceof가 2개가 등장 -> **제네릭은 특정형 1개만 T대신 정의되도록 해주므로 불가능**

  - 1:N관계에서 등장한 N개의 instanceof는 제네릭으로 제거 못한다.
  - 관계를 롤백해야한다.
    - Paper에게 시켰다가 문제만 더 커졌다.
    - **시킬 때는, 추상체vs추상체가 아닐때만 시켜서 넘겨라.**

  ![image-20220206204751581](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206204751581.png)





##### Paper, Client, ServerClient 롤백

![image-20220624170449722](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624170449722.png)

- 관계도상, programmer는 paper를 알지만, 이것은 1:N관계에 대한 근거는 안된다.
  - Paper가 가진 필드를 보면, programmer front / back  둘다 받는다.
    - 1:N관계라고 할 수 있음.
  - programmer는 paper에 1개에 대한 정보만 추출한다.
- **이상적인 관계는 N(child)가 -> 1(parent)를 알고서 받아들여, 내부에서는 1:1 관계가 발생하는 것이다.**
  - RDB에서도 MANY테이블에만 One테이블의 id를 fk로 가지고 있다.
  - **1(paper)에게 N(this, programmer)을 던져 1내부에서 일 시키지말자!!!**

![image-20220207010809521](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207010809521.png)
![image-20220207012516441](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207012516441.png)

- 마커인터페이스므로 공통로직abc는 없다
  - **개별 구상체에 잇는 내용들 전부가 d()에 해당하는 내용들이다.**
  - 확장성LSP는 instanceof를 유발하므로 **공통로직 유무에 따라 템매 or 전략패턴으로 추상화해놓고 -> `사용/의존class인 Programmer에 제네릭을 적용`한다. **





##### Programmer, BackEnd, FrontEnd

- 그 전에 **`사용/의존 class(FrontEnd, BackEnd)의 추상층(Programmer)의 upperbound로 얹을 수 있게 기본적인 개별로직 추상화` 마커인터페이스에 적용해줘야한다.**를 시켜야한다.

  - 사용class에 공통로직이 보이는지 본다.

  - **템플릿메소드패턴은,  `흐름만 비슷해도` **

    1. 공통로직은 그대로 & 개별구현 private예비 훅메서드들 추출한 뒤, step별로 나열하는 **public 템플릿메소드**로 감싼다. 
       1. 구상체에서는 private으로 빼놓고 훅메서드로 올리면 된다.
    2. 개별사용 메서드들이 따로 존재하면, 메서드명을 추상화해서 정한다.

    ![image-20220207012641647](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207012641647.png)





- backEnd는 Programmer<특정형>을 상속하면, T자리에 특정형을 받아 사용할 수 있는데

  - **backend는 Client이외에 ServerClient 특정형** 즉, **여러paper형 T를 받을 수있게 다시 한번 추상화**한다.

  - **추상클래스로 중간추상층화** 해놓으면

    1. 실제로 구상층을 만들 수 있으나, 그만큼 class파일을 만들어줘야하므로
    2. **외부에서 익명클래스로 분신술- 실시간 구현하며 구상체가 됨. + 그 구상체는 제네릭 특정형을 알 수 있게 됨**을 이용한다.
    3. **추상클래스로 추상화했기 때문에, `안바뀌는 공통로직은 추상클래스에서 해결`하고, 개별구현 훅메서드만 외부구현으로 맡긴다.**

    ![image-20220207022203455](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207022203455.png)

- frontend도 여러형의 paper를 알 수있게 **다시 한번 T형 + 추상화**으로 만든다.

  ![image-20220207130309754](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207130309754.png)





##### Director와 Main (구상체들의 제네릭+추상클래스화 -> 이용하는, 직위가 높은 외부에서 실시간 선택해서 구현)

- 가장 위쪽 추상체인 Programmer, Paper에서 개별구현을 논하지말고, **`구상체들도 추상클래스화` 시키며, 더 바깥인 Director에서 선택&구현하게 한다.**

  - 상대적으로 더 외부에 있는 Director에게 
    - 내부에 들어갈 로직 구현해서 결정해서 들어가게 한다.
    - **클라이언트 쪽, 서비스를 이용하는 쪽 = 더 훌륭하고 돈 많은, 직위가 높은사람 = `구체적인 상황의 분기를, 바깥ㅔ 직위가 높은 사람에게 맡긴다.`**
  - **클라이언트방향에 책임이 떡질까 걱정하지마라.**
  - 역할->책임 모델에 의해 **if분기를 외부로 돌려 선택이 필요한 부분만 직위가 높은 사람에게 맡길 뿐이다.**

- 여러형을 받을 수 있는 중간추상층 FrontEnd와 BackEnd는 `이용하는 직위가 높은 외부의 Director`가 **선택 및 구현하면서 이용(객체 생성)한다.**

  - 형을 결정하면, `그 형마다 instanceof로 개별구현해야만 했던 로직`만 익명클래스에서 구현한다.

  ![image-20220207150327852](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207150327852.png)

- 아직 DIrector도 instanceof를 가지고 있다.

  - **이 때, 2개 이상의 instanceof는 if제거 방법을 써야한다(제네릭X)**

  - 의존관계 역전은 되나? 

    - **Director는 1에 해당하고 paper가 N이다.**
      - **`서로 아는 관계가 아닐 땐, 의존관계 역전은 생각하지말고, 최대한 구상체들 단독일만 뽑아서 추상화`하자.**
    - Director가 하던 일을 paper.시키기(this)로 시킬 수 있나?
      - **paper구상체들을 추상화하는 것 자체가 -> paper.전략메서드()의 시키는 작업이 되므로 의존관계는 생각할 필요 없다.**

  - **의존관계 역전은, `Director내부에서 director의 context를 사용하는 경우`만 넘겨서 준다.**

    - 하지만 Paper구상체들이 하는 일 가운데 **director의 내부정보를 이용하진 않는다.( 추상화시 메서드 인자로 넘어가진 않는다)**

  - **서로를 알아야할 때는 의존관계 역전을 하지만, `1:N`은 그냥 `if`의 문제로 보고 `구상체들을 추상화하여 if문 처리`만 하면 된다.**

    - **추상화시 director의 context가 안엮이는 부분 = 구상체 단독의 일만 추상화하여, `서로 아는 관계 = 추상화지 인자로 넘겨주는 관계`가 안되도록 하자.** 

    ![image-20220207172903526](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207172903526.png)
    ![image-20220207192138807](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207192138807.png)

- director내부에서 하던 일들을 추상화 이후에, **구상체들 역시 중간추상층화하여, `이를 이용하는곳 = 외부`에서 개별구현을 선택해서 `case에 맞게 구현`하도록 결정권을 넘긴다.**

  ![image-20220207192718902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207192718902.png)

  ![image-20220207200718479](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207200718479.png)



- **익명클래스는 바로 자식인데, 내부필드에 받는 것을 setter로 할 필요 없어진다.** 

  - protected 수준으로 필드를 만들고, 할당만 하자. 자식인데!

  ![image-20220207214406492](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207214406492.png)



- 만약 Director가 추상형이었다면, N:M으로서 제네릭을 통해 instanceof를 제거했어야함.
  - 사용하는 쪽의 구상형 -> 사용되는 놈의 특정형을 알도록





##### 정리

1.  **추상체vs추상체라면, N에게 물어보더라도 , 1에게는 시키지 않아야하는 구나**
    - **N(programmer)에서 1(Paper)를 받은 상태에서  `1:1`관계를 유지한체로 다른처리(`제네릭`)가 되어야하는구나.**
2.  **추상체vs추상체에서 instanceof를 제거하는 방법은 `제네릭`을 써야하는 구나**





#### 12 도메인분해 와 분해 3가지 방법(FLOW, ADT, OOP)

- object 7장 정리



- 조립(composition): 하나하나 의미가 있는 부속들을 모으는 것
  - 컴포지션 되었다 -> 모듈별로 격리가 잘 되었다.
- 분해: decomposition
  - 1개의 도메인을 **여러개의 의미있는 모듈로 격리**
    - 나중에는 다시 composition시켜서 소프트웨어를 만들 것임
- **목표: 도메인을 어떻게 잘 디컴포지션 할 것인가**





- 도메인은 시간 순으로 만들어지기 때문에 `비논리적 결합`이 된 상태로 만들어진다.
  - **논리적으로 디컴포지션시켜야한다.**
  - 분해를 안하면 **복잡성폭팔=인지과부하**가 되어 이해할 수 없다.
    - 복잡성 폭팔 = 인지과부하의 예
      1. depth 3이상의 if
      2. for안에 if : for자체가 if로서 하나씩 도는 것이나 마찬가지다.
    - **`if의 case들을 쪼개는 것`이 분해의 시작이다.**
      - 하지않으면 복잡성이 폭팔한다.



##### Functional decomposition

- **시작과 끝이 있는 flow**를 따라가며 **분해**함

  - 그러나 **변화가 생긴다면 flow는 취약해진다.**

  - 예를 들어, **중간에 데이터가 추가**되면 -> flow는 흐름이므로 **시작부터 미리 있었어야했다.** -> **첨부터 다 수저해야한다.**

    - 이것을 피하려면 scope(지역변수, 메서드인자, 중괄호)를 이용한 격리를 써야한다.
    - 피했지만,  이후에 또 새로운 데이터가 발견된다면 -> 격리된 로직을 포함해서 첨부터 다시 수정해야한다.

    ![image-20220213120642747](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213120642747.png)

  - **사람은 `중간에 추가될 데이터/변동사항을 처음부터 알 수 없으니 flow방법으로 분해하면서 짜는 프로그램은 좋지 않다`.**





##### ADT(Abstract Data Type)

- 데이터를 바라보면서 기능을 만든다.

- 데이터마다 기능들을 만들다보니, 비슷한 기능들이 눈에 들어온다.

  ![image-20220213122520921](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213122520921.png)

- **기능들을 추상화**하고, 조금씩 다른 데이터만 처리해주면 된다.

  - 그러나 **조금씩 다른 데이터 때문에 if가 발생한다.**

  ![image-20220213123510152](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213123510152.png)

  - if로 모든 기능을 한 군데 모으면 응집성이 좋아진다.

- **문제는 ADT는 `내부if를 품고 있기 때문에 데이터 추가(확장)`시 취약하다**

  - 작업량이 엄청나게 늘어난다.

    ![image-20220213133631772](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213133631772.png)

  - 또한, **기능의 추가가 특정데이터에만 발생(LSP에서 d())한다면, `나머지 데이터들은 null외 예외값을 내놓아야한다`**

    ![image-20220213134033368](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213134033368.png)

- **ADT가 성립하는 경우는 2가지를 만족해야한다.**
  1. (변수, **데이터**)가 더이상 **확장되지 않고**
  2. 안에 소속된 메소드들이 **모든 상태들에 대해 결과값을 가지는 메소드**일 때만





- **우리는 대부분 ADT 데이터를 보고 코드를 짜기 때문에, `구상체의 확장 + 기능의 추가`에 대비하지 못하는 코드가 된다.**
  - 대비하려고 instanceof를 쓰게 된다.
  - **그러나 `내부 if를 머금은 ADT타입의 코딩은 짜면 안된다.`**





##### OOP

- **상태값(데이터)과 무관하게  `기능만 추상화`**를 먼저하고, 

  - **상속된 자식들이 알아서 데이터 + 기능을 구현하는 방식**

    ![image-20220213141222577](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213141222577.png)

- **`데이터 없이 기능만 추상화`하면, `구상체(형)의 확장이 자유롭다`**

  - **데이터로 인한 if가 없기 때문에**

  ![image-20220213141459887](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213141459887.png)



- **추상체의 메서드만 호출하면, `알아서 range binding된 구상체의 메서드`가 호출된다.**

- **`if로 발생하는 복잡성 폭팔을 방지`**한다.

  - 2중 if -> **전략객체 (필드) 안에 전략객체(총 2개)를 가져  대체함.**
  - 전부 외부에서 조달해주도록 미룬다.
    - **movie가 policy + condition을 전략객체 2층으로 가져간다.** 
      - movice는 전략객체 policy만 안다.(if 1개를 대체한다.)
      - 전략객체 policy는 내부에 추메패턴의 condition객체들을 가지고 있고 내부에서 가진 condition으로 if를 대신한다.
      - main -> conditions -> policy에 꽂아줌 -> movie에 꽂아줌

- OOP의 문제점

  - **기능 d()를 단독 추가해버리면.. 추상화가 깨져 성급한 추상화 상태가 된다.**
  - 이 해결방법은 **다시 한번 추상화 후, 제네릭+구상체들의 중간추상층화 -> 외부에서 익클을 구현해서 인식하게 한다.**

  ![image-20220213153617144](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213153617144.png)





#### 14 합성과 의존 그리고 템플릿메소드 패턴

- object 9장부터 이어지는 `템플릿메서드패턴`이 왜이렇게 복잡하게 일어나며, `의존성`과는 어떤 관계가 있는지 살펴본다.



- 합성과 상속: 둘다 의존성과 관계되는 것
  - 합성: 전략패턴에 사용되는 것
  - 상속: 템플릿 메소드 패턴에서 사용되는 것
  - 객체망을 구성할 때, 객체가 다른 객체를 안다 = 인자로 가져와서 사용(전달X) or 내부에서 생성해서 사용
    - **의존성 없이는 객체망이 생성이 안되므로, `양방향 의존성을 갖지 않게 의존성을 관리`**하는 것이 목표



##### 템플릿메소드패턴

![image-20220219105912066](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219105912066.png)

- 좋은 상속인 이유

  - 공통로직만 물려받고 자식의 개별로직은 개별구현함
  - **부모가 변해도 ->** 자식들은 공통로직만 변하는 것으로서 변한 것을 그대로 물려받으며 **부모코드를 직접적으로 내부에서 사용하질 않음.**
    - **자식마다 다른 내용은 `훅메서드로 추상화`되어있어서** -> **자식만의 로직은 `부모의 변화에 격리된 상태`**
    - **자식들은 class구현시,  부모가 물려준 내용을 사용하지X. 갖다쓰기만 함. `내부에서 인자로 받아 사용하거나 생성하지 않음`**
      - **`내부에서 사용안한다? 의존성X`**

- 상속이 문제를 일으키는 이유

  - 여러자식들이  -> 하나의 부모를 의존(알고서 사용)하고 있기 때문

- **추상클래스-템플릿메소드를 통한 상속은 괜찮은 이유**

  - **`부모가 자식(의 필수인 훅메서드 = 추상메서드)을 알고` 사용한다**
  - **부모가 자식의 `추상메서드만 알고 사용한다면, 의존성은 추상체를 가진 부모에게 넘어가는 의존성이 역전`된다. **
    - 부모가 자식들의 **세부구현 모름.  = 세부구현과 격리 = 부모를 수정해도 자식에게 여파가 안감** 

  ![image-20220219113941060](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219113941060.png)

  - 자식내부는 **부모의 어떤 것도(필드, 메서드)알지도 못하고, 사용도 안한다. = 부모에 대한 의존성이 없다**

  ![image-20220219115515368](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219115515368.png)

  - **왜 부모 -> 자식으로 여파가 없을까?**

    - 자식이 부모를 알고(인자 or 내부생성) 사용하지 **못하는 이유**

    1. 부모는 private 필드를 사용한다. (자식이 알지 못한다)
    2. 부모는 템플릿메소드만 public이다 -> 자식뿐만 아니라 의존성 가지는 놈들이라면 누구든지 사용할 수 있다.
    3. 만약, 자식도 받아쓰는 public을 수정했다면? 여파아닌가?
       - **자식만의 여파가 아니라 기능을 사용하는 모든 외부에게 여파라서 상속의 문제가 아니다.**

  - **`public템플릿메소드 모두 private으로 만든 부모`는 ->  자식에게 여파주는 일이 없다.**

- **반대로 부모는 자식을 알아야햐한다. by 추상메서드**

  - 자식이 개별구현하는 훅메서드는 **부모가 알고서 사용할 수 있게끔 proected abstract수준으로만 통신한다.**

- **자식이 부모의 것을 알고서 쓰는 순간 -> 여파가 전에지기 때문에**

  - **`템플릿메소드, 훅메서드`외에 `다른 부모의 메서드를 받아쓰지마라`**
  - **부모의 필드를 private이 아닌`protected/public 필드가 있어도 자식은 쓰지마라`**
    - 부모의 public 템플릿메소드 외는 어느것도 자식이 받아선 안된다.
    - 필요하면 자식 스스로 만들어서 써라.

- **객체망에서는 `부모` <-> `자식`은 서로 다른 객체일 뿐이며 `약속된 훅메서드로만 소통`하는 객체망이다. **

  - **상속의 모양만 갖추었지, `부모의 속성과 메서드는 아무것도 쓰지마라`**
  - **이미 부모 -> 자식의 훅메서드를 추상화해서 사용 -> `자식이 쓰면 양방향이 된다`**







#### 15 Theater with 템메 to 전략패턴 (blog 링크 걸기)





#### 16 전략객체의 생성사용패턴적용을 위한 팩토리

- **`객체생성코드`와 `객체 사용코드`를 `분리해서 관리`하는 것이 `유지보수`에 더 좋다.**
  - **코드 사용주기**의 차이점
    - 객체생성코드는 1번만 만들고 끝난다.
    - 객체사용코드는 계속 사용된다.
  - **코드사용 타이밍**의 차이점
    - 객체생성코드는 지금 써서 만들었지만
    - 객체사용코드는 한참 뒤에 객체가 사용될 수  있다.



- 객체생성 코드와 객체사용 코드를 나누는 법
  ![image-20220220115144457](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220115144457.png)
  1. 생성코드는 client쪽으로 밀어라
  2. 사용코드는 service쪽으로 가져고 들어오라
  3. **Client쪽에서 생성한 객체를 `주입`을 통해 service에서 사용해라**



- 생성과사용을 동시에 나열한 나의 떡진 코드는
  - 일부를 형(type, class)으로 바꿔서, 생성따로 하고, 이용해야한다.





- **전략객체는 `외부생성` -> `생성자를 통한 주입` -> `내부 사용`의 생성사용패턴이 적용된 패턴이다.** 
  ![image-20220220123326297](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220123326297.png)
  - **하지만, DiscountPolicy입장에서는 `강제로 pushed당한 것`이다.**
    - **생성자주입은 `받아서 사용하는 쪽이 제어권을 잃은 상태`**
- 내부의 주인인 DIscountPolicy가 **원할 때 <외부생성> 전략객체가 pull**되어야한다.



##### 제어권 역전을 위한 생성자주입 전략패턴에 Factory 달기

![image-20220220152122531](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220152122531.png)

1. 전략패턴처럼 1개의 오퍼레이터를 가진 **전략객체Factory 인터페이스** 만들기

   - **전략객체 자체를 pushed당하지말고, `전략객체Factory를 pushed`당한 상태로 만들 예정**이다.
     - **이렇게 구성하면, `policy가 원할 때 Factory를 통해 전략객체를 주입`받게 된다.**
   - **Factory만 미리 알고 있은 체, 내가 원할 때 Factory를 통해서 pull하여 주입받기**
     - 전략패턴 적용처에 **주기기능(getter)로서 전략객체를 반환**하도록 한다.

2. **매 전략객체의 종류마다 구상Facotry**를 만들어야한다.

   - 재료인 Money는 생성자로 미리 받아야하지만
   - 개별전략객체의 공급은 getter로 받아오게 한다.
   - **추가적으로 synchronized를 걸어서, 멀티스레드에 대처하며, `cache`를 적용한체, 없으면 생성 있으면 그냥 반환한다.**
     1. **생성시 cache 적용할  객체를 private 변수 null초기화하여 선언**
     2. getter에서는 **없으면 ( == null(초기값)) 생성하여, 변수에 할당한 뒤(cache에 넣기)**
     3. **반환은 무조건 cache객체로**

   ![image-20220220152959772](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220152959772.png)

   - **이렇게 하면, 미리 생성된 전략객체를 받는 게 아니라, Factory가 없으면 생산, 미리 생산되어있으면, 그냥 가지고만 오는 cache를 이용해서 가져온다.**

   ![image-20220220161243400](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220161243400.png)

   - 이렇게 **Factory를 통해 원할 때  & caching 적용하여 생성하는 것을 `Lazy pulled`라고 한다.**

3. Factory가 pushed된 상태면 **Lazy pulled** 할 수 있게 된다. **하지만, 그대로 사용하면, `디미터법칙의 위반(최소지식=1차원관계=직접적으로 아는 놈만 쓰자)`이 발생한다.**
   ![image-20220220161402094](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220161402094.png)

   - `supplier.getCalculator()`를 통해 **포인터의 포인터라 현시점에서 생성되어 메모리에 올라간 개별전략객체를 얻는다.( 그전까지는 메모리에X)**
     - **DiscountPolicy입장에서는, 어느전략객체가 왔는지 모른다.**
     - **또한, `포인터의 포인터`라서 `supplier(Factory)`에 다른 전략객체Factory가 왔어도 왔어도 `업데이트된 Calculator`를 받을 수 있게 된다.**
       - 혹시나 runtime상에서 바뀐 , 업데이트된 Calculator된 것을 받게 된다.
   - Factory의 마법으로 **사용처는 무슨 개별전략객체를 가져왔는지 모르게**할 수 있지만,
     - **원래부터 추상체(전략인터페이스)를 인자로 받았기 때문에, 원래도 몰랐었다.ㅋ**



##### 위임된 Factory로 일반Factory의 문제점() (`.`)getter 후  (`.`) 체이닝해서 사용) 해결

- 이렇게 사용하면 디미터법칙위반으로서, **열차전복사고**가 생긴다.

  ![image-20220220164101216](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220164101216.png)

- **`클래스는 원래 field에 있는 지식(+인자/지역변수까지)만 알아야`한다.**

  - 즉, `.` 한번만 이용해서 사용하며, **포인터의 포인터를 다시 사용해서는 안된다.**
    - 사용가능한 지식: 필드 / 필드들의 형 / 자기가 만든 객체 / 인자로 넘어온 객체 / 지역변수들
  - **`Factory가 getter로 단순객체만 return하는 상황`이면, `무조건 디미터법칙을 위반`한다.**
    - 단순 객체 return하는 팩토리는 `팩토리.getter() ` .메서드()를 사용하니까..



- **Factory의 디미터 위반 해결 방법 2가지**

  1. 사용처class가 `Factory가 getter로 return하는 객체도, field or 지역변수 미리 안다.`

     - **getter로 받은 것을 `체이닝없이 지역변수로 선언해서 받아` 쓰기**

     - **getter를 지역변수로 받아도, 응답객체에 대한 의존성이 생긴다.**

       ![image-20220625232920167](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220625232920167.png)

     - **이렇게 되면, (`사실 이렇게 Factory를 유지하면`)순환참조가 생긴다?**

       ![image-20220220165139585](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220165139585.png)

       - 일반적으로 전략객체를 생성하는 **전략Factory를 `simple Factory`로  구상체들과 함께 회전풍차의 순환참조가 생길 수 밖에 없다.**
         - 디미터법칙과 별개로 **추상체에 대한 심플팩토리는 `회전풍차(Factory Circulation)` 때문에  사용하면 안된다.**
           ![image-20220626000017898](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626000017898.png)



- 위임된 팩토리로 **지역변수 없는 체이닝 simple Factory의 전복사고 해결하기**

  - **사용처DiscountPolicy는** Factory객체안에 **구상Factory를 가지므로, 아래와 같이 구현하면, factory인터페이스는 개별생성 from 구상체내부 + 개별전략메서드 from 추상체구현** 형태를 가질 수 있다.

  1. **Factory가 `전략메서드(추상메서드)를 위임`받고, 생성의 책임을 없앤다.**

     - 사용처에서는 **factory.전략메서드()**로 바로 호출가능해진다.
       ![image-20220220173329333](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220173329333.png)

  2. **`Factory의 구현체(구상Factory)들만` LazyPull(cache적용된 전략객체)가능하도록 `생성의 책임`을 가지며 + `전략메서드의 개별구현 안에서 <내부 생성책임이용해서 전략객체 생성> .전략메서드() 호출`을 동시에 한다**

     - **전략객체getter가 더이상 추상메서드의 구현(@Override)가 아니라, 구상class의 instance method로서 자신의 것으로 구현한다.**
     - **factory안에 들어있는 개별구상Factory가 `내부에서 자신의 전략객체 생성메서드호출 개별구현된 전략메서드()` 호출까지 담당한다.**

     ![image-20220220174409904](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220174409904.png)

  3. 사용처(DiscountPolicy)는 Factory만 알고서 **factory.전략메서드()**호출로 정의해서 끝난다.

     ![image-20220220180205155](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220180205155.png)





##### 위임된Factory는 알고봤더니 전략인터페이스??

- **전략메서드가 위임**된 팩토리만이 factory의 디미터법칙위반을 해결한다.

  ![image-20220626011450717](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626011450717.png)

  ![image-20220626011433141](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626011433141.png)

  

- **근데, 전략메서드를 사용하는 위임된 Factory의 책임이 이상하다.**

  - DiscountPolicy내부에서는 **Factory인데, 전략객체처럼 사용된다.**

  ![image-20220220180205155](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220180205155.png)

  - 전략Factory는 인터페이스명을 제외하고, **`전략인터페이스와 완전히 동일하다`.**
    - 실제로 **구상Factory가 전략객체만 imple해서 구현해도 동일하다.**

  ![image-20220626012343087](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626012343087.png)

  - **구상Factory들은 전략Factory를 삭제하고, 전략인터페이스를 구현해도 똑같다.** 

  ![image-20220220180538421](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220180538421.png)



- 전략객체 주입대신, 전략Factory를 주입했다가, **다시 전략객체를 주입(`전략 인터페이스를 변수로 받는`)하는 형태가 되었다.**

  - **형태는 전략 인터페이스를 받아 `전략객체를 주입하는 것 처럼 보이는` 형태지만**
  - **개별 전략객체의 `구상Factory`들 역시 `전략인터페이의 구상체`이며, `외부에서는 구상Factory`를 실제로 주입한다.**

  ![image-20220220181021716](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181021716.png)
  - **변수명을 factory로 바꿔준다.**

  ![image-20220626123923829](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626123923829.png)

- **위임된 전략Factory -> 위임된 구상Factory -> 전략Factory삭제**의 과정에서

  - **`Factory circulation`(회전풍차)가 사라지게 된다.**

    - 기존
      ![image-20220220181056735](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181056735.png)

    - 위임된 후 추상Factory 삭제

      ![image-20220220181102383](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181102383.png)

  - **보기에는 삼각형의 순환구조 같지만**

    - **생성된 전략객체는 Factory의 전략메서드 내용을 실질적으로 담당하기 때문에 `순환표에서 구상Factory는 삭제하여 생각`해도 된다.**

      ![image-20220220181742994](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181742994.png)

      



##### 위임된 구상Factory에서 trigger전략객체 받아와 제공해주는 책임 위임

- 현재 DiscountPolicy는
  - policy 적용 전략객체 -> 전략인터페이스를 구상한**구상factory**가 전략객체 생성 후 전략메서드 적용
  - **condition 조건 전략객체들 -> 외부에서 넣어주면, 받아서 저장**
    - **전략객체 condition의 의존성을 DiscountPolicy가 가진다.**
  
- **변경 하고싶은 DiscountPolicy**
  ![image-20220220182921161](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220182921161.png)
  
  - **정책조건 전략객체들의 생성**까지 **정책 구상Factory 내부로 위임**하고 싶다.
    - **구상Factory가 정책전략 + `정책조건전략`까지 전략객체 2개를 담당**하도록 하고 싶다.
    - Factory가 **의존성 2개**를 담당(생성+사용, **받기**)
      - **Factory에 의존성 폭발을 유발**
  
  ![image-20220220183112073](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220183112073.png)
  
  - 전략패턴의 의존성 폭발을 일으키도록(**의존성 1개 추가 -> 전략메서드가 2개**)
    - DiscountPolicy(사용처)가 저장만 해서 알던 의존성 Factory가 추가로 알게 됨.
  
- **전략객체를 1개 더 받아들이고, 전략메서드를 1개 더 추가하는 `전략패턴의 의존성폭발`을 어떻게 유발할까??**

  - 기존 DiscountPolicy가 담당하던
    - **전략객체 setter로 받기** ( 생성이후에 선택해서 받음. 여러개 받을 경우, default 빈컬렉션 -> add메서드 )
    - **저장한 필드**를 내부에  **(this.)필드**로 제공
      - **내부에서 필드사용 -> `외부로 옮긴다면, getter`가 된다.**

  ![image-20220626135743709](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626135743709.png)

  - **로직을 옮기려면, 받기기능 + 제공기능을 다 옮겨가야한다.**
  - **`받기기능은 내수용으로서 class라면 어디든지 내부에서 구현 가능`하지만, `제공기능은 getter로서 외부에서 사용`하는 메서드라서 `추상메서드`로 제공 -> 구상체들 개별구현 해야한다.**

1. **현재 위임된 구상Factory는 1)내부전략객체 생성 + 2) 전략메서드호출**를 시행하며 **기존`전략 인터페이스에서만 구상`된 상태다.**

   - Factory로만 다 로직을 옮겨가면, Calculator 인터페이스 변수를 그대로 사용해도 된다.
     - **하지만, Factory에 `옮겨가서 getter로 제공되어야할 새 기능 추가`가 필요하다**
     - **이것을 기존 전략인터페이스에 위임하면, 메서드2개의 인터페이스가 되어버린다.**
       - 1개 인터페이스에 2개의 추상메서드??

   - **이 전략인터페이스 <-> 구상Factory 사이에 `중간추상층(인터페이스)을 추가 `하여 `인터페이스당 1개의 메서드만 제공`하게 한다.**

     - 최종 구상층은 기존 전략메서드 + 중간층에 추가된 추상메서드 1개를 다 구현해야해서 **2개의 추상메서드를 구현해야함.** **이는 문제가 안된다?!**

   - **어차피 최종에서는 상위추상층의 기능을 구현하니, `바로 위 추상층`만 `주입용 인터페이스 변수`로 바꾸면 된다.**

     - 어차피 **주입되는 것은 외부에서 최종구상층이 생성되어 주입되며, 중간추상층이 그것들을 다 포용할 수 있을 것**이다.
     - **중간추상층에서도 추상메서드를 1개 추가한다.**
       - **제공기능은 이렇게 구현하게 하더라도, `받기기능`은 어디서?**
       - `제공기능`이 전략(추상메서드)일 때, **`내수용 받기기능`은 추상층(인터페이스)가 아닌 `개별 구상체에서 구현`할 수 있다.**

     ![image-20220626141302913](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626141302913.png)

2. **이제 전략인터페이스 대신 `최종구상층 직전의 추상층인 중간추상층 PolicyFactory`를 주입받는 추상체 변수로 둔다.**

   ![image-20220220184244911](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220184244911.png)

   - **만약 제공기능이 DiscountCondition의 추상체를 가진 것이 아니라, 다양한 type을 제공해야한다면, 그만큼  중간추상층이 늘어나서, 조합이 폭발된다.**

   - **최종구상층인 구상Factory가** 2개의 기능을 다 제공한다.

     - 기존 전략객체생성후 전략메서드호출 기능
     - 추가 trigger전략객체 제공메서드 기능

     ![image-20220220183112073](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220183112073.png)



3. **구상Factory는 `2개의 전략 메서드를 구현` +  그 중 제공기능을 위해 `타 전략객체 받기기능을 내수용으로 자체 구현`한다.**

   - **2번째 전략객체는, `구상class에서 주입`받는다는 것이 원칙이다.**
   - **2번째 전략객체의 기능을 도입하려면**
     1. 전략객체를 받기기능은 구상Class에서
     2. 받은 전략객체의 기능제공은 **중간추상층을 추가해서, 구상층이 다 가질 수 있게**

   ![image-20220220185128201](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220185128201.png)

   



4. 생각 정리하기

   - **`conditions`의 상태**가, 원래는 사용처 `DiscountPolicy`에 편하게 있다가 ->**`Factory`가 공급받는 식**으로 바뀌어서 -> **`구상Factory`로 이동** 
   - VO가 아니라 포장만 해서 그런지, 내수용 받기기능 외에 `내수용 add/remove기능`도 구상Factory에 정의해줘야한다.
   - 구상Factory의 주 목적인 getter `getConditions`를 제공한다. 
   - 이제 Factory의 구상Factory마다 수출품은 2개가 된다. 
     - Calculator -> 직접 getter로 제공하지 않고, 메소드위임받아서 결과값을 주고
     - conditions(set) -> getter로 직접 제공
       - **2개 이상 수출품을 가지는 Factory가 `추상 팩토리메서드 패턴`의 대상들이다.**
   - DiscountPolicy -> 구상Factory로 몰빵된 의존성 -> 
     - **`최종구상층`인 구상Factory를 `class로 계속 찍어내는 만큼`**, `알고리즘(if , if의 조합)`을 바꿀 수 있게 된 것이다.
       - **안에서 if로 처리하던 것을 class로 바깥으로 밀어낸 뒤, 바깥에서 공급** 받을 수 있게 된 것
       - 생성사용패턴으로 바꾸게 된 것
       - Factory패턴의 진정한 의미
         - 내 원래 코드 -> 인터페이스로 바꿈 -> 각 case마다 형(class)로 만들고 -> 바깥에서 생성하도록 밀어냄
   - **우리들의 코드에서 `추상팩토리` or `팩토리`가 등장하지 않는 이유:  코드를 형으로 바꿔서 바깥에서 공급받도록 개조하지 않았기 때문 -> 형을 통해서만 공급받아야한다.**
     - **그냥 주입하는게 아니라 `Factory`를 통해 주입 -> `lazy Pull 땡기는 타이밍을 정할 수 `있게 되었다.**

   



##### factory가 trigger제공 + action 기능을 각각 제공하다보니 디미터법칙 위반

![image-20220220201355471](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220201355471.png)

- factory만의 로직인데, **DiscountPolicy**에서 getter로붙 펼처진다.

  - **getter기능을 제공한다면, `디미터법칙`위반을 생각하고 `위임=시키기`를 생각해야한다.**

- **`getter가 for문`에 쓰일  때, 내부적으로 `개별요소들은 의존성이 추가`된다.**

  - **개별 전략객체들은 추상체인 factory만 알고서, 기능만 이용하도록 책임을 위임했는데, 의존성이 추가되는 문제가 발생한다.**
    - **인터페이스가 getter를 추상메서드로 제공한다면, `그 추상메서드 getter를 내수용으로 쓰고, 실제 일을하는 메서드를 default메서드 추가 정의`해서 바꿔야한다.**
    - **default메서드는 사용처에서 어차피 추상체변수가 쓰게 되어 구상층에서는 생각하지말자.**

  ![image-20220626191720195](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626191720195.png)



- 디미터법칙위반( `점2개` or `점1개 + for문`)는 **위임시켜서 -> 앞에 .이 getter일 가능성이 높고, 그 것을 마지막 . 메서드 안에 포함시켜지도록** 변경해야한다.

  - **기존에 위반은 객체.getA().B()였는데**

    - **B()내부에 넣어서 해결 **

  - **현재는 객체.get`복수객체들`() + for문 .B()**을 상태다.

    - **DiscountPolicy는 몰라도 되는 부분 `하늘색박스` 전체를 factory내부로 가져가야한다.**

    ![image-20220220201438499](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220201438499.png)

- **get복수객체들() + for문까지를 B() 내부로???**

  - **추상체 변수가 하는 공통의 일은 -> `구상class가 아닌 inteface`로 올려야한다. `default메서드를 이용하면, 일반method를 인터페이스에 정의`할 수 있다.**
  - 현재 중간추상층 PolicyFactory factory가 하는 일이므로 **중간추상층, 인터페이스 변수로 사용되는 추상층**에 **B()에 해당하는 calculateFee()**를 default메소드로 정의한다.

  ![image-20220220201633329](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220201633329.png)

  - **getter는 추상메서드지만, 내수용으로 사용되게 된다.**
  - **`추가되는 default메서드는 어차피 추상체변수가 사용되게 되니, 구상쪽에서는생각안한다`**

- DiscountPolicy내부에서는 getter가 없어지고, 복수의 객체를 for문 속 지역변수로 받아 의존성추가가 안되어도 된다.

  ![image-20220220203241684](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220203241684.png)



##### 최종 전략패턴이 적용된 DiscountPolicy 살펴보기

![image-20220220203241684](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220203241684.png)

1. **생성로직이 관여**되어있나? 
   - 전략패턴이라도, 생성/사용 코드는 따로 존재하도록 **위임된 factory를 적용해  lazy pull**되게 한다.
2. 중복로직이 있나?
3. **전략객체를 2개이상 소유**하고 있나? 혹은 엉뚱한 곳에서 **몰라도 되는, 지역 의존성 by getter 후메서드or getter+for문**이 사용되나
   - 전략패턴은, 개별구현로직마다 **의존성하고 있는 전략객체가 늘어나는 의존성 폭발의 문제점**
   - **전략인터페이스의 구조에 중간추상층 with 전략메서드을 추가하는 방식으로 factory에 의존성을 몰아주면, DiscountPolicy는 1개의 전략인터페이스만 알면된다.**



- **전략패턴에 Factory를 사용한 이유**
  1. 전략패턴 사용처에 1개의 전략객체인 factory객체만 알게 한다.
     - 의존성은 factory에서 폭발한다.
     - getter이후 로직들도, factory에게 넘겨 최종결과물만 내게 한다.






##### 왜 전략패턴이 적용된 DiscountPolicy에게 의존성을 낮추려는 보호를 할까?

- **DiscountPolicy**는 누굴 상속하지도 않은 **변하지 않는 class**

  - 설계요령: **변하지 않는 class에다가, 변하는(전략객체 등)을 주입한다.**

    - 계속 변하지 않는 이유: **변하는 부분은 factory와 같은 전략인터페이스가 위임해준다.**

  - **왜 DiscountPolicy가 변하지않는, 보호대상일까?**

    - **포인터의 포인터**에 있어서

      - **뒤쪽은 변하지만 추상체가 충격흡수하는 포인터** : DiscountPolicy 내부 **전략객체를 사용해주는 메서드**

        - Movie에서 policy.`calculateFee()`

          ![image-20220626194346503](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626194346503.png)

        - policy에 따라서 calculateFee방식은 바뀐다.

          - **caclulateFee()내부에는 변하는 전략객체를 사용하는 부분이 내장되어있다.**

          ![image-20220626194547148](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626194547148.png)

      - **앞쪽은 변하지 않는 포인터** : DiscounPolicy - **전략객체를 사용하는 곳이며, `변하는 뒤쪽 포인터(전략메서드)`를 변하지 않고, 굳건하게 앞에서 잡아주는 역할을 해야한다.**

        -  **`변하지 않는놈을 -> 확정포인터`로 기준 코드를 쫘났기 때문에, 내가 알고 있는놈은 `절대 변하지 않게 된다`.**

          - 변하지 않는 사람에게 <-- 가짜 가면을 제공하고 <-- 가면을 계속 바꿔준다.

        - **그래서`안정적으로 포인터의 포인터 안정적으로 사용`하려면, `전략객체를 주입받는/사용하는 discountPolicy 자체는 의존성이 낮게 보호`해야한다.**

          - 의존성이 높아지면, 변화에 여파를 받게 되며,  그러지 않기 위해 **변하지 않는 class를 먼저 만들고(discountPolicy), 변하는 것들(전략객체1,2,3 -> factory 1개로)을 주입**한다
          - 그러기 위해 **여러개의 전략객체에 대해 `factory`에 몰빵시켰다.**

          ![image-20220626194346503](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626194346503.png)

- **`변하는 코드를 짤 때`, `의존성`을 생각해서 상대적으로 짠다.**
  - 내가 어떤 코드A를 수정하는데, 의존하는 객체B가 있더라
    - **변하지 않으면서 의존되는 객체B부터 빨리 확정**지어야 -> **my) 거기에 `전략인페orFactory만`만 걸어놓고** 변하는 코드A를 짤 수 있다.
    - **도움을 받아야하는 객체들 -> 모두 변하지 않게** 만들어버려야한다. by 팩토리로 다 넘기고, 포인터의 포인터로 이용하게
    - **포인터의 포인터(Factory)에게 싹다 넘기자**.
    - **포인터(DiscountPolicy)는 변하지 않으니 -> 이놈을 의존하는 코드들은 (내가 짤 코드 포함) 안전해질 것이다.**
    - 내가 의존하는애들부터 확정시키자.
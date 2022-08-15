---
toc: true
layout: post
title: OBJECT 19 TODO1 작성을 통한 compoiste, visitor패턴 학습
description: object 책을 강의한 코드스핏츠 유튜브 요약

categories: [object]
image: "images/posts/java.png"
---

- 참고 유튜브 : https://www.youtube.com/watch?v=navJTjZlUGk
- 정리본: https://github.com/LenKIM/object-book
- 코드: https://github.com/eternity-oop/object
- 책(목차) : https://wikibook.co.kr/object/



## 디자인패턴 심화 - 컴포짓 + 비지터 패턴



### 프레임웤과 제어 역전

- 책 15장에 관한 내용을 정리한다.
- 이 책의 비밀은 디자인 패턴 중에 `전략패턴, 템플릿메소드패턴, 데코레이터패턴`을 써서 적용하는 것이었다.
  - 데코레이터 패턴 -> 핸드폰 요금 계산
  - 전략<->템플릿 -> 무비씨어터 
- **`우리는 프레임웤에다가` 제어의역전으로 인해 `기능만 공급`**해주고, **`언제 실행`될지는 `라이프사이클이 가지고 있는 프레임웤이 결정`한다.**



- 프레임웤과 라이브러리의 차이는?
  - 프레임웤 : 제어의 역전이 되어있는 것
    - 우리의 코드가 프로세스 제어구문을 가지고 있냐 안가지고 있냐
      - **우리 코드에 제어구문이 없다면? -> 바깥에 있는 것. 나는 visitor로서 라이프싸이클 메서드를 흐름대로 구현하는 중.**
      - **우리는 제어구문이 소비할 함수와 객체만 만드는 것**
    - 예를 들면, for문은 바깥에서 주어짐 -> 우리는 for문에 쓸 함수만 만듦
      - 그렇다면, 우리는 프레임웤을 쓴다면, 함수를 건네주는 것 밖에 할 수 없다.
    - for라는 프레임웤을 만들었다고 가정하자. **우리는 객체만 만들어서 넣어준다.**
      - 이 객체는 initialze, loop, end, **3개의 메서드를 만들라고 프레임웤으로 부터 강제** 받는다.
      - **프레임웤은 제어를 하는데**
        1. 프레임웤이 받은 객체의 initialize메서드를 호출
        2. 프레임웤이 for반복문을 돌리되, 받은 객체의 loop메서드를 호출
        3. 프레임웤이 객체의 end메서드를 호출
      - **우리의 코드에는 `for반복문`이 없어졌고, `라이프사이클에 맞춘 행동(순서대로 호출)`도 없어졌다.**
        - 우리는, **프레임웤이 제어구문을 미리 짜놓고, 거기에 맞춘 객체(메서드)만 전달해준다.**
    - **제어의 역전은,  하나의 역할(인터페이스)만 역전시키지 않고, 보통 `제어의 흐름대로 역전`을 시키기 때문에, `프레임웤은 내부에 라이프사이클`을 가지고 있게 된다.**
      - 스프링을 예를 들면, 우리는 컨트롤러를 만들지만, 스프링 프레임웤은 내부에 라이프사이클인 interceptor를 만들면 before-controller-after를 라이프사이클 순서대로 가지고 있다.
      - **my) 처음부터 코드를 짠다면, 라이프사이클 코드는 그대로 작성해두고, 바뀌는 기능부분만 제어역전시켜 밖으로 빼준다?!**
    - **`우리는 프레임웤에다가` 제어의역전으로 인해 `기능만 공급`**해주고, **`언제 실행`될지는 `라이프사이클이 가지고 있는 프레임웤이 결정`한다.**
      - **my) `프레임웤의 제어의역전`: 제어는 프레임웤이 미리 라이프사이클 순서대로 짜놓아서 우리가 못짜는 역전 현상이 발생하고, 우리는 흐름대로의 역할(기능, 메서드 by객체)만 공급해주니, 우리가 공급한 기능의 호출, 실행 타이밍은 프레임웤에 결정되어있다.**
  - **라이브러리: 제어의역전이 아니므로, 기능을 우리가 언제든 호출할 수 있다.**
    - 프레임웤은 기능을 공급만하고, 호출은 프레임웤이 제어해놓은 순서대로 결정해서 호출하게한다
    - **함수로서 오직 값의 획득만 위임할 수 있다. 조작해서 뭔가를 하는 프로세스는 위임할 수 없다.**



- **우리의 코드도 프레임웤화 되어있어야한다.**
  - 제일 어려운 부분은 알고리즘이다. 우리는 엑셀의 피벗테이블을 바로 짤 수 없다.
    - 2단 for인 것은 알고 있으나, 복잡한 상태와 제어가 관여되고, 분기가 심하다
      - **우리는 1개의 분기를 제거할때마다 전략패턴을 썼었다**
  - **제어가 없는 알고리즘은 없으니 필수적인데, 제어는 1개만 만들고, 나머지는 제어의 혜택을 받게 만들자.**
  - 우리는 라이브러리를 만들어서는, 제어를 위임할 수 없다.
    - **함수로서 오직 값의 획득만 위임할 수 있다. 조작해서 뭔가를 하는 프로세스는 위임할 수 없다.**



- 제어에 대해서 익숙해질려면 코드 연습을 통해서 할 수 밖에 없다.



### 컴포짓패턴: 파일탐색기에서 파일이름으로 검색

- **전략패턴과 데코레이터패턴을 짬뽕한 `컴포지트 패턴`이 책에 소개된다.**

  - `영화`라는 검색키워드를 **아래 트리형태의 구조에 어떻게 다 던질까**
    - 폴더1
      - 파일1
      - 파일2
      - 폴더2
  - **파일도, 폴더도 검색기능을 제공해야하니 `파일과 폴더 모두 searchText()라는 메소드를 공통으로 가지고 있어야한다`**
    - **`파일`이라면 돌면서, `빈 배열에 자기가 해당하면 자신을 담아`준다.**
    - **하지만 `폴더`라면, 자기자신이외에 `자식들까지 순회하면서 해당하면 다 담아줘야한다`**
      - 폴더자신도 재귀적으로 돈다
      - 폴더의 폴더도 재귀로 돈다
  - **여기서 핵심은, 폴더든 파일이든 모두 `searchText()메소드를 공통으로 구현`했다는것이다**

- **`NoDiscountpolicy`를 만들 때, 빈값리턴하는 특이한 클래스를 만들지말고, 아예 discountpolicy를 `top에서부터 상속받지만, discount가 안되는 클래스`를 만들어보자 할 때, `다른 policy들은 discountpolicy를 상속하는 전략객체`이나 `policy전체를 커버하는 애로 바껴서 leaf계열 tree계열로 바뀌게 된다.`**

  - 혹시... 할인 전략객체들 vs 노할인 단일객체를 포괄하는 정책으로 바꾼다는 소리인가?
  - 일단 [참고 깃허브](https://github.com/testrace/book-objects/blob/main/chapter08/src/main/java/movie/ReservationAgency.java)

  ![image-20220721182248739](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721182248739.png)
  ![image-20220725004910802](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725004910802.png)

#### 패턴을 배우는 순서

- 패턴을 배우는 순서
  - 상속 -> 좋은 상속의 템플릿메소드패턴
  - 조합폭발방지 -> 전략패턴
    - **전략패턴의 전략패턴 -> 어댑터 패턴**
  - 전략객체가 많이 나오다보면, **단일요소 객체** vs **컬렉션 객체**를 포괄하는 -> **컴포짓패턴**
    - element vs collection -> 컴포짓 패턴
    - **컴포짓 패턴의 loop를 외부에 위임 -> visitor패턴**
    - element간에 연결 by linkedlist -> 데코레이터 패턴
  - **중간에 멈출 수 없는 한번에 쭉 가는 for문을 써야한다는 문제점 -> 책임사슬 패턴**
  - 나머지들 -> **끝판왕 커맨드 패턴**
    - 지연실행에 대한 일이라 어렵다
    - **해야하는 일을 함수에 담아서 ->  객체로 만들고 -> 모았다가 -> 실행or역카운터실행**



- 1패턴 -> 1객체가 아니라 1객체가 여러가지 패턴을 소유할 수 있음.
  - 분산되어있는 컴포짓패턴 -> 다음장에 바로 **컴포짓패턴의 3가지 역할**을  다 수행하는 컴포짓패턴을 소개함.
  - 디자인패턴의 다이어그램은, 객체의 역할이지 객체가 아니다.
    - **다이어그램 갯수만큼 객체가 나오지 않는다.**
  - 역할별로 1개의 객체로 연습
  - 이후 **모든 역할을 1개의 객체에 몰빵하는 연습**
    - 전략객체이자 실행객체
    - 전략객체이자 상태객체
    - 전략객체 소유하는 객체를 상태객체로 만들기





### TODO 실습(컴포짓패턴)

#### task와 tasks(파일 -> 폴더안에 파일들 구성)



1. taks(할일) 클래스를 만든다.

   1. 기본적으로 title / date (localdatetime) / isComplete(boolean)를 가져야한다.
      - **새로만들어지면 항상 false상태**여야하므로, **false로 필드 초기화**해서 만든다.
      - **나머지(title, date)만 생성자에서 받는다.**
   2. **코드를 짤 때, `기본값이 있는 것들은 생성자에서 받으면 안된다`. 그렇단 얘기는, `기본값 -> 외부에서 받는 정책과는 구분`된다.**
      - 코드의 의도를 잘 읽어내야한다.
   3. **title과 date는 생성자에서 외부로 받는 정책인데, 왜 final을 안쓸까? `중간에 바뀌는 필드라는 의도`**

   ![image-20220721190913603](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721190913603.png)



2. taks라는 어떤 모델을 만들 때, **`상태 변화하는 필드(not final)에 대한 메서드(setter, 그외)를 만들어 행위를 제약`하고, 필드는 `내부에서 알아서 처리되도록 캡슐화`**를 해야한다.

   1. 대표적으로 todo앱에서는 **check를 켰다껐다 밖에 안되므로 `isComplete`에는 setter를 주면 안된다.**
      - **`외부에서 임의의 값`을 받아 상태를 업데이트(저장 복원 등)`setter를 주면 안된다.`**
   2. **my) `boolean으로 제약이 걸린 필드의 받아업데이트`는 `void setter(값)가 아니라 파라미터 없는 void toggle`이다.** 
      - void setter(파라미터) 대신 `제약걸린 받아 저장은 void(받기용!) toggle() 인자가 없이 제약걸린 뒤집기만 함`
   3. 객체는 자신의 상태를 자기가 관리한다는 개념 **state management의 책임**
   4. 그외의 **변하는 필드(not final)**에는  setter를 만든다.

   ![image-20220721191834284](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721191834284.png)

   

   

3. **`Task는 1개의 데이터객체일 뿐이며, Task를 담을 그릇이 필요`하다**

   1. 일급컬렉션 느낌으로 `Tasks`를 만든다.

   2. **하지만, 폴더의 개념으로서 폴더도 이름도 필요하다**

      - tasks폴더도 title을 가지게 된다.

        - **기본값x -> 필드초기화x -> 생성자에서 외부에서 받지만, 변경가능한 not final -> setter가 생긴다 -> `외부(생성자)에서 받고, 변하기도 하는(setter) 필드`는 `생성자에서 재할당 코드를 setter로 덮어 코드중복을 없앤다`**

        ![a38252b6-a0e8-4803-a609-8c9934b33f12](https://raw.githubusercontent.com/is3js/screenshots/main/a38252b6-a0e8-4803-a609-8c9934b33f12.gif)

        ![image-20220721193231452](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721193231452.png)

        ![image-20220721193336501](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721193336501.png)

      - **setter로 title이 들어올 때, 내부에서 데코레이터 글자색정하기+태그달기 등을 할 수 있다.**

        - **그 과정을 생성자에서 받을 때도 반영하기 위해서는 setter로 코드중복을 제거한다.**

   3. **최초 생성자에서 받고 + 그 다음에 변경되는 필드값은 `setter와 생성자에서의 코드 중복을 제거`해야한다. 많은 회사에서 이것때문에 문제가 발행한다.**

      1. **setter할때랑, 최초 생성할때랑 로직이 달라져서**

4. **task들을 가지기 위해 Set컬렉션 필드**를 가진다. 이  필드 역시 **`상태 변화 필드에 대한 메서드로 제약 + 캡슐화`인 `setter(받아 업데이트 기능)`을 만들어줘야한다.**

   1. **컬렉션필드는 변경가능성 없는 것으로보고 즉시 final을 걸어놓자.**
   2. **컬렉션은 void setter대신 `boolean addXXX()`로 만들어야한다.**
      1. 지금은 귀찮으니 void add로 만든다.

   ![image-20220721194130656](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721194130656.png)

   

5. **이 때, `setter/add의 인자 결정으로 2가지 판단`할 수 있다. **
   ![image-20220721215747341](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721215747341.png)

   - 데이터객체task를 받는 메서드가 public인데, **외부에서 `바깥에서 생성된 task` = `외부에 task의 생성자를 공개(내부발행X)` = `바깥에서 마음대로 task를 만들 수 있어`를 받을 것인지를..**

   - **우리는 바깥에서 task를 만들어야할지 말아야할지는 잘 모르겠으나, `task를 바깥에서 만들 수 있게 푸는 순간(public 받기 메서드에서 인자로 들어오는 순간) -> task의 생성자를 함부로 못 고치게 된다.`**

     - **받기 메서드의 인자로 데이터객체를 받는다 = 외부에서 생성된다 = task생성자를 수정할 때, 외부의 모든코드도 수정해야한다 = 상당한 비용을 치루고 task를 수정할 수 있다.**

   - **프로젝트 초창기부터 `수정에 대한 비용이 많이 드는 것을 방지`하려면 `어쩔수 없이 외부에 task를 공개하는 시점직전까진 외부의 task를 인자로 받는 것을 미뤄두고` -> `task생성 필수정보만 받아서 내부발행(생성)`하도록 한다.**

     - **일단 setter의 인자를 객체가 아닌 객체생성에 필요한 정보를 받도록 수정**한다.

       ![e0615a1d-f136-4d86-b08d-5d3071c938f6](https://raw.githubusercontent.com/is3js/screenshots/main/e0615a1d-f136-4d86-b08d-5d3071c938f6.gif)

       ![image-20220721220336318](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721220336318.png)

     - **`Taks의 생성자를 public -> 폴더인 Tasks와 같은 패키지에위치할 것이므로 internal로 수정`한다.**

       ![38f67fd6-1de8-423c-9fe8-77531d07f200](https://raw.githubusercontent.com/is3js/screenshots/main/38f67fd6-1de8-423c-9fe8-77531d07f200.gif)

       ![image-20220721220529124](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721220529124.png)

     - **필요에 의해 외부에  task가 생성되어야하면, 그때 공개**한다.

   - **`Task객체의 생성`을 관리폴더 Tasks내부에서 하게되면 `수정의 여파가, 내부발행처인 Tasks에만 미친다`.**

   - **public class의 `public객체`지만, `생성자는 public이 아닌 것은 당연한 것`이라고 생각해야한다.**
     - 왠만하면 아니여야 정상이다.
     - **`바깥쪽에서 new키워드쓰는 객체는 최소화하는 것이 최고`다**

   - **`remove`는 add(setter)와 다르다.**

     - **받을 때는, `없는 객체에 대해 외부 생성을 최대한 피하고, [같은 package에 존재하며, 그 객체의 추가를 관리하는 폴더]가 필요정보만 받고, 내부발행하여 -> 수정 비용을 줄이`는 것이 반영되지만,**

     - **삭제는 `이미 기존에 생성되어 존재하는 객체`며,  `public class의 public 객체`로서 `외부에 이미 존재`하게 된다.**

     - **`이미 존재해야지만 -> 메서드 인자로 들어와 -> 지워진다.`**

       - **삭제시 존재유무 검사**를 **안해도 된다.**

     - 원시형으로 지우는 것이 더 웃기게 된다.

     - **Set -> 객체는 메모리주소 = 값x = 객체  = id로 식별로 식별되며, 삭제시에는 `이미 존재하는 public객체이므로, 인자로 받아도 된다`**

     - **add는 `생성자를 외부에 감추기 위해 원시형으로 받았지만`, remove는 `지울때는 반듯이 객체를 통해 지워야한다. 값으로는 객체 식별이 안되기 때문이다.`**

       - **값이 주어지면, 같은 title을 가지는 다른 객체가 지워질 수도 있다(unique보장이 안된 필드이므로)**

       ![0fe3de5e-9359-4ea5-9ad9-cba0cfcb1738](https://raw.githubusercontent.com/is3js/screenshots/main/0fe3de5e-9359-4ea5-9ad9-cba0cfcb1738.gif)

       ![image-20220721222252230](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721222252230.png)

   - **`지우는 것은 이미 바깥에서 존재하는 객체를 지우는 것`이다. 내부 정보를 가지고 발행하는 정보전문가의 상황이 아니더라도, `컬렉션으로 관리하는 데이터객체`를 `받는 메서드`를 정의할  때, `일단은 필요정보만 인자로 받고,  내부발행으로 시작`하여, `외부에서생성로직의 수정시 드는 비용을 절감`하기 위해 내부발행으로 시작한다.**

     



6. tasks만 가지고 있는 task set을 **외부에 getter로 공개해야지만, 렌더링이 될 수 있을 것이다.**

   - **이 때, 컬렉션을 외부에 제공할 때는, `우리가 짜는 코드는 메모리 걱정을 안해도 되니, 사본정책을 쓴다`**
   - **컬렉션은 외부에 주면 `외부 조작가능성`이 있다.**
     - 누군가 반드시 한다고 생각한다.
   - **원시값이 아닌 이상 `컬렉션 뿐만 아니라 객체필드 역시 사본`으로 제공해줘야한다.**
     - **객체를 주면, `외부에서 객체메서드를 통해, 객체의 상태를 조작`할 가능성이 있다.**
     - **`컬렉션`을 주면, `컬렉션의 메서드로 통해 순서나 요소들을 조작`할 수 있다**
       - **Set의 add/remove를 막을 수 있는 방법은 없다**

   - **이 때도 2가지 판단을 할 수 있다.**

   - **사본 중에도`깊은 복사/얕은 복사` 둘중에 뭐를 해줄지 어떻게 판단할까?**

     - **`반환되는 객체/컬렉션에 대해 [원래 객체의 메서드들이 public으로 외부에 공개되어있던 놈이라면] -> 얕은 복사를 해준다.`**

       - **원래 task객체는, `외부에서 조작할 수 있는 public을 가지는 놈`이라서 얕은복사를 해준다.**

       ![image-20220721223453859](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721223453859.png)

       - public은 원래 외부에서 조작하는 것이 허가된 메서드

     - **`만약, task객체가 외부에서 조작불가능한 메서드를 가지고 있었다면 -> [외부에서 조작하는 것이 겁나므로] -> [깊은 복사]`**를 해주면 된다.

   - **따라서, `컬렉션이라도 내부 객체 task는 원래 외부조작 가능`하니 `컬렉션을 깊은 복사할 필요없다. 얕은 복사만` 해주면 된다.**

     ![image-20220721224502412](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721224502412.png)

     

     ![image-20220721224746784](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721224746784.png)

     ![image-20220721225108795](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721225108795.png)

   - **`Tasks의 상태인 컬렉션 필드는 건들이면 안되니 무조건 사본`을  줘야하는데**

     - **외부에서 컬렉션사본을 받았지만, 그 사본과 내부컬렉션 필드 속 `요소는 여전히 연결될 수 있다`**
       - **`내부 상태와 연결된 요소들은 외부에서 조작해도 상관없다`면, `얕은복사`**
       - **`연결된 내부 요소들을 조작해선 안된다면, 깊은 복사`를 내어준다.**

7. **task의 제공은 `순서를 가진 set이 아닌 list`로 반환해줘야한다.**

   - task는 **date순서대로 오름/내림차순 되어야하므로 `Set으로 식별한 객체들이지만, 외부에 제공은 List로`해준다.**

     ![image-20220721231901782](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721231901782.png)

     

8. **`내부관리 Set대신 순서적용이 가능한 list를 반환`한다면, `미리 sort도 해주고 반환`한다.**

   - 보통은 정렬까지해서 반환한다.
   - **이 때, 정렬을 바로 오름차순으로 하지말고, `public외부 제공getter해줄 때, [정렬방법]과 같은 [제공방식을 인자]로 외부에서 받아서 제공해준다`**
     - 객체지향에서 **메세지는 객체로 받으므로 `정렬type도 객체 파라미터`로 받는다.**
     - 객체지향에서 **처리는 `값type의 객체`이므로 `정렬type객체`는 `제한된 종류의 값type을 정의하는 enum`일 것이며, `값으로 췩브해서 처리`될 것이다.**

   ![image-20220721232839728](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721232839728.png)

   



9. **제한된 종류의 값type은 enum(애넘)으로 만든다.**

   - **객체를 정렬할 때, 그 기준은 `해당 객체가 가진 필드`를 기준으로 `오름차순/내림차순`이 있을 것이다.**
   - **`필드의 종류마다 _ASC, _DESC`형식으로 enum필드를 만들어준다.**

   ![image-20220721233318412](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721233318412.png)



10. **객체지향에서는 직접일을 하지않고, `enum필드라도 객체에게 일을 위임`한다.**

    - 값type이라고 **인자를 `위임해줄 객체`가 아니라 `값`으로 본다면? swtich문이 들어가게 된다.**

    - swtich문 == if문이 싫으면 **전략객체로 바꿔주면 된다.**

    - **enum은 싱글톤처럼 인스턴스를 미리 확정지어서 만들어주는 것이다.**

      - **인스턴스를 static초기화 전에, jvm이 만들어주는 인스턴스**이므로 
        - os가 보장하는 인스턴스, thread시작전에 만들어진다.
        - thread에 안전한 인스턴스를 만들 땐, 1개는 싱글톤객체, n개의 인스턴스만들라면 enum으로 만들어야한다
        - 싱글톤객체를 만들 땐, syncronized를 걸어야하는데, enum은 안걸어도 된다.

    - **SortType을 클래스로 보고, enum필드들을 `이미 만들어진 인스턴스`들이라고 보자.**

      - **enum은 내부적으로 `enum필드로 만들어진 인스턴스들`의 `추SortType추상클래스`가 되며, `;`이후에 정의된다.**
        - 공통로직을 정의한 클래스

    - **enum은 ;뒤로는 추상클래스로서 `abstrac 메서드를 정의`할 수 있다.**

      - 이 때, **`정렬을 하려면 Task a와 Task b를 compare`해야한다.**
      - **이렇게 추상메서드를 정의해놓으면, `인스턴스 4개는 추상메서드를 구현해야만 한다`**

      ![image-20220721234333898](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721234333898.png)

      ![image-20220721234526956](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721234526956.png)



11. **enum을 ;아래 추상클래스의 추상메서드를 `구상class없이 익명class로 구현하는 분신술`로 보고 `공통로직 추상메서드`를 구현하도록 한다**

    - **익명클래스를 구현해서 인스턴스를 생성하는 과정과 똑같은 과정인데, `단지, thread문제가 생기기도 전에, jvm에서 만들어주는 것이 다르다`**

    ​	![e94496ce-f610-442a-90dc-46343cb045db](https://raw.githubusercontent.com/is3js/screenshots/main/e94496ce-f610-442a-90dc-46343cb045db.gif)

    ![image-20220722000401513](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722000401513.png)

12. **이렇게 enum객체에 `sort()에 들어갈 인터페이스Comparator(compare 구현 인터페이스)을 위임`한 `전략객체 4개 enum으로 만든 것`이다.**

    - **`전략객체의 전략메서드로 대신했다면, [메서드 1개의 함수형 인터페이스Comparator]가 들어갈 자리에 람다식[ (인자) -> 전략객체.전략메서드(a,b) ]`처럼 ` (인자) -> 추상체Enum의 객체.Enum속(공통)추상메서드()`**를 넣어주면 된다.

    - **[전략패턴과 함수형인터페이스 참고](https://blog.chojaeseong.com/java/lambda/%ED%95%A8%EC%88%98%ED%98%95%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/comparator/predicate/consumer/%EC%9A%B0%ED%85%8C%EC%BD%94/2022/03/27/%EB%9E%8C%EB%8B%A4%EB%A1%9C-%EB%A7%8C%EB%93%A0-%ED%95%A8%ED%98%95(%EB%A1%9C%EC%A7%81-%EB%84%98%EA%B8%B0%EA%B8%B0,-%EC%9C%A0%EC%9A%A9%ED%8C%A8%ED%84%B4).html)**

    - **Comparator는 함수형 인터페이스로서 Consumer의 .accept()나 runnable의 .run()처럼 `내부에서는 함수형인터페이스 추상체.compare(실제인자a,실제인자b); 호출 == 전략객체.전략메서드(); 내부정의`와 `파라미터에선 함수형인터페이스 추상체 파라미터(전략인페-추상체) +  외부 입력인자는 전략메서드구현 구상 전략객체`나 `(가상인자) -> 가상인자로 전략메서드 실시간 구현의 람다식`이 필요하다**
      ![image-20220722135143898](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722135143898.png)

      - 내부정의는 `.sort( Comparator파라미터 )`의 내부에서 **실제인자로 전략객체.전략메서드 호출(실제인자); 이 정의되어있다**

      ![image-20220722133730450](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722133730450.png)
      ![image-20220722133913700](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722133913700.png)

      - 외부에서는 **가상인자를 통한 전략메서드 실시간 구현 람다식을 전략인터페이스 인자 자리에 넣어줘야한다.**


      ![image-20220722001508041](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722001508041.png)

      ![image-20220722001653215](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722001653215.png)

      - **이 때, 람다식+가상인자를 통한 전략메서드compare 실시간 구현에 필요한 것이 외부에서 보내준 정렬방식 값객체이자 compare구현을 위임받은 SortType객체이므로, 해당객체에서 정의된 compare로 전략메서드compare를 구현하게 한다.** 

      ![image-20220722001719038](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722001719038.png)

      ![image-20220722001541250](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722001541250.png)

      ![image-20220722001600364](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722001600364.png)

      ![9e821029-0c1a-445d-9c5c-d709320c300e](https://raw.githubusercontent.com/is3js/screenshots/main/9e821029-0c1a-445d-9c5c-d709320c300e.gif)

    

13. enum의 필드들의 익명클래스 compare들을 구현해준다.

    - **String의비교는 compareTo()로서 `>=< 0없이 비교만`해주면 된다.**
    - 역순이라면 compareTo에 들어가는 인자만 바꾸면 된다.

    ![f57607c4-97fd-46e7-ac8c-7d11366c526a](https://raw.githubusercontent.com/is3js/screenshots/main/f57607c4-97fd-46e7-ac8c-7d11366c526a.gif)

    ![image-20220722002631617](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722002631617.png)



14. **우리는 처리를 위한 값type인 SortType을 enum을 선택하고, enum을 추상클래스로보고 내부 익명클래스로 구현하여, 각 enum객체들이 정렬에 관한 인터페이스를 구현하도록 전략패턴으로 위임했다.**

    - **우리가 class를 만드는 것은 `인스턴스가 n개 이상이야` 끝을 모를 때만 만든다.**
    - **`우리가 인스턴스 갯수가 확정일 땐 class로 만들면 안된다.`**
      - 왜냐면, **다른놈이 해당 class의 인스턴스를 더 만들어낼 것이니까.. 안잠겨있으니까!**
        - 무조건 발생한다 생각한다. 그 **상태가 오류라고 생각한다.**
        - 인스턴스가 1개만 있어야하면 된다면, 싱글톤패턴으로 만든다.
        - 인스턴스가 n개만 있어야한다면, enum으로 만든다.
      - **클래스를 만들기 전에 enum을 통해서 객체의 수량을 한정지어야되는지 결정부터 하고 class를 만드는 것이 java에서 맞는 방법이다.**
    - enum을 쓰면 **수량오류가 안생긴다.**

    



15. 밖에서는 tasks사본을 받게 된다. 그것을 작업을 하든 뭘하든 하면 된다.
16. **지금까지 컴포짓패턴을 쓰려고 `파일`과 `폴더`를 만든 것이다.**



#### 파일과 폴더를 합친 composite객체(CompositeTask) 와 파일+폴더정보를 묶어서 출력할 사본Composite객체(TaskReport)만들기

- todo안에 또 todo들을 만들고, 그안에 또 todo들을 가질 수 있다.
- 업로드시스템도 왼쪽메뉴가 끝없는 tree depth를 가질 수 있다.
- **나는 파일(task)이면서, 그안에 자식들을 가지는 폴더(tasks)가 되게 된다.**

- **컴포지트 패턴은 4가지 구성요소가 있는데, 1class에 4가지를 한꺼번에 구현할 것이다.**



1. **CompositeTask 클래스를 새로 만들고 , task과 tasks의 내장을 다 가져올 예정이다.**

2. CompositeTaks는 기본적으로 Task이므로, **Task의 코드를 다 가져온다.**

   - 이 때, **폴더에서 파일 추가시 `수정비용 최소화를 위해, 폴더객체내부에서 생성을 위해, 생성자를 default`로 걸어놨던 것을 `public`으로다시 바꿔준다.**
     - **왜냐하면, Composite는 더이상 `폴더객체 내부에서 생성되는 파일객체`가 아니라 `파일+폴더역할의 객체`기 때문에**
     - 내장을 긁어올 때, 생성자의 이름은 생성한 class로 다시 바꿔줘야한다.

   ![image-20220722172119598](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722172119598.png)

3. 또한,CompositeTaks는 기본적으로 Tasks(폴더) 역할도 하니, **내장을 다 긁어오되, `task랑 겹치는 필드와 생성자는 빼고` 가져온다.**

   ![2958b17d-c07f-4458-8ad7-bc2ca303e35f](https://raw.githubusercontent.com/is3js/screenshots/main/2958b17d-c07f-4458-8ad7-bc2ca303e35f.gif)

   ![image-20220722172557497](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722172557497.png)

   

4. **간과하기 쉬운 것 -> `생성할때 받고(생성자) + 변하기도(setter) 하는 필드`는 `받을 때 가공처리를 setter에 배정후, setter를 생성자에서 사용`한다.**

   ![image-20220722173110906](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722173110906.png)

5. 현재 CompositeTask는 **task이자, sub_task들을 붙일 수 있게 되었다.**

   - **이제 Tasks(폴더)와 Task는 안쓰게 되었다. **

   - **CompositeTask에 붙인 `Tasks의 내장`속에 사용된 `Task들을 모두 CompoisteTask로 변경`한다.**

     - 함수형인터페이스의 `가상인자 람다식`도, 그 주체가 바뀌면 자동으로 변환 추천해준다.
     - **바꿀 때는 `ctrl + d`의 `Select next occurrence`를 이용해서 `하이라이팅만 해서 바꾸자`**
       - 변수가 아니라 여기저기서 쓰이는 형이므로 `ctrl + h`로 한번에 다 바꿀수 없다.

     ![image-20220722175010249](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722175010249.png)

   ![2d0dcb66-dfbe-4977-b6ba-4e8c6c186d52](https://raw.githubusercontent.com/is3js/screenshots/main/2d0dcb66-dfbe-4977-b6ba-4e8c6c186d52.gif)

   ![image-20220722175108760](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722175108760.png)



5. task관련 메서드들은 task관련 메서드보다 내려서 정렬해주자.

6. 현재 CompositeTask를 보면 **Set(컬렉션)데코객체가 되었다.**

   - 기존에는 구간처리시 prev데코객체, 순차적 기능추가시 구상 next데코객체를 사용해서 linkedlist로 연속된 처리를 for문이나, 꼬리재귀를 통해서 해줄 수 있었다.

   - **데코객체가 같은형의 컬렉션필드(Set)필드를 가지는 순간 `무한뎁스가 가능`해진다.** 

     ![image-20220722175813148](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722175813148.png)

7. **폴더인 tasks의 `자식 반환 기능`인 `정렬방식을 받아 list제공기능`이 바뀌어야한다**

   - 현재

     - 컬렉션의 사본 반환 -> 생성자 복사를 통한 얕은복사 사본이 바로 가능하다.
     - 컬렉션 1개만 반환 -> 여러 필드의 반환이 아니므로 객체 만들필요없이 바로 반환가능하다.

     ![image-20220722181619431](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722181619431.png)

   - **`나 자신(task1) + 자식들(set -> list)` 모두 반환되어야한다.**

     - **`set을 list로 변경해서 반환` -> `반환메서드는 내가 하지말고 내부에서 변환을 다른객체에 위임`해서 처리할 수 있다. 여기서는 직접 얕은복사로 사본을 만들었다.**
     - **`서로 다른 필드 2개를 묶어서 반환해야한다.` - >`묶어주는 처리`는 반환메서드 내부에서 `다른 객체에게 객체생성으로 위임`해야한다.**
     - **값의 반환이 아니라, `컬렉션반환부터는 사본으로 반환`해야하는데 `컬렉션이 포함된 객체(컬렉션 + 다른필드)의 자신this를 반환하고 싶다면, 나의 사본class를 만들어서 반환`한다.**

   - **`객체의 사본`은 `-Report`객체를 만들어서 반환한다.**

   

8. **컬렉션을 포함한 객체의 반환 = 사본 객체 `TaskReport`클래스를 만든다.**

   ![image-20220722183156117](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722183156117.png)

   - **`출력될 사본 속 반환후보1. 나 자신`인 CompositeTask필드**를 만든다.
     ![image-20220722184717679](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722184717679.png)
   - **`출력될 사본 속 반환후보2. 컬렉션 자신필드`인 내 자식들 컬렉션을 Set대신 List로 변환된 것을 보유**해야한다.

9. **컴포짓객체의 사본인 TaskReport는 반환후보2인 `List<CompositeTask>를 필드로 가져야하나?`**
   ![image-20220722184447909](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722184447909.png)

   - **내 자식들 컬렉션을 Set대신 List필드로 변환되어 가지고 있되 `List<CompositeTask>가 아니라,,, List<TaskReport>`를 가진다.** 
   - **무한뎁스를 자랑하던 CompositeTaks처럼  `TaskReport도 무한뎁스를 가진체 출력`되어야한다.**
     - **`(동일형)Set컬렉션(을 들고 있는) 데코객체`로서 `자신형의 컬렉션 필드`에다가 자식들을 달고 있어야 `무한뎁스`가 가능하다.**

   

   

10. **TaskReport가 CompositeTask의 `출력될 사본`을 담당한다면, `자식들도 다 출력될 사본인 TaskReport를 가지고 있어야`하므로 `TaskReport`가 List의 타입으로 와야한다**

    ![image-20220722191637376](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722191637376.png)

    - **왜 그렇다면 사본 속 나 자신은 CompositeTask로 필드를 만들었을까?**
      - **tree구조와 상관없는 `나 자신의 정보 필드`는 `아무형이나 갖다써도 된다`**
      - **`중요한 것은, 나 자신의 자식들을 컬렉션+나와 같은형`으로 들고 있는 것이 중요하다.**

    ![image-20220722191622525](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722191622525.png)





11. **나 자신의 사본인 CompostieTaks** 는 **이미 생성되어있고 `변하지 않아, 생성자에서 최초 주입 -> final로 확정하여 setter는 안만든다`**

    ![image-20220722191912684](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722191912684.png)

    

12. **나의 `출력사본 자식들`은 바깥에서 `추가를 해줘야하는 변수 -> 필드초 빈컬렉션 초기화 -> add(Setter) 추가`해줘야한다.**

    - **출력사본객체를 만들 때, `원본에 더해진 자식들을 반복문으로 돌면서  출력사본의 자식으로 그대로 add`하는 과정이 발생할 것이다.** 

    ![33ef6e7e-2412-40eb-974b-f1ae398ef4a4](https://raw.githubusercontent.com/is3js/screenshots/main/33ef6e7e-2412-40eb-974b-f1ae398ef4a4.gif)

    ![image-20220722192235032](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722192235032.png)





13. **`출력하기 위해  묶은` 출력사본 객체라도 `출력을 하는 곳에서는 각각의 필드 정보(나 자신+자식들)을 제공`해줘야하므로, `각 필드에 대한 getter는 필요`하다**

    - report의 실체
      - **task -> 자식이 없는 사본task인 report -> `바깥에다가 report를 만들어주면`**
      - **출력책임이 있는 놈은, `report의 getTask or getList`를 통해, 자기자신 + 자식들을 받아가 출력하려고 할 것이다.**
        - 자식들을 받았으면 반복문을 돌면서, 각 개별 자식들도 하나의 task가 되어, getTask + getList자식들을 요청할 것이다.

    ![image-20220722192452837](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722192452837.png)

    ![image-20220722192503420](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722192503420.png)

    

14. 이제 4가지 기능 중에 3가지는 나왔고, **사용하는 쪽과 그에게 제공할 operation()기능만 남았다.**

    ![image-20220722193003767](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722193003767.png)

    - **이제 `원본인 CompositeTask가`  출력을 위해 `List를 만들어 제공`할 때, `출력 사본인TaskReport를 만들고 사용`한다.**



15. **`원본 컴포짓객체인 CompositeTask`가, 외부에 출력용으로 자신+자식들을 반환할 때 `출력사본용 컴포짓객체 TaskReport`를 쓴다.**

    - 현재

      ![image-20220722214204719](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722214204719.png)

    1. **이제 외부에 제공을 `폴더가 자식들만 반환`하는 `getList메서드 + List<자신task>`형태가 아니라 `getReport메서드 + 자신&자식들묶은 사본객체`로 `시그니쳐를 변경`한다.**

       ![image-20220722214442728](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722214442728.png)

    2. **내부에서는 new TaskReport**를 만든다.

       - **`객체 자신의 사본을 내부에서 생성`할 땐, `this`로 객체 자체가 들어간다.**

       ![image-20220722214611911](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722214611911.png)

       

16. **`출력사본 객체도 컴포짓객체`이므로 `자식들을 만들고 add 끼워넣어야한다`. 그러려면 `원본객체 내부(현재context)에서 자식들을 for로 돌면서 -> 자식들 또한 report로 변환하여 add해준다`**

    - `Report도 컴포짓객체`라 **`컬렉션 필드에 자신과 동일형으로 자식들을 받는다.`**

    - **자식들task를 돌면서, 자식들을 report로 변경하는 방법은?**

      - `본인 내부`에서의 변환은 `new TaskReport(this)`였지만,

      - **`외부 task객체 상태`에서의 변환은 `현재 정의하고 있는 getReport(SortType type)`호출해야한다.**

        - **즉, 재귀형식으로 부른다.**

        - **재귀호출은 `마지막 스택에서 호출없이 끝나는 종착역`을 가져야하는데, `-> 다음 자식 -> 자식메서드`의 재귀로 안가려면  `for문에 걸리는 자식이 없을 때`이므로, 자식없으면 알아서 재귀호출이 종료된다.**
        -  prev,next데코객체는 **종착역이 데코객체필드 null을 지정** 
        - 컬렉션데코객체(컴포짓객체)는 **종착역이 자식컬렉션필드가 빈 컬렉션 -> 자식이 add안된 task라면, 이미 종착역**

      ![d869af61-cab8-44f3-99dc-61b8cdc6f69b](https://raw.githubusercontent.com/is3js/screenshots/main/d869af61-cab8-44f3-99dc-61b8cdc6f69b.gif)

      ![image-20220722215629488](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722215629488.png)

    - **`컬렉션데코객체의 컴포짓패턴`은, `자신 내부출력사본 객체를 만드는 곳에서 재귀호출`이 발생하며, `출력사본 객체 반환메서드를 작성도중`에 `not꼬리재귀로서, return이 아니라 for문 내부에서 자식들의 출력사본객체 만들 때` 발생하며, `종착역은, 해당 재귀호출이 없게 되는 for문을 안거치는, 빈컬렉션으로 자식들이 없는 객체에 도달했을 때`이다.**

      - prev, next데코객체는 **자신내부 계산처리 부분에서 prev필드나 next필드의 메서드를 호출해서 재귀를 호출하며 null을 가진 객체의 차례에서 종착역 터미네이팅했었다.**

      ![image-20220722220113223](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722220113223.png)

    - 위에 두줄은 필요없으니 삭제한다.

      ![image-20220722220156582](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722220156582.png)



17. composite패턴에서 composite객체를 실제 사용하는 것은 **원본내부에서 외부에 제공할 출력사본을 만드는데, 출력사본 composite객체를 생성하는 과정에서 재귀를 사용한다.**

    - 역할4개가 다 사용되었다.
      - [참고 벨로그](https://velog.io/@ye-geeee/Composite-Pattern)
        ![image-20220722223041879](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722223041879.png)
      - add + remove + getChild(getList)
      - **getReport( getList + operation)**

    - 파일+폴더를 합친 컴포지트객체는 if로 파일 or 폴더를 구분할 필요가 없다.

18. 이제 report생성시 정렬인자가 Task를 정렬하는 SortType이 아니라 **CompositeTask를 정렬하는 CompositeSortType을 생성해야한다.**

    - 기존 Task를 정렬하던 SortType은 그대로 두고 복사해서 생성한다.

    - 기존 getReport

      ![image-20220722231219299](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722231219299.png)

    - **인자를 변경하여 CompositeSortType을 새로 만들 건데,  SortType내용을 복사해서 수정한다.**

      ![e5077b16-91f4-4085-830a-504cbcc2cef1](https://raw.githubusercontent.com/is3js/screenshots/main/e5077b16-91f4-4085-830a-504cbcc2cef1.gif)

      ![image-20220722231454385](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722231454385.png)

    - **이 때, `사용하는 파라미터 형만 변경`된다면 Task -> CompositeTask `제네릭 적용을 생각`해본다.**

      - **만약 enum에 제네릭이 적용될 수 있다면, `기존 파라미터의 형을 SortType -> SortType<Task>  + SortType<CompositeTask>`로 사용할 수있을 것이나, `enum에서는 제네릭이 사용안된다.`**

        - enum은 내부적으로 class로 취급해주지 않는다는 말이다.

      - **enum이 제네릭이 적용안되는 순간부터 `enum을 추상클래스<T>제네릭조합으로 변경 + enum필드==인스턴스들(익클분신술) 4개의 훅구상체class로 변경`해야하는 타이밍이기도 하다.**

      - **하지만, enum의 thread안전성을 이용한다.  추클+T조합으로 바꾼다면, 인스턴스 생성과 동시성(싱글톤)문제를 내가 처리해야한다. `일반적으로 기업코드는 동시성문제가 훨씬더 무섭다 -> 코드중복을 허용해서라도 동시성 문제를 해결한다.`**

        - 반대로 안드로이드플랫폼에서 enum을쓰면 엄청느려지기 때문에 안쓰기도 한다.

        ![78e7589e-0379-49d9-947f-36b6010d2d39](https://raw.githubusercontent.com/is3js/screenshots/main/78e7589e-0379-49d9-947f-36b6010d2d39.gif)

    - CompostieSortType의 인자를 Task -> CompositeTask로 변경해준다.

      ![fcd2634a-dcf0-42df-b0b7-bbdc00d4eb89](https://raw.githubusercontent.com/is3js/screenshots/main/fcd2634a-dcf0-42df-b0b7-bbdc00d4eb89.gif)

19. **더이상, CompositeTask는 `정렬된체로 외부에 제공`하는 `getList( sort )`의 역할이 사라졌다.**

    - **`외부에 정렬된 상태`로 자식들List를 반환하는 것은 `TaskReport의 getList(   )`이므로 `정렬방식 파라미터를 여기로 옮긴다.`**
    - CompositeTaks의 getReport는 사본객체만 만들어내는 역할. **파일+폴더정보를 묶어서 가지고 있는 사본객체가 외부에 list를 제공해준다.**
    - **`외부 컬렉션 제공부터는 사본을 만들어서 반환한다`**
      - **각 요소가 외부에서 public메서드를 가져 조작가능한 객체라면, 요소들은 그대로 있고, 컬렉션 주소만 바뀌도록 얕은복사를 해준다.**
        - **컬렉션은 건들지말되, 요소들을 변경하고 싶으면 뽑아서 해라!**

    ![f6bcb7a6-af9c-4b3a-916e-aa4f59d01c5a](https://raw.githubusercontent.com/is3js/screenshots/main/f6bcb7a6-af9c-4b3a-916e-aa4f59d01c5a.gif)

    - CompositeTask#getReport

      ![image-20220723000312832](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723000312832.png)

    - TaskReport#getList

      ![image-20220723000359075](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723000359075.png)

    - ~~CompositeSortType~~ -> TaksReportSortType

      ![image-20220723000440146](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723000440146.png)

      - **래핑된 객체의 필드로 정렬되므로, `getter 추가 및 내부객체->필드까지`**

        ![image-20220723000649328](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723000649328.png)

20. **파일 + 폴더 반환기능을 위임받는 출력사본 report객체는 `파일반환`도 해줘야한다.**

    ![image-20220723005336697](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723005336697.png)

#### Main에서 Composite객체 및 출력사본 CompositeReport객체 사용하기

1. root를 만들고 자식들을 추가한다. **1개의 컴포지트 객체를 공짜로 받아와 add하는 순간은 지금 뿐이다.**

   ![image-20220723004119020](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723004119020.png)

   

2. **sub1(자식)아래에 자식들을 넣으려면 `list를 통해 반환받는 수 밖에 없으며`, composite객체는 list제공기능이 없고, `출력사본으로서 자식list를 제공해주며, 얕은 복사를 통한 제공으로, 요소들을 꺼내서 조작가능`하게 하는 것은 `TaskReport를 통해 받아보는 수 밖에 없다`**

   - sub들을 얻을 수 있는 유일한 방법: 현재객체의 출력사본 report생성 -> list를 반환 -> 꺼내서 씀
     - **CRUD처럼, `if addTask가 [실시간 외부조작 가능한 객체]를 추가한 것을 그대로 반환`해주면, `추가된 객체를 list에서 받지 않고, 그대로 객체 조작`할 수도 있을 것이다.**

       ![image-20220723004634412](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723004634412.png)

   ![image-20220723011639423](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723011639423.png)





3. **컴포지트객체는 `폴더-파일구조를 바로 확인할 수없다`.**
   - **오여주는 애인 Renderer를 정의**해서 봐야한다.



#### Renderer: 순회의 제어구조 -> Visitor패턴으로 프레임웤 만들기

- 컴포지트 패턴은 결과물자체도 컴포지트하게 생겼다.
  - **컴포지트 객체를 이용해 만든 Report도 컴포지트하게 생겼다. = 컴포지트를 순회해서 만들었다.**
    - 안에 모아놓을 수 있다.
  - **또한, Report를 소비하기 위해서는 또 컴포지트 패턴을 순회해야한다.**
- 컴포지트 패턴을 보려면, **컴포지트 패턴을 다시 순회할 수 있는 순회자**를 만들어야한다.
  - **하지만, Report와 달리 `순회자는 제어구조`다.**
  - **`순회의 제어구조는 라이프사이클을 정해두되, 해야할 일은 바깥쪽에서 위임하여 라이프사이클을 탈 수 있다.`**
  - **즉, `순회자=제어구조가 해야할일을 거꾸로 정의할 수 있다라면, 프레임웤`이 된다. `Visitor패턴은 처음 배우는 프레임웤`이다.**



1. **출력을 담당하는 class(Renderer)가 제어구조를 가져야한다면, `제어구조에서 제어의 역전을 위해 Visitor를 입력받는다.`**

   - **Renderer는 다양한종류의 할일을 하는 visitor(다양한 기능의 전략패턴)을 받아들이는 `Visitable`이지만, 여기서 renderer는 `순회의 제어구조를 가지고 있는 프레임웤`으로서 여러종류를 가지는 것이 아니기 때문에 `전략패턴으로 생성`하진 않는다.**
     - visitable이 인터페이스가 아니므로, **필드를 가지는 구상클래스로서 visitor을 생성자 주입** 받는다.
       ![image-20220723230451093](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723230451093.png)
   - **제어의 역전으로 `라이프사이클을 타면서 일을하는 Visitor 종류만만 달라`지므로 `생성해서 그때그때 사용한 생성context`로 간주하여 일반 클래스로 만든다.**

   ![image-20220723230130329](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723230130329.png)

2. **Visitor는 원래 인터페이스**로 들어온다. **여러 종류의 할일(라이프사이클마다 그에 맞는 데이터가져다주는 기능)을 가진다.**

   ![image-20220723230733096](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723230733096.png)



3. **Renderer의 역할은 `TaskReport`를 받아서, `결과물을 바깥에 주는게 아니라(public Item)  출력render(public void)`만 해준다**

   ![image-20220723231035013](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723231035013.png)

4. **render내부에서는 `컴포지트 객체를 만드는 방향과 동일한 방향으로 순회`할 것이다. 문제는 `순회하며 그릴 때 depth가 생겨난다는 것`이 다르다.**

   - **보이진 않지만, `인자가 더 필요`하다. `누적되는 depth의 초기값을 밖에서 주는 꼬리재귀구조로서, **
     - **계층적, 재귀적인 구조**는 **구조객체 이외에 depth정보도 인자로 달고 있어야한다.**
   - **`컴포지트 구조도 순회하려면 정해진for가 아니다`**
     - **더 깊이 들어가는 애도 있는 `동적트리 순회 구조`이다.**
     - **실무**에서는 정해진 트리구조만 **다 도는 것이 아니다**. **리프가 다양한 구조로 있는 여러구조체를 돈다.**
   - **`동적트리 순회`시에는 `최소한의 depth정보를 누적결과값으로 물고다녀야한다`**
     - **`계층적, (꼬리재귀)적, 동적트리 구조의 순회`는 `누적결과값파라미터를 물고, 최초호출시 인자에서 주어진다`, ** 

   ![image-20220723232312262](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723232312262.png)





5. **`최초 depth는 0으로 주어져야`한다. 하지만, `사용자가 0으로 준다는 보장이 없다` -> 이 때 `꼬리재귀를 위한 private메서드 추출`이자 `최초(누적결과값변수의) 인자를 내부에서 상수로 주는 오버로딩`을 드디어 사용한다.**

   - **사용자(public)는  depth가 없는 render(TaksReport report)만 호출할 수 있게 한다 ** 
   - **`누적결과값 파라미터의 최초인자 0을 줘야하는 depth를 파라미터가 추가된 꼬리재귀 메서드`**는 **`직접 0을 주고 내수용으로 오버로딩`해서 `private(사용자 사용금지)으로 으로 생성-정의`한다**
     - **좋은 함수는 인자0~1개인 함수다. `더러운 것 = 인자 2개이상 && 직접 초기값을 줘야하는 함수`는 -> `전부 private으로 내부에서 하는 오버로딩으로 정의` 하면 된다. **
     - **더러운 것은 안에서 하고, `바깥에는public 좋은 함수를 줘라`**

   ![ba147b71-2dff-41da-95f0-a7a04836d541](https://raw.githubusercontent.com/is3js/screenshots/main/ba147b71-2dff-41da-95f0-a7a04836d541.gif)

   ![image-20220723233638840](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723233638840.png)

6. **Visitor패턴에서 visitor를 받는 visitable(Renderer)는 `1개의 메서드 안에 라이프사이클에 맞는 제어구조`를 작성해야한다.**

   - render메서드 안에서 다 작성해주자.

   1. **task부터 그려야한다. -> `visitor에게 task객체를 줘서 그리는 책임을 위임한다.`**

      - **`내가 안한다. 라이프싸이클을 타고 있는 visitor에게 시킨다.`**

      ![image-20220723235121225](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723235121225.png)





7. **근데, 객체지향에서 생각해야할 것이 있다. `협력인스턴스가 생성하는 context(동시성 문제가 발생할 수 있어서, 매번 새로 생성할지)`인지 `협력인스턴스를 소모하고 재활용하는 context`인지 생각해야한다.**

   - **`협력인스턴스를 내부에서, 메서드호출마다 매번 생성`하면 `동시성 문제가 하나도 없어진다`**

   - `전략패턴의 visitor를 단일객체`을 외부에서 생성된 것을 받아서 **`new때리지 않고 있다.`**
   - **그러나 제일 안전한 것은 `언제나 협력 인스턴스를 생성`하는 것이다. 그러려면 `외부에서 생성된 협력 객체가 아니라, 외부에서 생성자를 받아야`한다.** 
   - **`생성자를 받는다`는 것은, `인스턴스를 return해줄 수 있는 (인자있)Functional or (인자없)Supplier를 받는 것`이다.**
     - **그래야 범용적인 Visitor를 매번 `상태가 초기화된 협력인스턴스`를 가질 수 있다.** 
     - my) 내부정보로 내부발행객체를 생성하는 것처럼, **협력 인스턴스가 추상체일 때 내부 발행하려면**
       1. 외부에서 받아야하는 추상체변수로 new때리질 못한다. **외부에서 생성자 호출직전까지의 로직으로 받아야한다.**
       2. **생성자  로직을 받을 때, 생성자 로직이라면 return이 필요하므로 Supplier나 Functional을 받고,  제네릭에는 받는 협력인스턴스 형을 T로 준다.**
       3. 내부에서는 받은 로직을 실행하기 위해 .get() 나 .apply()를 통해 **lazy evaluate의 연산지연실행 메서드를 사용한다**
       4. **외부에서는 `가상의 인자로 람다식을 통해 [생성자 로직]`을 `인자`로 전달해 `아~ 호출시마다 매번 해당로직이 지연실행`되는 구나. `생성자호출이라면, 객체생성이, 매번 되는 구나`라고 생각한다.**

8. **`협력인스턴스의 추상체 visitor`**를 이미 생성된 객체가 아니라, **`render()`호출시마다 `내부에서 언제나 새로 생성`해서 사용하기 위해 `Visitor주입 -> Supplier<Visitor> 주입`으로 변경한다.**

   - 변수명은 factory로 받는다.

   ![4e8f5357-ead4-4370-9141-18040b6dade7](https://raw.githubusercontent.com/is3js/screenshots/main/4e8f5357-ead4-4370-9141-18040b6dade7.gif)

   ![image-20220724122340349](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724122340349.png)

9. **visitor의 생성은, 꼬리재귀 호출전에 생성되어 인자로 줘야한다.**

   ![image-20220724122901932](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724122901932.png)

   - 꼬리재귀에서 **재활용하기 전에, 재활용될 visitor를 생성**한 뒤, **오버로딩 된 꼬리재귀 메서드에만 인자로 추가한다.**

   ![d0f40455-d0cf-4224-805e-b1b5a90ac01e](https://raw.githubusercontent.com/is3js/screenshots/main/d0f40455-d0cf-4224-805e-b1b5a90ac01e.gif)

   ![image-20220724123102130](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724123102130.png)



10. **`java헛소리`로서 `즉시 인자로 반영될 값이라면, 지역변수로 빼놔야 가독성에 좋지 않나요? `라고 한다.**

    ![image-20220724123346314](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724123346314.png)

    - **하지만, `인자에 넘어갈 변수 <-> 메서드 사이`에 `지역변수를 통해 트랜잭션(검증, visitor.타메서드() 호출 등)을 안할 거면, 그리고 어려운코드가 아니라면, inline으로 쓰는게 맞다`**
    - **`지역변수가 있다. == 해당 변수로 추가적인 일을 시키고 나서 인자로 넘기겠다.`**
    - **`지역변수가 없다. == 만든 변수를 건들지않고 인자로 바로 넘기겠다.`**

    ![0200fa44-082b-459c-8b28-9961f02793f9](https://raw.githubusercontent.com/is3js/screenshots/main/0200fa44-082b-459c-8b28-9961f02793f9.gif)

    ![image-20220724123653366](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724123653366.png)





10. **꼬리재귀 정의부에서는 `최초호출시 인자가 넘어와서, 누적/재활용되기 전 제일 처음 변수`로 어떤 조작이 될 것이다.**

    - 생성되고 제일 처음visitor로 task를 그린다.

    ![image-20220724123835889](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724123835889.png)

    - **Visitor가 상태를 가지는 인스턴슨지 뭔지는 모르겠지만, `호출시마다 매번 생성되는 상태니 동시성 문제가 하나도 없으며, 찌꺼기 상태값을 고려안해도 된다.`**

    - **우리는 gc를 믿고 개기는 것이다. `객체를 재활용할 생각 자체를 버리자 무조건 만들자. 객체지향은 new도는 것`**

    ![image-20220724124152271](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724124152271.png)

11. 파일(task)는 그렸으니, **자식들subtask들을 돌리 위해서는 반복문**을 써야한다.

    - **`자식들도 하나의 task`로서, 반복문내에서 `꼬리재귀render`를 해야한다.**

      - render해야 자신의 task를 그리고, 자식들마다 for문돌면서 task들을 다시 그린다.

    - **`꼬리재귀 내부에서 자식들의 (return or 마지막문장의) 종착역설정은 render를 안타게 되는 == for문을 안타는 ==빈List를 가진 자식`일 것이다. **

    - **데코객체가 아니므로 연결된 next/prev데코객체.꼬리재귀형태가 아니라`메서드 수준에서의 꼬리재귀`는 `재귀재료에 [현재 주체]까지 모두 인자로 표현하여 다들고 다니므로, 호출인자만 업데이트된 인자로 [내수용 꼬리재귀 메서드 호출]`로 재귀를 호출한다.**

      ![](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220716135212111.png)

    - **주체자가 report -> subReport로 바뀌었다.**
      - **visitor는 그대로 재활용**된다.
      - **depth는 1개 증가한 것으로 줘야한다.**

    ![e27539df-37e6-4f66-87d2-22c3e420e5b9](https://raw.githubusercontent.com/is3js/screenshots/main/e27539df-37e6-4f66-87d2-22c3e420e5b9.gif)

    ![image-20220724130810425](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724130810425.png)

    

12. **그렇다면, `자기자신을 그릴 때, 현재의 depth정보(자식은 +1)도 필요`할 것이다.**

    ![e6b8beea-7d77-488d-aac3-24bf6088786f](https://raw.githubusercontent.com/is3js/screenshots/main/e6b8beea-7d77-488d-aac3-24bf6088786f.gif)

    ![image-20220724131132410](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724131132410.png)

    

13. **라이프 싸이클에 따라, `visitor에게는 end(depth)를 호출`해서 `task + 자식taks를 다 그린 [종결처리]`를 해줘야한다.**

    - **왜 꼬리재귀에서 따로 종결처리를 해줘야하지?**
      - **결과값을 누적하는꼬리재귀가 아니라 `depth를 업데이트하며 출력만 하는 꼬리재귀`이며, `마지막 node일 때 출력이 달라지므로?`**

    ![cfa201b7-7f89-4b09-b2ae-f688e1b05e5e](https://raw.githubusercontent.com/is3js/screenshots/main/cfa201b7-7f89-4b09-b2ae-f688e1b05e5e.gif)

    ![image-20220724131448706](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724131448706.png)





14. **라이프싸이클에 따른 composite제어구조는 Visitable에 해당하는 Renderer가 다 가져갔다. `visitor`는 라이프싸이클에 따른 `생명주기 함수들만 정복하고, composite제어에 대한 정보가 없다.`**

    - 제어와 실행을 분리하는 것이 visitor패턴이다.
    - 앞으로 3년간... 어려운 **`제어를 실행하는 놈` vs `제어를 통제하는 구조`**를 분리하는 연습해야한다.

    ![image-20220724131700527](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724131700527.png)
    

15. **이제 인터페이스 Visitor에 `제어를 실행하는 [라이프싸이클 함수] 2가지를 추가`해준다.**

    - **라이프싸이클 함수가 생기는 이유 -> `해당 인터페이스는 제어의 흐름에 들어가있는 visitor객체다`**

    ![20d40a63-bc22-4ee8-a2b9-80259ca91f12](https://raw.githubusercontent.com/is3js/screenshots/main/20d40a63-bc22-4ee8-a2b9-80259ca91f12.gif)

    ![image-20220724132241769](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724132241769.png)



#### ConsoleVisitor: 제어구조에 끼워넣을 제어실행visitor 구상체1

![image-20220724132756011](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724132756011.png)



1. task1개를 표현하면, depth만큼 `빈 padding문자열`을 **실제 padding으로 들어갈 문자열을 정해서 depth수만큼 누적합**되어야한다.

   - depth만큼 반복문을 돌면서 정해진 padding문자열을 누적합한다.

   ![154ebfe2-818a-434b-8a55-23e46c27634f](https://raw.githubusercontent.com/is3js/screenshots/main/154ebfe2-818a-434b-8a55-23e46c27634f.gif)

   ![image-20220724133221173](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724133221173.png)

2. **padding이후 가장 먼저 표시해야할 것은 토글여부이다. `isComplete 필드의 getter를 만든다.`**

   - **보통 isXXX boolean필드의 getter는 get을 뺀다고 한다.**

     - **불린flag자체가.. return boolean변수이므로... 물어보는 것과 동일하다**

     ![30103f84-d760-49f8-8b3b-a43dd683bd08](https://raw.githubusercontent.com/is3js/screenshots/main/30103f84-d760-49f8-8b3b-a43dd683bd08.gif)

   - **isComplete()여부에 따라 `[v] `나 `[ ] `를 표시하도록 삼항연산자를 활용해준다.**

     ![1486c91b-946f-4e96-9aad-3495ebbe638c](https://raw.githubusercontent.com/is3js/screenshots/main/1486c91b-946f-4e96-9aad-3495ebbe638c.gif)

   ![image-20220724133946833](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724133946833.png)

   





#### Main에서 render + consolevisitor로  visitor.taskDraw() 출력 확인하기

1. Render를 만들되 **생성자에서는 내부에서 `라이프싸이클을 타는 visitor객체가 제어메서드 호출시마다 새로객체를 생성`하도록 `람다식으로 supplier제공해주기`**

   - **`라이프싸이클타는 객체`를 생성해서 넣어주지 않고, `라이프싸이클을 가진 제어메서드호출시마다 생성`하도록 하기 위해 `supplier or functional`을 통한 `factory를 넣어줘서 생성`한다고 표현한다.**

2. render.render( )시, 위에서 만들어둔 root를 report로 만들어서 넣어주기

   ![ae22440a-f72b-4aee-a58e-86cc6a2b32c4](https://raw.githubusercontent.com/is3js/screenshots/main/ae22440a-f72b-4aee-a58e-86cc6a2b32c4.gif)

   ![image-20220724134814286](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724134814286.png)

3. 출력해보기

   - depth마다 `-`가 붙은 체로 출력된다.
   - 종결처리를 해주면 각 task마다 뭔가 표시를 해줄 것이다?

   ![bb208850-846a-4750-825a-4a03a82573be](https://raw.githubusercontent.com/is3js/screenshots/main/bb208850-846a-4750-825a-4a03a82573be.gif)

   ![image-20220724135057928](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724135057928.png)

4. **정렬방식 고민하기**

   - **현재 문제는, 정렬방식을 내부에서 정하고 있다. `원래는 root task를 통해 report를 만드는 getReport()시 정렬방식을 인자로 줘야한다.`**

     - 내 코드(report의 자식들을 꺼낼 때만 정렬방식 적용해서 제공)

       ![image-20220724135307603](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724135307603.png)

     - **되어야할 코드**

       ![image-20220724135327957](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724135327957.png)



#### task -> 내부에서 TaskReport만들때부터 정렬방식이 전해져야한다.

5. **정렬방식 받는 것을 추적해서, task#getReport에서 정렬방식 인자를 주입하도록 하기**

   - 현재는 report에서 꺼낼때만, 정렬방식을 인자로 주입받고 있다.

     - Main에서 Report의 자식들을 꺼낼때, 정렬된체로 꺼내기

       ![image-20220724135730289](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724135730289.png)

     - render시 내부에서 report들의 자식들을 꼬리재귀로 출력하려고 꺼낼때, 인자로 받는 중

       ![image-20220724135820067](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724135820067.png)

   - **하지만, report를 만들때(`task#getReport()`부터 `이미 인자로 주어져서 정렬된 체로 report를 생성`하도록 변경해야, `render시, task -> 정렬된 report를 만들어 report만 넘기기`를 할 수 있다.**

     ![image-20220724140741542](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724140741542.png)

     ![image-20220724140808337](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724140808337.png)

     ![image-20220724140830194](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724140830194.png)

     ![image-20220724140919885](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724140919885.png)

     

#### JsonVisitor:  depth를 가진 출력은 json형태라서 만들 수 있다

- 원래 json은 쌓아가야하는 drawTask시, string들을 쌓아가야하는 입장이다.
  - sout이면 안쌓아도되고, 문자열을 원하면 쌓아야한다.
  - 여기서는 sout로 간다.
- json은 taks정보 + sub task들정보 + **다끝나면 중괄호**가 닫힌다.
  - **end에서 할일이 있다.**

1. **ConsoleVisitor의 내용을 가져오되, `처음 작성할때 drawTask의 padding만큼 밀리고, end에서도 padding만큼 밀리니` -> `depth만큼 쌓이는 padding로직을 내수용(drawTask + end 공통으로 사용) 메서드 추출`을 통해 `class 내부로 빼낸다`**

   ![43942d63-22d9-4cf8-b135-ff92136ae4e5](https://raw.githubusercontent.com/is3js/screenshots/main/43942d63-22d9-4cf8-b135-ff92136ae4e5.gif)

   ![image-20220724182854246](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724182854246.png)

   - 이 때, **padding은 ` -  -> " " 빈 문자열`로 바꾼다.**

![image-20220724183006438](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724183006438.png)



2. **첫번째 줄은 padding + 중괄호{다**

   ![image-20220724183115813](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724183115813.png)

3. **필드는 padding + ` 중괄호대신 공백` + `필드명:` + 문자열이므로 `"내용"`으로 양쪽 큰따옴표가 나가야하므로 java내에선 `\"`를 사용하며 + 끝날때는 콤마까지 가지고 가야한다. `\",`**

   ![image-20220724183608061](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724183608061.png)

   - 복사해서 date도 해결한다.

4. **boolean은 내용에 큰따옴표가 필요없다.**

   - true, false는 자동으로 문자열로 true, false로 바뀐다.

   ![image-20220724183747535](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724183747535.png)



5. **문제는 sub들이다.**

   - **`sub: [ `  내용으로 대괄호 + 공백인 상태로 주면,**
   - **그담 task가, 자신의 패딩+ 중괄호로 열고 시작하게 된다.**
   - **`대신, end의 닫기역할만 잘해주면 된다. 자신은 자기닫기만 잘챙겨주면, 된다.`**
     - **자식들을 가져, 1개의 누적결과값반환이 아닌, 반복문의 재귀에는 **
       - **자신은 시작만 해놓고, 자식들을 호출하며**
       - **자신과 자식들모두 각자의 end의 종결처리가 필요하다.**

   ![b59fd5f3-a439-431d-9af5-b088c7a1519d](https://raw.githubusercontent.com/is3js/screenshots/main/b59fd5f3-a439-431d-9af5-b088c7a1519d.gif)

   ![image-20220724184531977](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724184531977.png)

   - 종결처리

   ![42e1845f-458d-4db2-bb7f-073ec758418a](https://raw.githubusercontent.com/is3js/screenshots/main/42e1845f-458d-4db2-bb7f-073ec758418a.gif)

   ![image-20220724185311240](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724185311240.png)

6. **json의 괄호들은 내턴을 닫을 때,  `중괄호뒤에는 }, 콤마`를 `반드시`찍어준다.**

   - 자식들끼리 콤마로 연결되어야하므로.. 안찍어주면 에러다

   ![image-20220724190151768](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724190151768.png)

   - **root의 중괄호도 마지막이 콤마가 찍힌다.**

     ![image-20220724190350188](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724190350188.png)

   - js에서 object에 **트레일링콤마를 허용하자**라는 말이 나온다.

     - **트레일링 콤마**(Trailing comma)는 여러 요소들을 쉼표(콤마)를 사용하여 나열할 때, 가장 마지막 요소 다음에 오는 쉼표를 말한다.

       - 위 트레일링 콤마는 생략하여도 아무런 변화를 만들지 않으며 단지 수정을 편하게 하기 위한 도구이다.

       - 한편 [Go](https://femiwiki.com/index.php?title=Go(프로그래밍_언어)&action=view&redlink=1)의 경우 배열을 여러 줄에 나눠 입력할 때 트**레일링 콤마가 강제 사항**이며 이를 생략하면 다음과 같은 에러를 발생시킨다.

   - **프레임웤을 visitor패턴으로 만들 때, 트레일링콤마를 허용안한다면, `end종결처리 메서드와 꼬리재귀의 파라미터에 isEnd를 추가해줘야하고` + `end에서 분기를 걸어줘야한다.`**

     - 프레임웤을 만드러보면, 트레일링 콤마를 쓰자고 한다.

     ![image-20220724190940711](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724190940711.png)

     ![image-20220724191019051](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724191019051.png)





#### Main에서 사용

![image-20220724185910322](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724185910322.png)





### 프레임웤은 visitor패턴과 템플릿메소드패턴으로 구성된다.

- 몸통 : visitor패턴을 상속한 visitor객체들이 구현한다
- **가장 큰 프로세스(제어): templatemethod의 훅으로 통제**한다.

- 스프링의 예
  - 컨트롤러를 pojo로 만들면, 어노테이션(router)에 의해 **visitor로서 작동한다.**
  - 그에 비해 **스프링 interceptor는 템플릿메소드**를 만들어서, 구상은 before/after intercept를 만들어서 훅을 구현한다.
- **큰 흐름은 템플릿메소드패턴으로 강제시키고, 안에 몸통의 유연성 + 다양한 라이프사이클은 visitor패턴으로 처리한다.**
  - 안드로이드 개발시 activity상속받으면 전부 라이프사이클함수 구현이다.
    - **라이프싸이클을 타는 거대한 visitor객체였다.**





- 프레임웤에서 구현해야하는 것들은 모두 visitor다. 제어구조를 역전시키려고
- **우리는 rendner1개에다가, visitor를 전략객체로서 공급했다.**
  - 큰범위는 다 전략패턴이다. 전략패턴은 추상체로 소유하면 다 전략패턴이다. 더 큰 범위
  - 그 중에 visitor패턴은, **제어역전으로 제어능력을 잃어버린 전략객체(라이프싸이클 함수만 구현)하는 것들만 visitor패턴이라 부른다.**
- **의존성 역전(DI) : 구상체를 알아야하는 애가 추상체를 알도록 바꾸는 것**
  - 전략패턴
- **제어 역전(IoC) :  제어구조는 미리 짜놓고, 라이프싸이클을 타는 애가 라이프싸이클함수만 구현**
  - visitor패턴









- 다음시간에는 컴포넌트 패턴
  - 패턴을 조합해서 새로운 패턴을 만드는 것
  - 대표적으로 MVC패턴 -> 옵져버 + @를 조합했는데 새로운 파워의 패턴이 나온 것
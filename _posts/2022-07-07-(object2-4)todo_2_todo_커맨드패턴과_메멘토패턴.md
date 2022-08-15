---
toc: true
layout: post
title: OBJECT 20 TODO2 작성을 통한 command, memento패턴 학습
description: object 책을 강의한 코드스핏츠 유튜브 요약

categories: [object]
image: "images/posts/java.png"
---

- 참고 유튜브 : https://www.youtube.com/watch?v=navJTjZlUGk
- 정리본: https://github.com/LenKIM/object-book
- 코드: https://github.com/eternity-oop/object
- 책(목차) : https://wikibook.co.kr/object/



## 디자인패턴 심화 - 커맨드 패턴



- visitor패턴: 제어를 뺏고 행위만 남겨둔다.
  - renderer: 제어를 뺏음. 제어의 역전(invasion)
  - visitor: 행위만 공급하게함.

- **제어만 역전해서는 프레임워크가 작동하지 않는다. `행위도 역전하지 않았기 때문`**
  - **행위를 각자 객체가 가지고 있으면 너무 많은 자율권이 부여되기 때문에 `행위도 캡슐화`해야한다**
  - 행위캡슐화하는 패턴: **커맨드패턴**



- 커맨드패턴은, 이미 존재하는 코드에 다 적용할 수 있다. **처음부터 커맨드패턴을 적용한다는 생각을 버려야한다.**
  - 헤드퍼스트에 나와있는대로 쓰면 안된다.
  - **내가 어떤 객체를 소유하고 있는 상황 -> 커맨드패턴화 시킨다.**
    - **`CompositeTask를 소유`하고 있어서 -> 그 기반으로 CommandTask를 만들 것이다.**



1. 커맨드 인터페이스를 먼저 만들고, 오퍼레이터로 `기능 객체 조작행위`의 오퍼레이트를 정의해서 `행위를 위임`한다.

   - 일반적으로 대표적인 메서드는 2개가 있다

     - **`void execute()`**
     - **`void undo()`**

     ![image-20220731222114476](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731222114476.png)

   - 현재는 인자를 안받고 있지만, **인자에 객체를 줘서, 객체를 조작하는 행위를 위임한다**

     - **실행/취소는 뭔가를 대신한다는 개념인데, `Task를 대신하려면 Task객체를 보내서 대신 시켜야`한다**
       - **사실상 기존에 있던 task속 set계열 메서드들을 인자로 온 task를 통해 호출한다.**
       - my) 을의 입장에서 물건을 반환해주는 / 기능을 가진 갑을 인자로 받는 것과 유사

   - **직접 Task를 조작하던 것을 `Command객체에게  기능객체의 조작을 위임`하기 위해 , `인자로 기능객체를 먼저 보내준다`**

     ![image-20220731222252353](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731222252353.png)

     

2. **커맨드패턴은`행위 위임`뿐만 아니라 `자기만의 저장소(인스턴스 context)`를 이용해서 `행위를 조작할때 외부저장소`로 이용한다**

   - undo할때 한다. redo도..
     - **우리가 하는 것에 대부분은 undo/redo가 있다. 기본인데 못하는 중...**

   - 인터페이스가 아닌 `위임행위`를 가지는 `커맨드 구상객체`라는 인스턴스에서 내부 필드로 행위 외부저장소를 가진다.

3. **CommandTask (커맨드 구상객체)를 만든다.**

   - **오브젝트 책에서 나오는 `(행위)위임 객체`로서 `CompositeTask를 소유`하여 `바깥으로 매핑`하고 있는 상태다.**
   - **`기능객체의 소유만 하고, 커맨드인터페이스를 구현하는 것은 아니다!!`**

   ![image-20220731223255856](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731223255856.png)

4. **해당객체를 소유하므로 `해당 객체 내장을 모두 복사`해서 가지고와서 `바깥에서 매핑해주고 있는 부분부분을 수정`한다.**

   - 소유객체인 compositeTask내용을 모두 복사해온다.
   - **필드는 CompositeTask를 소유하도록 수정한다**
     - 나머지 필드는 날린다.
   - **생성자는 자신형으로 바꿔준다.**
     - impl은 오타

   ![image-20220731223149486](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731223149486.png)



5. **복사해온 코드중에 set계열들(커맨드패턴 대상)은 일단 두고, `get계열코드`부터 확인한다.**

   - **매핑객체가 소유객체의 코드를 복사해왔다면 `getter계열은 매핑해주고 있으므로 소유객체에 위임`하도록 바꾸면 된다.**
   - toggle은 get계열이 아니라 set계열(상태변화)이다.
     - **set계열은 command패턴을 직접적으로 먹는 애들이다. 나중에 수정한다.**

6. getReport() 같이 getter계열에는 return만 해주면 되는데, **이미 소유객체 내부에서 정의된 메서드들이고, 현재 그 메서드 정의부에 있으니 `시그니쳐만 보고`이므로 `소유객체.해당메서드명(인자)`으로 위임호출하면 된다.**

   - 인자가 있는 getter라면, `return 소유객체.메서드(인자)`로 getter를 위임하면 된다.
   - **getter제거를 위한 위임이 아니기 때문에, `소유객체 자체의 메서드만 호출해서 get계열위임을 쉽게`할 수 있다.**

   ![85083728-1803-4e70-b721-a7feaae70df7](https://raw.githubusercontent.com/is3js/screenshots/main/85083728-1803-4e70-b721-a7feaae70df7.gif)
   ![image-20220731224520503](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731224520503.png)

7. **getter계열들은 소유객체코드 + 소유객체를 통해 위임해서 쉽게 해결했다.**

   - **`상속안쓰려면 소유 후-> 위임 메서드 정의`해야한다.**

8. **set계열은 `새로운 커맨드 객체를 통해, 소유객체의 행위를 위임`해보자.**

   - 제일 쉬운 toggle부터 처리해본다.

     - **쉬운 이유는... toggle의 execute 와 undo가 같기 때문에..**

   - **지금까지 set계열 = 상태변화는 `문`을 통해 해결했는데, `set계열의 처리를 소유 -> 커맨드 객체를 통해 위임`하기로 한다.**

     ![image-20220731224758688](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731224758688.png)

   - **set계열을 처리하는 커맨드객체들은 Command인터페이스를 구현해서 생성되고, 인터페이스  추상체변수(for 오퍼레이터 사용)로 받는다.**

     ![ac3fcd19-456d-4627-999c-ab6c03697d87](https://raw.githubusercontent.com/is3js/screenshots/main/ac3fcd19-456d-4627-999c-ab6c03697d87.gif)

     ![image-20220731224953578](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731224953578.png)

9. **set계열의 메서드들도 `커맨드객체 -> execute에서 set계열 위임`하는 것이다.**

   - 대신 undo에서 **카운터칠 수 있는 메서드를 만들어내는 것이 관건이다.**

   ![6b93287e-c01b-4f10-bcf5-50f1961a9b55](https://raw.githubusercontent.com/is3js/screenshots/main/6b93287e-c01b-4f10-bcf5-50f1961a9b55.gif)

   ![image-20220731225516730](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731225516730.png)

10. **이제 `객체소유 커맨드객체`에서는 커멘드인터페이스 변수를 통해 execute를 호출한다.**

    ![image-20220731225623493](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731225623493.png)

11. **우리는 커맨드 구상객체를 통한 실행 뿐만 아니라 `undo/redo호출을 위한 외부저장소`가 필요하다.**

    - **그래서 객체소유필드 외에 `여러 커맨드 구상객체들을 쌓아놀 스택(List)`가 필요하다.**
    - **또한, 소유객체 행위를 할때마다, 빈 컬렉션(커맨드스택)에 add해줘야하므로 `addCommand(Command cmd)`메서드도 필요하게 된다.**
      - **my) 저장소 컬렉션 필드는 `빈컬렉션 + add메서드`조합이 필수임**
    - **어차피 `커맨드 실행도 add하고 바로 할 것이므로 addCommand에서 execute까지`해주면 된다.**
      - **앞으로 set계열의 커맨드 행위 메서드들은 `addCommand(new 해당커맨드())만 호출하면 저장+실행을 동시에` 한다.**

    ![3d8924d8-c908-45e8-9679-28937c819295](https://raw.githubusercontent.com/is3js/screenshots/main/3d8924d8-c908-45e8-9679-28937c819295.gif)

    ![image-20220731231621198](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731231621198.png)

    ![image-20220731231638415](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731231638415.png)

12. 기존에 소유한 객체에서 하드코딩되었던 toggle명령이 **커맨드 구상객체로 빠져나간 것을 알 수 있다.**

    - **`getter계열`들은 바깥에 제공해주는 것이므로 `행위가 아니다`.**

    - **상태를 건들이는 setter나 remove등은 객체로 다 뺄 것이다.**

13. **setter들은 모두 같은 패턴으로 행위를 위임한 커맨드객체로 빼보자.**

    - **행위에 있어서 필요한 인자는 커맨드객체에 그대로 전달해줘야지 일을 할 것이다.**

14. **생각해볼 거리**

    - 인자를 받는 setter계열들은, **인자로 커맨드객체내에서의 재연을 위해, `행위 위임에 필요한 것들을 모두 인자로 가져`서 `당시의 context로 기억`해야한다. 특히, `undo, redo를 하려면, 여러번 재연& 시간이 흐른뒤 재연`하려면 `당시의 context를 모두 기억`하고 있어야하므로 `생성자의 인자`로 기억해야한다.** 

      - 그래야 task.가 setTitle(title)로 커맨드객체내에서 행위를 할 것이다.

      ![image-20220731232732398](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731232732398.png)

    - **그렇다면, `task는 인자로 받아서 기억안해도 되나?`**

      - **`필요한 것을 인자로 안받고 소유한 필드로서 계속 재활용하여 호출 `하는 이유는, `소유객체는 불변객체`라는 가정이 있기 때문이다.**

    - **그렇다면, `소유필드 task를 생성자 초기화하여 불변객체`로 만들어놔야한다.**

15. **커맨드에게 행위를 위임하고 소유될 객체는 `생성자초기화하는 불변객체`여야한다.**

    1. CompositeTask는 final로 확정되는 불변객체다
       ![image-20220731233347373](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731233347373.png)

    2. **내부에 필드가 불변이려면, 생성자에서 이미 완성된 상태로 초기화 되어야한다.**

       ![c47ce38b-db5f-4247-8644-ce582bf2c90a](https://raw.githubusercontent.com/is3js/screenshots/main/c47ce38b-db5f-4247-8644-ce582bf2c90a.gif)

       ![image-20220731233512046](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731233512046.png)

    3. **final의 불변객체라면, `내부에서 커맨드객체가 사용할 때, 그 상태를 인자로 기억안하고 필요할때마다 갖다쓰면 된다.`**

       ![image-20220731233626535](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731233626535.png)

16. **소유한 불변객체 이외에 필요한 것들은 모두 `당시의 상황을 메서드 인자 -> 커맨드 객체의 생성자`로 받아서 기억하고 있어야한다.**

    - **계속 기억되려면, 생성자로 받아야 한다.**

    - 불변객체는 불변하니까 필요할때마다 갖다쓰면 된다.
    - **`set계열의 메서드는 호출될때마다 당연히 다른 값이 들어올 것`이며, `그 당시의 context를 기억해두기 위해선, 객체의 생성자로받아 필드로 박아서 기억`해야한다.**
      - **그렇지 않으면 set계열 메서드내 인자는, 메모리에서 한번호출후 제거되는 값으로서 메모리에서 영원히 사라진다.**

    - **객체의 상태값으로 남겨야, GC에 안걸리고, 남아있다.**
      - 함수형언어의 Closure역할로서 외부상태를 보관하려고 만든 것이 생성자->필드이다.

17. 다시 setTitle에서 작동할 커맨드 구상객체 Title을 만들어보자.

    - 당시 context를 기억해야해서 생성자로 들어온 것은 final로 받는다.
    - **커맨드패턴의 특징이다. 생성당시 context들은 확정되어야하기 때문에 다 final이다.**
    - **execute는 set그대로 작성하면 된다.**

    ![22a5c894-7236-48c2-b4f0-53b25ebc942e](https://raw.githubusercontent.com/is3js/screenshots/main/22a5c894-7236-48c2-b4f0-53b25ebc942e.gif)

    ![image-20220731234350241](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731234350241.png)





18. **undo는 어떻게 할까?**

    - 마법은 없다. **set하기 전에 older로서 미리 기억해야한다. `당시 컨택스트를 기억하는 방법은 필드로 박아두는 것`이다.**
    - **execute전 상태값을 getter -> 필드에 기억 ->  old값으로서 set해서 undo한다.**
      - getter를 통해 당시 상태를 기억해야하며 기억은 필드에 한다.

    ![b9aedcdb-d9ea-47f8-9183-3893752627c9](https://raw.githubusercontent.com/is3js/screenshots/main/b9aedcdb-d9ea-47f8-9183-3893752627c9.gif)

    ![image-20220731234715864](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220731234715864.png)

    

19. **undo의 비밀은**

    - **`커맨드객체를 사용하면, 객체로서 자기만의 context`를 가질 수 있다.**
    - **실행당시의 context는 생성자를 통해 받아들여 필드로 기억하지만**
    - **`실행당시 자신의 상태도 getter -> 필드로 기억할 수 있다`**
      - toggle은 그냥 왔다갔다만 하므로 기억해야할 상태가 없어, 필드도 없다.

20. 나머지 setter들은 1개만 만들면, class자체를 복붙해서 편하게 만들 수 있다.

    - setDate

    - **인텔리제이로 복붙하면, 형+생성자도 자동으로 지정한 이름으로 바꿔주고 나머지코드들을 그대로 복붙해주면 -> 필드변경으로 파라미터까지 다 바꿔준다.**
    - 나머지 필드타입 등만 다 잘바꿔주면 된다.
      - **shift + F6으로 타입바꿔주면,,, 물린 것 다 바뀌므로 조심.. 복붙한 클래스에서는 사용안해야할듯..**

    ![854d8651-83e4-41cb-84ae-c679e27317cc](https://raw.githubusercontent.com/is3js/screenshots/main/854d8651-83e4-41cb-84ae-c679e27317cc.gif)

    

21. add/remove도 마찬가지로 `new Add( ), new Remove( )`를 때려서 만들고, 구상객체들은 복붙해서 수정한다.

    - **이 때, 또 생각해볼 거리가 생긴다.**
    - **add할 당시의 task를 기억해야만 for undo = add의 카운터는 remove -> 당시의 task를 삭제**해야한다.
    - 일단, `new Add()`부터 만들어보자.
      - **`단일객체 task의 생성자는 관리자인 compositeTask내부에서 생성하여 add시 내부에서 생성하도록 공개를 안한 상태`임.**

    ![872026ab-bfa3-421f-94eb-b13d3609942c](https://raw.githubusercontent.com/is3js/screenshots/main/872026ab-bfa3-421f-94eb-b13d3609942c.gif)

    ![image-20220801001307249](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801001307249.png)

22. **add를 카운터(undo)치려면, `당시의 add된 객체(task)를 remove`해야하는데, `task는 저장된 title, date를 재료 받아 내부에서 객체생성`하는 상황이다.**

    - **`execute 당시의 값을 getter` -> 필드에 저장하는 것과 달리,**
    - **`execute 당시의 add되는 객체task`알아야 -> 필드 저장해서 -> `remove`때릴 수 있다.**
      - 하지만, **task.add()메서드 내부에서 재료를 통해 생성된다.**

    ![image-20220801001602321](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801001602321.png)

    

    

23. **특정 메서드 내부에서 `재료받아 내부 객체 생성후 add`되는 메서드는 `생성되어 add된 내부생성객체를 반환하는 add메서드`로 정의해야한다.**

    - 기존 add

    ![image-20220801001709083](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801001709083.png)

    - remove -> add된 task를 알아야 카운터 칠 수 있다.

    ![image-20220801001917974](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801001917974.png)

    - **void가 아니라 `내부생성되는 객체를 반환해주는 add메서드`로 수정하고, `add당시 task를 반환받아 -> 필드에 기억 -> undo에서 remove로 카운터 칠 수 있게`끔 해줘야한다.** 
    - **my) `void한 add메서드는 undo를 할 수없다!!`는 결론**

24. void add를 add한 객체반환 add로 바꾼다. 

    - execute에서 반환받은 add한task를 `oldTask = `의 필드로 저장한다.

    ![18896eff-cddc-4d4a-a53d-642d999f7090](https://raw.githubusercontent.com/is3js/screenshots/main/18896eff-cddc-4d4a-a53d-642d999f7090.gif)

    ![image-20220801003537090](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801003537090.png)

    ![image-20220801003611041](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801003611041.png)



25. add의 undo는 기억한 oldTask를 remove한다.

    ![image-20220801003745633](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801003745633.png)

26. **켄트백이 말하는 대칭성은 커맨드패턴을 작성하다보면 알게 된다.**
    - **undo가 되려면 대칭성 메서드를 만들어야한다.** 
    - **undo를 하려면, 자연스럽게 인터페이스를 대칭성있게 만들 수 있게 된다.**

27. **remove같은 경우에는 인자로 소유한 불변의 task를 받아들인다. task를 기억하면 된다.**

    - 커맨드객체는 항상, 소유객체가 받던 인자를 받는다.
    - **대칭 커맨드객체 만들땐 복사를 활용하자.ㅠ -> 거의 대칭성있게 작성하니까 코드를 재활용하거나 확인할 수 있음.**

    ![a7761c31-03c1-4d34-8459-6645958c7e85](https://raw.githubusercontent.com/is3js/screenshots/main/a7761c31-03c1-4d34-8459-6645958c7e85.gif)

    ![image-20220801004519952](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801004519952.png)

28. **remove의 카운터는 add인데, add에 필요한 것은 객체가 아니라 내부생성을 위한 title, date의 재료들이다.**

    - **`카운터add를 위해선 재료들을 old로 기억`해야한다.**
      - **undo인 카운터에 사용되는 메서드를 위해 execute상태를 기억함**

    ![e77086f2-ab02-4cd0-9dfa-07a9aebea028](https://raw.githubusercontent.com/is3js/screenshots/main/e77086f2-ab02-4cd0-9dfa-07a9aebea028.gif)

    ![image-20220801010538709](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801010538709.png)

    - old필드들로 카운터add해주기

    ![image-20220801010619198](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801010619198.png)

    

29. **remove작성시 `생성자를 통해 들어온 task context`와 `execute/undo 메서드의 인자로 들어오는 task객체`의 변수명을 똑같이줘서 에러가 발생한다.**

    ![image-20220801012203811](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801012203811.png)

    - **실제 소유한 객체로서 일을 하는 놈인 execute인자는 `task`그대로 두고, context로 Remove 행위시 기억해야할 대상인 task객체 -> 불변필드는 `baseTask`로 바꾸자.**

      ![image-20220801012524235](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801012524235.png)

30. **커맨드패턴에서는 `2가지 기억필드`가 있다.**

    - **위임역할을 하기 위해, `소유객체가 필요한 context`를 `생성자로 받아` 기억**
    - **`old계열`를 붙여, `undo를 위해 execute당시의 상태`를 기억**
      - **되돌아가려고**  execute시 old계열을 기억한다.
      - **만약, `커맨드패턴을 쓰지 않고 undo`했더라면, `old계열들은 CommandTask내부에 set계열메서드마다 모두 실행당시 상태를 필드로 & 배열로 기억`해야만 한다 **
      - **하지만, `객체로 빼고, 인스턴스 메모리`를 사용함으로써 `각기 다른 사정들을 각기 인스턴스로 기억해서 해결`**

31. **CompositeTask를 `CommandTask로 감싼 후 감싼 환경속에서 새로운 객체에게 각 set계열행위들을 위임`하지않으면, 이런 기능들을 이용할 수 없다.**
    1. 아니 내 함수를 왜 또 감싸서 쓰지????
    2. **`한번 감싸고, 감싼 곳에서는, 행위들을 같은 인페의 구상객체에 위임`하면, `당시의 context를 기억해서, 원본기능에 인스턴스메모리에 실행당시 상태들을 기억`함으로써 `되돌아가기, 다시하기등의 기능`을 할 수 있다.**
    3. **행위를 행위객체에 감싸면, `감싼 객체에서 context를 필드로 기억할 수 있어서 undo, redo가 가능`하다. `Closure같이 실행당시의 context들/상태들을 기억`할 수 있다.**

    

32. **행위를 하는 대신에 `포장 공간 속에서 [행위를 command객체]`로 만드는 것이 비효율적인 것 같지만, `인스턴시 메모리 활용으로 상태저장`이외에 `execute하기전까지는 실행되지 않는 지연실행`도 할 수 있다.**

    - **함수형언어만 지연실행을 하는 것이 아니다.**

    - **행위를 객체에 위임한 순간 `커맨드객체를 만든 시간 <-> execute하는 시간`이 나눠진다.**

      - **현재는, `이해하기 좋으라고 commandTask에서 바로 execute때리고` 있지만, `실제로는 원할 때 원하는 만큼 command객체를 생성해놓고, 원할 때 execute를 때린`다**
      - **게다가 `command객체를 List로 모아두는 필드`를 가진 이유는 `값context은 index를 활용해서 원하는 갯수(1~5번까지, 짝수들만, 홀수들만)만 실행 가능`하기 때문이다.**

      ![image-20220801013034312](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801013034312.png)

    - 이렇게 **execute전 `command객체들만 모아서 지연실행`하면, `코드중복이 엄청나게 없어진다.`**

      - **이유: 우리가 쓰는 제어문은 대체할 수 없다.`즉, for문 내부가 조금만 달라져도 for문을 다시짜서 처음부터 돌리는 수밖에 없다`**
        - **제어구조를 재활용하려면, visitor패턴 or composite 쓰던지 해야한다.**
      - **`행위를 객체위임함으로써 지연실행이 가능하면, 원하는 만큼만 / 원하는 형태로 나눠서 실행시킬 수 있다.`**
      - **for문 10번실행 구문 <-> 지연실행가능하여 `for문에서는 index를 물고 있는 커맨드객체를 생성만 하고`서 `지연실행가능한 것들로서 상태를 물고 있는 지연실행 객체를 배열에 넣어두는 것`이랑 굉장히 다르다.**
        - **for문 10번 실행:  무조건 1번부터 10번까지 실행**
        - **for문 10번 index를 물린 커맨드 객체: 짝수만 실행/홀수만실행/반만 실행/ eventLister가 도착할때 실행**

    - **또한, `지연실행은 [순차적실행=동기실행]이 아닌 비동기실행`도 가능하다.**

      - **앞에 놈을 실행하고, 다 끝난 사인이 오면, 2번째놈을 꺼내서 실행**
      - **`지연실행은 비동기 실행의 기반이며, 비동기실행은 커맨드객체가 아니면 객체지향에서는 불가능`하다.**
        - js에서 promise객체는, **생성당시 context를 promise객체에 넣어놓고 then을 때리는 시점에 실행이 일어나도록 바뀌는 일종의 커맨드객체다.**

33. **우리가 진짜 하고 싶었던 것은 undo/redo**다.

    - 커맨드객체는 응용범위가 너무 넓다. 
      - 비동기 실행, 패턴화 실행, undo/redo실행 다 다르게 쓰인다.

34. **undo는 커맨드구상객체 컬렉션 필드에 쌓여있는 마지막 요소를 처리해야한다.(그래서 List로 보관)**

    - 개별 undo는 커맨드객체에 정의되어있지만, **포장하여 소유하는 Command객체**에서 호출해야한다.

    ![image-20220801115849966](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801115849966.png)

35. **제일 먼저 해야하는 일은 컬렉션에서 빼써야하는 `조회개념`으로서 `존재검증인 list의 사이즈부터 파악`해야한다.**

    - **`컬렉션 조회 사용시 존재유무 검사부터` :  0개이면, undo할게 없어서 early return**

    - **1개 이상일 때, 마지막꺼 remove후 반환되는 객체를undo()**

      - index로 remove시 반환은 삭제되는 객체다.

        ![image-20220801120342535](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801120342535.png)

    - **디자인 패턴은, 각 문제를 해결하는 가장 짧은 패턴이다. undo를 하는데 코드가 몇줄 안된다.**

      - **모든 경우에 대한 위임객체에 코드가 좀 들어가긴했지만, `각 case별 제어문의 중복코드`를 다 제거해서 코드가 짧다**

      ![89461181-e076-41c6-8909-3f0e4dfc3a88](https://raw.githubusercontent.com/is3js/screenshots/main/89461181-e076-41c6-8909-3f0e4dfc3a88.gif)

      ![image-20220801120416874](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801120416874.png)

36. undo다음에 이어지는 **redo는 어떻게 할까?**

    - **undo시 이미 컬렉션에서 command객체를 제거했는데??**

      ![image-20220801120733741](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801120733741.png)

    - 현재상태에선 불가능하다.

      - **undo만 짠다면 remove로 쉽게 짜여지지만, `redo기능이 존재할 예정이라면, remove로 커맨드객체를 바로 날리면 안된다.`**
      - 만약, undo로 바로 날리게 되면, undo -> add 시 **`마지막 index만 사용하게된다면, `undo할 위치를 덮어써서 날아가**게 된다.

37. **언두와 리두를 하는 방법은 `[마지막index만 고정대상] -> [undo로 바로 삭제]가 아니라, database에서 하는 것처럼 list-index를 커서로 사용`해서 커서가 존재해야만 한다.**

    - 나의 list에 커서가 있어서, **undo는 커서를 한칸씩 뒤로 보내기. / redo는 커서를  다시 한칸씩 앞으로 보내서 작동**하며
      - **제일 마지막 커서에 도착하면, 더이상 redo할 것이 없다.**
      - **제일 앞 커서에 도착한다면, 더이상 undo할 것이 없다.**
    - 만약, 커서없이 undo하다가 add를 한다면?
      - **커서가 없다면, 마지막index 고정대상으로 보고, 앞쪽 (직전)의 상태는 다 날아간다.**

38. **컬렉션에 커서를 도입하려면, 컬렉션 필드가 초기화되는 곳에  cursor필드를 default 0인 값의 커서로 초기화해줘야**

    ![image-20220801122007266](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801122007266.png)

39. **커서 필드를 도입했다면, `컬렉션을 건드는 곳`들(addCommand)을 수정해줘야한다.**

    - 일단, add하자마자 알기쉬운 cursor부터, **기계적으로 cursor를 + 1할려고 한다.**

      ![image-20220801122453886](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801122453886.png)

    - **하지만, `증감연산자 ++`을 사용하는 것은 `상태를 알아서 잘 관리하겠다는 말도 안되는 자신감`이다.**

      - variant로 연산하지말고, **`[불변식 -> 재할당을 통한 상태 업데이트]해서 [연산 전 원하는 상태를 확정해주는 invariant를 지키게 만들도록 상태를 변화시키자.]`**
        - variant로 관리한다면, 훨씬 더 민감하게 상태를 관리해야한다.
        - calculator의 예제의 경우, 연산전 check()하여 **원하지 않는 상태땐 스스로 죽게만들어서**, variant변수가 **원치않은 상태는 연산에 진입하지 않게 invariant임을 확정**했다.
        - cursor++의 경우, **`list의 index라는 제한범위`가 있다. `객체가 아니므로 스스로 죽지도 않고,  if cursor로 제한범위를 확인`도 해야한다. `원치 않은상태를 아예 방지하려고, 컬렉션의index로부터 cursor를 만드는 식을 통해 불변식을 만들었다.`**

      - **`상태를 맘대로 업데이트하는 대신, [불변식-검증을 거쳐서 원하는 상태로 만듦]을 쓰면 [원치않은 상태 방지를 위한 if]가 없어진다.`**

      - **`컬렉션의 커서를 +1하는 [불변식]은, cursor++를 상태를 바로 건들 + if로 제한범위 확인하는 것이 아니라, [현재를 상태를 구한 식(0도 마지막 인덱스)을 생각해서, 안전범위(size를 통한 index도출하여 range확인안해도됨)가 되도록 내부요소만 수정하여 변화된 상태를 만들어야한다.(여기선 api가 알아서 마지막 인덱스 구해줘서 내부요소 변화x) -> [컬렉션.size() - 1]`이다.**

        - **cursor라는 상태를 변화시키지말고, `상태변화된 새 값을 재할당|반환  or  상태변화된 새 객체을 재할당|반환`해주자.**

        ![5187030b-5413-4181-ac25-3a47cba69148](https://raw.githubusercontent.com/is3js/screenshots/main/5187030b-5413-4181-ac25-3a47cba69148.gif)

      ![image-20220801123410590](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801123410590.png)

40. **이제 `상태변화하는 cursor를 불변식으로 확정`시켰다면, `어플리케이션에서 새로운 행위를 할 시(addCommand),  현재커서를 마지막 index로 만들어서, cursor+1~마지막index의 command는 삭제`해야한다. 그리고 나서 `현재시점에서 +1개`를 해야한다.**

    ![image-20220801132422944](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801132422944.png)

    - **`추가한 행위에 대한 undo하기 위해  현재커서가 마지막 인덱스 위치`에 남아있어야한다. 그 뒤로는 안돌아가게 한다.**
    - **`새로운 행위인 add(cmd)를 하기 전`에 `만들어놔야하는 일`어나는 일들이다.**

41. **제일 마지막index에서부터 cursor+1까지 까면서 삭제해야한다.**

    - **`cursor+1부터 출발하면, cursor가 제일 마지막index인 경우, indexError가 발생할 것이다.`**
    - **컬렉션의 불변식**(원치않은 range상태 방지)인 **.size() - 1**을 **시작 index로 주자.**

    ![image-20220801163219752](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801163219752.png)

    - intellij는 표준api인 subList를 이용하라고 한다. **하지만, 가독성이 안좋아 다시 ctrl+z**
      - 성능도 for가 더 좋다.
      - 마지막인덱스보다 cursor가 작을 경우만, cursor+1~마지막index까지 subList를 만들고, clear때려서 삭제해라..?
        ![972c0fec-a546-44b1-8b12-ba5f6af205c9](https://raw.githubusercontent.com/is3js/screenshots/main/972c0fec-a546-44b1-8b12-ba5f6af205c9.gif)
    - 노란색보이면 한번은 보자.



42. **이제 undo시, cursor처리를 해야한다.**

    - 기존
      ![image-20220801134108630](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801134108630.png)
    - **우리는 `커서 도입`순간부터 `commands 자체를 바탕으로 invariant기준을 잡으면 불변식 위반이다. 이미 커서로 commands를 다루기로 약속한 상태 `이다.**
      - **커서를 불변식으로 유지하고 이를 바탕으로 움직여야한다.**
      - **커서가 잘못되어도 commands에 의해 작동할 수도 있다.**
    - **`commands 컬렉션필드의 [원치않은 상태 방지 invariant 검증]은 모두 cursor 기준으로만 작성해야한다.`**
      - 커서가 237이라도, commands.size()가 0이 아니면, 작동하게 되어버린다.

43. **`(기존)undo는 [마지막index에 있는 것을 꺼내 실행하여 없애는 것]이 remove`하는 것인데, `(바뀔)커서로 치면, 현재 것을 얻고 1칸 왼쪽으로 커서 이동`해야한다. `cursor는 0이 마지막 위치이며, 0보다 작아지면, 현재것을 얻을수도&왼쪽칸으로 갈 수 없는 상태로서 작동불가능`이다.**

    - index == cursor == 0까지는 undo할게 0번자리에 남아있다.
      - **size == 0 은 아예 없는 상황이다. 이것과 동치는 `cursor < 0`이다.**
      - cursor 0은 데이터 size가 1개라는 것을 생각

    ![image-20220801140127652](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801140127652.png)

    - **cursor를 통한 불변조건(원하지 않는 상태 방지)을 만들었다.**

44. **이제 undo할 커맨드객체를 `commands의 index`를 통해 가져오면 안된다. `커서를 통해 get으로 가져와야한다`**

    - 기존

      ![image-20220801140246971](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801140246971.png)

    - **이 때, cursor위치의 command를 얻고나서 `커서는 --로 한칸 줄어야한다`**

      - **remove하여 강제로 마지막index를 없애는 대신**
      - **`현재것을 얻고, 커서만 1칸 뒤로 가는 것이 undo`로 바꼈다**

    - **`이미 cursor는 <0이 아니라 안전한 invariant상태라서-> 단항연산자|증감연산자를 써도 된다.`**

      - **`cursor상태의 것을 얻으면서 && --증감연산자를 써서 바로 1칸 왼쪽 이동시킨다`**
        - 이미 cursor < 0상태라서 **빠른 상태 업데이트가 가능하다**

      

      ![image-20220801141814997](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801141814997.png)

45. **현재 트랜잭션 추가처리할 것도 없는 지역변수로 `쿠션`을 준 상태다. `이해하기 쉬우라고`**

    ![image-20220801141944103](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801141944103.png)

    - **원래는 쿠션안준다.**

      ![image-20220801142021460](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801142021460.png)



46. **사람들은 단항연산자|증감연사자를 쓰지말라고 한다. `멍청하기 때문에 이해를 못해서`**
    - 해당 문장을 순서대로 파악해보자.
    - **가장 먼저 일어나는 것은 첫번재 괄호다. `메서드의 호출은 괄호안을 해소해야 메서드가 호출`된다.**
    - **`그래서 cursor만 빼내서 메서드에 들어가고 -> --된 cursor가 return되어 업데이트된다.`**
    - **`그 결과, 체이닝된 메서드 호출이전`에 `이미 cursor는까여져있다`**
      - **my) 증감연산자 이후 체이닝은 까진상태!!!**
    - 체이닝한 이유는 **딴짓을 못하게 트랜잭션으로 만들기 위함이다.**
    - **트랜잭션 내에서 체이닝 속에서 증감연산자로 cursor를 업데이트하는 이유는, `트랜잭션이 아니라면, cursor를 가지고 또다른 짓을 할 가능성`이 있기 때문이다.**



47. 드디어 **redo를구현할 수 있는 기반(cursor기반의 commands작동)**이 마련되었다.

    - **redo도 undo처럼, `커서기반 행위하기 전에, 커서를 invariant로서 먼저 검증`해야한다.**

      - **커서가  마지막인덱스에 놓인 상황**이면, **`더이상 커서를 앞으로 갈 수 없으`므로 커서를 한칸 앞으로 땡기는 redo**를 할 수 없다.

      ![image-20220801161837387](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801161837387.png)

48. **redo는 현재커서의 것을 execute하는 것일까?**

    - **undo는 `현재커서의 커맨드객체가 execute한 상황`에서 `현재 커서의 커맨드객체에 정의된 카운터 undo를 실행시키면` 해결되지만**

    - **redo는 커맨드객체에 정의되어있는 것이 아니라서 `현재 커맨드객체를 redo하는 것이 아니다.`**

      - **redo는 (1) 해당커맨드 execute (2) `해당커맨드 undo해서 카운트치면서 cursor를내린 상태`에서 시행된다.**
      - **즉, `이미 (1) execute된 커맨드객체`에 대해 `그곳으로 커서를 +1 옮겨 redo`하려고 한다.**

      

    - **즉, `redo의 대상은 cursor를 먼저 ++해주고 execute`해줘야한다.**

      ![image-20220801163300725](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801163300725.png)

      - 참고) **`컬렉션에 add와 동시에 cursor도 이동은 set`다. 여기서 add시 실행도 한번 일어나는데, `add된 객체 == cursor를 add된 객체로 이동 == add되어 실행될 커맨드객체 위치`에서, 현재 커서위치의 command는 add와 동시에 실행된 상태다.**

        ![image-20220801163953377](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801163953377.png)

      - **또한, `인자로 들어온 command`에 대해서 -> `[add(커맨드관리코드) + 커맨드 실행] 둘중에 뭐가 먼저 일어나도 상관는 없다`**

        ![image-20220801164255195](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801164255195.png)

      ![image-20220801163934558](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801163934558.png)

    - **어찌 됬건, `add된 마지막index위치로 들어간/들어갈 command객체는 이미 실행된 상태`다.**

49. **redo를 할 땐, `현재 커서의 위치 == 이미 쓴 command의 위치`인데 이것을 다시 실행하진 않는다.**

    - **redo의 대상은 `현재커서[=이미쓴 객체]의 다음위치`이다.** 
    - **undo의 대상은, `현재커서[=이미쓴 객체]의 카운터함수`를 실행하는 것이므로 다르다.**
      - **cursor는 invariant상태이므로, 증감연산자를 앞에 달아서, 먼저 증가시킨 뒤 실행되게 한다.**

    ![image-20220801164617685](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801164617685.png)

    

    

    

50. **Main에서 수정**

    1. **일단 `root`객체의 CompositeTask형을 `포장 소유한 CommandTask`로만 바꿔주면, 나머지 기능들은 다 매핑되어서 작동하게 된다.**

       ![03c92406-ae47-44ae-a2fb-b67f999a720f](https://raw.githubusercontent.com/is3js/screenshots/main/03c92406-ae47-44ae-a2fb-b67f999a720f.gif)

       ![image-20220801165225519](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801165225519.png)

    2. **다음으로 `.addTask()`가 `add한 객체를 return하는지 안하는지` 살펴본다.**

       - **커맨드 구상객체(new Add())는, `add시 undo를 위해 내부생성하여 add될 객체를  인스턴스메모리(필드)에 저장하기 위해 반환`했었지만**

         - Add

           ![image-20220801171539536](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801171539536.png)

       - **`(컴포짓 객체, 행위객체)포장 소유 커맨드객체(CommandTask)`는 add시 객체를 반환하지 않는다.**

         - CommandTask

           ![image-20220801171648977](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801171648977.png)

         - **커맨드객체는 원래 return할 수 없다. **

         - **커맨드객체가 객체를 return하려면, 처음부터 return하는 인터페이스를 써야된다.**

       - **`일반적으로 커맨드객체의 메서드는 객체를 return하지 않는다.`**

         - 이유는 여러가지가 있으나 **`커맨드객체가 커맨드 구상객체를 return한다 ==  커맨드 구상객체 만들자말자 execute했다는 뜻  ==무조건 동기적으로 움직인다.`는 뜻이다.**
           - CommandTask

             ![d0a91003-3af9-4c6b-9dbe-0cd5b7159b06](https://raw.githubusercontent.com/is3js/screenshots/main/d0a91003-3af9-4c6b-9dbe-0cd5b7159b06.gif)

           ![image-20220801172048390](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801172048390.png)

         - **그러나 `커맨드 구상객체의 장점`은 `마음대로 지연실행가능하다`이다.**
           
           - return으로 보고하지 않고 -> return을 콜백으로 보고하던지 뮤텍스에?보고하던지 한다.
         - **그럼, 비동기적으로 복잡하게 작동하는 커맨드객체는 어떤 이링 생길까**
           - **값을 모아야하는데 (aggregate) 어떻게 한다?**
           - **커맨드객체 내부에 aggregator필드를 만들어놓고, 실행될때마다 값을 모아준다?!**
           - **보통 이 agreegator는 invoker가 담당한다. 커맨드task에서는 root가 해당한다.**
             - **자신의형을 물고 태어나서 반환하도록 하는 컴포짓패턴 시작점을 말하는 듯**
         - return값들을 쌓아서 다시 return
    
   - **프레임워크 작동원리**
    
         - **visitor패턴으로 제어를 역전한 다음**
     - **실행명령을 반복하거나, 처리하거나 `쌓아두기`위해 `커맨드객체로 구현`되어있다.**
    
3. **기존에  report를 만드는 기능들을 주석처리하고, ConsoleVisitor를 살려 올려서 뿌려보자.**
    
   ![da8a6f1b-28e5-4aff-b98c-208af2099ed1](https://raw.githubusercontent.com/is3js/screenshots/main/da8a6f1b-28e5-4aff-b98c-208af2099ed1.gif)
    
4. **이제 addTask2번을 출력하고, undo때린 뒤 출력해보자.**
    
   ![image-20220801175400095](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801175400095.png)
    
   ![image-20220801175409848](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801175409848.png)
    
   ![image-20220801175510873](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801175510873.png)
    
5. redo하고 출력해보자.
    
       ![image-20220801175605782](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801175605782.png)



51. **다시 commandTask로 가서 `set계열이어서 상태를 변화하던 행위를 매핑`했던 친구들을 보자.`서비스 메서드에 래핑되어있지만, 1개의 메소드(addCommand())로 행위를 사용할 수 있다.`**

    - **private인 `addCommand( new커맨드객체( ) )`**를 호출하고 있다.
    - **private메서드만 호출하고 있다면, `사실은 addCommand()를 private만 public으로 바꾸고 & 커맨드객체를 밖에서 호출 만들어서 호출`하도록 바꾸면, `없어도 되는 서비스 메서드들`이다.**
      - **`밖에서 커맨드객체를 직접 만들어 입력하는 것이 불편해서, 재료정보만 파라미터로 받아 대신 커맨드객체 add후실행을 호출`해주는 `command generator가 내장되어있는`형태를 띄고 있다.**
      - 딱히 커맨드객체는 internal이나 private으로 만들지 않았으므로 

    ![image-20220801223613922](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801223613922.png)

52. **또 좋은 점?? 커맨드패턴을 쓰면?? `객체에 메서드가 하나도 필요없게된다.`**

    - **왜?? 모든 메서드가 `외부 커맨드 구상객체들로 공급`되니까**

    - 모든 행위들이 **컴파일 타임에 `확정된 메서드로 정의`되는 것이 아니라 **

    - **`커맨드객체를 쓰는 객체는 public addCommand()외에는 노출할 메서드가 없다`**

      - addCommand만 있으면 **`내부의 다른 메서드들도 (현재 있지만) 다 필요없게 된다.`**

    - **컴파일타임에 정의된 메서드들을 다 지워버리고, 런타임에 투입된 커맨드구상객체들의 조합으로 행위가 정해진다.**

      - **`그로 인해 객체가 범용객체가 된다`**
      - **`커맨드 패턴을 사용하는 커맨드홀더==invoker객체들은, 전부 사실상 메서드가 필요없다.`**
        - 행위를 위임한다는 의미는 이런 것이다.

    - 커맨드패턴 기반으로 만든 프레임웤에는 모두 포조객체같은 것들만 있다. 왜냐면, **커맨드홀더, 커맨드invoker만 있으면 된다. 어떤 행위를 하고 싶으면 커맨드구상객체만 넣으면 된다.**

      - **Member클래스(커맨드홀더, invoker) -> Member관련 command구상객체만 받으면 Member가 된다.**
      - admin도 admin관련 커맨드구상객체만 넣어주면 된다.

    - **`범용적인 invoker객체 생성` -> `UI의 버튼을 통해 클릭시, 해당 커맨드 구상객체만 넣어주면` -> `객체탄생`**

      - **홈페이지 빌더에 인터랙션 가능한 객체를 만들 때, 해당 커맨드만 넣어주면 된다.**
      - 예를 들어, 버튼에 helloworld를찍어주는 execute를 가진 커맨드객체를 넣으면, 버튼 누를때마다 helloworld를 계속 찍어준다.
      - 만약 커맨드슬롯이 3개가 있다면, 그 놈은 command1.execute() ... 이런식으로 3개만 받아서 실행하게 된다.

    - 1클래스에서 생성한 객체1, 객체2가 차이점을 가지고 있다면, 매번 클래스를 생성하는게 아니라 **차이를 만드는 궁극적인 방법은 1개의 범용객체 + 차이점 부분만 커맨드패턴을 주입받도록 만드는 것이다.**

    - 커맨드패턴은, 들어온 것 모두 동일한 execute를 실행하는 것이니 `어댑터패턴`과 마찬가지다

      - 그외 visitor와 같이 하는 전략패턴, 상태패턴, 책임사슬패턴 등...

    - **사실상 커맨드패턴을 쓰는 순간 addCommand()만 있으면 되고 바깥껍데기는 서비스메서드라서 `사망된 것`이다. `그외에는 다 메서드내부의 context에 의존성을 가진 메서드`이다**

      - **객체에 의존성을 가진 메서드라면, `객체가 바뀌면 메서드는 죽는 메서드`이다**
      - **하지만, 커맨드패턴의 서비스메서드들은 안죽는다.**
      - **`커맨드 구상객체를 쿠션삼아서 일을 처리하게 되어 [중계만하고 의존성이 없어진다.]`**

      ![image-20220801235406574](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801235406574.png)

53. **커맨드패턴이 무적같지만, `undo/redo만 되고 save/load는 안된다.`**

    - 8비트 게임들은 보물상자 잡몹상태 등 다 기억된다.
    - **어떻게 save/load했길래, 모든게 다 기억되고 `객체 상태가 복구`되는가**
    - **진정한 프레임웤을 만들려면, `시리얼라제이션 == 영속화`해야한다.**

54. **진정한 프레임웤으로 가는 길**

    1. **visitor를 익혀야한다.** -> **그럴려면, composite(제어역전 필요한 것)/전략패턴(제어역전 기반)을 배우고 와야한다.**

       - **visitor패턴은 `제어가 필요한 객체(컴포짓객체)`에 대해 `Renderer의 종류별 XXXRenderer를 안만드는 대신, 전략패턴을 적용한 XXXVisitor를 끼어넣는다`**
       - **데이터를 가지고 제어될 객체인 `컴포짓객체는, Renederer의 메서드인자 -> 방문하는 추상Visitor의 메서드인자로 들어가 사용`된다.**

       ![image-20220802000506351](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802000506351.png)

    2. **커맨드 -> `제어에 참여하는 객체를 포장`하고, 메서드는 1개로 제공하고 커맨드구상체로 빼서 `유연하게 포장된 범용객체로 만들고, 행위는 모두 커맨드들로 분리하는 것`**

       - **`visitor가 사용(소유?)하는 컴포짓객체를 -> 범용 커맨드홀더 객체로 만들면, 다양한 행위를 외부에서 다양한 커맨드객체로 대신하게 할 수 있다.`**
       - **`visitor는 참여객체를 -> 커맨드홀더만 소유함으로써, 행위변동/메서드변동에 대해 안정화된다.`**

    3. **메멘토 -> 모든 것을 저장한다.**

55. **마법은 없다. `저장할 수 있으려면, 모든 상태를 시리얼라이즈`할 수 있도록 `시리얼라이즈 메서드를 구현`해야한다**

    - 반대로 load시에는 시리얼라이즈된 데이터로붙 객체를 생성하는 generator가 있어야한다.

56. **일단 시리얼라이즈를 완성하기 위해 `Renderer에 방문하는 JsonVisitor에서 트레일링콤마를 회수`해야한다.**

    1. **`컴포짓객체를 메소드 인자로 받아 제어에 참여시키는 하는 Visitor`의 오퍼레이터는 `자신의 일처리 == drawTask` + 자식들 동적트리순회 +  `끝처리 == end` 2개의 메서드가 있다.**

       ![image-20220802001344887](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802001344887.png)

       ![image-20220802001357508](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802001357508.png)

    2. **이 visitor는 동적트리순회의 재귀에 돌면서, `재귀를 타는 모든 컴포짓객체마다 항상 호출`된다.**

       ![image-20220802002049908](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802002049908.png)

57. **끝처리에서 마지막 트레일링콤마를 회수하는 방법은 `end메서드가, [현재끝인지 아닌지(==Level선상 마지막 !=마지막레벨)]를 알 수있도록 -> [재귀 속 end 메서드]의 파라미터에 불린 isEnd를 주고 업데이트 시켜야한다`**

    - **`재귀속 메서드의 파라미터 -> 재귀메서드의 파라미터에서 와야 -> depth마다 업데이트되는 변수`가 되므로 `재귀함수 render에도 파라미터를 추가`해야한다.**

    1. 컴포짓용 재귀(Renderer-render)속에서 **`역전된 제어에 참여하는 visitor객체`의 인터페이스에서 end메서드 시그니쳐에 불린 `isEnd`를 추가한다.**

       - 인터페이스의 시그니쳐변경하면, 구현체들 알아서 바뀜

       ![57dba129-32ce-4964-a8ac-b9cfc5341f13](https://raw.githubusercontent.com/is3js/screenshots/main/57dba129-32ce-4964-a8ac-b9cfc5341f13.gif)

       ![image-20220802002805348](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802002805348.png)

58. **JsonVisitor end처리에서, `항상 마지막에 붙여줬던 콤마를 조건부 붙이기`로 수정한다.**

    - println이 아니라 print로 붙여줘야한다.

    ![d223e0dd-5fa2-4dbd-951a-15f6f9bdd8d4](https://raw.githubusercontent.com/is3js/screenshots/main/d223e0dd-5fa2-4dbd-951a-15f6f9bdd8d4.gif)

    ![image-20220802003812553](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802003812553.png)



59. **이제  `재귀를 돌리는 renderer에서 [재귀 내부 메서드가 업데이트되는 파라미터를 가지고 싶으니, 재귀 당사자의 파라미터에 추가] -> [동적꼬리순회에서 <<트리의 각 LEVEL상 마지막 객체>> 마지막일때만 true가 들어가도록]`수정해줘야한다.**

    - 기존

      ![image-20220802004217882](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802004217882.png)

    1. **재귀 내부 메서드(end)가 업데이트되는 파라미터를 가지고 싶으니, 재귀 당사자(render)의 파라미터에 추가**해준다.

       ![fb9505ef-0ede-46b3-b7f7-264fa78bc5a8](https://raw.githubusercontent.com/is3js/screenshots/main/fb9505ef-0ede-46b3-b7f7-264fa78bc5a8.gif)

       ![image-20220802004816579](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802004816579.png)

    2. **`최초 재귀호출의 인자`에서 컴포짓객체처리는 `root에 level에서의 정보들`이며, `같은 레벨선상에서 마지막인가?`를 물어보고 맞으면 `true`를 넣어줘야한다.**

       ![image-20220802005023035](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802005023035.png)

    3. **`재귀 속 end메서드의 인자는 [직접 업데이트하지 않고, 현재 재귀의 정보를 이용해서 메서드 처리해하는 것이므로 파라미터를 그대로 받아먹어야]한다.` -> `파라미터 업데이트는 [동적트리순회 속 자식들로 depth가 깊어질때만 업데이트]`시킨다고 생각해야한다.**

       - 동적트리순회속 depth깊어지는 자식재귀의 인자를 제외하고는, **최초호출인자 -> 정의부에 그대로 반영해서 사용**해야한다.

       ![image-20220802005429953](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802005429953.png)

60. **이제 자식들을 동적트리순회할 때, `순회를 도는 list 마지막 == Level상의 마지막 번째`에만 true를 인자로 넣어줘야한다.**

    - **여긴 list라서 마지막 인덱스를 불변식으로 알 수 있긴한데...?**

    - **`set의 loop라고 가정`하고 `index를 알 수없다고 가정`하고 직접 세어야한다.**

      - **.size()를 때리면, 전체길이는 안다. 다만 index를 알 수 없다.**

    - **`index를 모르는 loop의 마지막을 찾는 방법`은**

      1. 반복문위에 업데이트 변수를 **컬렉션의 길이**로 초기화한다.

         - **0부터 시작안하는 것이 신기하다.**
         - **`만약, 0으로 시작했으면 반복문 내부에 끝인지 검사를 매번 if i == list.size()로 api를 호출해야하며, 만약 [조건문속 메서드 호출]을 상수 값으로서 위쪽에 뽑아놓는다면, 0 시작변수 + 끝 상수 2개를 변수로서 초기화해야한다`**
         - **`my) 끝인지 검사는 컬렉션.size()를 출발변수로 앞?뒤? 판단해서 --로 먼저 깍아 변수 업데이트해서, --size변수== 0인지 검사한다.`**

         ![deffd6ec-cf68-4587-b8df-da48ed8c350c](https://raw.githubusercontent.com/is3js/screenshots/main/deffd6ec-cf68-4587-b8df-da48ed8c350c.gif)

         ![image-20220802013652456](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802013652456.png)

      2. **인덱스를 모르는 컬렉션의 끝인지 검사(boolean)은 size 상수 시작 -> 먼저까고 > 0인지 검사해서, 끝이면 true다**

         - **`만약, size가 2라고 치면 2로 시작할텐데`**
           - 첫번째 루프 ->  level선상 첫번째node -> 안까면2, 까면 1
           - 두번째루프 -> 2번재node -> 안까면1, **까면 0**
           -  **size시작변수는 마지막 요소일 때 `먼저까고 0인지 검사`하면 된다.**

         ![97150473-fcd7-4855-92f2-84d388ad2f2c](https://raw.githubusercontent.com/is3js/screenshots/main/97150473-fcd7-4855-92f2-84d388ad2f2c.gif)

         ![image-20220802015257581](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802015257581.png)

61. **이제 Main으로 돌아가서 JsonVisitor를 통해, commandTask를 찍어서 `트레일링콤마가 빠지나 확인`하자**

    ![add4f3a9-d0f0-458b-9568-e56dcce58bf1](https://raw.githubusercontent.com/is3js/screenshots/main/add4f3a9-d0f0-458b-9568-e56dcce58bf1.gif)

    ![image-20220802015851294](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802015851294.png)

    - level상 마지막들은 콤마를 안찍는 것을 확인할 수 있다.

      ![image-20220802015938042](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802015938042.png)

    - **isEnd가 아닌, 중간들만 콤마가 붙는다.**

      ![image-20220802020037789](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802020037789.png)

    

62. **json의 트레일링콤마를 해결했다면, 이제 `직렬화가 가능하므로, 메멘토패턴으로 save를 구현`해야한다.**

    1. **command holder(invoker)인 `commandTask`의 제일 꼭대기에다가 `public void save()`를 만든다.**

       - 메멘토로 만든 시리얼라이제이션을 **1. 외부출력하는 경우, 2.내부 기억하는 경우**가 있다.
       - 일반적으로 프레임웤들은 내부에 기억한다.
         - 외부에 노출되면 변조될 수 있다.

    2. **내부에 기억하는 경우는 `기억한 것을 찾기 위해`일반적으로 `String key`를 파라미터로 줘서 `외부에서 저장할 key를 공급받아 save한다`**

       ![05154a7f-817e-4a6f-a661-8bce27954215](https://raw.githubusercontent.com/is3js/screenshots/main/05154a7f-817e-4a6f-a661-8bce27954215.gif)

       ![image-20220802122342446](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802122342446.png)

    3. **public void load도 해당String key로 찾을 수 있도록 파라미터를 줘서 생성한다.**

       ![image-20220802122626771](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802122626771.png)

    4. **Main에 있던 `Renderer 생성후 root를 .render()`호출하는 2줄의 코드를 복사해온다.**

       ![image-20220802122445317](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802122445317.png)

    5. **key로 save하고 load한다면 `커맨드홀더 내부에  hashMap 빈컬렉션 필드`를 만들어서 저장소로 둔다.**

       - **이 때, String으로 보아둔 json을 저장하므로 String(key), String(JsonString) 자료구조다**

       ![image-20220802122823288](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802122823288.png)

63. **이제 출력하는 JsonVisitor를 상태를 토해놓도록 sout 대신 문자열 모음으로 바꿔야한다.**

    - 기존
      ![image-20220802122924009](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802122924009.png)

64. **메서드내부에서 처리된 문자열들을 `visitor구상체로서 상태값을 선언한 뒤, string을 모아야`한다.**

    ![image-20220802123056464](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802123056464.png)



65. **더이상 `출력하지 않으므로 padding은 관심사가 아니다.` 출력확인후 padding관련내용은 삭제해줘야한다. **

    ![ccc83e38-0bb7-4874-9836-482428719f7a](https://raw.githubusercontent.com/is3js/screenshots/main/ccc83e38-0bb7-4874-9836-482428719f7a.gif)



66. **이제 json을 구성하는 문자열들을 result에 누적합으로 더해주자.**

    ![2d67c75b-0719-4d38-b31e-962f2477fc97](https://raw.githubusercontent.com/is3js/screenshots/main/2d67c75b-0719-4d38-b31e-962f2477fc97.gif)

    ![image-20220802123751156](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802123751156.png)



67. **더이상 `상태없이 유틸메서드를 담는 객체로서 JsonVisitor를 매번생성`하는게 아니라 `상태를 가져 재활용되는, 상태필드에 string을 모으는 객체`로서**

    - **Renderer에 방문하는 Vistor를 `함형factory를 통한 매번 지연생성`이 아니라 이미 생성된 객체를 반환하도록 `람다식을 () -> 이미 생성된 객체`로 바꿔줘야한다.**

    - **왜 이미 생성된 객체를 넣어줄까???**

      - **재귀내에선 `내부생성 -> 재귀 타는 것`이나 `외부주입 -> 재귀타는 것`이나 `재귀내에선 재활용되면서 상태업데이트(result에 모으기)는 똑같은데??`** 

      ![image-20220802130921137](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802130921137.png)

    - **왜냐면, saved.put( )에 visitor객체가 가진 result를 getter로 받아서 넣어줘야하기 때문에, `출력용으로 내부생성하면, 반환로직이 없는 상태다.`**

      - **`my) 출력관련 객체(ViewRenderer 출력용JsonVisitor)에서는, return이 없으므로 유틸객체는 factory로 받아 -> 매번 내부생성후 사용하자`**
      - **`객체의 데이터를 꺼내쓸 외부에서 객체를 먼저 생성하고` -> `Reneder에 주입되서 객체상태변화`하고 -> `다시 외부에서 변화된 상태의 값을 getter로 얻어야한다`**
      - **`my) 데이터를 상태값으로 저장하는 객체(saved에게 데이터 제공용 JsonVisitor)에서는, 외부에서 getter로 return을 해줘야하므로, Visitor로서 방문하기 전에 객체를 미리 선언해놓고, 방문하면서 업데이트하자.`**

    - **바깥에서 생성한 포장 소유 CommandTask객체인 root대신 `내부 CompositeTask객체를 직접 사용`하도록 변경하자**

      ![089d0c1e-320c-4d2d-bd3a-88a67091ec75](https://raw.githubusercontent.com/is3js/screenshots/main/089d0c1e-320c-4d2d-bd3a-88a67091ec75.gif)

      ![image-20220802133522435](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802133522435.png)



68. **JsonVisitor는 Visitor오퍼레이터 이외에 `자신만의 getter .getJson()`을 만들어서, svaed에 `시리얼라이즈된 데이터를 put`가능하게 해야한다.**

    - getter호출전에 이미 방문한 상태로서, result에 jsonString들을 모아놨으므로 return result만 해주면 된다.

    ![image-20220802133840696](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802133840696.png)

    ![image-20220802133911456](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802133911456.png)

69. save될 결과를 main에서 찍어보기

    - **save를 load없이 찍어보려면, `시리얼라이즈되는 로직을 가지고 와서 visitor.getJson()을 출력해야한다.`**

      ![image-20220802171007290](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802171007290.png)

      ![image-20220802171235714](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802171235714.png)

70. **메멘토는 visitor로 구현하게 되면 껌이 된다.**

    - 그러나 Composite패턴과 visitor구현이 안되서 일반적으로 메멘토 구현이 안된다.
    - 메멘토는 visitor동네이다.
    - 커맨드가 대단한 것은, bridge, adapter, 커맨드패턴이 한 동네이다. 
      - 행위를 포장하는 것이라 더 큰 동네이다.
      - 더 힘들다.
        - 전략패턴동네 -> 컴포지터+비지터 -> 전체의 40%
        - 나머지는 커맨드패턴의 동네
    - composite, visitor가 자유로우면 중급개발자라 본다
      - 무한트리를 쓸 수 있으면, 실무를 해도 된다.

71. **load는 과제이다. 일단 key를 통해 saved에서 json String을 받아온다.**

    ![image-20220802142125148](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802142125148.png)

71. 범용객체로서 커맨드들을 runtime에서 교체할 수 있는 것 : 커맨드패턴

    - xml그 커맨드들을 설정해놓으면, 스프링빈즈가 가서 runtime에조립한다.
    - 빈즈는 거대한 커맨드객체다
    - @autoweird등 애노테이션을 달면, 범용 커맨드객체를 만들어서, 애노테이션애들을 끼워넣는다.
      - 범용객체를 만들었으니 끼워진다.
      - 다 커맨드invoker로 만들어진다.
    - @Transactional을 하려면, 상태를 저장해야한다.
      - 메멘토 패턴을 이용한다.
      - undo/redo를 할 수 있다. 트랜잭션으로서 롤백을 할 수 있다.
      - 내가 만든 메소드도 롤백할 수 있게 된다.

72. 숙제 시작

    1. 기존 composite객체를 동적트리 순회하는 것 살펴보기

       1. **Composite객체 자신**이, 반환형을 만들 때 **내부순회하는 재귀를 돌리며 report형 만들기**

          - 자신내부의 재귀메서드 정의 -> 자신처리  -> **필드 자식들**돌면서 -> 자식들객체. 재귀메서드호출 -> 끝처리
          - 끝처리가 새로운객체반환일 경우 자식들도 반환하니, 새로운객체에 add해줘야함

          ![image-20220802171628938](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802171628938.png)

       2. **바깥의 객체가 메서드 인자로 받아서 돌리기**

          - **바깥 객체** Renderer가 composite객체(report)를 **재귀를 돌려주는 상황**
            - 생성자로 comsposite객체를 받아도 되지만,
            - **바깥에서 따로 돌려주는 객체가 있다면, 제어역전 객체로서,**
              - **생성자에는 visitor가 방문하게 한다**
              -  **visitor가 대신 라이프싸이클을 타면서**
          - **`제어역전 객체의 재귀 메서드의 인자`로 compoiste객체가 들어오는 상황**
            - visitor의 메서드 인자로 composite객체가 들어가서자신의 일처리 -> 
            - getter로 자식들꺼내줘서  자식들 처리 -> 재귀
            - 끝처리에는 주로 composite객체 자신을 이용안한다 -> visitor가 끝처리

          ![image-20220802171957486](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802171957486.png)

    2. **참고, prev데코객체는 컬렉션 필드 자식들이 아니라, 같은형의 단일객체 필드를 가지고 돌아야한다.**

       - **Rule이라는 데코객체를, Calc라는 `바깥 포장객체`가 `돌리는 상황`** -> **`내부필드로서 메서드인자로 안받아 돌릴 수 있음`**
       - **또한, `재귀 속 자신 -> 자식들getter -> 자식처리 ->끝처리` 대신 **
         - **`자식들 트리순회를 안해도 되니 -> while != 시작특이점객체의 [루프]`로 돌릴 수 있다. **
         - **`1개씩 자신처리 메서드만 반복호출`하면 되니**
         - **마지막에 `자신 -> 다음 것으로 업데이트`만 해주면 된다.**
       - **꼬리재귀 형식이며, 1번씩 계산하여 `반복문 위에 누적변수에다가 누적해준다`**
         - **데코객체를 통한 누적처리는 `해당사항 없을 시 기본값 반환`으로 처리해줘야한다.**
           ![image-20220802174123286](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802174123286.png)

       ![image-20220802174003066](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802174003066.png)

    3. 힌트

       1.  복원하려면, 기존의 task에 있던 모든 subtask들을 날리고 봐야한다.
          - removetask도 있고, getList도 있으니 가져와서 다날린다.
       2.  json을 파싱하여 composite로 순회하면서 task에 끼워넣어줘야한다.
          - json이 한단계내려갈 때, target되는 task도 같이 한단계내려가야한다.

73. **현재 CommandTask라는, Composite을 포장하는 바깥객체**에서 **재귀순회 돌아야 하는 상황**이므로 

    1. **`내부에 없다면 생성자 or visitor객체 + 메서드인자`로 받아서 재귀 메서드가 받아서 돌린다**

    2. **`내부에 필드로 존재한다면, 필드사용`으로 재귀를 돌려줘야한다.**

    3. 현재는 바깥포장객체 CommandTask가  CompositeTask를 싸고 있는 상황에서 load를 만들어야하므로 **내부필드를 이용해서 돌리면 된다. 그래도 바깥이므로, 재귀로 돌릴 거면, getter로 자식들을 뽑아서 동적트리순회 돌려야한다.** 

       ![image-20220802174857080](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802174857080.png)

       

74. 일단 들어오는 key로 존재검증 + value의 json포맷 첫글자 검증을 한다.

    ![image-20220802181138130](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802181138130.png)

    ![image-20220802181454647](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802181454647.png)

75. **load를 위해서는 `내부필드로 가지고 있는 Composite객체라면, 속을 먼저 비워야`만, 다시 새로운 composite객체를 만들 수 있다.**

    - **save로 데이터가 저장된 필드 composite task는, 내부를 초기화 해줘야한다.**

      ![image-20220802185008456](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802185008456.png)

    - 컬렉션의 삭제시, 존재검증을 해줘야한다.
      ![image-20220802185344377](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802185344377.png)

76. **자식들의 삭제도 `동적트리순회`이므로 `반복문 속 재귀로 호출`되어야하며, `끝처리에서 자신의 자식들을 clear()해줘야한다.`**

    - 자신의 처리로서 자신의 단일 필드를 비우는 것은 나중에 덮어쓰니까 상관없다.??
    - **`자신의 처리에서 list를 비우는 것`은, 자식들 돌고 비워야하므로, 끝처리에 배정한다.**

    ![4acfb861-07e1-4adc-99f7-605fbc02d3fb](https://raw.githubusercontent.com/is3js/screenshots/main/4acfb861-07e1-4adc-99f7-605fbc02d3fb.gif)

    ![image-20220802185819258](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802185819258.png)

77. task를 다 비웠으니, **자신의 처리로서 setTitle, setDate -> 재귀로 자식들 load를 해야한다.**

    - 그전에 먼저 **json을 depth별로 파싱해야한다.**

    - **찾는 과정을 다음과 같이 한다.**

      1. 원하는 데이터의 prefix(`"title : "`)와 cursor를 도입해 **시작점 cursor를 찾는다.**

         - string의 indexOf(타겟, 시작index)를 통해 찾고, 커서를 이동시킬 수 있다.

         ![image-20220802191456214](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802191456214.png)

      2. **data시작점 cursor + substring  + [ cursor+1부터 데이터끝따옴표의 index찾아 substring마지막index(exclusive)), 데이터만 골라낸다.**

         ![image-20220802192323997](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802192323997.png)

78. **이제, 각 행위를 메서드로 추출하여, title외에 다른 것들도 데이터 추출할 수 있게 한다.**

    - 가변변수 업데이트는 return을 통해 가변변수에 들어가게 한다.

    

    ![image-20220802192753878](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802192753878.png)

    - **cursor는 가변변수라서, inline으로 만들경우, 다음타겟에 적용이 안될 것이다.**
      ![image-20220802193042105](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802193042105.png)

    - **이 때는 inline으로 만들 되, `인자에서 가변변수 재할당업데이트`를 해주면 된다.**

      - inlien하면 가변변수의 밑줄이 사라짐. 근데 업데이트되서 생겨야함

      ![38702fea-5ce1-4950-9dde-afac93d71202](https://raw.githubusercontent.com/is3js/screenshots/main/38702fea-5ce1-4950-9dde-afac93d71202.gif)

      ![image-20220802193243774](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802193243774.png)

79. **이제 root task에 setter로 자신의 title을 넣어준다.**

    ![0e71b638-b9e9-4df3-acb8-7dee9ae2a23e](https://raw.githubusercontent.com/is3js/screenshots/main/0e71b638-b9e9-4df3-acb8-7dee9ae2a23e.gif)

    ![image-20220802193505704](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802193505704.png)



80. **타겟도 inline으로 만들어주고, 타겟을 바꿔서 date도 만들어주자.**

    - date는 string으로 가져와도, `LocalDateTime.parse()`를 거쳐서 set해줘야한다.

    ![e8350c9f-f190-4c08-b89b-01f9ee124a2c](https://raw.githubusercontent.com/is3js/screenshots/main/e8350c9f-f190-4c08-b89b-01f9ee124a2c.gif)

    ![image-20220802193850189](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802193850189.png)



81. **이제 자신의 `생성`처리가 끝났으니 `자식들을 반복문 + 재귀호출해서 생성`해줘야한다.**

    - **여기서는 `for 소유한 자식돌기 + 자식객체.재귀호출()`이 불가능하다.**

    - **composite를 만들어나가는 과정은 다음과 같다.**

      - **자신을 만들고 -> `add메서드로 정보가 있는 만큼 생성`하여 자식 add하기 때문이다.**

      ![image-20220802194748680](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802194748680.png)

      ![image-20220802194829939](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802194829939.png)

      - **자식의 자식을 add하는 것은 add한 자식list에서 getList+indexing해서 add해줘야했다.**

      ![image-20220802194917562](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802194917562.png)

    - **즉 `json정보를 통해 나오는 만큼 반복문을 돌면서 자식 만들어add` + `json에서 자식의 자식정보가 확인되면, 재귀호출`**

      - **결국 `자식의 자식add를 위한 add재귀호출을 위해 add과정을 메서드추출해서 재귀함수로 만들어야한다`**

82. **자식직전까지 처리해야하므로 `sub: [`를 target으로 해서, cursor를 `[다음에 자식데이터가 시작하는 곳으로 커서를 이동`시켜줘야한다.**

    ![image-20220802223750847](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802223750847.png)

83. **일단, `컴포짓 객체의 생성은, 자식의 정보가 있는 만큼만 while로 반복문`을 돌아서, 자식을 생성후 add해줘야한다.**

    - **일단, json의 총길이만큼 cursor가 움직일 수 있으니 반복문을 cursor < json.length()로 준다.**

    ![image-20220802223807754](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802223807754.png)

    - **반복문조건으로 들어간 변수를 이용해서 업데이트해줘서 끝나게 해야한다.**

    ![image-20220802225417337](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802225417337.png)

    - **현재 커서위치의 문자열을 뽑아 판단한다.**
      - `{`: **title, date를 뽑아내서 이동했는데도, `{`가 열린다는 것은 `새로운 자식의 시작`이다.**
        - title과 date를 뽑아 add한다. cursor는 그 뒤로 업데이트 될 것이다.
      - `[` && **`다음depth부모가 될 child!=null로 존재`**:  대괄호가 발견되면, **`자식의 [다음자식]이 시작되는 부분으로 재귀호출되어야한다.`이다. `load(String key) 중 [자식처리 시작부분부터]를 -> 재귀함수를 만들어 호출해야한다`** 
        - **`[를 만났더라도, 현재자식이 부모역할로서 재귀호출인자로 들어갈 수 있게 존재해야`만 다음 depth로 넘어가는 `재귀호출 시점`이다.**
          - **모든 자식들은 `{`를 가지고 있으므로 `depth별 [지역변수를 도입하여, depth별 null초기화 되는, add된 현재자식child]를 기억놔야한다.`을 해야한다.**
        - **`바깥 포장 객체내부필드로 있는 Composite객체의 생성`은 `root로서 자신은 setter로 완성`하고, `자식들부터 root(parent)에 add하며 재귀로 생성`한다**
        - **`그전까지 composite객체의 생성은 Main에서  root만들고, add add만 때려서 제어문 없이 생성했었다.`**
        - **`composite객체의 사용`은 모두 `재귀에서 자신처리 + 반복문 자식처리`로 편하게 했었다.**
      - `}`: depth레벨선상 마지막이다. **`cursor를 return`해서 현재depth의 stack을 제거한다.**
        - 재귀의 종착역...
      - `{`, `[`, `}`가 안나왔다면, cursor++로 다음 위치로 이동한다.
        - `]`는 자식의 끝인데, 나와도 무시하고 커서++해서, `{`로 넘어간다. `{`로 새로운게 뒤에 없다면 `}`로 끝나서 종료다.

84. 정보가 있는만큼 돌면서, `sub:[` 이후로 `{`가 나왔다면, 자식정보 시작이다. 현재 자식의`}`가 나올때까지 정보에서 title, date를 뽑아, root에 add해줘야한다.

    ![image-20220802230323188](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802230323188.png)

    ![image-20220802230358198](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802230358198.png)

    

85. **`[`는 다음 자식정보의 시작인데, `[지역변수에 child가 존재 == 부모로서 자격을갖춘놈]이라면... 다음depth의 부모`로 넘어가도록 재귀를 호출해야한다.**

    - **`첫자식임을 확인하는 방법은 [반복문 위 지역변수(depth마다 초기화되는)flag]`를 둘 수도 있으나 `[`로만 판단한다.**
    - **`자식을 add를 통해 생성하고, add는 카운터용으로서 내부생성되어 add되는 객체를 반환`하므로 `자식 add마다 뱉어내는 빈 task객체를 지역변수로 선언`해놓고, `자식발견후 add로 생성되어 반환되는 자식객체를 재할당업데이트`해서, `그 변수가 null이면, 첫자식`으로 판단한다.**
    - **또한, `자식 -> 다음자식으로 가는 getter는 없다` -> 다음depth로 가는 방법은 `add시 반환되는 자식객체를 잡을 수 있게, 반복문위에 지역변수로 반드시 빼놔야`한다. `그래야 다음depth의 자식으로 갈 때, 재귀메서드에 넣어줄 수 있다`**

    1. **`첫자식이 발견되는 순간`부터, `composite는 자식처리 로직부터 재귀함수`를 만든다.**

       ![187b4cab-7a6c-40ed-969c-071cf9a6347c](https://raw.githubusercontent.com/is3js/screenshots/main/187b4cab-7a6c-40ed-969c-071cf9a6347c.gif)

       ![image-20220803000440563](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220803000440563.png)

86. **`composite객체의 생성에 대한 동적트리순회`는 `자식처리 시작 로직부터`를 재귀함수로 추출한다.**

    - **재귀함수로 메서드 추출해야한다.**
      - **`메서드 추출`의 첫단계는 `내부context(보라색)를 지역변수로 위쪽에 따로 빼놓고 추출하여 -> 파라미터에 잡히게 한 뒤-> 인자에 inline`이다.**
        - 누적 업데이트 가변변수는 예외로서 파라미터에 없애는게 좋다?!
        - 누적되는 값만 추출한 메서드가 반환하게 수정할 수 있음 한다.
        - **재귀함수에서는 예외다. 가변변수는 변하기 때문에 `재귀메서드추출시 가변변수도 파라미터로 뽑혀야 업데이트된다`**
      - **`재귀메서드 추출`의 첫단계는 `depth마다 변할 것들은 모두 파라미터로`잡혀야한다.**
        - **`재귀호출에서 가변변수는 파라미터로 반드시 잡혀야한다. depth마다 달라질 것이다`**
      - **`composite의 동적트리순회용 재귀메서드`는 `외부에서 돌아간다면, 반드시 [composite객체]를 재귀메서드의 인자 -> 파라미터로 잡혀야한다`**
        - **외부 내부필드로 있으면 내부context로서 메서드추출시 안걸릴 수 있으니 반드시 확인해야한다.**
        - **`즉, 내부context(필드)를 쓴다면, 메서드 파라미터로 잡혀야하는지 확인해야한다. 특히, 외부에서 돌리는 재귀메서드는 재귀대상(composite객체)가 파라미터로 잡혀야한다`**

    - **일단 추출해놓고, `합성된 내부필드의 보라색인 composite객체를 파라미터 추출`하면 된다.**

      - **`composite생성에서는 자식처리부터 시작하는 재귀`를 돌고 있으므로, `재귀메서드 추출시 파라미터의 composite객체의 파라미터명`을 `parent`로 주자.**

      ![a48c9ea7-60d6-4a98-b8cd-2a4db3b70925](https://raw.githubusercontent.com/is3js/screenshots/main/a48c9ea7-60d6-4a98-b8cd-2a4db3b70925.gif)

      ![image-20220803001217236](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220803001217236.png)

87. **다음 depth로 가서 정보를 빼내도록 `재귀로 [다음 depth상 첫번째자식]`을 넣어준다**

    - **이 때, `재귀를 빠져나온 뒤, 업데이트된 cursor값은 반환`해줘야한다**
      - **depth로 들어가 자식들을 다 처리해준 뒤, `자식들level에서 앞으로나간 업데이트 cursor`가 있어야, 돌아와서 부모것의 데이터를 뽑을 수 있다.**
    - **`다음depth로 갈땐, [composite객체의 부모를 parent -> child로 인자 업데이트로 변경]해줘야한다`**
    - **`cursor를 이용한 동적트리순회`는 `재귀의 반환이 cursor인 것을 기억`하자**

    ![image-20220803002332279](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220803002332279.png)

88. **이제 `재귀에 대한 종착역`으로서 `}`(`]`같은데..)를 만나면 cursor를 return해준다.**

    ![image-20220803003649930](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220803003649930.png)

89. **이제load가 완성되었으니, `Main`에서 save / load해서 출력해본다.**

    - **save는 커맨드홀더 속 `내부필드로 있는 root Composite객체에 저장/로드`된다.**

    - 출력하려면, `root -> report -> visitor -> renderer에 꽂기 -> render로 돌리기 -> visitor객체로 getJson`으로 출력해야한다.

    ![image-20220803124731998](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220803124731998.png)



90. **오타들 수정하기**

    - jsonString에 필드는 `"`가 없다
      ![image-20220803124811550](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220803124811550.png)

      ![image-20220803124911203](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220803124911203.png)

    - **레벨선상의 마지막은 `}`가 아니라 `]`다.**

      ![image-20220803124934009](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220803124934009.png)
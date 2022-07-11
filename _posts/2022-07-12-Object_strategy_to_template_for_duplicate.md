---
toc: true
layout: post
title: Object) 중복제거를 위한 Strategy to Templatemethod (Plan3)
description: next필드 + setter + 다음타자 재귀호출 중복로직을 추상클래스로 제거

categories: [java, 우테코, oop, object, strategy, templatemethod, plan, connectobject ]
image: "images/posts/java.png"
---

#### 전략패턴 to 추상 템플릿메소드패턴 for 전략객체 중복코드(실습)

![image-20220712003834206](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220712003834206.png)

- [해당 커밋](https://github.com/is2js/object2/tree/d1c394694f3495aa78a485546756e06c7ef19f4c/src/main/java/goodComposition)
- 전략 to 추상템플릿패턴 방법 정리
  1. 전략인터페이스를 추상클래스로 변경하고, 전략메서드를 삭제한다.
  2. 구상체(전략객체) 1개를 택하여, 추상클래스를 상속후, 중복되는 필드(next)를 생성자 삭제후 setter 먼저 만들어 같이 올린다. 올라온 필드는 privte을, 템플릿메소드도 final을 달아준다.
  3. 다른 전략객체들도, 추상클래스를 상속하고, 삭제된 중복필드 및 생성자주입부를 삭제한다.
  4. 구상체들을 compare files이후, 인자로 넘어온 객체context를 많이 사용하는 구상체를 택하여, 예비훅메서드를 private내수용 메서드로 추출한 뒤, 공통로직을 가진 전략메서드를 템플릿메서드로 올린다. 
  5. 다른 구상체들은 올라간 템플릿메서드와 훅메서드에 대해, 훅메서드를 구현하여, 전략메서드 중 개별로직을 붙여넣고, 전략메서드를 삭제한다.
  6. 추상클래스가 좋은 부모인지 확인한다. (private필드, final메서드 or protected abstract 메서드)
  7. 연결되는 합성객체인 경우, setter에 return this + 추상체 응답이후, client에서는 생성자체이닝처럼 setter를 체이닝한다. 

1. **전략적용객체(Plan)에 주입되는 추상체**를 전략인터페이스 -> **추상클래스로 변경**하고,  구상체에서 템플릿메소드로 올릴 예정이므로 **전략메서드 오퍼레이터는 삭제**한다.

   ![image-20220711113646965](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711113646965.png)

   ![7959c650-1b4d-4bf4-9450-54c73db63b06](https://raw.githubusercontent.com/is3js/screenshots/main/7959c650-1b4d-4bf4-9450-54c73db63b06.gif)



2. **`구상체(기존 전략객체) 중 1개를 선택`하여, `추상클래스를 상속 후 -> 중복되는 필드 + setter 메서드를 올린`다. **

   - **`이 때, 생성자 주입 상태라면 setter를 먼저 만들고 setter 주입으로 변경(생성자 주입 삭제)해서, setter메서드를 올리면서 필드를 같이 올린다.`**
   - **`올라간 필드는 private을 통해 자식이 변경못하도록 막는다.`**

   ![8d2c07a1-9137-4656-8d36-b83db2396907](https://raw.githubusercontent.com/is3js/screenshots/main/8d2c07a1-9137-4656-8d36-b83db2396907.gif)

   ![image-20220711120949357](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711120949357.png)

   

3. **다른 구상체들도** 모두 추상클래스를 상속하고, **생성자 주입부분 + 공통필드를 삭제한다.**

4. **각 구상체들의 `개별로직을 compare files로 확인`이후 `전략메서드로 넘어온 인자들을 내부context라 생각하고, 가장 많은 인자를 사용하는 구상체를 택`하여 `훅메서드생성을 위한 개별로직을 private내수용 메서드 추출`을 해놓고 `템플릿메소드`로 올린다.**

   - 이 때, 내수용 private메서드 추출시, **`템플릿메서드 파라미터를 기준`으로 하여 `필요한 인자를 가장 많이 사용한 구상체를 기준으로 private 예비 훅메서드를 만들`어야, 나중에 인자가 모자라지 않는다.**
     - 템플릿메소드가 부모context이며, **그 파라미터들이 훅메서드가 인자로 가질 수 있는 객체context**임을 생각해야한다.
     - **그외 보라색 내부context로직들은  내수용 private내부 훅메서드에 추출되어도, `추상템플릿으로 올린 뒤, @Override protected 개별구현 훅메서드`로서, 내부context로 개별구현하게 된다.**

   ![5e1f28e7-1ca2-4624-ad9a-4fc952c1dfc3](https://raw.githubusercontent.com/is3js/screenshots/main/5e1f28e7-1ca2-4624-ad9a-4fc952c1dfc3.gif)

   ![image-20220711122800626](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711122800626.png)

   ![image-20220711122906210](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711122906210.png)

   

5. 나머지 구현체들은 **올라간 훅메서드들을 구현**하면서, **기존 전략메서드 속의 공통로직을 제외한 개별구현 로직만, 훅메서드로 옮긴후, 전략메서드를 삭제**한다.

   ![021b48cf-ec69-419a-984d-8129ab503621](https://raw.githubusercontent.com/is3js/screenshots/main/021b48cf-ec69-419a-984d-8129ab503621.gif)


   ![image-20220711123556671](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711123556671.png)

   ![image-20220711123611514](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711123611514.png)

   ![image-20220711123620376](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711123620376.png)

6. **생성된 추상클래스는 좋은 상속의 부모로서 `변수는 private, 메서드는 final or protected abstract, 생성자없이 setter로`**확인하여 달아준다.

   ![68a3a521-5cbc-40a0-a186-99b07cd7f2ec](https://raw.githubusercontent.com/is3js/screenshots/main/68a3a521-5cbc-40a0-a186-99b07cd7f2ec.gif)

   ![image-20220711124830443](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711124830443.png)

7. **부모클래스로 올릴 때 `삭제한 생성자 주입`으로 인해, `next필드에 다음 전략객체 주입시, 변수로 먼저 선언후 setter메서드 호출()`까지 해야하는 번거로움이 생긴다.**

   - **생성자 주입은 setter메서드호출을 안해도 되니, 따로 변수로 선언후 호출하는 번거로움은 없었다.**

   ![628fdeb3-b7b5-4faf-97f1-6f5437e3098c](https://raw.githubusercontent.com/is3js/screenshots/main/628fdeb3-b7b5-4faf-97f1-6f5437e3098c.gif)

   ![image-20220711152318617](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711152318617.png)



#### next필드로 연결되는 합성객체용 생성자 주입 대신, void setter를 가진 추상클래스에 [구상층 객체별 체이닝 받기기능]으로 대체하기

- **생성자 단계별 주입 방식이 아닌 형태로 `객체별 체이닝 받기`를 시행하려면**
  1. (호출주체객체 ==  응답형자리에 자신class응답 )`1개 class 모아서 받는다`면 ->  매번 변한 상태의 객체를 `return this`한다
  2. **(구상층들은 같은 class가 아니더라도 1type 응답가능하므로)`각 class의 필드에 다음 객체 받는다`면 -> `응답형은 추상체 + return this(구상체)`한다**
     - **여기서는 각 전략객체별로 다음 타자를 받도록 한다.** 



1. 현재는 각 전략객체별로 `void setter받기기능`을 이용해야하므로 **변수로 빼서 받기기능()을 호출해야한다.**(문제점)

   ![image-20220711153022536](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711153022536.png)

2. **원하는 방향은, `생성된 전략객체를 지역변수 추출없이 [객체별 체이닝 받기기능]을 구현`하고 싶다.**

   ![image-20220711153222306](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711153222306.png)

   ![image-20220711153245677](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711153245677.png)



3. 대신 이렇게 구현할 경우, **변수가 없으니 외부에서 받아놓은 객체들을 직접적으로 접근하는 것이 힘들어진다.**



##### next필드용 생성자 체이닝을 대신하는 void setter를 [구상층 객체별 체이닝 받기기능]으로 만들기 (실습)

1. **구상체 1개만 들어가는 자리에 .setNext()를 호출하여 빨간줄**을 만들고 **void setter -> `추상체 setter`로 시그니쳐를 변경**한다. **자동완성시, `return this`가 되는지 확인하고 변경해준다.**

   - **변수 추출후 setNext()는 빨간줄이 안만들어진다. void라서, 만들려면 좌측에 추상체 변수에 할당해서 만들어야 `체이닝 받기의 형이 틀림`의 에러가 뜬다.**
     - 에러의 확인은 `Ctrl + F1`의 Error Description을 활용한다.

   ![3cdc6d84-036f-4f20-8b38-55df43fabb0b](https://raw.githubusercontent.com/is3js/screenshots/main/3cdc6d84-036f-4f20-8b38-55df43fabb0b.gif)

   ![image-20220711171341015](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711171341015.png)





2. **체이닝 받기시, `1개의 객체가 변화하면서 다 받는 구조가 아니`므로 `메서드 체이닝을 하면 안된다`**

   - **조심) `1개의 class내부에서 1개의 객체가 연속해서 받는 것이 아니`므로 `진정한 메서드 체이닝`이 아니라  `new 1번재객체().setNext( new 2번째객체().setNext() )`로서 `생성자 체이닝 모양`이다.**
     - **`1개 객체가 연속해서 체이닝하여 받는 경우` ->  `변화된 객체를 반환`하므로 `메서드호출 끝나고 체이닝`하지만, **
     - **여기서는 `new 객체 생성() 하자마자 .setter로 다음타자 넣어준 객체를 생성자에 넣어준다`는 느낌이므로 `마치 생성자 체이닝과 같은 형태`가 유지되어야한다.**
   - 만약, 메서드체이닝을 해버리면, **return this되는 첫번째 객체의 next에 들어가는 객체만 돌아가면서 바뀐다.**

   ![40fcf84d-44a7-469a-ae35-4c4d83052413](https://raw.githubusercontent.com/is3js/screenshots/main/40fcf84d-44a7-469a-ae35-4c4d83052413.gif)


   ![image-20220711172830756](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711172830756.png)





#### 연결되는 합성객체를 가지는 추상템플릿메서드패턴 -> 여러개의 전략객체를 가지고 누적용시키는 전략주입 구상클래스를 가지는 전략패턴으로 변경(실습)

![image-20220712003807839](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220712003807839.png)

- [시작커밋](https://github.com/is2js/object2/tree/637a62919ac7e16e0993bcfa48de506fe116f80c/src/main/java/goodComposition)

- 추상클래스는 객체화 될 수 없어서, 공통필드next를 한 곳에 모아서 사용할 수 없다.
  - **개별 구상체들이 next를 소유하는 방법을 버리고 -> 1개의 구상클래스가 컬렉션필드를 가지고 모으는 구조를 구상클래스를 가지는 전략패턴을 통해 구현 가능하다.**
- **next필드를 각 구상체마다 가지려면, 데코레이터 패턴의 생성자 체이닝이나 setter체이닝을 해야만한다.**
  - **템플릿메소드 -> 전략패턴으로 변경하면서 생기는 `전략주입 구상클래스`는 `적용할 전략객체들을 모아서 누적 적용`시킨다. **



- 템플릿메소드 패턴 -> 전략패턴 변경 방법 정리

  - 전략적용대상의 class가 추상클래스로 개별로직을 가지는 상태 

  1. 추상class를 일반클래스로 변경 및 훅메서드 정의부 삭제
     -> 훅메서드를 alt+del로 삭제하면, 구상체들의 개별로직이 재활용안되므로 그냥 삭제한다.
  2. 내수용 private훅메서드 호출부를, 주입된 전략객체.전략메서드()호출로 변경
     -> 전략객체명은, 전략메서드(구 훅메서드)를 바탕으로 or로 만드는게 일반적이다.
  3. 전략 인터페이스 만들고, 전략메서드를 올린다.
  4. 전략객체를 생성자 주입받는다 (생성자 or 확장(좋은 상속 가능성)있으면 setter로 받기)
     -> 일단은 1개만 필수로 받도록 생성자 주입 받는다.
  5. 훅메서드구현 개별자식들을 [구상내용+전략인페명]으로 변경하고, 전략인터페이스 구현시킨다.
  6. @Override 개별구현 proected훅메서드 ->  public전략메서드로 시그니쳐 변경

  7. 전략주입 구상클래스가 된 부모클래스의 템플릿메소드 공통로직 with 내부 context변수들 -> 전체를 전략메서드에 인자로 context위임
     - 내부context변수들(보라색)을 지역변수로 추출 이후 -> 제외하고 메서드 추출 -> 인라인으로 메서드인자로 context 넘겨주기
  8. delegate로 추출한 전체로직을 전략메서드에 default메서드로 위임 
  9. 위임된 default메서드 로직에서, default를 지우고 로직을 개별 전략객체들에게 구현->전달후, 기존 전략메서드(context인자X)는 삭제

  10. 전략주입 구상클래스가 여러개의 전략을 받는다면, **Set컬렉션 전략객체 필드**를 만들고
      1. **1번째 전략객체는 필수로서 생성자 주입 받되, add해준다.**
      2. **2번째 전략객체부터는 setter로 add하여 받는다.(setNext())**
         - **만약, 메서드 체이닝으로 받고 싶다면, add 후 return this로 컬렉션필드를 가진 구상클래스를 반환해서, 체이닝하여 계속 받도록 하면 된다.**
  11. 전략객체를 사용하는 기존 템플릿메서드에서, for문으로 컬렉션필드를 돌면서 누적적용해준다.
  12. client에서 전략주입 구상클래스에 1번째 전략객체는 생성자 주입 -> 2번재부터는 setNext의 메서드체이닝으로 전략객체들을 받아준다.	

     - 전략적용객체에는 전략주입 구상클래스를 set해줘야한다.



1. 추상클래스를 일반클래스로 내리고, 훅메서드 정의부를 단순삭제한다.

   ![cca63d74-f36a-45db-a60e-ffa961b116ea](https://raw.githubusercontent.com/is3js/screenshots/main/cca63d74-f36a-45db-a60e-ffa961b116ea.gif)

2. 내수용 훅메서드 호출부를 `생성자 or setter 주입`받을 전략객체 `(소문자)필드`로 채운 뒤, 전략인터페이스를 생성한다.

   ![6d72324f-267e-463b-a47c-dc2a7ffeabf5](https://raw.githubusercontent.com/is3js/screenshots/main/6d72324f-267e-463b-a47c-dc2a7ffeabf5.gif)

3. 전략객체를 일단 1개는 필수로 생성자 주입 받는다.
   ![6305538a-7eb5-4640-be32-d68076f21c45](https://raw.githubusercontent.com/is3js/screenshots/main/6305538a-7eb5-4640-be32-d68076f21c45.gif)

4. 훅구상체들의 class명을 [구상내용 + 전략인페명]으로 변경하고, 전략인터페이스를 구현하도록 변경한다.

   ![604441d2-1b84-40eb-a8ed-6023702981a2](https://raw.githubusercontent.com/is3js/screenshots/main/604441d2-1b84-40eb-a8ed-6023702981a2.gif)

5. @Override + `proected`의 훅메서드를 -> `public` 전략메서드로서 개별구현하도록 접근제한자를 바꿔준다.
   ![6329ea32-9bc2-483a-91bb-eb65478bd084](https://raw.githubusercontent.com/is3js/screenshots/main/6329ea32-9bc2-483a-91bb-eb65478bd084.gif)

6. 전략주입 구상클래스가 된 부모클래스의, 템플릿메소드 로직 중, 전략메서드로 넘겨줘야할 부분이 남아있으면 SRP원칙에 맡게 메서드추출하여 넘겨준다.

   - **여기선 없다. 여러 전략객체 적용부만 남은 상태**

   ![image-20220712000522775](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220712000522775.png)

7. 전략주입 구상클래스가 여러개의 전략을 받는다면, **Set컬렉션 전략객체 필드**를 만들고

   1. **1번째 전략객체는 필수로서 생성자 주입 받되, add해준다.**
   2. **2번째 전략객체부터는 setter로 add하여 받는다.**
      - **만약, 메서드 체이닝으로 받고 싶다면, add 후 return this로 컬렉션필드를 가진 구상클래스를 반환해서, 체이닝하여 계속 받도록 하면 된다.**

   ![68f79285-e6b1-4a73-a44d-d13dea9019fb](https://raw.githubusercontent.com/is3js/screenshots/main/68f79285-e6b1-4a73-a44d-d13dea9019fb.gif)



8. 모은 전략객체 컬렉션필드를 돌면서, 구 템플릿메소드 현 전략객체 호출 메서드를 누적적용해준다.

   ![image-20220712002553173](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220712002553173.png)

9. client에서 전략주입 구상클래스에 1번째 전략객체는 생성자 주입 -> 2번재부터는 setNext의 메서드체이닝으로 전략객체들을 받아준다.	

   - 전략적용객체에는 전략주입 구상클래스를 set해줘야한다.

   ![307c9079-7eb0-45f2-bc5d-0ce760cb131b](https://raw.githubusercontent.com/is3js/screenshots/main/307c9079-7eb0-45f2-bc5d-0ce760cb131b.gif)

   ![image-20220712002635893](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220712002635893.png)
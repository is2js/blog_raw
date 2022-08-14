---
toc: true
layout: post
title: menu를 만들며 익히는 composite, visitor패턴
description: menu를 composite객체로 만들고 동적트리순회, 출력은 visitor패턴

categories: [java, 우테코, oop, object, menu, composite, visitor, side ]
image: "images/posts/java.png"
---

# menu를 만들며 익히는 composite, visitor패턴

- 나중에 menu에 적용할 코드들
  - [깃헙](https://github.com/ye-geeee/headfirst-designpattern-practice/tree/master/iterator-pattern/src)
  - [블로그정리](https://thefif19wlsvy.tistory.com/45)



## Composite패턴을 적용한 메뉴+폴더 = CompositeMenu

### 01 단일 menu(파일)부터 만든다

#### 필드초기화 필드, 생성자초기화지만 상태가 변하는 not final + setter필드

- **초기값이 이미 주어지는 필드**는 생성자에서 받으면 안된다. -> 필드초기화
  - available = true;
  - 외부에서 받는 생성자초기와 구분된다. 생성자 파라미터에서 빠진다.
- **외부에서 받아 시작되지만, 바뀌는 필드 -> 생성자 + not final + setter**

![713ed4d4-7f8d-4301-80ac-b62700bdadce](https://raw.githubusercontent.com/is3js/screenshots/main/713ed4d4-7f8d-4301-80ac-b62700bdadce.gif)

![image-20220725005647139](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725005647139.png)



#### boolean필드는 setter가 아닌 toggle로 상태변화한다.

- 외부에서 값을 받아 상태를 변화하는 setter가 아니다.
  - this.flag = `!` this.flag형태로 외부값없이 t/f만 토글된다.
- true/false왔다갔다만 한다면, setter대신 toggle로 따로 정의해줘야한다.





![image-20220725005946664](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725005946664.png)



#### 생성자초기화 + not final/setter조합은 반드시 setter메서드를 통해 생성자 할당코드를 대체한다.

- 중복코드를 제거함과 동시에 **외부정보에 대한 특별한 처리를 setter에 정의하고, 생성자는 setter를 이용해 처리로직도 같이 적용되도록 한다.**
  - setter시 처리와, 생성자 초기화시 처리가 **다르면 문제가 생긴다.**



![26d84998-d6a9-46f8-a199-5380dcf82855](https://raw.githubusercontent.com/is3js/screenshots/main/26d84998-d6a9-46f8-a199-5380dcf82855.gif)

![image-20220725010442742](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725010442742.png)



- 필드 -> setter (-> getter)가 정의되면  **객체 구분을 위한 eq/hC + toString도 정의해주자.**

  ![image-20220725010829507](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725010829507.png)

### 02 Menu를 담을 Menus(폴더)를 만든다.

#### 폴더menus는 Set으로 파일들을 관리하는일급컬렉션인데, 이름 필드를 가진다

![image-20220725011001479](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725011001479.png)



#### 폴더객체는 기본적으로 getChild+ add + remove 기능을 가져야한다.

- add/remove/getChild3개는 폴더가
- draw(operation)은 파일의 기능으로 정의되어있다.



#### 컬렉션필드의 setter(add)/remove는 CRUD에 포함되므로 boolean으로 반환해야한다.

- 컬렉션필드는 void setter개념으로 void add를 만들어도 되지만
- **컬렉션의 상태변화는 실패가능성을 가진 CRUD이므로, 사용하지 않더라도, boolean으로 결과를 알려줘야한다.**

![2e577924-d4b2-4668-aff4-bcbceeeac5f9](https://raw.githubusercontent.com/is3js/screenshots/main/2e577924-d4b2-4668-aff4-bcbceeeac5f9.gif)

![image-20220725012253450](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725012253450.png)



#### 일급컬렉션(폴더) add를 통해 관리되는 컬렉션 관리 단일객체 추가시  외부public생성한 것 받아add vs 외부정보 받아 일급컬렉션(폴더)내부 생성해서add 할지 결정해야한다. [컬렉션관리 단일객체의 public외부생성은 당연한 것이 아니다. 외부new때리는 것은 최소화]

- **`컬렉션으로 관리할 객체`를  `public메서드(add)의 파라미터`로 받는다는 말은**
  - **단일객체의 생성자가 public**이며 
  - **외부에서 객체가 생성**되고 들어와 add한다.
  - **만약, 단일객체의 생성자가 수정되면 -> `외부의 생성자 사용코드들도 모두 수정`해야한다.**
- **`컬렉션 관리객체`를 `public메서드(add)의 파라미터에서는 생성정보만 받고` + `add메서드 내부에서 내부발행`하게 한다는 말은**
  - **단일객체의 `생성자를 internal`로 수정한다**
  - **내부에서만 생성되므로, `생성자 사용코드들은 여기에만 존재한다`**
  - **`단일객체가 필요에 의해 외부공개(public 생성자)`되기 전까지, `일급컬렉션 내부발행으로 잠궈`놓아 `수정시 드는 비용을 줄인다`**

- **`[컬렉션으로 관리될] 단일객체가 public외부에서 객체 생성되는 것이  당연한 것이 아니다`라고 생각해야한다.**
  - **동시에 `바깥에서 new키워드 때리는 객체는 최소화`해야한다.**



1. **컬렉션으로 관리되는 단일객체를 내부생성**할 수 있게 `add메서드의 파라미터를 생성된 객체 -> 생성정보`로 바꾸고, **add메서드내부에서 new때린다고 생각한다.**
2. **객체의 생성자를 internal로 수정한다.**
3. **객체의 생성자 파라미터를 복사**해와서 add메서드의 인자로 바꿔준다.
4. **내부에서는 단일객체를 생성**후 add까지 해준다.

![468bbdf2-809c-42ce-980f-28adc8b9d2a3](https://raw.githubusercontent.com/is3js/screenshots/main/468bbdf2-809c-42ce-980f-28adc8b9d2a3.gif)

![image-20220725123307980](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725123307980.png)

![image-20220725123325197](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725123325197.png)



#### 일급컬렉션 remove는 add와 달리, 이미 생성된 객체를 인자로 받아야한다. [생성코드만 내부에 있지, 객체자체는 public메서드를 가진 public클래스라서 외부에 공개/조작가능한 객체이다.]

- **생성코드를 외부공개 안하는 것과 객체 자체를 외부공개 안하는 것은 다르다.**
  - **`생성 자체는 컬렉션관리객체가 내부`에서 하고, `외부에 반환 +  조작가능`은 거의 다 public메서드를 통해 가능하다.**
  - **내부생성 -> 컬렉션add -> 외부 컬렉션반환 -> 외부 단일객체 추출 -> 단일객체 조작가능 -> remove는 외부에 존재하는 단일객체를 식별해서 remove**

- **`이미 존재하고 있는 && public메서드를 통해 외부 공객객체`를 삭제하는 것이기 때문에, `remove의 인자는 객체를 받는다`**
  - add에서만 생성을 외부vs내부를 판단한다.

- **객체의 식별은 값이 아닌 객체 자체로 되기 때문에, `반드시 객체인자를 받아 지우자.` 대신 `생성만 인자를 선택해서 생성장소를 선택한다`**



#### 폴더객체는 add/remove외에 get컬렉션()을 통해 외부에 공개하는 getter를 가지며, Set컬렉션을 정렬가능한 List를 변환 공개해야한다. 주의) 컬렉션 자체는 무조건 사본으로 반환해야한다. [생성자를 통한 얕은복사(단일객체 조작가능)]는 사본생성 및 변환도 동시에 가능하다.

- **외부에 컬렉션을 반환할 때는, `객체반환후 조작과는 달리, 내 컬렉션필드가 영향을 받으므로 사본반환`한다.**
- **`생성자를 통한 얕은 복사`는**
  1. **Set -> List로 변환이 가능하다**
  2. **얕은 복사이므로 개별요소(단일객체)의 조작이 가능하다.**
     - **깊은 복사 vs 얕은 복사는 `개별요소의 메서드가 public(외부조작가능)인지 아닌지`를 보고 판단하면 된다.**
     - **`출력용이 아니라면 거의 다 얕은복사`**
     - **`출력용은 객체조작하면 안되니 깊은복사`**





![511749b4-8770-4f15-9f27-d72e2496595d](https://raw.githubusercontent.com/is3js/screenshots/main/511749b4-8770-4f15-9f27-d72e2496595d.gif)

![image-20220725125203316](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725125203316.png)





#### 제공기능에 제공방식(정렬방식 지정)를 원할 경우, 파라미터로 정의해서 외부에서 선택하게 한다.



- 외부에서는 `정렬된 컬렉션인 List의 반환을 원하`며, **`제공방식 또한 외부에서 원하는대로 받고 싶어한다`**
  - getter를 가지는 것은 명백한데, **외부가 따로 정렬을 원하는 경우, 외부가 원하는 것을 인자로 받아서 처리해 줘야한다.**

![185067ec-85d4-40d7-9a9f-cb20cc9c405b](https://raw.githubusercontent.com/is3js/screenshots/main/185067ec-85d4-40d7-9a9f-cb20cc9c405b.gif)

![image-20220725132127374](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725132127374.png)



#### Comparator같은 함수형인터페이스(3가지 패턴) 자체를 파라미터로 빼지 않는다. 파라미터는 메세지로서 객체만 허용하므로 1) new 인터페이스()를 통한 전략메서드 실시간 구현 익명클래스 인자 -> 2) 객체에 전략메서드 구현 후 가상인자와 람다식 인자로 -> 3) 추상체 정의후 구상체로 가상인자와 람다식으로 인자. 정렬방식 지정은 상수개념의 값객체가 필요하니 enum을 도입하고, enum을 추상체로 본다.

- 만약, 정렬방식을 **어떤 값**으로 준다면, switch분기가 필요하지만, **값객체 enum으로 주면, 값객체도 객체로서 분기없이 각자의 일을 정의해놓고 위임할 수 있게 된다.**
- 외부에서 정렬방식을 지정할 땐 **상수개념의 값객체가 필요하다**
  
- **java에서는`제한된 수의 값객체`에 ` enum을 활용`한다.**
  
- **함수형 인터페이스는 `instanceof를 대신하는 분기추출용 전략인터페이스`일 수도 있고, `로직을 추출한 함수형인터페이스`일 수 있다.**

  - **`함수형인터페이스 인자`는 `내부에서 [인터페이스 추상체변수].전략메서드()로 사용하여 정의`되어있다는 뜻이다. **

  - **`우리는 전략인터페이스를 구현한 전략객체`를 만들어 인자로 넣어주면, 전략메서드는 이미 구현된 상태이므로 추상체변수대신 구상체가 들어가 그대로 시작된다.**

  - **`전략객체의 구상체를 만들지 않고, new인터페이스(){익명클래스로 실시간 구현한 객체}`를 넣어줘도 된다.**

    - **`impl한 전략객체를 만들기 싫다면, new 인터페이스(){ 익명클래스로 전략메서드 실시간 구현} 인자`를 만들어서 넣어줄 수 있다.**

      - 인자 부분에 new 인터페이스() 를 하면 익명클래스가 만들어지는데, **여기서 `전략메서드 시그니쳐를 확인`할 수 있다.**

      - 또한, 각각의 익명클래스를 변수로 빼서 Naming하여 적용되게 할 수 있다.

        ![image-20220725140710451](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725140710451.png)

  - **중요한 것은 전략메서드내용을 객체로 구현만 하면 되므로, `가상인자의 람다식`을 통해 `전략메서드 시그니쳐 유지한 체, 내부에서 지연실행될 로직을 객체의.메서드() 등을 통해 람다식에서 호출`한 것을 대입해도 된다. `중요한 것은 지연 실행될 전략메서드 로직을 시그니쳐대로 구현한 로직`이 필요한 것이다.** 

    - 예를 들어, **객체 생성을 내부에서 하고싶은데, `여러종류의 객체 생성이라 추상체로 두고, 구상체 new때리는 로직은 외부`에서 전달하고 싶다면?**

      - **생성로직에 맞는 시그니쳐를 가진 함수형인터페이스를 사용하고, `지연실행 될 내부로직에서는 함형인페.전략메서드()`형태를 가진다.**

        ![image-20220725143503108](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725143503108.png)

      - **외부에서는 `가상인자의 람다식으로, 지연실행될  로직을 객체.메서드(여기선 생성자호출)로 작성`만해서 `인자`로 넣어둔다**

        - **내부에서 알아서, `건네 준 로직의 람다식을 -> 추상체.전략메서드()로 지연실행`한다**
          ![image-20220725143649882](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725143649882.png)

    - 들어가면 전략메서드_**실행메서드**() -> **구현내용을 가진 부분인, 전략객체.전략메서드()를 실행**

      - get(supplier), accept(predicate?), apply(functional) 등

##### 블로그1 함수형인터페이스 정리

##### 함수형 인터페이스 파라미터 -> 지연실행을 위해 전략객체가 아니더라도, 전략메서드 내용구현한 객체.메서드()로 대체할 수 있다.

1. 파라미터에 함수형인터페이스가 있다는 것은 `전략객체` or `익명클래스 전략메서드 실시간구현` or **`전략메서드 구현객체의 람다식 호출`등 복잡하므로 `객체`로만 메세지를 전달하게 한다.**

   - **대신 `전략메서드가 무엇인지 확인`하기 위해 인자 자리에`new 인터페이스(){}`익클을 한번 구현해서 `전략메서드 시그니쳐를 확인`한다.**

   ![80229311-d180-4a65-a66a-b157d4f5c1d4](https://raw.githubusercontent.com/is3js/screenshots/main/80229311-d180-4a65-a66a-b157d4f5c1d4.gif)

   ![image-20220725162809076](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725162809076.png)

2. **`인터페이스를 구현한 전략객체`로 `분기를 대체`할 것은 아니므로 `전략메서드 시그니쳐대로 로직을 구현한 객체.메서드()`를 만들 준비를 한고 `인자 자리`에는  `전략 시그니쳐에 맞는 가상인자 -> 람다식`를 이용해서, 객체가 구현할 준비를 한다.**

   - 인자 2개를 받고, int를 반환하는 compare이름의 메서드

   ![c488f4f1-96f3-4904-8c86-f039be679bbf](https://raw.githubusercontent.com/is3js/screenshots/main/c488f4f1-96f3-4904-8c86-f039be679bbf.gif)

   ![image-20220725163051683](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725163051683.png)

   

3. **sortType은 제한된 종류의 값`객체`이며, 종류를 가지므로 enum에 정의한다.**

   ![b3bb37b6-3d7c-4c4e-a09f-46fe966f7cb8](https://raw.githubusercontent.com/is3js/screenshots/main/b3bb37b6-3d7c-4c4e-a09f-46fe966f7cb8.gif)

   ![image-20220725163318826](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725163318826.png)





#### Enum은 enum필드를 확정된 인스턴스, 그  아래로는 추상클래스로 보고, 추상메서드로 정의하면, 인스턴스(enum필드)들은 추상메서드를 익명클래스로서 구현할 수 있다.

- enum는 static초기화보다 더 빨리 jvm이 먼저 확정짓는  인스턴스를 만든다.

  - os가 보장하는 thread안전 인스턴스다. 
  - 싱글톤의 syncrhronized안걸어도 안전해진다.

- **enum은 이미 만들어진 필드들의 추상클래스 역할도 하기 때문에 abstract를 정의할 수 있다.**

  - enum은 추상메서드를 정의해놓고 -> enum필드들이 익명클래스로 실시간 구현을 전개해놓을 수 있다.
    - enum필드들의 구상체를 만들진 않기 때문에, **익클을 통한 구현 밖에 안된다.**

- string이나 date는 내장 compareTo를 이용해서, 2개를 비교해서 int를 반환한다.

  - 이 값을 compare의 int반환에 return해주면 된다.

  

1. **`일반메서드로 정의된 compare`를 시그니쳐만 가진 `추상메서드`로 바꾸고, enum필드들을 선언 하여 `구상체 인스턴스`로 생각한다.**

   ![8d42427e-01f8-411f-aeef-4154827441fa](https://raw.githubusercontent.com/is3js/screenshots/main/8d42427e-01f8-411f-aeef-4154827441fa.gif)

   ![image-20220725164441274](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725164441274.png)



#### List의 정렬방식은 요소들의 필드들에 대해 X 2(오름차순ASC, 내림차순DESC) 로 enum필드을 만든다.

1. **enum필드는 `Menu들의 정렬방식`이 와야하는데  `Menu의 필드들`을 기준으로 오름차순/내림차순으로 만든다.**

   ![5d6e586a-8000-480e-9b0e-09bed74e884d](https://raw.githubusercontent.com/is3js/screenshots/main/5d6e586a-8000-480e-9b0e-09bed74e884d.gif)

   ![image-20220725164800743](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725164800743.png)





#### enum필드의 소괄호()는 값/메서드를 인스턴스내 필드로 매핑하여 생성자를 추가해야하지만, 맨끝의 {}를 달아주는 순간 익명클래스로 인식되어, enum내부 추상클래스를 구현하도록 유도된다.

![d8c49109-e4fa-49ad-82cc-f43789b73ce6](https://raw.githubusercontent.com/is3js/screenshots/main/d8c49109-e4fa-49ad-82cc-f43789b73ce6.gif)





#### enum필드(인스턴스)마다 자신의 필드getter로 꺼내서 compareTo시켜 정렬방식을 구현한다.

- 내림차순은 String, LocalDateTime의 compareTo() 인자의 순서를 바꿔주면 된다.

![2c9827a7-1df9-4983-93ce-0182e2b8faa7](https://raw.githubusercontent.com/is3js/screenshots/main/2c9827a7-1df9-4983-93ce-0182e2b8faa7.gif)

![image-20220725165623034](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725165623034.png)



##### row == 인스턴스 종류가 확정일 땐 enum으로 객체를 구현하자. n개이상 끝을 모르는 객체에 대해서만 class를 만들어야한다.

- **만약, enum으로 구현안하면, `확정적인 종류 이상의 객체를 누군가 만들어낼 것이며, 그것 자체가 오류`이기 때문이다.**
- enum을 쓰면, 객체 수량오류(상수) 및 객체종류 오류가 안생긴다. class를 만들기 전에, enum으로 만들어보는 것도 방법이다.



- 이로서 폴더가 구현해야하는 getList with 제공방식(정렬)까지 구현했다.

  ![image-20220725170020984](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725170020984.png)





### 03 컴포지트패턴은 파일+폴더를 합친, Set컬렉션 데코객체라고 요약할 수 있다.

#### 컴포지트 패턴의 4가지 구성요소 파일(operation) + 폴더(add/remove/getChild)를 합쳐서 CompositeMenu에 한번에 구현한다.



1. **Menu를 복사**해서 **CompositeMenu**를 만들고, 생성자 등을 수정한다.

   - **폴더<->파일 따로 있을 때, `파일인 Menu`를 폴더내에서만 생성(외부생성X)으로 수정비용 최소화 했던 것을, 이제는 `CompositeMenu라는 폴더겸 파일은 폴더로서 외부에서 root폴더 최초 생성`해야하기 때문에 `생성자를 public으로 복구`한다.**
   - **필드 수정외에도 변화가 일어나면, eq/hC + toString은 `다 옮겨진 때 재정의`한다.**

   ![52c54fd0-4dc8-44ed-b336-a3c4ea2ae830](https://raw.githubusercontent.com/is3js/screenshots/main/52c54fd0-4dc8-44ed-b336-a3c4ea2ae830.gif)

   ![image-20220725172817643](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725172817643.png)

2. **이제 폴더역할을 하던 Menus의 내용을 가져온다.**

   - **중복되는 필드 및 getter/setter는 제외하고 가져온다.**
   - 재정의할 것들을 재정의한다.

   ![6cd8132b-1974-49c2-a89a-edde41ea55e1](https://raw.githubusercontent.com/is3js/screenshots/main/6cd8132b-1974-49c2-a89a-edde41ea55e1.gif)





3. **Set컬렉션 데코객체로서, `자신형의 Set컬렉션을 관리하도록 변경`한다.**

   - 참고

     ![image-20220722175813148](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722175813148.png)

   - 현재는 폴더(Menus)가 파일(Menu)를 관리했지만
   - **현재는 폴더 겸 파일인 CompositeMenu가 CompositeMenu를 관리하도록 변경한다**
     
  - 수동변경해야한다. Menu형 -> Composite형

![2524e7e6-5bfe-4a32-9b14-54daef047003](https://raw.githubusercontent.com/is3js/screenshots/main/2524e7e6-5bfe-4a32-9b14-54daef047003.gif)

   



#### Menu정렬 enum과 새로운 CompositeMenu정렬 enum은 다른 것이다. compare(a,b)의 대상이 달라짐. 새로 구현해야한다. enum은 제네릭이 안되므로 형이 달라져도 새롭게 구현해야한다.



![image-20220725174005842](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725174005842.png)





#### 컴포지트 객체는 자신+자식들의 정보를 같이 반환해야하므로 출력사본 컴포지트객체(Report)형이 새롭게 필요하다.

- getMenus()에서 

  - 기존에는 자식들만 list에 넣어서 반환했지만,
  - **지금은 자신의 정보 + 자식들 정보 같이 return해야하는데, 여러정보를 1번에 return하려면, `출력사본 컴포지트 객체`가 필요하다.**

- 출력사본 컴포지트형이 만들어지면

  - 자신 이외에 자식들은 add로 넣어서 **Composite객체와 동일하게 자신 생성후  + add 자식들까지`차례대로 컴포지트 객체를 새롭게 만들어서 반환`해야한다.**

- **현재 CompositeMenu**

  - title
  - date
  - available
  - Set CompositeMenu

- **`출력사본 MenuReport`**

  - **CompositeMenu** : 파일1개를 묶은 것 (CompositeMenu를 묶은 것)
  - **List MenuReport** : **`출력사본` 파일들**을 묶은 것
    - ~~List CompositeMenu~~

- 참고

  ![image-20220722191622525](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220722191622525.png)



1. **일단 `출력사본 Report`는 `1개의 Composite객체로부터 최초 사본`이 만들어진다.**

   - 이후 **Composite객체처럼, `add를 통해 자신형(Report)을 모으는 컬렉션필드`를 가지고, `add를 통해 자식들을 추가`한다.**

     ![9b1bf636-a8b5-4d14-8061-47b5be7f818d](https://raw.githubusercontent.com/is3js/screenshots/main/9b1bf636-a8b5-4d14-8061-47b5be7f818d.gif)

   ![image-20220725214641931](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725214641931.png)



#### composite객체는 자식들을 add시 내부생성을 고민할 필요가 없다. 폴더역할을 하는 이상 외부에서 root폴더는 무조건 생성되므로 composite객체는 외부생성이 풀린 객체이므로 add시 정보가 아닌 객체를 받는다.

- 출력사본은 **원본의 getter내부에서 사용될 것들을 정의한다.**
  - 먼저 원본의 파일을 -> 출력사본 report를 만들고
  - 원본의 자식들을 순회하면서 -> report들의 자식으로 add한다.

2. compositeMenu는 변할일이 없고(final + setter  X), 이미 외부에서 생성된 것을 받아 생성(생성자에서 객체를 인자로 유지)되지만, **자식들은 add로 추가해야 composite패턴을 따르는 출력사본이 된다.**

   - 컬렉션 필드는 빈컬렉션 + add조합이 많다.
   - **report들을 자식으로 받아야한다. CRUD는 boolean으로 반환하도록 void가 아닌 boolean응답형으로 작성해주자.**
   - **add시 내부관리하는 객체를 `add내부에서 객체 생성`하도록 `외부에서는 생성정보`만 받는 경우도 있었지만, `report는 컴포지트 객체라서 폴더+파일의 역할을 동시에 수행하므로 외부에서 이미 생성하는 작업`이 이루어졌기 때문에 고민 안해도 된다. 객체를 받는다.**

   ![702b06d5-76c2-40d3-b6b2-c9dc7e7dccc4](https://raw.githubusercontent.com/is3js/screenshots/main/702b06d5-76c2-40d3-b6b2-c9dc7e7dccc4.gif)

   ![image-20220725215756099](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725215756099.png)





3. **출력사본 composite객체** report는 파일+폴더(자식들)의 **getter로제공하는 것이 주목적 -> 무조건 getter를 정의**해준다. 

![image-20220725220543267](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725220543267.png)



### 04 컴포지트 객체의 사용은 getter에 for+자식들도 getter재귀호출로 동적트리순회하는 것이다. (자식없으면 순회x -> 재귀호출x -> 종착역)



#### composite객체는 더이상 getChild(getList)가 아니라 getReport로 변경해야한다.(composite가 된 이상 자식들만 반환할 일은 없다)

- getList로 반환되면, 재귀호출시 문제가 생길 수 있다. 
- **출력사본 객체(파일+폴더 다 반환)를 만들어서 report객체 1개를 반환하도록 수정해야한다.**
  - **자식들을 Set -> List 사본 만들어, 정렬하는 로직은 그대로 둔다.**
    - 자식들을 이미 정렬해서, report자식들도 정렬된체 add되게 한다.

![c2d41bfc-ca34-4825-8016-ff0ebf63e87a](https://raw.githubusercontent.com/is3js/screenshots/main/c2d41bfc-ca34-4825-8016-ff0ebf63e87a.gif)

![image-20220725230419942](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725230419942.png)



#### 원본 컴포지트객체(CompoisiteMenu)의 operation()은 getter에서 (1)자신의 정보 이용 + (2) for문과 getter재귀호출로 동적트리순회하여 자식들 이용 것인데, 여기선 getter내에서 출력사본을 만들 때 작동된다.

- **자신 + 자신형의 컬렉션필드**를 가진 **컬렉션필드 데코객체**는 **자신필드orthis이용 + getter 속 for문과 재귀호출을 통한 자식사용이 `operation`이다.**
- 원본 -> this로 **출력사본 report**를 만든다.
- 원본의 자식들 (CompositeMenu) menus -> **List사본 + 정렬**된 상태 -> 내 자신의 **사본에 자식사본으로 add**해줘야한다.



##### 주의점) for내부에서 자식menu를  report로 변환 생성하여 addReport해줘야하는데,  composite객체라면 `자신이용(this)의 report생성`이 이미 `for내부 자식 재귀호출 전 로직`에 명시되어있을 것이며, `자식이 getter를 재귀호출하면, 자식 자신에서 재귀호출전 로직이 자신에게도 적용될 것`을 생각해서 `자식들도 재귀호출`해야한다.

- 자신 -> this를 이용해서 사본 report를 만든다.
- 자식들 -> for문에서 순회하여 하나의 composite지만, **this로 이용해서 report를 만들면 안된다. **
  - **자식들도 하나의 composite객체로서, 자신의 자식들을 순회하여 반영되도록 `동적트리순회를 위해, 자식들도 현재 정의 중인 getter를 재귀 호출`하여 변환되도록 한다.**
  - **`재귀호출전까지의 로직이 현재 자식에게도 이루어진다 -> this로 report만드는 로직이 그대로 적용된다.`**
  - **재귀의 종착역은, 자식들 자신의 컬렉션필드가 비어서 for문을 안타 -> 재귀호출이 없어지는 순간이다. 즉, 자식들이 없는 자식은, 재귀호출안하고 끝난다.**

![0ecf1239-1652-4ce3-a9fd-2be4273a9946](https://raw.githubusercontent.com/is3js/screenshots/main/0ecf1239-1652-4ce3-a9fd-2be4273a9946.gif)

![image-20220725231054493](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725231054493.png)





##### 역으로 for반복문 속에서 재귀호출되고 있다면? "아~ 동적트리순회해서 자식들을 돌면서 자식입장에서도 재귀호출전의 로직이 똑같이 적용 되고 있구나~! 자식없으면 순회x -> 재귀x -> 종착역이구나."

![image-20220725233612204](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725233612204.png)





##### my) 동적트리순회하는 for속 자식들을 변수1개로 생각할 때, (1) 자식들은 <파일이자 폴더이므로> 무조건 자식들 동적트리순회 로직을 가지는 operatin()을 재귀호출해야만하며, (2) 재귀호출전까지의 로직이 자식에게도 반영될 것 생각 (3) 재귀호출() 결과 자체를  ==  종착역(자식이 없어서 for걸리지 않고, 결과값이 반환)의 상황으로 가정하자.

![image-20220725234952271](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220725234952271.png)

- 이렇게 생각하면, for문 속에서 자식을 new MenuReport(자식)으로 report를 생성하지 않을 것이다.
  - **composite객체의 자식들(파일들)은 폴더로서 operaiton을 재귀호출 할 의무를 가진다.**





##### my) 컴포짓객체는 자식들 -> for재귀로 동적순회 -> 자신은 결과물 return -> 자식들 동적순회 재귀호출시 결과물 return하므로, 자식들의 재귀호출도 자식자신의 결과물이 있으며, 재귀호출전 자신로직이 반복된다.

![image-20220726002623357](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726002623357.png)





### 05 [Main] Composite객체는 root부터 생성하여 첫번째 자식들은 add(생성정보)로 넣어주지만, 자식의 자식은... getter(정렬문제가 아니라 꺼내려면, set->list사본 반환)로 자식들만 꺼낸 다음, 처리해야하는 불편함을 가진다.

- 자식의 자식부터는 add해주기가 너무 불편해진다.
  - 자식을 list로 받아와 -> 객체로 받아서 -> add

![image-20220726004609075](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726004609075.png)





#### composite객체의 add시 내부생성 -> 외부에서 생성후 조작된(자식add된) 객체를 받도록 메서드 오버로딩 메서드 추가후 중복코드 제거

![dd2291bd-d03e-40cd-8b43-f0e83928bbe9](https://raw.githubusercontent.com/is3js/screenshots/main/dd2291bd-d03e-40cd-8b43-f0e83928bbe9.gif)

![image-20220726004524544](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726004524544.png)



#### root의 자식들에 자식들이 추가될 것이라면, 조작가능한 객체로 생성하되 가장 아래층부터 만들어 자식들 add해서, 마지막에 root에 add하기

![image-20220726005053721](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726005053721.png)



### 06 Composite를 출력 하기 전, root만 출력사본 report를 만들어주면 된다. 동적트리순회로 자식들을 다 report로 변환해서 넣어준다.



#### createReport메서드는 자식들을 재귀로 알아서 변환하여 넣어주기 때문에, root만 호출하면 report가 완성된다.

![image-20220726005301237](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726005301237.png)







### 07 [MenuRenderer] Report를 출력할 방법이 없다.  출력 역시 report를 [자신출력 -> 자식들 for+재귀호출을 통한 출력] 의 메서드를 동적트리순회를 돌아야 (report하나하나) 출력이 가능하다.  



#### 출력을 담당하는 객체 MenuRender를 만들되, render(report) 호출시 Composite -> report 와 마찬가지로, report -> renderer로 동적트리순회를 돌아야한다. 

![1611480e-c19b-425a-b284-6f57a7e2acdd](https://raw.githubusercontent.com/is3js/screenshots/main/1611480e-c19b-425a-b284-6f57a7e2acdd.gif)

![image-20220726010022490](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726010022490.png)







#### composite객체(컬렉션필드 데코객체)의 동적트리순회는 재귀함수를 정의하고, 그 내부에서 (1) 자신이 할일 (2) Set이라면 정렬+사본(report는 생략) (3)  자신의 할일 끝난 상태에서 for 자식순회 -> 내부에서 재귀호출로 자식들이 자신의 일 (4) 종결처리(결과물을 반환한다면 return 자신이 할일 결과물)

![image-20220726012658546](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726012658546.png)



## Visitor패턴으로 구현하는 Renderer(메서드에 제어구조 소유)

![image-20220723230451093](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220723230451093.png)

### 08 [동적트리순회의 제어구조를 가진 메서드를 가진 class]는 라이프싸이클을 타는 visitor객체를 소유하여, 제어를 역전한다.

#### 기본적으로, visitable(renderer)은 visitor객체를 생성자 주입받아 필드로 가진 상태에서, 제어구조 속 라이프싸이클 메서드들을 배정받는다. Visitor는 생성자주입하되, interface로 정의한다.

![7da52948-d740-47fb-bbdc-71636804409a](https://raw.githubusercontent.com/is3js/screenshots/main/7da52948-d740-47fb-bbdc-71636804409a.gif)

![image-20220726122634562](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726122634562.png)



#### Visitor인터페이스가 제어역전Visitorable내에서 제어구조에 맞게 [컬렉션 데코객체, Composite객체]가 하는 일을 위임받는다. (1) 자신 할 일 (2) 자식 순회 (3) 종착역시 종결처리

![ba0b3034-bb42-4c61-b4c8-431aec765f67](https://raw.githubusercontent.com/is3js/screenshots/main/ba0b3034-bb42-4c61-b4c8-431aec765f67.gif)

![image-20220726123424718](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726123424718.png)



### 09 Composite객체는 타 클래스 메서드의 인자로 들어가서 사용될 때, 자식들 역시 인자로 재귀호출 된다.

#### Composite->Report의 [Composite객체 내부 자신메서드 동적트리순회 -> 자식도 자신 메서드에서 재귀호출]가 아니라, Report -> print [타 구상클래스(Renderer#render) 메서드의 인자로 들어가서 동적트리순회]를 돈다면, [자식들도 타 클래스 구상메서드의 인자로 들어가면서 재귀호출이다.

- CompositeMenu **내부에서 자신의 메서드**에서 **자식들도 호출 주체 객체가 되어** 동적트리 순회

  ![image-20220726123850886](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726123850886.png)

- 현재 **Renderer 메서드의 인자**로 들어가서 동적트리 순회된다면, **자식들 또한 메서드의 인자로 동적트리 순회**

  ![image-20220726123945809](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726123945809.png)





### 10 Composite객체의 동적트리 순회시, depth마다 누적/변하는 값이 필요하다면,  파라미터에 추가되어 사용되고, 최초호출(root)시 초기값을 인자로 넣어줘야한다. 이 때, 메서드 추출부터해서 오버로딩이 필요하다. 

- 자식으로 진입할때마다 `+1씩 증가하는 depth`의 값을 알아야하며, 그 depth로 `padding`을 만들어서 `출력에 반영`되어야한다.



#### 일반 재귀를 일만 하던 데코객체 동적트리순회(자기일 + 자식순회 자식일)에서 depth마다 변하는 값을 반영하고 싶다면 (1) 그 순간 누적결과값을 달고 다니는 꼬리재귀가 될 것이므로 재귀부분 전체를 메서드추출(오버로딩) 후 (2) 들고 다닐 값을 인자에 root 초기값 넣어주기  (3) 들고 다닐 값을 파라미터로 추가  (4) 트리 속 자식들 순회시 depth진입시 업데이트 될 인자로 업데이트

- **`Extract Method`로 파라미터 추가전에 먼저 추출한다면, `signature같아서 추출안될 수 도 있으니 일단 아무이름으로 추출해놓는다.`**

  - python은 오버로딩이 안되서 helper로 추출해서 쓴다.

  

- **외부에서 public재귀되는 메서드의 시작을 호출**하는 것 <-> **private꼬리재귀가 시작되는 것**은 서로 다르다.
  - **꼬리재귀는 `개발자가 넣어줘야하는 누적결과값의 초기값 입력`을 필요로하는데**
  - **이것을 외부에서 입력하게 둘 수 없으니 `오버로딩한 private메서드를 내부에서 호출`하는 방법을 선택한다.**

- 내부에 **depth라는 누적하여 변하는 인자가 필요할 땐, 꼬리재귀로서  전체 로직을 메서드 추출부터** 한다.





![40be10ea-40e1-4790-b02e-da74004546ba](https://raw.githubusercontent.com/is3js/screenshots/main/40be10ea-40e1-4790-b02e-da74004546ba.gif)



![image-20220726131054937](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726131054937.png)







### 11 [외부 생성자 주입되는 협력객체] Visitor의 사용 메서드에서  [사용 전 동시성 문제] 생각하기 -> 생성vs재활용context

- **라이프싸이클을 타면서** **`출력하는 일`을 위임**받는 **외부주입 협력객체** Visitor가
  - **`다른데도 쓸 수 있는 것을 배제`하기 위해, 매번 사용될때마다 초기화된 객체여야하냐** -> **`생성context`**
  - **`다른데서도 써야하며, 재활용`되기 위해, 외부에서 이미 생성된 것을 받아야하냐** -> **`재활용context`**



- **제일 안전한 것은 `언제나 협력객체는 생성context`로 보고 `생성자 파라미터를 [협력 객체형]가 아닌 [지연실행될 협력객체의 생성자 자체]`를 받는 것이다.**



#### my) 협력객체가 상태값을 가진 체 재활용 될 것이 아니라면, (전략객체라면) 생성context로 보고, 객체형이 아닌 [지연실행 생성자로직]을 [함수형 인터페이스 파라미터]로 받자.

- 라이프싸이클을 타고, 실행만 시켜주는 인터페이스 visitor는 **인터페이스로 정의될 것이며, 여러종류의 visitor전략객체가 주입되며, 전략객체는 상태값을 가져 재활용 될 확률이 거의 없다.**



- **함수형 인터페이스는, 1개의 메서드를 인터페이스로 정의해놓고**
  - **내부에서는 전략객체(추상체). 전략메서드()**형태로 정의해놓고
  - **외부에서는**
    - **전략메서드를 구현한 전략객체** 자체를 주입하거나
    - **new 전략인터페이스(){} 전략메서드의 실시간구현 익명클래스**를 주입하거나
    - **가상인자+람다식을 이용한 [전략메서드 구현 메서드를 호출]하는 방법이 있다.**
  - **전략객체를 안 만들것이라면, 사실상 `3번`이 제일 많이 쓰인다.**



##### 블로그2 생성자로직을 지연실행하기



#### 추상체(인터페이스)의 협력관계에서 [형 파라미터]로는 [지연 생성]은 전략패턴 포함 불가능하며, [의존성으로서 내부 생성]도 추상체라 안된다.

![image-20220726135458218](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726135458218.png)





#### 추상체 협력관계에서, [생성context == 객체의 지연 생성]의 유일한 방법은 객체 생성에 맞는 시그니쳐를 가진 [이미 짜여진 함수형인터페이스 + 자신만의 전략메서드()]를 이용해  지연실행을 정의해놓고, 외부에서 가상인자의 람다식으로 생성자호출 로직을 전략메서드내용으로 전달하는 것이다.

- 기존: **협력객체 visitor를 외부에서 생성된 것(재활용된 것일지도 모름) 그대로 받아 사용**

  ![image-20220726140414317](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726140414317.png)

  

1. **지연 생성으로 바뀔 협력객체(visitor)는 `보라색 내부context`으로 표기되어 사용되고 있는데, `내부context가 아닌 것으로 간주하고 메서드 내부라면 파라미터로 추출`한다.**

   ![fe8738db-0ac2-418c-8ec5-e2b93607159d](https://raw.githubusercontent.com/is3js/screenshots/main/fe8738db-0ac2-418c-8ec5-e2b93607159d.gif)

   ![image-20220726140214010](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726140214010.png)



2. **`협력객체의 생성자 시그니쳐를 확인`한 뒤, `외부 주입 생성자 파라미터`자리에 시그니쳐에 맞는 `함수형인터페이스<협력객체형>`으로 받는다.**

   - **받는 필드 또한 `함수형인터페이스<협력객체형>`이 되며, `생성자 지연로직 함형을factory`로 이름짓는다.**
   - **이제 `협력객체가 사용되는 메서드`에서 `사용 직전에 함형.자신전략메서드()`로 생성자를 지연실행시켜 변수로 받는다.**

   ![b2ba374b-acb5-47b1-9980-860d16fbce6d](https://raw.githubusercontent.com/is3js/screenshots/main/b2ba374b-acb5-47b1-9980-860d16fbce6d.gif)

   ![image-20220726141102554](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726141102554.png)

   ![image-20220726141112799](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726141112799.png)

   

   

3. **내부 생성된 협력객체는, 꼬리재귀에선 초기값으로 줘서, 재활용된다.**

   - 참고

     ![image-20220724122901932](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220724122901932.png)

4. **외부에서 협력객체를 주입할 땐, `가상인자 람다식을 통한 전략메서드 구현`부에 `해당 객체 생성자 호출로직`을 넣어주면, `생성자호출을 통한 객체 생성`이  class내에서 지연실행되어 매번 생성된다.**

   - **`구상체까지 구현하고 넣어줘야한다`**

     

   ![61fb1bf6-3edb-400c-96dd-efbe0ef8d7b2](https://raw.githubusercontent.com/is3js/screenshots/main/61fb1bf6-3edb-400c-96dd-efbe0ef8d7b2.gif)

   ![image-20220726141234985](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726141234985.png)

   ![image-20220726141313764](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726141313764.png)





### 12 composite사본객체 Report는 getter로 자신 + 자식들 꺼내서 써야한다.

#### Report는 전달객체일 뿐, 사용하는 곳에서는 자신 getter + 자식들 getter로 각각 꺼내서 써야한다.

![8416666d-5ae5-4677-aace-1742eca9197e](https://raw.githubusercontent.com/is3js/screenshots/main/8416666d-5ae5-4677-aace-1742eca9197e.gif)

![image-20220726160353916](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726160353916.png)



####  composite객체 출력 종결처리는, 재료는 필요없지만, depth는 필요하다. 자식들 순회에서는 재귀로 자식 자신들을 그리니 따로 처리할 일이 없다.

- **depth만큼 padding을 만든 뒤 -> `닫아주는 출력`을 해줘야한다.**

![image-20220726160629590](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726160629590.png)



### 13 visitor는 인터페이스로서, 라이프싸이클 함수들을 가진다.

![image-20220726160804304](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726160804304.png)



#### visitor구상체들을 라이프싸이클함수를 구현하여, 재료인 report -> menu를 depth만큼 padding 넣어서 그리면 된다.

1. 일단 구상체를 구현한다. **그리되 콘솔에 그리는 놈을 Console을 붙여서 구현한다.**

   ![image-20220726161017974](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726161017974.png)

2. **depth에 따라서 padding을 만들고, 그만큼 띄어서 출력해야한다.**

   - **0부터 시작하며, 그만큼 반복문 돌면서 padding을 `빈 문자열`에 누적으로 쌓아야한다**

   ![54e94842-03a7-4489-a182-97b23b201bcf](https://raw.githubusercontent.com/is3js/screenshots/main/54e94842-03a7-4489-a182-97b23b201bcf.gif)

   ![image-20220726161355364](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726161355364.png)

   

#### 자신들의 할일은 [자신 ~ 자식들의 자신 직전] 까지 [depth만큼 padding + 자신의 필드 정보를 get하여] 그리는 것이다. 불린flag가 있다면 삼항연산자를 통해 그려준다.

- **todo의 complete처럼, `menu도 toggle이 반영된 available`부터 `삼항연산자로 그려준다`**

![image-20220726161825555](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726161825555.png)



- 그외 일반 필드정보는 편하게 getter뽑아서 찍어준다. 
  - 괄호필요하면 따로 끊어주기



#### 종결처리는 [자식없어 순회없이 끝나는 마지막 node]를 생각해보면 빠르고, 실제 적용은 매 node마다 적용된다. [시작시 무엇을 열어줬는지에 대해, 닫아줘야할 것이 있는지] 등을 생각한다.

- console은 `자신~자식호출직전까지` **시작시 특별하게 열어주는 것이 없으므로 종결처리가 필요 없지만**
- **자신 ~ 자식들 다끝나고, 마지막 node의 끝에 `한줄씩 띄워주는 것`으로 넣어보자.**

![image-20220726162420491](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726162420491.png)



### 14 Main에서 라이프싸이클을 채운 구상Visitor를 지연생성용 factory에 넣어주고, 어떻게 출력되는지 확인해보자.

![fab526df-674e-4b1e-8e25-c28703da0ce1](https://raw.githubusercontent.com/is3js/screenshots/main/fab526df-674e-4b1e-8e25-c28703da0ce1.gif)

![image-20220726162602228](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726162602228.png)



#### Composite객체(자신 + 자식들)의 종결처리는 [바로 아래 자식들의 종결처리 이후 마지막 직후자식의 종결처리후 1번만 종결처리된다.]

![image-20220726163519249](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726163519249.png)



#### 줄바꿈으로 종결처리 하지말자. 자신의 시작시 [여는 처리]가 있을 때만 [닫는 처리]를 종결처리에 추가하자. (종결처리는 생략해도 된다)

- 줄바꿈 종결처리를 지운다.

- **따로 여는 처리가 없기 때문에 종결처리를 생략**하자

- 추가로,  available 출력은 지워도 될 것 같다.

- 추가로, **LocalDateTime중에 date만 표시해도 될 것** 같다.

  

![image-20220726163940602](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726163940602.png)

![image-20220726163952535](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726163952535.png)





### 15 Composite객체는 호출하는 메서드가 depth를 함께 가지므로 depth와 함께 자신+자식+종결처리를 할 수 있어서 json을 만들 수 있다.

- consoleMenuVisitor를 복사해서 만든다.
- **종결처리를 할 수 있다면, json의 시작괄호를 닫아줄 수 있어서, 생성 가능하다.**



#### json의 문자열을 모으지말고 일단 출력하는 형식을 만든다. padding은 -대신 공백으로 바뀐다.

1. ConsoleMenuVisitor를 복사해서 **JsonMenuVisitor**를 만든다.

   ![image-20220726171244593](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726171244593.png)



2. **패딩을 `-`에서 `공백`으로 바꾼다.**

   - **공백의 갯수는 상관없으니 `공백 2개`를 기본으로 바꿔보자.**

   ![image-20220726171745191](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726171745191.png)

#### json의 구성은 padding + 여는중괄호 / padding + 중괄호 대체공백 + 필드 + 큰따옴표 속 필드정보 + 콤마 / 자식들의 시작 직전인 sub :  [ 여는 대괄호까지를 자신처리를 한다. -> 매번 padding이 들어가니, 매 줄을 일단 sout으로 생성한다.

1. **json의 출력은 `필드마다 매번 공백 padding`을 사용하므로, `매 라인을 sout`으로 편하게 작성하자.**

   ![image-20220726172057180](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726172057180.png)

   

2. 이대로 한번 Main에서 출력해보자.

   - **아직 `매 자신의 닫는 중괄호} + 콤마,`의 종결처리가 안되어있다.**

     ![image-20220726172239040](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726172239040.png)





#### json은 자신의 처리시 여는 중괄호 존재 + 모든 자식들 끝날 때 1번 처리해야할 부분(닫는 중괄호 + 콤마)이 있기 때문에 종결처리를 채워줘야한다. 이 때, detp를 padding을 바꾸는 로직이 재활용되니 내수용 메서드로 추출해야한다.

![255f71f0-eed8-4463-8b2a-4bf179f5fd6c](https://raw.githubusercontent.com/is3js/screenshots/main/255f71f0-eed8-4463-8b2a-4bf179f5fd6c.gif)

![image-20220726172758391](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726172758391.png)

​	



#### 종결처리의 확인은, 자식없는 마지막 node에서 [자신의 종결처리] + [나의부모의 마지막node뒤 종결처리]를 확인하면 된다. -> [내 시작뿐만 아니라, 자식들 진입직전에 열어주는 것도 종결처리 해주자!]

![image-20220726173000555](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726173000555.png)

- **sub의 자식들이 끝나고 `자식들 진입직전에 열어주는 대괄호`는  닫는 종결처리가 아직 안되어있다.**
- **자식들끝에 1번 등장하는 `내 시작시 열어주는 중괄호`는 닫는 종결처리는 잘되어있다.**

#### 자식들 시작시 열어주는 처리는 콤마없이 종결처리 해주면 된다. 먼저 처리하고, 내 시작시 열어주는 처리를 뒤에 종결처리한다.



![102a00be-fe23-4a2c-bbbb-c5d08e158bb0](https://raw.githubusercontent.com/is3js/screenshots/main/102a00be-fe23-4a2c-bbbb-c5d08e158bb0.gif)

![image-20220726173343897](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726173343897.png)

![image-20220726173357547](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726173357547.png)









#### 내 시작시 열어주고, 내 종결시 닫는 처리의 콤마는 트레일링 콤마라고 한다.

![image-20220726173451611](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726173451611.png)

- 프레임웤을 만들어본 사람이라면, depth를 통한 분기처리를 통해 없애주지말고 사용하자고 하는 사람들이 많다고 한다.



### 16 Json자체를 만드려면, 출력관련(report, visitor, renderer)가 필요없이 Composite객체 원본이자 자식들을 다 담고 있는 root 객체만 Gson라이브러리에 넣어주면 된다.

- [참고 블로그](https://hianna.tistory.com/629#gson11)

1. implementation

   ```java
   dependencies {
       //gson
       implementation 'com.google.code.gson:gson:2.8.7'
   ```

2. **composite root객체를 gson#toJson()에 넣어주면**

   ![image-20220726180518040](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726180518040.png)

3. **자식들을 포함하여 다 json으로 만들어준다.**

   ![image-20220726180556973](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726180556973.png)

4. **필드명을 변경하고 싶다면, composite객체 필드에 `@SerializedName("바뀔필드명")`을 달아주면 된다.**

   ![image-20220726180648460](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220726180648460.png)





### 17  JsonVisitor가 끝처리로서 내어주는 트레이링콤마를 회수해야 serializing이 가능해진다.

#### 다시 한번 구조분석(Rendere-Visitor-Composite)

1. `Renderer`는 composite객체인 MenuReport를 받아, 재귀를 돌면서 뿌려주는 놈이다.

   1. 재료인 Composite객체를 바로 돌지 않고,`Visitor`(Supplier-Factory-지연생성-돌기직전에생성)를 주입받고, `Visitor와 Composite`객체를 같이 재귀태운다
      ![image-20220810120832476](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810120832476.png)

2. Visitor는, Composite객체가 재귀를 타면서 할 일`자신의처리` + 자식들호출+ `끝처리` 가운데

   1. **Composite객체가 스스로 해야할 일을, Visitor에게 위임하며, Visitor메서드의 인자로 넘어간다**

      ![image-20220810121049396](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810121049396.png)

   2. **visitor는 인터페이스로서, 구상Visitor들이 `종류별로 알아서 composite객체를 재료삼아, 자신의 처리 + 끝처리`를 하게 해준다.**

      ![image-20220810121316727](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810121316727.png)

      ![image-20220810121215562](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810121215562.png)

3. Composite객체는 어떤 제어문의 재료로서 **재료를 사용하는 책임 위임을 해준 놈으로서, 위임자Visitor의 파라미터로 들어가, Visitor의 책임들에서 사용된다.**





#### JsonVisitor의 composite객체 끝처리(endMenu)마다, [외부 동적트리순회-반복문의 끝(같은레벨선상 마지막자식) 호출 일때]마다 [재귀함수 isEnd플래그 파라미터]에  true를 넣어주세요~!를 부탁하면서 재귀함수에 파라미터를 추가 -> 끝처리함수에선 이용하기 위해 파라미터를 추가 한다

1. **끝처리 함수에 있는 트레일링콤마 출력이, `if 레벨선상마지막일 땐, 콤마없이 출력`하도록 `필요한 정보가 끝처리메서드 파라미터`로 들어오도록 해야한다.**

   ![ba726bc5-bec0-46a7-a3e2-3bf5d55202f6](https://raw.githubusercontent.com/is3js/screenshots/main/ba726bc5-bec0-46a7-a3e2-3bf5d55202f6.gif)

   ![image-20220810122917736](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810122917736.png)

2. **끝처리에 쓰일 정보 `isEnd`는 `동적트리순회 메서드 내에서 사용`되므로 `재귀의 파라미터로 업데이트 되어야한다`**

   ![image-20220810123008556](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810123008556.png)



#### 재귀메서드는 depth마다 업데이트 변수 뿐만 아니라 동적트리순회마다 반복문을 통과하면서 바뀌는 변수도 파라미터로 올린다. -> 반복문내에서 특정요소에서 바뀔 것이다.

- 기존까진 재귀메서드 -> 자식호출후 depth(stack)이 변했을 때, 변수들에 depth+1도 업데이트시켜셔, 현재depth마다 padding이 계산되게 하였다.
  - 이 때, **자식들 트리 순회하는 `반복문내에서의 재귀호출`마다, `다른정보가 발생한다면, 전체 재귀메서드의 파라미터로 올려` 반영하게 할 수 있다.**



1. 일단 isEnd는 재귀메서드의 파라미터로 올려야한다. **새로운 변수를 재귀메서드 파라미터로 올리면, `재귀 최초인자 호출 for root`에서 시작값을 입력해줘야한다.**

   - **root는 레벨선상 마지막이 맞으므로 true를 넣어준다.**

   ![eed36eda-33f7-4f33-8ad3-883e2b39a650](https://raw.githubusercontent.com/is3js/screenshots/main/eed36eda-33f7-4f33-8ad3-883e2b39a650.gif)

   ![image-20220810124228350](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810124228350.png)



#### 반복문 속 컬렉션(set)이라면, invariant(불가능한 상태 방지) 불변식인 .size()로 초기화한 변수를 반복문내에서 업데이트하며 사용한다(레벨선상 마지막 요소 찾기)

- 만약 i = 0부터 시작해서 i++로 업데이트해나가며 찾는다면, if i == size() - 1메서드를 매번 호출해야한다.
- **`컬렉션에 대한 불변식`인 i = size()를 변수로 빼놓고, 반복문내에서 `--i`로 업데이트해나가면**
  - 메서드를 1번만 호출해도되고
  - **어차피 i--를 업데이트해야니, 1개 큰 것에서 시작하여 미리 까고 나서 그 변수로 확인한다**
- **자바에서는 `반복무내 .size()로 시작하는 인덱스가, 먼저까고 검사 인자`에 넣으면, `단항연산자를 통해 뒤에 업데이트식이 없어진다.`**



#### 자바에서는 `반복문내 등차업데이트 변수`라면, 1개 큰데에서 시작하여 `--i, ++i`등의 `단항연산자를 통해 업데이트가 완료된 변수`를 바로 사용하여, 업데이트 문이 없앨 수 있다.



1. **size()-1( 마지막인덱스 )부터 시작**한다고 치면, **`시작부터 해당사항이 있어서 [검사후 업데이트`해야한다**
   ![image-20220810130000028](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810130000028.png)

2. **인자로 true/false가 들어간다면, `조건식 자체를 메서드 인자에 넣자`**

   ![image-20220810130019247](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810130019247.png)

3. **만약, `.size()`의 +1 큰곳에서 시작하여 `단항연산자로 먼저깐 변수`를 사용한다면, `업데이트문이 없어진다`**

   ![a0263657-de45-4d54-8666-d516616a1e4d](https://raw.githubusercontent.com/is3js/screenshots/main/a0263657-de45-4d54-8666-d516616a1e4d.gif)

   ![image-20220810130322350](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810130322350.png)



4. **조건문에서 `자식동적트리에서 레벨선상 마지막요소가 호출시 True`가 들어가게 되었다**

   - Main에서 이제 찍어봐야한다.

   ![image-20220810133100793](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810133100793.png)

5. **이렇게 마지막엔 트레일링콤마가 안붙게 되면, json으로 직렬화가 가능하다**
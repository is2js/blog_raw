---
toc: true
layout: post
title: menu를 만들며 익히는 command, memento 패턴
description: compositeMenu객체를 commandMenu객체로 memento를 통한 저장,로드

categories: [java, 우테코, oop, object, menu, command, memento, side ]
image: "images/posts/java.png"
---

# menu를 만들며 익히는 command, memento 패턴

- [composite패턴, visitor패턴 적용](https://blog.chojaeseong.com/java/%EC%9A%B0%ED%85%8C%EC%BD%94/oop/object/menu/composite/visitor/2022/07/30/menu%EB%A5%BC-%EB%A7%8C%EB%93%A4%EB%A9%B0-%EC%9D%B5%ED%9E%88%EB%8A%94-visitor%ED%8C%A8%ED%84%B4.html)
- [시작 깃헙](https://github.com/is2js/object2/tree/d503e51cfa4dadd3063a0c2cd06ba45388fe91a9/src/main/java/menu)



## Command패턴 적용하기

### 커맨드 인터페이스에서 역할(execute, undo)부터 정의하고, 기능위임한 객체는 파라미터로 받는다.

1. 인터페이스를 만들고 2가지 기본역할을 만든다.

   1. void execute

   2. void undo

      ![image-20220810175233743](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810175233743.png)

2. **`행위를 위임하여 캡슐화할 객체를 메서드인자로 받아서 내부에서 실행`시킨다.**

   - task객체를 직접적으로 조작하는 것이 아니라 **객체 인자 -> 객체 기능 자체를 포장해서 지연실행시킨다.**
   - **`객체 기능 위임 -> 위임한 객체의 메서드 파라미터`로 들어온다.**

   ![image-20220810175507565](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810175507565.png)

   

### 커맨드홀더객체 -> [기능 위임받을 객체를 소유]해서 래핑하는 구상클래스를 만들며, [모든 기능을 래핑]하기 위해, 위임객체Composite의 내장을 다 복사해온다.

#### 상속없이 특정객체의 기능을 위임하려면, 필드로 소유 -> 내장복사 -> 래핑하는 방법 밖이다

1. 커맨드홀더 객체는 **기능위임객체를 소유**해서 래핑하고 있다.
   ![image-20220810175831733](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810175831733.png)

2. **모든 기능을 위임받기 위해, `소유객체의 내장을 모두 복사`해서 가져온다.**

   ![dbabd3f0-fd62-4e4b-9ce9-51931040e0f4](https://raw.githubusercontent.com/is3js/screenshots/main/dbabd3f0-fd62-4e4b-9ce9-51931040e0f4.gif)





### 소유객체 기능복사후 내장을 정리한다

#### 01 소유객체 기능 래핑을 위한 내장복사후 [필드는 소유객체 빼고 다 삭제] + [생성자 이름 수정]부터 한다.

![25d509ee-d289-47a6-b893-39745a5e68d5](https://raw.githubusercontent.com/is3js/screenshots/main/25d509ee-d289-47a6-b893-39745a5e68d5.gif)

![image-20220810180242725](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810180242725.png)





#### 02  기능의 command화 대상들인 [상태변화 set계열] 메서드들은 일단 두고, [getter계열만 소유객체를 통해 래핑]한다.

##### getter는 제공기능이므로, 행위가 아니다. -> 행위를 캡슐화하여 지연실행하는 커맨드객체 대상이 아니다

- **어떤 객체에 대해 `상속없이 기능 위임`하는 방법은 `소유한 뒤, 래핑하면서 위임`하는 방법밖이다.**
- `return 필드` -> `return 소유객체.get필드()`
- getter가 파라미터가 있다면 -> 소유객체.getter(파라미터)를 넣어주면 된다.

- **getter내부가 복잡해도, 해당 메서드이이름만 보고 -> `소유객체.해당메서드()`로 만들자.**

  ![image-20220810181141278](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810181141278.png)

  ​	![image-20220810181119901](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810181119901.png)

![3e660f7b-654e-4ffe-a8f1-f937ace560ab](https://raw.githubusercontent.com/is3js/screenshots/main/3e660f7b-654e-4ffe-a8f1-f937ace560ab.gif)

- getter래핑이 끝나면, 맨 밑으로 내려놓자

  ![image-20220810181224648](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810181224648.png)









### 커맨드화 대상인 setter계열 메서드의 [실행시점] 위임하기

#### 새로운 객체인 command객체를 만들고, 커맨드홀더 객체가 [소유객체 래핑해서 실행]하는 책임을, 다시 한번 [커맨드객체에서 실행]하도록 [실행시점]을 위임한다



#### 01 execute와 undo가 같아 제일 쉬운 set계열 toggle커맨드객체부터 만들어보자.

1. **`소유객체의 set계열 메서드 실행`의 위임받을 커맨드객체는, 커맨드 인터페이스를 구현해서 생성한다.**

   - 정해진 메서드로만 위임할 것이다.
   - **특정메서드의 실행위임은**  **파라미터로 원래 기능객체가 가서 실행되는 것으로**위임된다.

   - **위임될 행위는, 파라미터로 같이 가는 이상, `해당객체.이미정해진 기능()`으로 실행된다.**
     - 따라서 따로 구현하거나 하지 않는다.

   ![95c5d03a-00f1-44d2-b642-f8e701be0907](https://raw.githubusercontent.com/is3js/screenshots/main/95c5d03a-00f1-44d2-b642-f8e701be0907.gif)

   ![image-20220810182500894](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810182500894.png)

   ![image-20220810182720508](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810182720508.png)



### 커맨드홀더객체는, 소유객체의 실행을 커맨드객체에게 위임하되  [커맨드 객체 내부 생성]후 [커맨드객체 위임 실행]함으로써, [지연실행 주체인 커맨드객체 자체]를 [실행전 컬렉션 필드에 저장]할 수 있다.

#### undo/redo는 지연실행 주체인 [커맨드객체]를 컬렉션 필드에 저장해놓기 때문에, execute한 주체를 다시 불러와서 undo할 수 있게 된다. 커맨드홀더객체가 [커맨드객체들 외부저장소(List필드)] 역할을 한다

1. 커맨드객체를 내부생성후, 실행 전 저장하는데, 컬렉션필드 생성후 한참 뒤에 add되므로 **빈컬렉션 + set/add메서드가 필수다**

   - **이 때, Set이 아닌 `List를 외부저장소로 채택`하여, `차후 마지막 꺼만 or 커서도입 or 짝수만 등`의 형태로 command를 뽑아 쓸 수 있게 한다**

   - **외부에서 add하는게 아니라, 내부에서 요소 생성후 add하니까, setter메서드는 현재 필요없다**

     ![3f5595a6-76a4-4c34-a6ef-f9cbfed6030f](https://raw.githubusercontent.com/is3js/screenshots/main/3f5595a6-76a4-4c34-a6ef-f9cbfed6030f.gif)

     ![image-20220810183432914](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810183432914.png)

     ![image-20220810183442992](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810183442992.png)





#### 모든 set계열메서드들은 커맨드객체에 실행시점위임으로서 해당 커맨드객체생성 -> 외부저장소에 저장 -> 실행한다. 이 때, [추상체변수cmd의 add -> cmd.execute]는 반복된다. 내수용 addCommand()메서드를 만들고, 반복사용하도록 하자.

- **공통필드**외에  **추상체변수 호출의 반복** 또한 반복되는 코드라서 내수용메서드로 추출해야한다.

![127d319e-adc3-406c-8bf3-a64544878491](../../../AppData/Local/Temp/127d319e-adc3-406c-8bf3-a64544878491.gif)

![image-20220810183915693](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810183915693.png)



#### **이제부터 `매 set메서드들의 커맨드화`할 때마다 addCommand()를 통해 `저장 + 실행`까지 한다.**



### 커맨드 홀더 소유객체는 [메서드인자를 통한 <호출 당시 해당context>를 기억해서 사용] 안해도될까?

- **아래와 같이 소유객체는 `호출당시 context`를 안받아도 될까?**

  ![3691e182-295a-4989-9a42-ea58a1c3848c](https://raw.githubusercontent.com/is3js/screenshots/main/3691e182-295a-4989-9a42-ea58a1c3848c.gif)

  ![image-20220810190305204](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810190305204.png)



#### 현재 모든 set계열메서드마다 [필드소유객체 CompositeMenu]를 [호출메서드의 파라미터]가 아니라 [내부context필드로서 반복 사용]하고 있다

![image-20220810190651777](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810190651777.png)

##### 행위 위임을 위한 메서드(위임당할 객체는 파라미터로)가 아닐 때, 파라미터로 받을 필요없이 내부context를 재활용해도 되는 객체는? [절대 상태변화가 없는 final 불변객체]만 가능하다

1. 내부context라도 **setter등으로 상태변화하는 객체**는 **상태 확인후 사용**해야하지만, **상태확인 없이 내부context를 재활용해도 되는 객체는 `final이 붙어서, context생성 당시, 생성자로만 받고 난 뒤, 아예 변할일이 없는 필드`만 가능하다.**

   



#### command홀더 클래스는, 래핑할 소유객체를 final불변으로 유지하여, 어떤 커맨드객체에서든 똑같이 작동하도록 한다

1. final 필드 -> 생성자 주입 받도록 수정한다.

   ![050bd901-b2c2-4755-801d-9b4653a82a26](https://raw.githubusercontent.com/is3js/screenshots/main/050bd901-b2c2-4755-801d-9b4653a82a26.gif)

   ![image-20220810191230273](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810191230273.png)



### Command홀더는 소유객체를 대신해서 외부에서 생성 -> 활용될 것이므로 소유객체 생성자 파라미터(값을 받아 필드 채우는)를 그대로 유지하며, 소유객체를 내부 생성한다

#### command홀더객체를 도입하는 순간부터, 기존 Composite객체는 외부에서 사용안된다. command홀더내에서 소유객체composite객체를 생성한다. 생성자 내장을 그대로 활용한다

![bc49ab22-2ae2-4910-a7d8-cd8ed8ce4108](https://raw.githubusercontent.com/is3js/screenshots/main/bc49ab22-2ae2-4910-a7d8-cd8ed8ce4108.gif)

![image-20220810191550687](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810191550687.png)



### 모든 상태변화메서드 setter(setTitle 등)를 커맨드화 한다

#### 정해진 인터페이스의 구상클래스가 기능을 위임받을 땐, 필요한 외부정보를 메서드로부터 받지못하니, 생성자를 통한 필드로 context를 기억해놔야한다.

#### 02 setTitle 커맨드화

1. 커맨드객체를 인터페이스 구현후 생성한다.

2. 외부저장소add 후 exexcute해주는 addCommand( ) 메서드에 cmd객체를 넣어준다.

3. **이 때,  오퍼레이터 execute에 필요한 외부 정보는, 구상클래스로서 생성자->필드로 기억해서 내부에서 사용한다**

   ![1ec01558-9ee0-4828-ba0c-64116ec83330](https://raw.githubusercontent.com/is3js/screenshots/main/1ec01558-9ee0-4828-ba0c-64116ec83330.gif)

   ![image-20220810192428138](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810192428138.png)

   ![image-20220810192509540](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810192509540.png)



### undo의 비밀은? 구상 커맨드[객체]에 [메서드인자로 들어가는 실행위임]했다면, [execute직전의 context를 getter로 가져와 객체 내부필드에 저장(set)]해놓아서, [undo시 불러와 사용]하여 counter칠 수 있다.

#### 커맨드객체라는 새로운 구상class에 위임하게 되면, 당시context를 필드로 기억할 수 있다.

### 단순 setter의 undo: execute직전에 getter를 통해 당시context를 가져와 내부필드(oldXXXX)를 이용해서 [old값으로 다시 setter]한다.

1. **execute당시의 context를 필드에 박아 기억**해둔다

   - 필드명은 `oldXXX`로 지으면 된다.

   ![1311f28a-8c62-46d3-8ab4-f02b7fd7d646](https://raw.githubusercontent.com/is3js/screenshots/main/1311f28a-8c62-46d3-8ab4-f02b7fd7d646.gif)

   ![image-20220810221534099](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810221534099.png)



#### 03 setDate 등 나머지 단순Setter 커맨드화

1. setTitle클래스를 복사해서 만들면 쉽다.

2. execute당시 필요한 context들은 위임객체(커맨드객체)의 생성자로 주입해서 박아둔다

3. **execute직전** 바뀌기 전 old context는 getter로 불러와서 필드로 주입해서 기억해놓는다.

   ![e192c4f6-a2dc-4ea3-9f69-4d49b5290547](https://raw.githubusercontent.com/is3js/screenshots/main/e192c4f6-a2dc-4ea3-9f69-4d49b5290547.gif)

   ![image-20220810222231170](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810222231170.png)



### add의 undo: 원본메서드에서 컬렉션에 add된 객체를 반환하도록 해야,  저장 후 undo를 칠 수 있다.



#### 일단 04 add execute만들기

1. 인터페이스 구현 Add 커맨드객체 생성

2. addCommand()를 태워서 외부저장소 add후 execute까지

   - 기존 add메서드는 void add가 아니라 boolean add였지만, **cmd.execute() 오퍼레이터는 반환값없이 다 void로 정의되어있어서 add래핑메서드는 boolean반환이 불가능해서 void로 바뀐다.**

     ![1bb28cb3-36eb-4ded-891e-a7990a7f7794](https://raw.githubusercontent.com/is3js/screenshots/main/1bb28cb3-36eb-4ded-891e-a7990a7f7794.gif)

     ![image-20220810233349666](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810233349666.png)


     ​	![image-20220810233424073](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810233424073.png)

     ![image-20220810233405626](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810233405626.png)




#### add의 undo는 실행 전 old값이 아닌, add될 내부생성객체가 필요하다

![image-20220810233819059](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810233819059.png)



#### add메서드는 undo를 위해서라도 내부생성되어 내부컬렉션에 [add될 객체를 반환]를 해줘야한다.

- 내부에서 생성된 후 , add되는 만큼, **해당 메서드에서 return해줘야 외부에서 가질 수 있다.**

- `public void add`로 만드는 컬렉션setter는 undo가 불가능하다고 생각하자.



1. 원래 기능을 가지는 compositemenu의 add메서드를 **add되는 객체를 반환하도록 수정**한다

2. **커맨드객체는, execute시 반환되는 `undo용 add된객체`를 필드로 저장한다.**

   ![cb06a746-b4c5-48cc-bf58-7d1bf92fdbd8](https://raw.githubusercontent.com/is3js/screenshots/main/cb06a746-b4c5-48cc-bf58-7d1bf92fdbd8.gif)

   ![image-20220810234237367](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810234237367.png)

   ![image-20220810234307874](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810234307874.png)

3. **저장된 add된 객체를 바탕으로 undo치면 된다.**

   ![image-20220810234517422](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810234517422.png)



### remove의 undo: remove된 객체가 아니라, [remove되기 전 객체의 필드정보들]을 [getter]로 받아와야,  저장 후 add할 수 있다.

#### 05 remove 커맨드화

- add의 undo는 내부생성된 객체를 필요로했지만
- **remove의 undo인 add는 `삭제 객체의 필드정보들`을 알아야 `내부생성`되도록 add할 수 있다.**
  - **remove되면 해당 객체필드정보들을 얻을 수 없으니, `execute에서 객체 삭제전, 객체필드정보들을 미리 getter로 빼놓는다.`**

![262e883f-e5f3-4101-9bbf-52796e2ad8df](https://raw.githubusercontent.com/is3js/screenshots/main/262e883f-e5f3-4101-9bbf-52796e2ad8df.gif)

![image-20220810235434224](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220810235434224.png)



### 생각해보기

#### 커맨드객체에는 2가지 종류의 기억필드를 가진다.

1. `소유객체의 기능호출에 필요한 context`를 생성자 -> 필드로 기억한다.
2. `execute당시의 context를 old계열`필드로 기억한다.
3. 만약, 커맨드객체에 위임안하고 undo를 한다면?
   - 당시의 context들을 모두 set or 배열 컬렉션으로 기억해놓고, 순서대로 가져와서 쓴다.
4. 하지만, **하나의 객체를 만들어놓고, 거기다가 기능 위임(파라미터로 원본객체)하게 되면, `객체context로서 필드에 이름으로 저장`해놓고 뽑아 쓸 수 있다.**



#### 커맨드객체로 굳이 기능(행위)를 한번 더 포장해서 써야하나? undo/redo와 지연실행

- 위에 말한 것처럼, Closure와 같이, **포장 객체 필드에 당시context를 기억**할 수 있어서 **undo, redo**가 가능하다.
- **또한, 행위 포장객체를 이용하면, 지연실행이 가능해진다**
  - addCommand()에는 외부저장소 add + execute를 묶어놨지만
  - **따로 분리하여 add따로 / execute따로 가능하다**



#### 커맨드객체 외부저장소를 `Set<Command>`가 아닌 `List<Command>`를 쓰는 이유

- 보통 같으면 index가 없이 객체식별자로 구분하는 Set을 썼겠지만

- **List를 저장소로 채택하면 index(값context)를 이용해서 `짝수만/홀수만/원하는 갯수/ 마지막꺼만` 뽑아 쓸 수 있기 때문이다.**

  - **Set은 마지막꺼만 뽑아 쓸 수 없어 undo를 할수 없다**

  ![image-20220811005136048](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811005136048.png)



#### 지연실행(포장객체에 위임)의 장점

- 지연실행이 안된다면, **여러 행위 실행시, 반복문에서 무조건 행위를 호출해야만 한다**
- **지연실행 된다면, `반복문을 10번 돌더라도, 객체만 뽑아놓고, 나중에 행위호출`할 수 잇께 되어, `비동기 실행`이 가능해진다.**



### 커맨드홀더의 undo메서드(미완성)

#### 지금까지는 커맨드홀더의 addCommand() -> cmd객체의 execute호출만 했고, cmd객체의 undo()를 호출하지 않았다.

![image-20220811005842037](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811005842037.png)



#### addCommand와 달리 undo()는 외부저장소List의 (조회전 처럼) remove 전(pop과 동일) 존재검증을 해야한다.

- undo할 게 없으면 그냥 early return하고 끝내자 (원래는 thr)

![image-20220811010028962](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811010028962.png)

- 원래는 list에서 stack으로 pop을 해야하지만, **java에서는 pop == remove(맨 마지막index)이다.**

  ![image-20220811010406685](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811010406685.png)

  ![image-20220811010434818](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811010434818.png)

  ![image-20220811010459328](../../../AppData/Roaming/Typora/typora-user-images/image-20220811010459328.png)





### 커맨드홀더의 redo: undo시 외부저장소(List)에서 remove(pop)로 날리지말고 Cursor 도입

- undo만 짠다면 외부저장소(List)에서 remove(size-1)로 pop하여 쉽게 짜여지지만, 
- redo기능이 존재할 예정이라면, remove로 커맨드객체를 외부저장소List에서 바로 날리면 안된다.



#### 외부저장소List의 index를 cursor로 도입하여, 컬렉션 요소 날리지 않고 add-execute하기

1. 저장소List 필드에 대해 **cursor의 위치도 항상 기억되어야하므로 `cursor대상 컬렉션 밑에 int cursor필드를 추가`한다.**

   - **빈 컬렉션 시작이므로 `cursor를 0으로 초기화`**
     - 원래 cursor 0은, 1개 아이템이 index 0에 들어가있는 상태이다.
     - **`원래라면 cursor를 -1 상태로 초기화`해야할 것 같다**
     - 어차피 cursor는 add시 add-execute시 size()-1로 마지막 위치로 업데이트될 것이다.
   - cursor는 바뀌는 것이니 not final

   ![image-20220811012311739](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811012311739.png)

   

   

#### cursor는 대상컬렉션에 요소를 add할때마다 1씩 증가되어, 맨마지막 index에 위치시킨다. 컬렉션이 사용되는 곳(ctrl+F7, alt+F7, add기능이 있는 곳 등)에서 cursor도 업데이트 로직을 추가한다.

1. ctrl + F7로 컬렉션변수가 사용된 곳을 다 찾는다.

   ![image-20220811013204369](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811013204369.png)

2. **add면 `add전 cursor++`, remove면  `remove전 cusror--;`업데이트를 해준다.**

   ![b61ce801-b9cb-41ac-8754-009770e28bf4](https://raw.githubusercontent.com/is3js/screenshots/main/b61ce801-b9cb-41ac-8754-009770e28bf4.gif)

   ![image-20220811013254482](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811013254482.png)

   ![image-20220811013305398](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811013305398.png)



#### 상태변화하는 컬렉션의 cursor(variant변수)를 직접 상태변화하지말고, 이미 변화한 연결 variant 필드(컬렉션)의 api를 이용한 불변식으로 관리해라

- 직접 `++`으로 업데이트하면, 틀림없이 오류가 생긴다.
- **`상태변화 필연시 되는 variant 필드는, 원치않는 상태를 방지하는 불변식을 찾아서 불변식으로 재할당하여 업데이트`해야한다**
  - List의 index는 제한범위가 있다. 직접 업데이트해서 if로 검사하지말고, 불변식을 찾아서 그것으로관리해야한다.



#### add시 cursor를 마지막index로 업데이트는, add전에 하지말고, add후 바뀐 컬렉션으로 불변식을 만들어서 한다.

- 앞으로 cursor의 업데이트는 add후 한다.
  - **add후에 컬렉션.size() - 1을 이용해 제일 마지막 index를 가리키게 한다.**



![a4103e47-b7d1-4ce7-ae50-3339826135ff](https://raw.githubusercontent.com/is3js/screenshots/main/a4103e47-b7d1-4ce7-ae50-3339826135ff.gif)

![image-20220811015636305](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811015636305.png)

### cmd외부저장소 List는 stack(역할)으로서 방금 add-execute한 커맨드객체가 [현재cursor + 1] 위치에 있어야한다. 그래야 undo(cursor-1)시 원래자리를 찾아올 수 있게 된다.

#### add후 cursor를 컬렉션api로 마지막위치로 업데이트하는 상황에서, add-execute하여 List에 add될 객체가 [현재cursor+1]위치에 이어야한다.  -> add전, [cursor+1~마지막]요소들을 remove해서 날려놔야, add후 size()-1이 마지막위치가 된다.

![image-20220811021902771](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811021902771.png)

1. 뒤로 이동했을 cursor의 위치에서, **마지막부터 ~ cursor+1까지 돌면서, remove로 날린다.**

   ![b9527979-acc9-4a94-9feb-4903051f019f](https://raw.githubusercontent.com/is3js/screenshots/main/b9527979-acc9-4a94-9feb-4903051f019f.gif)

   ![image-20220811022230632](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811022230632.png)



### 커맨드 홀더의 undo의 remove를 제거하기 위해 업데이트



####  undo에서 삭제 전 컬렉션  존재검증 -> 더이상 컬렉션 요소를 삭제하지 않으니, cursor로 검증(cursor 0은 undo시킬 cmd객체가 있는 상황)

1. **undo할 요소가 컬렉션에 있는지**

   - **(기존) add/remove로 관리었다면 -> `.size() == 0`  or  `.isEmpty()`**

     ![image-20220811023445411](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811023445411.png)

   - **(현재) 요소remove없이 cursor로 뒤로 이동만 함 -> `cursor 0이 첫 요소에 cmd객체가  undo할 것이 남아있는 상황` -> `cursor가 0보다 작으면 요소가 없는 상황`**

     - cursor0에서 뒤로 가면, 데이터가 없는 상황이다.
     - cursor0은 첫 원소가 있는 위치다

   ![image-20220811024501939](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811024501939.png)



#### undo는 (pop==remove(size-1) 대신) 현재cursor의 외부저장소cmd객체를 가져온 뒤, cursor는 한칸 뒤로 이동시켜야한다

1. 기존에는 remove로 pop해와서 썼는데, 지금은 현재커서 위치 갖다쓰고, 컬렉션은 유지하고 커서만 이동시킨다.

   ![db1f1a31-c608-4296-9658-009b3cc1c221](https://raw.githubusercontent.com/is3js/screenshots/main/db1f1a31-c608-4296-9658-009b3cc1c221.gif)


   ![image-20220811024619373](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811024619373.png)

2. **사용후 1씩 업데이트라면, java에서는 `메서드(변수사용--)` 이나 `메서드(변수++)`형태로 단일연산자로 처리할 수 있다.**

   ![image-20220811024644454](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811024644454.png)

3. 트랜잭션일어날 일이 아니면 **지역변수 쿠션을 안준다.**

   - **단항연산자는 앞에 붙을때만 업데이트되서 사용되고, 뒤에 붙으면 사용후 업데이트 된다.**
   - **`단항연산자 사용변수 이후 체이닝메서드는 이미 업데이트된 변수 상태니, 사용시 주의하자`**
   - **`트랜잭션을 만드는 메서드 체이닝시 단항연산자를 사용해서, 중간에 다른 사용 못하게 하자`**

   ![image-20220811024828326](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811024828326.png)

### redo 구현

#### (이론) redo는 [커서만 빽한 undo상태가 대상]이며 cursor + 1 위치로 가서 꺼내서 execute하는 것이다.

![image-20220811121544292](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811121544292.png)



#### (이론) undo의 대상과 redo의 대상

- undo의 대상: 현재 execute하여 stack에 add된 cmd객체
  - add-execute시, **cursor도 맨 마지막으로 업데이트 된 상태**가 되도록 로직이 짜여져있다.
  - **현재 cursor의 cmd객체를 가져와서 .undo만 치면 된다.**
- redo의 대상: 현재 undo하여 stack상 cursor가 -1 빽한 상태이며, **cursor한칸 앞의 cmd객체**
  - **cursor를 +1 해준 뒤, 그곳의 객체를 execute해주는 것이 redo**
  - cursor가 add-execute의 대상cmd객체에 위치하고 있으니, 다시 undo가 가능하다
- **execute는 처음 `add-execute든, redo든, 방금 시행한 객체를 가리키고 있어야 undo가 가능`해진다.** 





#### 구현1 - redo전 cursor invariant검사로, [redo할게 없는 상황이면 종료] (cursor한칸 앞으로 못감 == undo안한상황 == add-execute만 한 상황 == cursor가 제일 마지막에 위치) 

1. **cursor가 undo를 하지 않은 상태면, redo가 안된다.** 

   - redo는 undo한 상태에서 cursor를 다시 +1 한 뒤, execute하는 것인데
   - **cursor가 List 맨 마지막에 위치한 상태(undo안하고 add-execute만 한 상황)이면 redo는 불가능**하다.

   ![90ca8de4-f4ef-494e-bdcc-502195479129](https://raw.githubusercontent.com/is3js/screenshots/main/90ca8de4-f4ef-494e-bdcc-502195479129.gif)

   ![image-20220811122515721](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811122515721.png)

#### 진해서 두고, 그 놈을 한번 더 execute하는 것이다.

- 만약, 먼저 업데이트되서 쓴다고 하면,  **`++변수` 단항연산자를 메서드 인자로** 쓰면 된다.

  ![5fc43cc6-6bd2-4ca7-9853-7faa87c8305e](https://raw.githubusercontent.com/is3js/screenshots/main/5fc43cc6-6bd2-4ca7-9853-7faa87c8305e.gif)

  ![image-20220811122734403](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811122734403.png)

  ![image-20220811122750640](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811122750640.png)



## addCommand/ undo/ redo with cursor -> 객체소유 커맨드홀더객체 완성 -> 커맨드패턴 완성 -> undo/reo하며 출력



### Main에서 Composite객체(소유객체) 대신 Command홀더객체를 사용해보자.



#### 더이상 소유된 composite객체는 외부에서 사용하지 않는다. Command홀더객체가 생성자에서 재료만 받아 내부생성했다

- Main에 있는 CompositeMenu -> CommandMenu로 변경하자

  - ctrl + F7로 사용 context들을 표시하고 시작하면 편할 것이다.

  ![94b5cc1d-15c3-4e57-957e-bbf5617a1296](https://raw.githubusercontent.com/is3js/screenshots/main/94b5cc1d-15c3-4e57-957e-bbf5617a1296.gif)

  ![image-20220811124541044](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811124541044.png)





#### root에 menu에만 add할게 아니라, 자식menu을 먼저 미리 생성(자식에 자식 미리 추가)한 것도 add하려면, 자식 menu를 지역변수로 받아서 생성해놓고, [root에 미리 생성된 menu도 add가능하도록 수정]해야한다

- 현재는 
  - root menu의 정보 -> root생성
  - root의 1차 자식의 menu정보 -> **root 1차 자식들 add만 할 수 있는 상황**



- **정보를 통한 add 이외에** **외부에서 미리 생성된 CommandMenu를 add할 수 있도록 메서드를 추가해보자.**
  - 문제점이 발견된다.

#### 기존의, 정보를 통한 소유객체 내부생성 Add커맨드객체는, 이미 생성된 Menu객체와 전혀 다른 context -> 생성자-필드가 다름 -> 새로운 커맨드객체가 필요하다

1. 일단, 외부에서 Composite객체 대신, Command홀더객체로 만든 것을 add해보면, **기존 커맨드객체가 instance context로서 기억하는, 당시context == 필드가 달라진다.**
   ![image-20220811133820213](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811133820213.png)

2. 기존 필드들을 초기화할 수 없어서 생성자 및 필드에 불이 들어오게 된다.

   ![image-20220811133848633](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811133848633.png)

3. 억지로 필드에 기억했다고 치더라도 **execute시에도 다른 메서드를 호출해야해서 곤란하다**

   ![image-20220811133934991](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811133934991.png)

4. **게다가 `커맨드홀더는 포장객체일 뿐, 내부 자식들은 Composite객체(소유객체)형으로 자식들이 추가`된다.**

   - **메서드 파라미터가 달라지면, `행위위임받은 커맨드객체는, 메서드처럼 오버로딩해서 재활용`못한다**

   ![image-20220811134014851](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811134014851.png)



### 파라미터만 다르더라도 새 커맨드객체 생성

#### 행위위임 커맨드객체는, 커맨드호출 서비스메서드의 파라미터가 달라지면, 커맨드객체의 기억필드가 달라져서 [메서드 오버로딩처럼]재활용 못하니, 새 커맨드객체를 생성해서 위임해야한다.

1. `add시 정보` -> 내부compoiste객체 생성 형태가 아니라 `add시 이미 완성된 command객체`로서 파라미터가 달라졌다면, **새롭게 커맨드객체를 만들어야한다.**

   ![52f0ac03-237c-49a1-892e-20b7baf259b6](https://raw.githubusercontent.com/is3js/screenshots/main/52f0ac03-237c-49a1-892e-20b7baf259b6.gif)

   ![image-20220811135319617](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811135319617.png)

   ![image-20220811135507553](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811135507553.png)

2. **위임해줬던 Composite객체는, 커맨드객체 내부에서 지연실행 될 때, `완성된 커맨드객체가 아닌, 자신형을 자식으로 add`한다**

   ![image-20220811135745691](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811135745691.png)

3. 커맨드홀더객체로부터 소유객체 getter를 만들어준다.

   - Add과정과  동일하게 처리해준다.

   ![2e7ae8b3-a902-4135-a9b9-2c3fb8ee1fad](https://raw.githubusercontent.com/is3js/screenshots/main/2e7ae8b3-a902-4135-a9b9-2c3fb8ee1fad.gif)

   ![image-20220811135943175](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811135943175.png)

   



### 출력은 Command홀더객체(root) -> report생성 -> Renderer with Visitor -> ConsoleVisitor로 출력이다.

#### 제어는 Renderer가, 제어를 타는 것은 특정 Visitor들을 미리 생성자 주입

#### 제어타면서 행위를 할 객체를 Renderer에게 건네주면, 내부에서 Visitor에게 파라미터로 객체를 넘겨, Visitor에게 위임한다

1. 출력은 ConsoleVisitor를 주입해서 만든 Renderer로 한다.

   - 어차피 **Visitor는 Renderer내부 (지연)생성이니, `Render의 메서드에게 재료객체Report`를 던져줘서 호출한다.**

   ![image-20220811140728010](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811140728010.png)

   ![image-20220811140904808](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811140904808.png)



### 생각해보기

#### 소유객체는 undo에서 remove하기 위해,  add시 add되는 객체를 return해서 필드로 저장  /  커맨드홀더객체의 add는 return없는 void

- CommandMenu

  ![image-20220811155816972](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811155816972.png)

  ![image-20220811160044421](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811160044421.png)

- CompositeMenu

  ![image-20220811155912879](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811155912879.png)



- **`커맨드홀더객체의 add는 원래 return이 없다` **
  - 소유객체를 **지연실행**시키기 위해 **add동작이 구상 커맨드객체 내부에서 소유객체의 add가 실행**된다.
  - **커맨드객체에 위임한 뒤, 커맨드객체를 필요할 때 지연실행 시킬 수 있어야, `실행 전 외부저장소에 저장해놓고 실행`할 수 있기 때문에, `최종 소유객체의 add가 미리 실행되서 return되면 안된다.`**
    - 바로 실행한다면 무조건 동기적인 실행이며, undo/redo를 못한다.

#### 커맨드객체에 지연실행을 위임함으로써, 실행 시 aggregation필드(외부저장소List)에 저장해놓을 수 있으며, `다시 반복실행, undo실행, redo 등을 저장소에서 꺼내서 한다`

![image-20220811160618447](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811160618447.png)

### 오타 수정

#### CompositeMenu의 remove에서 존재검증시 !느낌표 하나 빠짐

![image-20220811161948509](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811161948509.png)

### Composite객체 -> Command객체 사용전환 완료됨 -> 커맨드홀더객체(커맨드패턴)만의 undo/redo를 사용해보자.



- **포장한 커맨드홀더객체를 만든 이유**는, 내부에 **커맨드객체 외부저장소(aggregation)**를 가져서 undo/redo를 하기 위함.



#### 커맨드홀더객체.undo()는 커맨드홀더객체 어느행위 든(addMenu, setTitle 등) 직전 행동에 대해 카운터를 친다.



- **undo는 해당 Command홀더객체의 최근행동에 1개씩 적용된다.**

  ![image-20220811162230541](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811162230541.png)



#### redo는 직전의 undo에 대해서 카운터를 친다

![image-20220811162318006](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811162318006.png)



#### 잘 작동한다

![3c7071af-ce20-41cf-ab57-04d9de15394e](https://raw.githubusercontent.com/is3js/screenshots/main/3c7071af-ce20-41cf-ab57-04d9de15394e.gif)





### 생각해보기

#### public 서비스메서드 with private 도메인객체 파라미터 메서드 -> 도메인객체 generator 서비스메서드 



##### 커맨드홀더객체의  소유객체 래핑 set계열메서드들은 다 [addCommand(해당 커맨드객체) 를 호출하는 래핑된 서비스 메서드]들이다

- **전부다 내부에서 execute-add하는 addCommand(new 커맨드객체())만 호출한다.**

  ![image-20220811162721858](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811162721858.png)



##### 래핑을 풀고 private addCommand메서드 만 public으로 공개하면 처리 가능하지만, 외부에서 직접 커맨드객체를 new때려서 생성해서 넣어줘야한다. -> 차라리 서비스메서드를 제공하고 커맨드 홀더객체 내부에 private generator로 생성하는게 낫다

- 가정

  ![image-20220811162849837](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811162849837.png)

##### 객체 파라미터를 public으로 공개한다는 것은, 외부에서 생성한 뒤 받겠다는 말인데, 도메인을 보호하자. -> public메서드의 객체 파라미터는 최대한 피하자 -> 서비스 메서드를 제공하자

![image-20220811163021173](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811163021173.png)



#### 커맨드패턴(커맨드홀더객체)을 쓰면 좋은 점(지연실행으로 인한 외부저장소 -> undo/redo 장점 외)



##### composite객체가 제공하던 모든 메서드들 -> command홀더객체는 사실상 외부에 public addCommand(execute+add)만 제공 + 구상 커맨드객체만 외부에서 입력해주면, 그외 메서드는 없어도 된다.(편의상 커맨드객체별 서비스메서드로 제공 중)

- 행위(compositeMenu)를 구상클래스(command객체들)에 위임한다 -> 행위객체를 소유하는 홀더객체(포장객체, commandMenu)는 위임된 구상클래스를 인자로 받는 메서드 1개(addCommand())만 가지면 된다.



##### 실시간, 실행(런타임) 중 상태변경은 커맨드객체가 최고다. 커맨드홀더 + addCommand( 원하는 상태변경 커맨드객체 ) ex> 홈페이지 빌더



- 이렇게 되면, **컴파일 타임에는 메서드가 없고, 런타임에 위임받은 구상클래스만 `원할때마다 동적으로 바꿔서 넣어`주면 기능이 작동한다**

- 즉, **커맨드홀더객체만 가진 상태**로 + **관련 구상 커맨드객체**만 받으면, 원하는 기능을 수행하게 된다.

  - 잠시 서비스 메서드들 없다고 가정하고 addCommand를 public으로 풀어놓은 상태
  - **버튼 클릭시, 해당 구상체만 addCommand해주면 원하는 상태가 된다.**

  ![image-20220811164310426](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220811164310426.png)

  - homepage 빌더들은 **해당 요소클릭시, 해당하는 커맨드객체만 add-execute해준다**



##### 즉 범용 커맨드홀더객체 + 차이점을 만드는 구상커맨드객체들을 소유하고 있으면, 객체간 차이를 런타임에서 실시간으로 만들어낼 수 있다.





##### 커맨드홀더객체 내 서비스메서드들(addCommand래핑)이외에 메서드들(get계열) 의존성을 가진 것으로, 소유객체가 변하면 같이 죽는다. 하지만, 서비스메서드들은 이미 죽은상태의 메서드들이라 의존성없이 좋은 메서드들임

![image-20220801235406574](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220801235406574.png)



#### 커맨드패턴이 무적같지만.. save와 load가 안된다. save와 load를 하려면, [시리얼라이제이션을 통한 영속화]를 따로 해야한다.



## 메멘토 패턴 save with Visitor패턴



### 커맨드홀더객체는 소유객체(Composite객체)의 메멘토(상태정보)를 관리하는 CareTaker역할을 하여 저장컬렉션 필드를 가진다.

#### 고전에는 Stack으로 관리하지만, 여기서는 Map으로 한다. map의 key는 주로 string으로 받는다. save는 json으로 저장될 메멘토라서 string으로 저장한다.

![image-20220812123737095](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812123737095.png)



### 메멘토 관리자 커맨드홀더는, save와 load함수를 가지며, void save, String load 시그니쳐부터 작성한다.

####  save시 저장할 string key를 외부에서 받고, load로 꺼낼 때도 key를 받는다.

![image-20220812124146329](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812124146329.png)



### Save는 Renderer와 JsonVisitor를 활용해서 만든다.

#### 제어역전한 Renderer, 생성자주입되어 제어를 타는 전략객체 Visitor, 메서드인자로 들어가는 데이터객체(Composite->report)의 역할을 변경해야한다

1. 실질적으로 전략 중 하나로서 **Composite객체를 메서드 인자로받아, Renderer부터 Composite객체 제어로직을 위임받은 JsonVisitor의 역할**이 

   - 현재는 **Composite객체의 데이터**를  json형태로 만들어서, **콘솔에 출력하는 상황**이다.

     ![image-20220812124544994](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812124544994.png)

     ![image-20220812124601092](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812124601092.png)

   - **출력이 아니라 `string으로 모아서 반환해주도록 변경`해야한다**

2. **최종적으로 `Renderer가 render함수를 통해 데이터 객체의 제어 처리`하므로 `JsonVisitor를 태운 renderer`는 출력이 아닌 `데이터객체 + 제어 시 string을 모아서 반환`하게 하고, 이것을 이용해 `save`해야한다**

   - ConsoleVisitor만, 데이터객체를 제어를 태우면서 출력

     ![image-20220812124942863](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812124942863.png)

   - **JsonVisitor는, 데이터객체를 제어를 태우면서 String반환**



#### Renderer는 [제어만 역전해서 visitor에게 제어로직을 위임해주는 void메서드]만 제공한다. 뭔가를 반환안해준다.

![image-20220812125320309](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812125320309.png)





#### 전략Visitor들은, Renderer(제어역전)클래스에 주입되서,  데이터객체를 메서드인자로 위임받아, 자기 전략메서드들에서 사용하지만,  [제어처리 전략메서드들도 다 void]이다. 뭔가를 반환안해준다

- Renderer속 Visitor

  ![image-20220812125456303](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812125456303.png)

- Visitor인터페이스

  ![image-20220812125516950](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812125516950.png)



#### 출력관련 제어역전Renderer와, Visitor 전략오퍼레이터들은 모두 void로 정의해놓고, [구상 전략객체들이 데이터+출력 제어로직을 담당]한다

![image-20220812125753628](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812125753628.png)





### 구상 Visitor들은 [위임받은 데이터 객체를 처리]할 때, [싸이클을 타면서 처리한 정보를, 구상 객체context로서 필드에 저장]해놨다가, [차후 getter로 제공]해줄 수 있다.



#### 출력, rendering관련 제어로직들은 다 void로 시작한다. Visitor가 싸이클을 타면서, 처리된 정보를 [구상 Vsititor전략객체마다 저장하고 싶은 데이터개체 처리정보는 상태값으로 저장]으로 일단 저장해서 품고 있자!!

1. JsonVisitor에 **제어 라이프싸이클을 타는 메서드들의 `처리결과를 모을 수 있는 상태 필드`를 추가하자**

   ![image-20220812131846831](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812131846831.png)

2. 더이상 출력하지 않으므로, sout 와 padding부분을 삭제한다.

   - json을 출력안한다면, 공백문자열인 padding을 삭제해줘야한다.

   - padding 관련 삭제

     ![f6dcf6df-87c6-4d92-9100-8065db3ad2ef](https://raw.githubusercontent.com/is3js/screenshots/main/f6dcf6df-87c6-4d92-9100-8065db3ad2ef.gif)

     ![image-20220812132146294](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812132146294.png)

   

3. **sout삭제**하고 **`Visitor의 상태값`에 다가 stirng add로 모으기**

   - Visitor의 저장상태값은 final이면 안되겠구나! **사이클타면서 계속 저장**
     ![f48f2af5-ac47-4626-b72f-a30ad8093e97](https://raw.githubusercontent.com/is3js/screenshots/main/f48f2af5-ac47-4626-b72f-a30ad8093e97.gif)

     ![image-20220812132953555](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812132953555.png)

   



#### factory에 ( () -> new객체) 지연생성  vs  ( () -> 외부생성객체 로컬변수) 이미 생성된 객체 주입

![image-20220802130921137](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220802130921137.png)

##### my) 함수형 인터페이스 factory(지연생성)으로 주입하는 이유는 [가상인자 람다식을 통한 구현]시 [new 때리는 것을  넣어 지연생성]할 수도 있지만, [이미 외부에서 생성된 객체를 주입]도 선택가능하기 때문이다. 



### 상태값을 가지는 Visitor는 더이상 [Reneder 속 factory로 주입 -> 내부에서 new때려서 생성 후 처리하고 소멸]해서는 안된다.



#### save에 필요한 정보를 이미 생성한 JsonVisitor가 renderer의 싸이클을 타고와서 getter로 제공해줄 것이다.

- **JsonVisitor는 상태값을 가지는 순간부터, [외부 미리 생성 -> 싸이클 태워 상태값에 정보채우기 -> 외부에서 getter로 채운 데이터 사용]이 가능**하다



#### renderer + jsonvisitor를 커맨드홀더객체 속 메멘토 save메서드로 가져와 정보, 싸이클태워, 출력대신 상태값 저장된 정보를 getter로 제공해준다.

1. Main에서 출력에 사용됬던 Renderer + Visitor 사용코드를 CommandMenu#save메서드로 가져온다

   ![5c30774b-dc9e-420f-b16b-3fa7d8eb9341](https://raw.githubusercontent.com/is3js/screenshots/main/5c30774b-dc9e-420f-b16b-3fa7d8eb9341.gif)

   ![image-20220812134503073](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812134503073.png)

   - **`Renderer와 Visitor는 이제 커맨드홀더객체의 내부생성 의존도`를 가지게 된다.**

2. **JsonVistor를 Renderer에 주입해서 싸이클을 태우도록 변경**한 뒤, **`Renderer 내부 지연생성이 아니라, 외부에서 미리 생성된 jsonVisitor`를 지역변수로 빼서, 넣어준다.**

   ![64c4c348-ac60-4692-a970-ecdc73e46db8](https://raw.githubusercontent.com/is3js/screenshots/main/64c4c348-ac60-4692-a970-ecdc73e46db8.gif)

   ![image-20220812134917250](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812134917250.png)



#### 참고) 지연실행을 위한 람다식 속 지역변수는 inline하면 안된다! 

- 람다식에서 생성식이 있다? -> **지연실행이 -> 지연생성**이다. -> **지연생성은 내부생성이며 -> 내부에서 소멸되는 `상태값 정보가 외부에서 필요없는 유틸성 메서드만 가진다`**
- **람다식에 이미 생성된 객체를 넣어준다? -> **
  - **`구상체 중 지연생성 == 내부생성`하는 `유틸성, 상태값없는 객체`도 있지만, **
  - **`다른 구상체는 외부에서 미리 생성해서 내부에서 처리되며, 상태값에 정보를 저장`하는 과정을 거치게 된다.**
    - 이것을 인라인해버리면... 내부생성으로 바뀐다.



#### 참고2) 커맨드홀더객체는 Main에서 생성한 [컴포짓 root객체]를 소유한다. 그래서 save속 renderer에 필요한 report를 만들 때, root인 소유객체필드(Composite필드)로 만든다.

- Main 속 root 객체 -> report생성 -> renderer가 태움

  ![image-20220812135651667](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812135651667.png)

  

- **save 속 compoiste소유객체 -> report 생성에 사용 -> renderer에 사용**

  ![image-20220812135724238](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812135724238.png)

##### my) 지연생성은 내부에서 new때려서 생성하는 것과 동일하다. 상태값 없는 불변객체 생성시 이렇게 한다. 그러나 어떤 구상체는 상태값을 가지고, 내부에서 처리된 후 [변경된 상태] == [새로운 정보]를 외부에 제공할 수 도 있다. [지연생성 람다식에 -> 이미 생성된 객체]를 넣어줘서, 내부로직이 끝난 후에도 정보가 외부지역변수 속 객체는 정보를 가지고 있게 된다.



#### 외부미리 생성된 뒤, 싸이클을 타는 Visitor객체는 싸이클을타며 저장한 상태필드 속 정보를 getter로 제공해줘야한다.

![0c99b1e8-65d9-438c-9361-c37c7e05bdaa](https://raw.githubusercontent.com/is3js/screenshots/main/0c99b1e8-65d9-438c-9361-c37c7e05bdaa.gif)

![image-20220812140058863](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812140058863.png)

![image-20220812140042921](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812140042921.png)





### save내부 json만드는 일을 메서드추출하여 리팩토링하고, json만드는 일을 테스트하자



#### 내수용 메서드추출시, 내부context가 불변객체면 파라미터로 안빼도 된다.

- 메서드 추출시 **`내부context(보라색)`이 불변 필드면, 굳이 파라미터로 추출안해도** 된다!

  - **만약, 다른객체에 위임할 예정이라면, 파라미터로 올려줘야한다.**

  ![fd4ed7f5-b458-40bb-a44d-551aadf7d564](https://raw.githubusercontent.com/is3js/screenshots/main/fd4ed7f5-b458-40bb-a44d-551aadf7d564.gif)

  ![image-20220812140847628](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812140847628.png)



#### create json하는 일은 private내수용메서드다. 테스트는 그대로 복사해가서 하고 지운다

- 테스트는 작성안하는 중이니 Main으로 복사해놓고

  - static을 붙이고
  - **필요한 context는 파라미터로 빼서, main내에서 공급받아 테스트한다**
  - json출력을 확인하려면, `.json`파일을 만들어서, 자동정렬시켜보면 된다.

  


  ![24afe264-683c-46a2-a6f0-fa4ebaef114e](https://raw.githubusercontent.com/is3js/screenshots/main/24afe264-683c-46a2-a6f0-fa4ebaef114e.gif)

  ![image-20220812162857539](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812162857539.png)

  ![image-20220812162831307](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812162831307.png)



### 생각해보기

#### memento패턴은, visitor패턴이 이미 구현되어있으면 쉽게 구현가능하다

1. Visitor패턴으로 **데이터객체의 정보처리를 `Visitor의 상태값에 저장`**한다.

   - Visitor는 출력관련 제어로직을 역전시킨 Renderer클래스에 주입되어
   - 출력관련 라이프싸이클을 타면서
   - 데이터객체를 인자로 받아 라이프싸이클 메서드 내부에서 제어로직을 처리한다
   - **제어로직 처리가 출력 or 데이터를 모으는 과정이라면, `메서드마다 처리내용을 상태값에 저장`해놓으면 된다.**
   - **한번 타고온 외부생성Visitor는 상태값에 가진 정보를 getter로 토해낸다**

   ![image-20220812163436350](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812163436350.png)

2. **Visitor가 Memento정보를 내부 상태값으로 가지고 있는 것**이므로, **데이터객체 홀더 객체(커맨드홀더객체) 내부에 save, load를 구현하는데, `save메서드에서 데이터객체 인자 + Visitor 생성자주입`으로 라이프싸이클 태우고 나온 `Visitor의 상태값`을 그대로 받아서, save할  컬렉션에 저장한다.**

   

   ![image-20220812163450728](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812163450728.png)







## 메멘토 패턴 load 구현(jsonString -> root부터 자신setter + 자식동적트리순회 -> composite객체 만들기)

### load는 조회이므로, [존재검증 -> 조회 -> 가져온 데이터의 postcondition검사]까지 동시에 한다.

![image-20220812171906759](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812171906759.png)

![image-20220812174319707](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812174319707.png)

- 오타수정

  ![image-20220812192105685](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812192105685.png)

### load시 컴포짓객체의 생성은 커맨드홀드가 소유한 필드객체(root Composite)을 재활용해서 한다. 

#### 재활용하는 composite root 객체는 자식들을 먼저 비워야한다. 이 때, 자식들도 자신내부의 자식들을 비워야하니, [재귀 -> 동적트리순회를 통한 삭제]를 해야한다.

1. 재활용할 커맨드홀더객체 내부필드인 root composite객체에게 **자식들을 삭제하라는 메세지를 보낸다.**

   - **`Composite객체 내부`에서 자식컬렉션을 지우는 작업이 이루어진다.**
   - 자신을 비우는 것은, **생성시 setter를 사용할 예정이므로** 안해도 된다.

   ![image-20220812175140991](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812175140991.png)





#### composite객체의 자식들 삭제는 [자식의 자식들도 삭제되거야하는 동적트리 순회] -> 재귀메서드가 된다.

1. 외부에서 자식들 지우라고 메세지를 보냈지만, **자식들의 자식들도 지워져야하므로 `composite객체의 삭제`역시, 동적트리순회를 통한 삭제 -> 재귀메서드이다.**

   ![image-20220812175353691](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812175353691.png)



#### [동적트리삭제] 재귀메서드의 종착역으로서, [삭제할 자식들이 없으면 ealry return]으로 종료한다. (삭제 전 존재검증은 thr, [없을 수 도 있는 것의 삭제]는 early return이다. )

![40fe9655-5141-4035-b515-73573fcac395](https://raw.githubusercontent.com/is3js/screenshots/main/40fe9655-5141-4035-b515-73573fcac395.gif)

![image-20220812175655588](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812175655588.png)



- 자식들 삭제를 위한 재귀에서, [자신의 처리] -> [자식들 처리]의 호출만되고 삭제로직은 아직 없다. 

  ![image-20220812175937239](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812175937239.png)





#### [자식들의 동적트리삭제]는 자신의 차례에서 [자식들삭제 로직]을 넣어줘야한다. [자식들을 동적트리순회하면서, 호출하고 난 다음] -> 끝처리로서 [자식들 컬렉션필드를 .clear()] 해준다

#####  자식들에게 피드백받는 재귀처리는, 자식들을 살려놓고 돌린상태의  [동적트리 or 자식재귀호출 1~여러개 호출]이 끝난 상태에서, [끝처리로 자식들을 처리]한다

![image-20220812180055145](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812180055145.png)







#### 리팩토링 -> load + 자식들처리에서 load로직을 1개 메서드로 추출

##### 할당문이 포함된 로직에서, 아직 할당된 지역변수가 뒤에 쓰이지 않았다면, return없이 추출된다. -> 뒤에 억지로라도 써놓고 추출하면 편하다

- 참고) 할당문 추출시, 뒤에 쓰이지도 않았는데, return한다? **반복문 내 업데이트변수를 포함한 추출이다 -> 어차피 외부에서 업데이트될테니, 가변변수 빼고 추출할 수 있도록 해보자.**

![ff784f4b-c42f-4d8f-a765-e1447cd5647e](https://raw.githubusercontent.com/is3js/screenshots/main/ff784f4b-c42f-4d8f-a765-e1447cd5647e.gif)

![image-20220812180723286](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812180723286.png)

![image-20220812180738951](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812180738951.png)





## json String을 cursor도입하여 다음depth로 이동하면서 Composite객체 재생성하기



### load메서드내 json(string) 파싱하는 복잡한 작업이 시작된다. 디버깅하면서 내부로직을 만들기 위해 load한 json을 반환하도록 수정한 뒤, 테스트에서 환경을 조성해 만들어나가자

![db6bedac-6e54-4def-86b5-7ec09032f498](https://raw.githubusercontent.com/is3js/screenshots/main/db6bedac-6e54-4def-86b5-7ec09032f498.gif)

![image-20220812192208045](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812192208045.png)

![image-20220812192141957](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812192141957.png)

![image-20220812192317195](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812192317195.png)



#### 최소한 자식을 가지는 예제 json String을 만들어둔다.

![14155c0a-3e64-4b82-b269-da3d9b4e51fc](https://raw.githubusercontent.com/is3js/screenshots/main/14155c0a-3e64-4b82-b269-da3d9b4e51fc.gif)

![image-20220812192820284](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812192820284.png)



### 커맨드홀더객체 내, root자신부터 만들기 위해 데이터 추출 by target String + .indexOf() + cursor + .substring

#### 01 찾고싶은 string속 data의 직전 string을  target변수로 추출한다 (예제 jsonstring 복붙활용)

- title의 데이터를 찾기 위해 데이터 직전까지 중에 타겟string `title:  "`을 잡는다

![a94eda59-80eb-4b25-a3eb-a8b75e256101](https://raw.githubusercontent.com/is3js/screenshots/main/a94eda59-80eb-4b25-a3eb-a8b75e256101.gif)

![image-20220812193341896](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812193341896.png)





#### 02 cursor을 0으로 초기화하고, index.Of( string타겟, 시작index)에 쓰일 [데이터추출시마다 업데이트되는 검색 시작인덱스]로 사용한다

![image-20220812193843458](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812193843458.png)

#### 03 .indexOf( target[데이터직전까지의 문자열], cursor[검색시작인덱스] )을 통해 -> target start index를 찾는다. -> 못찾는 경우 -1을 반환하며, 그 때 cursor도 -1으로 업데이트한다

![image-20220812225749243](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812225749243.png)

![image-20220812225853761](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812225853761.png)

#### 04 target start index + target.length() 로 cursor 업데이트하여 -> cursor가 데이터시작점에 오도록 위치시킨다.

![image-20220812230052240](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812230052240.png)



#### 05 data의 끝을 알려주는 target인 `"`를 cursor(데이터시작) + 1부터 .indexOf( , )로 찾아, data끝 + 1위치를 찾는다.

![image-20220812230527294](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812230527294.png)



#### 06 data시작(cursor) ~ data끝+1 까지를 substring을 통해, data만 추출한다

![image-20220812230656351](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812230656351.png)

- title에 해당하는 데이터만 잘 추출되는지 확인한다.

  ![image-20220812230857316](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812230857316.png)



#### 07 data를 추출할 때까지, cursor의 움직임을 관찰하고,  다음 target도 찾아본다.

![image-20220812231645953](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812231645953.png)



- cursor  `0` -> `data시작위치`로 업데이트된다
- **다음은 직전data시작위치 -> 다음data시작위치**로 업데이트 될 것이다.



#### 08 [가변변수 cursor 초기화]와 [변수 초기화] 부분은 제외하고 [가변변수 업데이트 부분만 메서드 추출]한다

- **가변변수 업데이트로서 벌써 1개의 메서드추출이 1개의 역할**을 하고 있는 것이다.
  - 가변변수가 로직에 포함되면, **메서드 추출시, 가변변수 재할당업데이트 = 추출메서드()로 뽑히기 때문에, 그것부터 해결해야한다**

##### 가변변수는 외부에 두고 [추출한 메서드가 반환한 값]으로 [외부 가변변수 업데이트]하게 한다

##### 가변변수는 외부에 두어 파라미터로 뽑히더라도, 내부에서 다시 가변변수를 업데이트하고, 그 가변변수를 return -> 외부에서 다시 업데이트도록 뽑힌다. [어차피 외부에서 업데이트하도록 메서드 추출]되므로 [내부 메서드에서는 가변변수가 업데이트될 값만 return]하도록 수정한다.

![image-20220812232610228](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812232610228.png)

![image-20220812232755957](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812232755957.png)

- 어차피 외부에서 가변변수 재할당해서 업데이트되도록 메서드가 추출된다.
  - **내부에서는 가변변수 업데이트하여 반환안해도 된다. 그냥 업데이트할 값을 return하도록 수정하자**

![image-20220812232537689](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812232537689.png)



##### 지역변수 초기화 부분은 외부에 두고 [추출한메서드의 파라미터로 입력]되도록 [외부에서 다른 값으로 초기화해도 사용할 수 있는 메서드]로 만든다. 

- 추출하면, 해당 지역변수값만 쓸 수 있는 것처럼 나온다
  ![image-20220812232953503](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812232953503.png)



##### 변수초기화관련 메서드추출에서, [지역변수 -> 추출메서드 인자]의 과정을 [INLINE으로 바꾸는 순간, 특정 지역변수with초기화없이, 다른 값을 대입]할 수 있게 된다

![image-20220812233125844](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812233125844.png)

​	![image-20220812233143574](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812233143574.png)



##### [값이 바뀌는 변수]는 지역변수를 inline화 했지만, [가변변수는 inline하면 안된다. 계속 업데이트 되서 외부 다른 메서드들도 쓸 것이기 때문에, 초기값 + 외부 업데이트가 유지되어야한다]  -> inline해도 지역변수가 사라지지 않고 남아있음

![image-20220812233639837](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812233639837.png)

##### 즉, 내부context(불변필드제외)를 지역변수로 위에두고 추출하여 파라미터로 만들 듯, [가변변수]와 [다른 값으로 바뀔 수 있는 변수 초기화]부분은 위쪽 == 외부에 두고 메서드추출한다



##### 바뀌는 지역변수의 값 -> 인자로 inline화 되었다면, [특정 지역변수로 만들어진 파라미터]의 이름을 바꿔주자 (title target -> dataPrefix 등)

![59ec283f-6ac4-4573-9390-4d4866c7dedf](https://raw.githubusercontent.com/is3js/screenshots/main/59ec283f-6ac4-4573-9390-4d4866c7dedf.gif)

![image-20220812234426691](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812234426691.png)



#### 09 업데이트된 가변변수cursor를 이용하는, 다음로직인 substring 부분을 메서드추출해보자

##### `"`가 데이터의 끝임을 알려주므로, 변하지 않는 상수로 취급한다면, 바뀌는 변수는 [포함된 가변변수 사용]밖에 없다

![image-20220812233818138](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812233818138.png)

![image-20220812233935833](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812233935833.png)



#### 10 조심! [가변변수 = 업데이트 --이후-->  가변변수 ]사용 부분은 inline화 하면 안된다!



- 기존

  ![image-20220812234738164](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812234738164.png)

- **잘못된 예: `가변변수 업데이트 -> 가변변수 사용을 inline`**

  ![image-20220812234516183](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812234516183.png)



##### 가변변수란, [위쪽에 컨트롤타워로서 지역변수에 계속 업데이트 되는 것이 목적]이다. 업데이트 로직을 inline화 시키면, 컨트롤타워가 업데이트 안되고, 일회성 사용하는 것 밖에 안된다.

- 가변변수 업데이트 이후 사용

  ![image-20220812234937284](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812234937284.png)

- **가변변수 업데이트 로직을 inline화 하여, 업데이트된 값만 사용하고 치웠을 때**

  ![image-20220812235027611](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812235027611.png)

  



##### 가변변수는 [컨트롤 타워에 업데이트하면서, 사용할 사람들은 사용]해야한다. [가변변수를 업데이트와 동시에 인자로 INLINE화]하고 싶다면 -> 메서드 인자에서  [ (가변변수 = 업데이트로직)]자체를 수동INLINE해야한다

![image-20220812235324488](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812235324488.png)

- java는 python3.8의 업데이트후 사용 -> 코끼리연산자로  업데이트와 동시에 사용이 가능하다



#### 11 targetPrefix만 바꿔서, 다음 타겟이 잘 추출되는지 확인한다 (title -> date)

- json예시 출력을 활용해서 prefix를 만들고
- 컨트롤타워를 통해 업데이트되는 가변변수 cursor를 지속 업데이트하면서
- title대신 date도 뽑아본다.

![199d4a6a-3544-43be-8189-f3925e8e3b5a](https://raw.githubusercontent.com/is3js/screenshots/main/199d4a6a-3544-43be-8189-f3925e8e3b5a.gif)

![image-20220812235651670](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220812235651670.png)



#### 12 available까지 추출해보고, 가변변수업데이트 inline으로 바꾼 뒤, 자신의 처리가 끝났으니 커맨드홀더객체#load로 옮겨서, root Composite객체를 setter로 만들어주자



##### Test에서 만든 로직 + private메서드들을  load메서드로 옮긴다

![1362bf23-28ac-48b8-a949-91cb9a053990](https://raw.githubusercontent.com/is3js/screenshots/main/1362bf23-28ac-48b8-a949-91cb9a053990.gif)

![image-20220813002916083](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813002916083.png)

![image-20220813002927802](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813002927802.png)





##### 커맨드홀더객체 내 root Composite객체를, 재활용하기 위해, 추출한 필드정보를 setter로 채운다. String date -> LocalDateTime.parse() / String boolean -> Boolean.parseBoolean() 으로 를 한번더 씌워서 set해줘야한다

- available필드는 setter대신 toggle만 있었는데, setter를 추가해줬다.

![cc51258b-632c-44f6-ae1c-0cf214bed3a8](https://raw.githubusercontent.com/is3js/screenshots/main/cc51258b-632c-44f6-ae1c-0cf214bed3a8.gif)

![image-20220813003532107](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813003532107.png)





### 커맨드홀더 객체 내, 자식들 정보가 있는지 확인 후, add해서 붙여주기

#### composite객체의 자식생성은 [데이터가 있을 경우] -> 데이터를 추출해서 .add()로 root에 붙여주면 된다. 

- 기존 main에서 자식을 생성하는 방법

  ![image-20220813131524124](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813131524124.png)

#### 13 자식 존재유무 판단할 수 있는 곳인 [`sub: [` 를 prefix로 그 1칸 뒤까]지 이동시켜, 자식 있다면, {로 시작하도록 cursor업데이트 시키기

- 데이터는 추출안하고, 자식데이터를 뽑을 수 있는 위치까지만 cursor를 업데이트시켜놔야한다

  ![image-20220813131010418](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813131010418.png)

  ![image-20220813131103143](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813131103143.png)



#### 14 다시 한번 복잡한 로직(자식 존재유무 && 데이터 추출)이 예견되므로 Test로 현재까지 결과를 옮겨서 디버깅하며 처리하기

##### 자신의 정보 추출까지는 cursor가 업데이트 되어있으니, cursor 업데이트 로직까지 들고와서 Test에서 진행한다.

![7a89cc74-1780-4f7e-92d9-a46b1fe506ce](https://raw.githubusercontent.com/is3js/screenshots/main/7a89cc74-1780-4f7e-92d9-a46b1fe506ce.gif)

![image-20220813135031229](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813135031229.png)





##### Test참고) 커맨드홀더객체내 load메서드에서 재활용하는 객체는 command홀더객체가 아니라 소유  composite원본객체를 만든다. 하지만, Test에서는 Command홀더객체 내부 composite객체를 생성해서 테스트한다면, save나 load가 호출불가하므로 테스트의 목적인 load메서드를 호출하고, load의 뒷부분만 처리하기 위해 중간결과인 json을 받고 처리 중에 있다.



#### 14 자식정보가 남아있을 때까지 add -> while문을 사용해서 반복하며, [cursor와 같이 움직이는 배열]은 [cursor로 현재 위치를 확인]하도록 while문을 작성한다. 이때, cursor업데이트 조건은 1칸씩 이동이다(데이터 추출이 아니라 데이터 존재유무 확인부터)

- while문은 if매번확인후 action을 여러번 하는 것이며, 내부 확인변수를 업데이트도 시켜줘야한다

  - **cursor가 json의 끝에 위치하여 -> 자식정보 탐색이 끝났는지 매번확인**하면서 자식데이터를 추출한다
  - 현재는 Test파일에 작성 중.

  ![image-20220813135810385](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813135810385.png)

- while속 조건문은 매번 호출되는 확인문장이라서 **메서드호출 결과가 상수라면, 지역변수로 빼서 상수로 검사한다**

  - **조건문에 들어가는 메서드호출은 값이라서 -> 조건문 속 메서드 호출을 피하는게 좋다.**
  - 만약 내수용으로 로직정리용이라면, 그대로 둔다.

  ![image-20220813135912447](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813135912447.png)



##### 참고) command홀더객체 내, cmd객체들 외부저장소(List배열)과 cursor로 상태확인

1. redo는 undo로 인해 cursor가 원래배열의 끝보다 더 뒤에 있을 때, 한칸 앞으로 움직이므로

   - **cursor가 `배열의 맨끝`에 있지는 않은지** 검사한다

     ![image-20220813132600229](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813132600229.png)

2. undo는 실행되어 기록이 저장된cmd가 1개라도 있어야한다. 

   - **cursor가 `원소가 1개도 없을 경우인,  0보다 뒤에 있는지 확인`**한다

     ![image-20220813132731535](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813132731535.png)

3. execute후 add는, **현재 add된 것이 배열의 제일 마지막에 위치**하도록 **(add를 단순 cursor++만 해선 안됨)** 해야 **add후 redo시 에러가 발생안하게** 되는데, cursor는 undo로 인해 배열 끝보다 더 뒤에 있을 수 있으니, **add하는 순간에는 cursor이동 전에, 배열 remove로직도 들어간다**.

   - **cursor만 빽(undo)하고 redo를 위해 배열에는 cmd객체를 남겨놓는데, `cursor가 빽 한 상태에서 add한 것이 배열의 제일 끝에 위치하여, redo는 불가능`하도록 만들어놔야한다 ** 

   - **그럴려면, `현재cursor(빽한 상황)보다 더 앞에 있는 cursor +  1 ~ 마지막까지는 먼저 배열에서 삭제`해놓아야 한다**

   - 그리고 난 뒤, **요소를 배열 맨마지막에 add했으면, cursor의 업데이트는 불변식 size()-1로 업데이트**한다

     ![image-20220813133653313](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813133653313.png)



#### 15 `sub: [` 1칸 뒤에서부터 cursor를 1개씩 증가시키면서 문자열1개씩 확인하며, 자식존재여부를 판단한다.

##### 데이터 추출이전에, cursor에 있는 문자열 1개씩 json.charAt(cursor)으로 뽑아 검사하여, if 자식데이터 존재유무를 확인한다. cursor++업뎃 중에 if에 안걸린다면 건너띈다는 의미이다.

![image-20220813143125087](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813143125087.png)



##### [담에 나온 문자열이 `{`라면, 자식이 최소 1개 존재한다는 의미이다. -> 자식데이터들을 추출한 뒤, add자식 해준다.

- root 자신 데이터 추출시 사용했던 코드를 복사해와서 수정한다.

![9c39923f-947a-42dd-a610-37199a41e9ab](https://raw.githubusercontent.com/is3js/screenshots/main/9c39923f-947a-42dd-a610-37199a41e9ab.gif)

![image-20220813143330517](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813143330517.png)



- root.add( )에 들어갈 데이터들을 추출하고, **파싱까지 한 뒤 add**해준다.

![fac47099-491e-4f8e-949c-0451b1be4c1b](https://raw.githubusercontent.com/is3js/screenshots/main/fac47099-491e-4f8e-949c-0451b1be4c1b.gif)

![image-20220813143713131](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813143713131.png)



#### 현재 Available(boolean)에 대한 setter메서드는 toggle만 커맨드로 만들어줬다. 여기서 available의 setter를 추가해줘야한다.

##### 객체를 생성하면 기본 default로 정해져서 toggle만 정의하고, setter는 무시했지만, save된 jsonstring으로 load할때는, 기본값이 아닌 저장된 값으로 채워줘야한다

- 기본값을 가지고 태어나서, setter를 정의안해준 Composite객체의 available

  ![image-20220813144002483](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813144002483.png)

- command#addMenu의 파라미터 / composite#add시 파라미터에 available이 빠져있다.

  - CommandMenu
    ![image-20220813144206109](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813144206109.png)

  - CompositeMenu

    ![image-20220813144251724](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813144251724.png)



##### CompositeMenu생성자에 available을 받아서 생성되는 것까지, Test에서부터 추가해 나가야한다.

- 현재 Test

  ![image-20220813144516507](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813144516507.png)



1. command객체에 available을 포함하여 자식을 추가하는 **서비스 메서드를** 만든다.

   ![image-20220813162209782](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813162209782.png)

2. 커맨드객체는, 래핑할메서드의 **파라미터가 완전히 달라**지면 **새로운 커맨드객체를 생성**해야하지만, **파라미터가 추가되는 경우는, 기존 커맨드객체의 생성자를 추가함으로써 가능하다. 대신 기존 생성자는 기본값 처리 + 오버로딩해줘야한다**

   - 기억해야하는 context의 종류가 달라짐 -> 필드종류가 달라짐 -> 새객체
   - context가 추가해됨 -> 필드 추가 -> 생성자 추가 및 기존은 부생성자로 변경

   

3. 기존 Add 커맨드객체에 기억context추가에 따른 필드를 추가하고, 그 전에 파라미터가 없던 것은 기본값을 초기화해보자.

   ![image-20220813162442384](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813162442384.png)

   ![image-20220813162526864](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813162526864.png)

4. execute에 추가 기억한 context를 넣어서 작동시키자. 만약 안들어왔으면, 기본값 true로 초기화되었다가 들어간다.

   ![image-20220813162621142](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813162621142.png)

5. execute되는 원본 composite객체의 add도 파라미터 추가된  메서드를 추가한다.
   ![image-20220813162805494](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813162805494.png)





#### 커맨드홀더 소유객체(Composite)의 필드가 추가되었으면, Add만 수정하지 말고 ->  Remove도 다 처리해줘야한다. (필드에 대해 oldXXXX처리)

1. 현재 remove커맨드 작동시, 삭제execute 전 old필드들을 기억했다가, undo시, old필드들로 되살리는데, **available도 기억했다가 되살리기 해야한다.**

   - 기존

     ![image-20220813163239902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813163239902.png)

   - 기억해야할context를 필드로 추가

     ![image-20220813163330555](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813163330555.png)



#### command홀더객체는, add/remove/undo/redo 이외에 소유객체의 필드setter들도 커맨드객체 처리해줘야했다.



##### available은 t/f라서 toggle만 만들어줬었는데, 객체 재활용을 통한 생성을 하기 위해 setter로 만들어줘야한다.

 - 기존

   ![image-20220813163551300](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813163551300.png)



1. command홀더객체내 available setter 서비스 메서드를 만들어주고

   ![image-20220813163701356](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813163701356.png)

   

2. setter에 대한 cmd객체를 만들어준다.

   - **setter의 execute는 set전에 old값 기억후, undo가능하게 하기다**

   ![image-20220813163932799](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813163932799.png)





##### 다시 Test로 넘어와서, 자식데이터 중에 available까지 뽑아서 자식menu를 추가해준다

![image-20220813164238446](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813164238446.png)



#### 16 sub[ 담에 `{`를 발견해서 자식데이터 추출후 add했다면, `}` 닫히기 전에 `[로 자식의 자식`이 있는지 확인해야한다

##### 하지만, `{` 자식 발견하도 바로 continue로 넘어가는게 아니라 이어서 `추출+cursor업뎃+자식add`후 `자식의 자식[`이 있는지 vs ` ]가 바로 나와서 sub[가 닫히는지 `검사하는 중이다.

- 일단 자식발견해서 add하는 부분을 리팩토링하자

  ![image-20220813165356011](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813165356011.png)



##### if로 1개의 작업이 끝났다면, 빠른 continue를 해주는게 좋은데... `뒤에 공통로직인 [반복문변수 업데이트로직] cursor++`가 남아있다면, continue로 끝내면 안된다.



- **최대한 if로 어떤처리가 끝나면 `continue`로 다음 루프로 넘어가자**

  ![image-20220813170636557](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813170636557.png)

- **하지만, 반복문에서는 `continue전에 반복문변수 업데이트`를 해줘야한다.**

  ![image-20220813171145554](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813171145554.png)

##### 매번 if - continue할때마다 cursor업데이트할지 vs if만 처리하고 continue없이 공통으로 cursor업데이트할지 정해야한다

- 매번 해준다면, 코드가 반복될 것이다.

  ![image-20220813171252697](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813171252697.png)



##### 반복문내 모든 if에 대해 밑에 반복문 업데이트 같은 공통로직이 남아있다면, if에서 early continue하면 안된다

- **`모든 if에 대해 밑에 반복문 업데이트 같은 공통로직`이 남아있다면, if에서 early continue하면 안된다.**

  ![image-20220813171411961](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813171411961.png)

##### 이대로 반복문이 흘러가면, 자식 발견후, 같은level의 다음자식들은 다 `{`발견될시 데이터 추출후 add가 끝날 것이다.

![image-20220813170810889](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813170810889.png)



#### 17  `{`확인으로 자식의 데이터를 추출해 add했다면, 다음 해야할 일은, 자식의 `[`가 열리고 `{`가 발견되어 또다른 depth(자식의 자식)이 열리는지 확인해야한다. 그리고 이것은  `{`가 발견되어서 `최소 1개의 자식이 있고, 거기서 sub: [`가 열리는지 확인하는 것이다.

##### 반복문 속에서 특정if에 걸린 것을 확인하는 방법은 가변변수를 통해 결과물or불린Flag을 챙겨놓는 방법 밖이다.

- if에 걸려서 결과물이 있다면, 그것을 형으로 하는 가변변수 = None초기화로 챙겨놓으면 된다.
  - 만약 결과물이 없는 void로직이라면, bolean가변변수 = false;초기화의 Flag변수를 챙겨야한다



##### 원래 load는 composite객체의 add로 자식을 넣지만, 테스트에서는 포장된 command홀더객체를 사용하고, 커맨드홀더객체들의 메서드들은 결과물이 없는 void라서 지연실행한다고 했다. -> 지금은 boolean으로 플래그를 챙기고. 내부에서는 add결과물로 if걸린 것을 확인하도록 바꾸자.

![3de730bd-f4bd-4250-ac1a-ce5297a6beb0](https://raw.githubusercontent.com/is3js/screenshots/main/3de730bd-f4bd-4250-ac1a-ce5297a6beb0.gif)

![image-20220813174409922](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813174409922.png)





#### 18 자신의 처리후 -> 반복문으로 데이터뽑아 자식들을 채우다가, [자식의 자식]을 발견한 순간부터는 재귀를 돌려야한다

##### 자신에서 [자식들의 처리]는  [반복문 + 자식 1개처리]로 여러자식들을 처리하도록 정의된다. 이 때, [반복문을 포함한 자식들처리 전체] 부분을 재귀함수로 추출해야, 내부에서 자식의 자식이 등장할 때, 똑같이 [반복문 + 자식1개로 처리 + 자식의 자식들 재귀로 처리] 처리를 할 수 있다.

![image-20220813175744358](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813175744358.png)



##### 재귀함수에서는 [반복문내 자식1개 객체]-> [재귀에서 자식의 자식으로 업데이트]되어서 호출되어야하므로 [재귀함수 파라미터에는 반복문내 자식1개 객체가 포함된 후 -> 자식의 자식으로 업뎃하여 호출]되어야 한다.

- 위에서 값이 초기화되어 필요한 변수인 json이나, 가변변수들은 알아서 파라미터로 뽑힌다.

- **재귀함수를 정의할 때 고려해야할 것은**

  1. 다음stack결정변수 

     1. 값이라면: cnt(0)-> cnt+1, n->n-1

        - 종착역 if cnt == 2, n == 1 등

     2. **연결된 객체라면:** 

        1. 객체 -> `객체 = 객체.next`, 
        2. **객체 -> `반복문속 자식객체`,** 
        3. 외부에서 사용한다면 `객체.재귀메서드() -> 다음객체로 업뎃 -> 다음객체.재귀메서드()`

        - 종착역 
          - if  객체 == 시작특이점객체
          - **아예 자식객체가 없어서 반복문 x -> 다음재귀 호출x**
            - **반복문속의 자식객체.재귀호출()는 종착역이 따로 필요없다**

  2. 필요정보 변수(전역변수면 노상관이지만 객체에선 x)

  3. 계속 업데이트되는 가변변수

  4. 연산식을 직접 적어서 업데이트하는 누적결과값





#### 19 직접 composite객체의 자식객체들 생성을 위한 재귀로 만들어보기

##### root의 자신처리는 재귀에서 제외한다.  [자식들부터만, 자신의 처리가 반복문 + 자신처리1개의 여러번 반복되기 때문]이다. 

##### 01 root의 자식들처리를 위한 반복문부터 재귀메서드로서 메서드 추출한다.

![da0966c2-7f16-41e9-b52f-9f18f5f2b52c](https://raw.githubusercontent.com/is3js/screenshots/main/da0966c2-7f16-41e9-b52f-9f18f5f2b52c.gif)

![image-20220813182545351](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813182545351.png)





##### 02 객체의 반복문처리 재귀는 [반복문내 자식객체1개 -> 자식의 자식 등장 -> 자식의 자식객체 1개로 업데이트된 재귀 호출]을 하면 된다.  그러기 위해서는, [파라미터로 자식객체 1개의 형]이 올라와야하며 [인자는 child -> 파라미터는 parent로 명명]해준다.

![06624dc6-9225-4a79-9419-b2eeecc74e7f](https://raw.githubusercontent.com/is3js/screenshots/main/06624dc6-9225-4a79-9419-b2eeecc74e7f.gif)

![image-20220813183206801](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813183206801.png)





##### 03 문제상황1) 객체반복문 재귀메서드의 stack결정 파라미터 초기값이 반복문내 자식1개 객체가 아니라, [root 자신]이 들어간다. -> "아~! 재귀에서 인자는 필요한 현재상태 값(root)일 뿐이고, 실제  내부 작동하는 것은,그 상태값으로 만들어진 다음 자식객체(child) 내부에서 만들어지거나 getter로 불려져서, 그놈이 다음재귀에 상태값으로 들어가는 구나~!"

![image-20220813183958568](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813183958568.png)



##### 04 문제상황2) root가 들어갔으면, [반복문내 자식객체1개]가 만들어져서 변수로 활용되어야, 다음 재귀의 root대신 들어가서 자신이 root역할을 한다?!

- 원래 커맨드홀더객체#load내에서는, 소유한 composite객체로 처리하며, add결과 **add된 자식객체 1개가 추출된다**

  - **하지만 지금은 cmd객체를 사용하고 있어서, void로 add결과 아무것도 안나온다**
  - **억지로 맨 마지막것을 뱉어내도록 getter를 만들어줘야하나??**
  - 기존의 자식들을 불러내주는 getter가 있으므로 이것의 제일 마지막 것을 대신 사용하자

  ![f331f88b-f453-48b5-8167-9c0b21f7610b](https://raw.githubusercontent.com/is3js/screenshots/main/f331f88b-f453-48b5-8167-9c0b21f7610b.gif)

  

- **add후 추가된 child 객체 1개를 뽑아냈다면, flag 가변변수를 대체하고, 사용하자**

  ![image-20220813184838503](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813184838503.png)







##### 05 이제 자식의 자식이 발견된 시점에서, 자식의 자식을 -> 자식처럼 처리하기 위해, 재귀메서드를 [parent -> child로 바꿔]호출해준다.

- 자식을 억지로 가지고이 위해 child가 composite객체로 가져와서 재귀에 넣었더니, **재귀메서드 자체가 최초인자가 command홀더객체라서 -> composite객체로 바꾸도록 변경하고, cmd홀더객체에 getter를 써서 넣어주었다.**

  ![4493f315-361e-4769-970a-7a2792a2a994](https://raw.githubusercontent.com/is3js/screenshots/main/4493f315-361e-4769-970a-7a2792a2a994.gif)

  ![image-20220813185503954](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813185503954.png)



- 이렇게 된다면, **cmd로부터 억지로 child객체를 빼올 필요없이 composite객체.add의 결과로 나온 child를 챙겨주자**

  ![image-20220813185635818](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813185635818.png)





##### 06 일반적인 반복문 객체재귀의 [for 자식객체들 -> 다음재귀]와 다르게 [종착역: 자식들 소유안해서 for가 안돌고 끝나는 곳이 종착역]이 아니다.   -> `반복문이 json과 cursor로 돌아가고 있으므로, 해당depth(stack)의 처리가 끝나는 곳을 직접 알려줘야한다 -> ']'를 만나는 순간 해당 stack을 종료`해야한다.



![25517375-9420-4c37-89bf-d36eaf8e1c2a](https://raw.githubusercontent.com/is3js/screenshots/main/25517375-9420-4c37-89bf-d36eaf8e1c2a.gif)

![image-20220813190713631](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813190713631.png)







##### 07 정해진 소스(json)을 돌며, 모든 재귀stack에서 cursor를 업데이트하고 공유해야한다면, 해당 stack이 끝나더라도, 외부에서 초기화된 가변변수 cursor를 반환해줘야, 다음 또다른 stack이 쓸 수 있다. -> 자식객체 for문이 아니라, [외부 컨트롤타워를 두고 초기화된 가변변수 cursor를 쓰는 재귀문]이라면, [stack끝나고 빽할 때마다 업데이트된 cursor를 종착역에서 반환]해줘야한다. (`종착역반환 -> 자식재귀 반환 -> 재귀정의부 반환 -> 외부에 반환`)





![f60806aa-be49-4a99-a7c4-09f1358d46a7](https://raw.githubusercontent.com/is3js/screenshots/main/f60806aa-be49-4a99-a7c4-09f1358d46a7.gif)

![image-20220813191422827](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813191422827.png)

![image-20220813191531002](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813191531002.png)



##### 08 재귀로 더해진 자식들을 확인하기 위해 cmd.getMenu()객체를 지역변수로 빼고 -> composite객체를 출력하라면 rendere에 consolevisitor를 주입하고, 메서드인자 compoiste-> report로 만들어서 넣어줘야한다

![a754e036-8c93-4644-bc98-2ab66426e50f](https://raw.githubusercontent.com/is3js/screenshots/main/a754e036-8c93-4644-bc98-2ab66426e50f.gif)

![image-20220813192334937](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813192334937.png)





![image-20220813192407412](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813192407412.png)



##### 09 이제 command홀더객체# load 내부로 옮긴 뒤, 재활용이 완성된 소유Composite객체를 반환하도록 load메서드의 시그니쳐를 바꾼다.



![6be89aea-8b70-4b72-b7a1-d4000ee1249d](https://raw.githubusercontent.com/is3js/screenshots/main/6be89aea-8b70-4b72-b7a1-d4000ee1249d.gif)

![image-20220813193011387](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813193011387.png)





##### 10 test에서는 load의 반환이 string json -> 재활용완성된 composite객체를 받도록 수정하고 다시 한번 출력해보자.

![a8b43b66-c8b6-456e-8f7e-12c65918c84b](https://raw.githubusercontent.com/is3js/screenshots/main/a8b43b66-c8b6-456e-8f7e-12c65918c84b.gif)

![image-20220813193248817](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813193248817.png)
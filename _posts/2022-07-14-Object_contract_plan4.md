---
toc: true
layout: post
title: Object) 정책적용관련 객체들의 계약관계(Plan4)
description: 정책적용과 관련된 객체들의 계약관계와 전파

categories: [java, 우테코, oop, object, contract, plan, precondition, postcondition, invariant, package ]
image: "images/posts/java.png"
---

### 계약

#### 계약조건 4가지

1. **precondition**: 전달받은 메세지(인자, 파라미터)의 검증
   1. validation이라고 한다.
    2. 인자들의 null검증 + 인자들의 상태(도메인)검증
        1. if로 무시 with 불린flag 토해내기(my early return false;로 null검증)
        2. ifThrow로 멈추기 with 메시지
   3. 내가 만든 형 객체라면, 형에게 시켜 스스로 검증해서 죽도록하여, 묻지않아 변화의여파가 없어진다.
2. **postcondition**: 전달할 메세지(결과값)의 검증
   1. 반환하기 전, 결과값을 검증하는 것은 도메인검증이다.
3. **class invariant**: 메세지 처리전, 클래스의 상태값(필드)의 불변성(or 필수조건 확보) 검증
   1. 필드초기화 하지 않고 & setter로 런타임에 받아서 초기화된다면, 그전에 null객체를 넣어 초기화 해주기
   2. 생성자type으로 초기화한다면, 생성자내에서 null검사하기
4. **위임된 책임의 context**: 내가 계약조건을 검증하는지 확인
   1. precondition위임: **발동은 받은 내가 해준다.**
   2. postcondition위임: **나한테는 코드가 안보인다.**





#### 계약조건 적용하기

- [시작커밋](https://github.com/is2js/object2/tree/dd9a367c03ad8f38cb207354500420e7c04b40e5/src/main/java/goodComposition)

- **Plan - Calculator(전략주입 구상클래스) - Calc(전략인터페이스) - 전략객체들**
  - **Plan과 1차 계약자 Calculator의 pre/post/invariant를 체크한다.**
  - **이후 계약의 전파를 생각한다.**



##### Invariant#Plan#setter로 초기화된다면, 그 전에 null객체로 미리 초기화해놓기

1. setter로 주입받는 **Calculator필드**는 **런타임 때 받기기능을 부르기 전에, calculateFee()를 호출한다면, NullPoint예외가 발생할 것이다.**

   ![image-20220713162535510](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713162535510.png)

   - calculateFee()에 **throws도 안달려있어서 NP예외를 예상할 수도 없다.**

2. 이럴 경우, Calculator필드를 **new Calculator()의 빈객체를 `NULL객체`로서**를 임시로 넣어두면 되는데

   1. Calculator자체가 **생성자를 요구하는데 안에 null**을 넣어두면 된다?

      1. **Calculator 자체의 생성자 검증(invariant) 작성시시 걸린다**

   2. **Calculator를 살펴보니, `자기가 일하는 것이 아니`라 컬렉션필드를 돌면서 일처리를 하고 있으니, `NULL객체용 아무것도 안받는 생성자를 추가`해주고, `작동해도 빈 컬렉션을 for문으로 돌면 아무일도 안하고 그대로 반환`해주니 괜찮을 것 같다.** 

      ![image-20220713160759941](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713160759941.png)

   ![d5903c77-880f-467c-8770-4843b64a3d07](https://raw.githubusercontent.com/is3js/screenshots/main/d5903c77-880f-467c-8770-4843b64a3d07.gif)





##### Precondition#Plan#파라미터로 넘어온 인자의 null검증 by thr

- 기본적으로 `외부`(package내 계약된 깨끗한 인자가 아닌)에서 넘어노는 인자는 만능키 null의 가능성이 있다.

  1. **Null일때는 if에 안걸리고 빠져나가면서 return false를 반환**하도록 **최소한 불린flagf를 반환하도록**짠다.

     - **null일때는 로직자체가 안걸리게 무시하고, != null일때만 로직을 통과하게 하던지 `null일때 early return false;로 무시`해도 된다.**

       ![image-20220713163047953](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713163047953.png)

     ![44cdde08-ec9d-49b5-8e42-86093550f40f](https://raw.githubusercontent.com/is3js/screenshots/main/44cdde08-ec9d-49b5-8e42-86093550f40f.gif)

  2. **불린flag반환없이 `if Throw로 멈춰서 죽`도록 null검증을 해도된다.**

     - if로무시하며 불린반환하는 것보다 더 강력하다.

     ![fa27b172-ac1b-4392-93be-27c04cf9d708](https://raw.githubusercontent.com/is3js/screenshots/main/fa27b172-ac1b-4392-93be-27c04cf9d708.gif)

     ![image-20220713163650091](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713163650091.png)





##### Precondition#Plan#파라미터로 넘어온 인자의 상태(NULL객체, 도메인)검증 by thr

- calculator는 **내부에 전략객체가 없는 것이 넘어오면 사실상 null의 객체다**

  - 도메인으로서 의미가 있을라면, **해당 도메인이 `NULL객체와 같은 상태가 아닌 의미있는 상태를 가짐을 검증`해야한다.**

    - **실제로 `해당객체의 필드들(상태값)들을 살펴보고` 의미있는 상태임을 코드로 적는다.**
    - Calculator는 내부에 가진 calcs(전략객체들)의 갯수가 1개 이상인 상태여야 한다.

    ![063d4321-8417-4e78-b887-ec91ecafa6ff](https://raw.githubusercontent.com/is3js/screenshots/main/063d4321-8417-4e78-b887-ec91ecafa6ff.gif)

  ![image-20220713170411232](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713170411232.png)

- **이렇게, `precondition으로서 날아가는 메세지 객체`는 `자신에 대한 validation의 책임을 외부에 서비스로 제공`해야하므로 `자신의 상태에 대한 validation책임(메서드)이 늘어난다`**
  - **`client에서 보이지 않지만` 메세지로 들어가는 객체들은, precondition검증으로 쓰이는 내부 메소드가 많다.**





#####  postcondition#plan#return해줄 결과값에 대한 도메인 검증(재료가 있을 땐, 결과값이 NULL객체가 아니어야한다)

![image-20220713170908634](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713170908634.png)

- 객체는 **직접 자기의 로직을 수행하기보다는, `메세지를 통해 적합한 객체에게 책임을 위임해서 시킨 뒤, 값을 받아`오는 경우가 많다. `위임객체에 받아온 결과값도 반환하기 전에 도메인 검증`해야만한다.**

  - **검증하기 위해서는 `지역변수로 받아서 확인`한다**
  - **계산값은 `calls가 1개이상인 경우`에만 && `0원이 되어선 안된다.`**
    - calls가 0개인 경우는 0원이 나와도 상관없다.
  - **`재료가 1개이상일 때는, NULL객체가 아닌 결과값`가 나와야한다.**

  ![01dd6af0-6c66-4b92-b40a-4dbbe7341ec2](https://raw.githubusercontent.com/is3js/screenshots/main/01dd6af0-6c66-4b92-b40a-4dbbe7341ec2.gif)

  ![image-20220713171834436](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713171834436.png)





##### precondition위임#Plan#인자 객체의 상태검증에, this context가 사용되지 않으면 묻지말고 시켜서 스스로 죽도록 코드를 옮기고 + 변화의여파를 없앤다.

- precondition의 null검증은 메세지 객체에게 위임할 수 없다. 
  - 객체 대신 null이 들어오므로, 아예 다른 범주다.
- **만약, 물어보고 여기서 죽인다? -> `해당 객체가 변할시, 기존 물어보는 것 자체가 의미가 없어진다.`**
  - 시켜서, 알아서 물어보고 알아서 죽으라고 한다.
  - **my) `객체 자신의 상태검증은, 물어보고 여기서 죽일 이유가 없으니 스스로 죽게 만들면, 변화의 여파가 안오며, 내가 만든형으로서 검증코드가 그쪽으로 이동되는 효과가 있다`**

- 해당객체에게 시킬 때는
  - 내수용 메서드로 추출 -> 해당 객체로 이동

![70c99a6b-82cd-4582-85a7-e1aa6c2d1067](https://raw.githubusercontent.com/is3js/screenshots/main/70c99a6b-82cd-4582-85a7-e1aa6c2d1067.gif)

![image-20220713181026214](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713181026214.png)





##### postcondition위임#Plan#결과값검증 과정에서 this context가 사용되지 않으면, 결과값 검증로직도 결과값 계산로직 내부로 위임한다.

- this(현재class의 필드들)가 없으면, 나의 코드, 나의 메서드가 아니다.



1. 결과값 검증 로직에 this가 사용되는지 확인한다. 

   1. **만약, plan의 필드들이 사용되었다면, postcondition은 그대로 두어야 하고, `처리전에 상태값들의 invariant`부터 검증한다**

   2. **만약, plan의필드들(this)가 사용안되었다면, 결과값 계산로직 속으로 위임한다.**

   3. **`필드가 사용되었어도 계산위임된 메소드의 인자로 넘어갔다면, this로 안보고 결과값 검증 로직도 위임할 수 있다.`**

      ![image-20220713183415777](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713183415777.png)

   ![12ae6fb1-89de-4c61-8c37-b82b1b11407d](https://raw.githubusercontent.com/is3js/screenshots/main/12ae6fb1-89de-4c61-8c37-b82b1b11407d.gif)

   ![image-20220713184028517](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713184028517.png)
   ![image-20220713184125893](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713184125893.png)

   ![image-20220713184047325](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713184047325.png)





2. **postcondition 검증이 안보인다면, 검증에 this가 사용안되서, 결과값계산로직 내부로 결과값 검증 로직이 위임됬음을 생각하자**

   - **precondition에서 인자 상태검증은 this가 사용안되더라도, 명시적으로 호출은 해야해서 보인다.**
     - cf) 만약 안보인다면, 메서드 호출하는 인자에서 이미 검증된 인자를 보냈을 수도 있다.

   ![image-20220712175645352](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220712175645352.png)

   - **pre/postcondition의 책임은 작성하고 나면, `대부분 위임받은 객체들이 가져가게 된다. 그 pre/postcondition검증 코드가 안보인다면, 이미 계약관계라고 생각`할 수 있다.**





3. **precondition은 위임되더라도 받은쪽이 발동은 시켜줘야하고, postcondition은 위임한 객체 내부에서 처리되어 안보인다.**
   1. postcondition은 return해주는 쪽에서 다 처리해줘
   2. preconditiond은 받은 쪽에서 검증해줘





##### precondition위임#Calculator#외부Client가 아닌 계약자가 넘겨준 precondition의 상태검증은 애초에 계약자가, 삼항연산자 등을 통해  검증된 precondition을 넘겨주도록 계약조건을 명시할 수 있다.

- Calculator는 계약자 Plan가 넘겨준 메세지의 상태검증을 직접하지말고, Plan에서 미리 검증된 인자만 넘겨주도록 할 수 있다.
  ![image-20220713235815539](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713235815539.png)

  - **메세지 calls의 상태검증을 precondition으로 여기서 하지말고, 계약자가 건네주기전에 미리 검증해서 줄 수 있다.**

- **메세지가 1개이상만 넘어가도록, 외부가 아닌 계약자는 `1개이상의 메세지`만 넘어가도록 `미리 검증하고 넘겨주는 계약내용을 추가`할 수 있다.**

  ![9b5499c9-29c9-4414-8ee0-7c6a02d0391f](https://raw.githubusercontent.com/is3js/screenshots/main/9b5499c9-29c9-4414-8ee0-7c6a02d0391f.gif)

  ![image-20220714000212797](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714000212797.png)



- 삼항연산자도 ealry return형식으로 바꿔준다.

  - **1건이상이면 진행 -> 0건이면 NULL결과값 반환**

  ![338943bc-0006-4e9d-94cc-9af78e24f385](https://raw.githubusercontent.com/is3js/screenshots/main/338943bc-0006-4e9d-94cc-9af78e24f385.gif)

  ![image-20220714000524157](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714000524157.png)





- **결과값 반환로직을 보고 `계약조건을 확인`할 수 있다.**

  - calls가 0건이면 계약안함 -> **1건이상일 때만 계약진행**

- **메소드 위임으로 진행되는 계약은, 계약서의 제목일 뿐이다. 구체적인 계약조건들은 코드로 나타나있다.**

  ![image-20220712190149813](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220712190149813.png)





##### precondition위임->Invariant#Plan->Calculator#setter를 가져  runtime시 상태변화하는 책임위임객체(전략주입 구상클래스)는 생성(주입)시점  precondition 상태검증가 의미없어 ->  해당객체의 계약된(위임된)책임메서드 호출시마다 invariant으로서 자신의 상태를 매번 검증하도록 위임하여 보장해야한다.

![image-20220714002654300](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714002654300.png)





1. Plan에 주입되는 Calculator 객체는, **setter로 상태변화가 가능한 놈**이기 때문에, invariant보장이 안된다 -> **precondition 상태검증은 의미없다**
   - **받은 해당 객체의 책임(계약)메소드 호출시마다 자신의 상태를 검증하는 invariant로 위임해서 처리한다.**

![a21577e5-0164-4f3a-8588-2be678bd4571](https://raw.githubusercontent.com/is3js/screenshots/main/a21577e5-0164-4f3a-8588-2be678bd4571.gif)

![image-20220714003458677](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714003458677.png)

![image-20220714003135958](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714003135958.png)







2. **compile타임에 invariant가 확정되려면, 생성자type으로 초기화해야만한다**
   - 만약, **setter type으로 상태변화가 런타임으로 미뤄진다면, 호출시마다 매번 invariant 검증을 해야한다.**
   - **life cycle처럼 순서를 가지도록 짜더라도, 중간에 메서드 호출을 빠트리는 것은 컴파일러가 확정시켜줄 수 없다. 런타임으로 미뤄진다면, 101번째 에러가 발생한다. 모든 경우의 수를 알아야하며, 매번 검사해야한다.**





##### precondition#Calculator#계약자가 넘겨주는 깨끗한 인자가 아니라 외부에서 들어오는 인자라면 precondition검증(null 및 가진 상태값)을 해야한다.

![2a28fe4d-803e-48f3-9df6-0d0ff6a72899](https://raw.githubusercontent.com/is3js/screenshots/main/2a28fe4d-803e-48f3-9df6-0d0ff6a72899.gif)

![image-20220714004435561](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714004435561.png)





- 만약, 계약자가 넘겨준 인자라면

  1. **메세지 처리전, 나의 invariant를 검증한다. setter를 가져 상태가 변한다면, 상태검증을 매번해줘야한다.**

  2. 계약자가 이미 검증된 인자를 넘겨주는지 확인한다.

  ![image-20220714004621589](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714004621589.png)





##### 계약의전파precondition#Calc(전략객체들)#계약전파된 메서드는 precondition 검증이 없다. 

- Plan(계약자) - Calculator(계약메서드) - Calc(인터페이스) - Calc구상체들(**계약전파 메서드**)

  - **계약메서드 이미 검증된 메세지들을 받은 상태이며, 그것을 그대로 받았는지 확인한다.**

  ![image-20220713101625017](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713101625017.png)

  - calls는 1건이상의 좋은 인자 + result는 초기값으로 좋은 인자가 들어온다
    - **하지만, Plan(계약자)와 Calculator(계약메서드)사이에만 최초인자가 계약된 깨긋한 인자로 들어감이 보장된다.**

- **`계약메서드, 계약전파 메서드들은 public으로 유지되는 한`, 계약자들 이외에 객체들이 호출할 수 있기 때문에, `좋은인자라는 보장이 없다`.**

  - **계약자들만 호출할 수 있도록 하려면 `생성자는 public으로 외부에서 생성되도록 하되, 계약메서드들은 한 package내 internal`로 처리해야한다.**
  - **plan은 public으로서 외부에서 호출되어야한다.**

  

  

  

##### 계약의전파precondition#package#계약된 좋은 인자 보장을 위해, 위임된 계약객체들의 계약된 책임메서드부터는 internal가시성을 이용하여 계약자에게 독점공급한다.  만약 public default의 인터페이스면 추상클래스로 변경한다.

![image-20220713105833512](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713105833512.png)

![image-20220713121342208](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713121342208.png)





1. 최초 계약자 Plan을 특정package `plan`을 만들어서 이동시킨다.

   - 패키지는 계약관계에 의해 이루어진다.

   ![image-20220714103033484](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714103033484.png)



2. **위임된 계약객체들**도 **특정package이동** 후 메서드는 **접근제한자를 지워 internal 가시성을 통해, Plan에 메서드를 독점 공급한다.**

   - 패키지내 부를 수 있지만, Plan만 해당 메서드를 호출하는 것은 개발자가 통제한다.

   ![image-20220714103219907](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714103219907.png)

   ![20eac00e-f19a-410a-b0e6-f230ba59bf57](https://raw.githubusercontent.com/is3js/screenshots/main/20eac00e-f19a-410a-b0e6-f230ba59bf57.gif)

   ![image-20220714103423965](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714103423965.png)

   - 접근제한자가 없는 **internal가시성을 보면 -> `package내 호출하는 쪽에 독점공급`하겠구나 생각한다.**





3. **다음 계약전파메서드를 보면, `인터페이스로서 가시성누수 문제로 인해 public으로 만들어야하는 제한`이 있으므로 `internal가시성 적용`을 위해  `전략패턴을 템플릿메소드패턴으로 변경`한 뒤, `public 템플릿메소드의 접근제한자를 삭제하여 internal가시성으로 변경`한다**

   - [참고 내 블로그 글(전략 to 템플릿메소드패턴)](https://blog.chojaeseong.com/java/%EC%9A%B0%ED%85%8C%EC%BD%94/oop/object/strategy/templatemethod/convert/2022/07/12/Object_strategy_templatemethod_convert_text.html)

     1. 추상클래스로 변경 및 전략메서드 삭제![4732b904-4b4c-41b1-8a99-a5934fcef736](https://raw.githubusercontent.com/is3js/screenshots/main/4732b904-4b4c-41b1-8a99-a5934fcef736.gif)![image-20220714104257895](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714104257895.png)

     2. 1개 구상체에 대해 추상클래스 상속 후, 개별구현 로직을 private내수용 메서드로 추출하여, 훅메서드를 만들어 올린다. 올린 메서드의 접근제한자를 final or protected abstract로 수정한다.

        - **개별로직만 존재하더라도, 템플릿메서드만 올리지말고, `해당로직을 다시 한번 메서드추출`하여 `공통로직 내부 개별로직`형태로 만들어야, 템플릿메소드패턴이 된다.**

        - 만약, 공통필드가 있다면, 생성자가 아닌 setter를 만든 후 setter를 올리면서 필드를 올린다.

          ![8e8deb5f-f1e0-4b07-83a6-330f75fdf0cb](https://raw.githubusercontent.com/is3js/screenshots/main/8e8deb5f-f1e0-4b07-83a6-330f75fdf0cb.gif)
          ![image-20220714104733634](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714104733634.png)

        ![image-20220714104532056](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714104532056.png)

     3. 구상체들이 올라간 템플릿메소드 + 훅메서드를 구현하면서, 전략메서드를 내용만 복붙후 삭제한다.

        - 훅메서드 추출시, 특정 구상체에서 템플릿메서드로 넘어온 인자들이 사용안됬다면, **`ctrl + F6`을 통한 시그니쳐 변경**이 필요할 수 있다

        ![82263936-e878-4d12-b940-492a7ac45b77](https://raw.githubusercontent.com/is3js/screenshots/main/82263936-e878-4d12-b940-492a7ac45b77.gif)

   - **이제 추상클래스의 템플릿메소드를 public이 아닌 internal로 변경하여, `추상클래스도 전파된 책임메서드가 독점공급`되도록 한다.**
     ![878264c4-0848-4f56-a0aa-059e6e44d228](https://raw.githubusercontent.com/is3js/screenshots/main/878264c4-0848-4f56-a0aa-059e6e44d228.gif)

     ![image-20220714105405237](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714105405237.png)





##### 계약의전파precondition#package#계약의전파로 인해 같은 병행package를 유지하여 internal독점공급하는 것은 부모와 protected관계인 구상체들에게는 해당사항이 없다. 구상체들만 따로 내부package를 파서 넣어준다.

![image-20220713121307458](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713121307458.png)

![image-20220714105737547](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714105737547.png)

![bbe7d9d7-0c95-4724-af29-789ec6e47dfe](https://raw.githubusercontent.com/is3js/screenshots/main/bbe7d9d7-0c95-4724-af29-789ec6e47dfe.gif)



##### 계약의전파postcondition#훅구상체들#postcondition검사는, 계약전파된 pre의 더 상위레이어 계약체결로 인한 생략과 달리, 매번 검증해야한다.



![image-20220713121538506](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220713121538506.png)

- precondition은 계약전파시, internal가시성을 통해 계약된 메세지들만 받아서 생략이 가능했다.
- **하지만 postcondition(반환될 결과값 검증)은 매번 해야한다.**





##### 계약의전파postcondition#구상체들#계약전파 메서드의 postcondition을 검사하기 전에 결과값 계산시 사용된 상태값의 invariant를 먼저 검증하자.

- **결과값 검증하기 전에, `내 필드값들이 눈에 밟혀서 invariant검증이 안된 것이 눈에 밟혀야한다.`**

![image-20220714114601626](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714114601626.png)

- 계약전파 메서드들의 precondition은 가기성확보로 좋은인자가 확보되어어 생략했지만, **postcondition은 검사해야하는데, 그 전에 계산에 사용된 보라색 상태값들의 invariant부터 NULL/상태 검증하자.** 

  - price는 Money형으로서 NULL검사 외 상태검증(0원이하면 의미 없음)을 검증해야한다.

    ![be1225e8-2cd6-47a1-9f46-bddcda236ce4](https://raw.githubusercontent.com/is3js/screenshots/main/be1225e8-2cd6-47a1-9f46-bddcda236ce4.gif)

    ![image-20220714115057917](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714115057917.png)

    - **내가 만든형이므로 스스로 죽도록 묻지않고 상태검증을 시킬 수 있다.**

  - second는 Duration형으로서 NULL검사 외 상태검증(0초이하면 의미없음)을 해야한다.

    ![de5f1080-80bf-4e46-b6ff-49573d923c87](https://raw.githubusercontent.com/is3js/screenshots/main/de5f1080-80bf-4e46-b6ff-49573d923c87.gif)

    - **외부객체이므로 새로운 wrapper형을 만들 것이 아니라면, 물어서 검증해야한다.**

  ![image-20220714115646264](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220714115646264.png)




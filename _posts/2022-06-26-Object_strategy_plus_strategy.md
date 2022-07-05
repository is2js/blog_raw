---
toc: true
layout: post
title: (Object) [F2]Trigger Strategy to Factory
description: 조건에 대한 전략객체를, 1번째 전략객체 담당 Factory에게 위임하기

categories: [java, 우테코, oop, object, strategy, factory]
image: "images/posts/java.png"
---

##### Trigger전략객체2의 로직을 위임된(주입되는) factory에게 위임하기

- 현재 DiscountPolicy in [step5 시작](https://github.com/is2js/OOP-Theater/commit/4b457099efb10abab9bdd2066ae686fe82d72ce9)는 **2개의 전략객체를 받기(condition, trigger) + 주입(policy, action)받아** 사용중

  - 전략객체 Calculator를 구상Factory로 받아서 사용하며
  - trigger전략객체 DiscountCondition은 외부에서 setter로 받고 -> 내부에서 호출해서 사용한다.

  ![image-20220626143937079](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626143937079.png)

  

  ![image-20220626143947931](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626143947931.png)



- **trigger기능을 가진 condition전략객체를 `위임된 Factory`에서 `받기+제공 책임을 위임`해보자.** 



1. **`사실상의 구상체`**들인 **구상Factory들**을 다시 strategy패키지로 이동했다.

   - **인터페이스는 전략인터페이스**이나 **전략객체 대신 구상Factory들이 주입**됬었다.

   ![image-20220626172929631](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626172929631.png)

2. **DiscountPolicy내에서 `필드에 받아진 trigger전략객체들` -> for문을 돌면서 trigger전략메서드를 사용하는 상태이므로 `2번째 전략객체들 묶음을 getter로 제공`하는 기능을 factory에 추가해야한다.**

   - 받을 2번재 전략객체가 1개면, factory에서 생성 -> 전략메서드까지 호출하는 메서드를 만들면 되는데, **현재는 `사용처에 전략객체들 묶음을 먼저 제공`해야 `이후 전략메서드를 사용`할 수 있는 상태기 때문에**

     - **factory가 내수용 받기메서드로 받아놓을 `전략객체 묶음을 제공하는 기능`을 추상메서드로 가져햐아한다.**
     - 기존: discountpolicy내에서 **필드로 받아서 `필드(보라색)`으로 사용**
       ![image-20220626173636593](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626173636593.png)
     - 수정: **factory가 (내부에서 받아서) getter메서드로 제공**
       ![image-20220626173701531](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626173701531.png)

   - **`내부에서 보라색필드로 꺼내서 사용`하는 로직을 옮긴다?**

     1. 로직이 옮겨간 곳에서는 `getter`로 제공해야한다.

     2. 로직이 옮겨간 곳에서는 **필드의 변수형에 맞는 컬렉션으로 응답해줘야한다.**

        ![image-20220626174912746](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626174912746.png)

   - **Factory변수에서 생성해주면, 추상체인 Calculator에 추상메서드로 올라간다.**

     - 1개 전략 인터페이스에 2개의 메서드가 정의된 상태로, **중간추상층의 추가가 요구** 된다.
       ![image-20220626175226432](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626175226432.png)

     ![d5e65010-eead-4dce-a4f5-c223239be4e0](https://raw.githubusercontent.com/is3js/screenshots/main/d5e65010-eead-4dce-a4f5-c223239be4e0.gif)



3. **전략 인터페이스에  1개의 메서드만 갖으며 & `최종구상체인 구상Factory들은 2가지 기능`을 가지도록 `중간추상층(2번째 전략객체의 인터페이스)`를 추가한다**

   - **인터페이스를 `extends`하여 중간추상층 인터페이스를 만든다.**
     ![image-20220626175813111](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626175813111.png)

   - 현재는 네이밍이 Calculator(전략객체) - AmountCalculatorFactory(구상Factory) 상태인데
     - **중간 추상층은 trigger관련이므로 action + trigger의 의미를 다 담는 `policy+Factory`로 작성하고 `1개 추상메서드를 위임`한다.**

   ![a986b586-c130-499b-b964-eed8f0e31a2e](https://raw.githubusercontent.com/is3js/screenshots/main/a986b586-c130-499b-b964-eed8f0e31a2e.gif)



4. **최종구상층인 구상Factory들은 `상위추상층`이 아니라 `중간추상층을 imple`하여 `2개의 추상메서드를 구상`하도록 상태를 만든다.**

   ![f774886f-24cb-4469-b3a4-1d30ccd2fc4c](https://raw.githubusercontent.com/is3js/screenshots/main/f774886f-24cb-4469-b3a4-1d30ccd2fc4c.gif)

5. **전략객체를 사용하는 DiscountPolicy에서는 `2개를 구상하는 최종구상Factory를 포용할 수 있는 중간추상체`인 `PolicyFactory`를 추상체 변수로 사용하도록 변경한다.**

   ![c6bb6229-482d-475c-b250-507ffacc2857](https://raw.githubusercontent.com/is3js/screenshots/main/c6bb6229-482d-475c-b250-507ffacc2857.gif)

   ![image-20220626180715295](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626180715295.png)





6. **전략객체묶음을 제공하는 기능은 이관(안에서 필드 -> factory.getter)**되었으니 **`전략객체 묶음 받기`를 `이관해서 내수용으로 정의해줘야 제공getter도 정상작동한다`**

   - **제공기능**을 **구현하는 곳**인 **구상Factory내**에서 **제공 기능을 작성하면서 `필요한 재료들을 받기기능으로 구현`**하면 된다.

     1. return 제공 변수(빨간색) 
     2. **내부에 가진 것을 응답하니 field**
     3. 내부에 field에 받기기능
        1. 1개를 객체생성과 동시에 받는다면 생성자주입
        2. **생성이후에 받는 것**이라면, **null초기화 필드에 setter 공짜로 생성**
           1. **여러개를 받는 것이라면, null필드를 빈컬렉션으로 변경**
           2. **setter메서드의 이름을 addXXXX로 변경 + 인자를 객체1개로 변경**

     ![f88cbaa0-2273-49b9-a291-1feba3b9e1fb](https://raw.githubusercontent.com/is3js/screenshots/main/f88cbaa0-2273-49b9-a291-1feba3b9e1fb.gif)

   - **나머지 구상체들**도 **제공기능을 위한, 받기기능을 구현해준다.**

     ![ecd620d5-141c-4589-a64e-1810ddee8b51](https://raw.githubusercontent.com/is3js/screenshots/main/ecd620d5-141c-4589-a64e-1810ddee8b51.gif)

7. DiscountPolicy에서는 위임된 전략객체 받는기능을 제거한다.

   ![image-20220626185707439](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626185707439.png)

8. **Main에서 policy적용을 위한 객체 생성시**

   - 기존 DiscountPolicy에  trigger전략객체인 Conditions를 추가 by 받기기능
   - **변경 -> Policy 전략 구상층인 `구상Factory`에 `타 전략객체 condition`을 추가 by 받기능**

   ![9d4c009c-1138-45a2-9760-8097c1ce5e71](https://raw.githubusercontent.com/is3js/screenshots/main/9d4c009c-1138-45a2-9760-8097c1ce5e71.gif)

   ![image-20220626190023636](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626190023636.png)



9. **factory에 getter로 인한, 전략객체 사용처(DiscountPolicy)의 디미터법칙 위반 확인**
   ![image-20220626224555773](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626224555773.png)

   ![image-20220626225656683](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626225656683.png)

   - factory의 기능이 getter기능이라면, **전략객체 사용처에서 내에서 `지역변수로 받거나 or getter.B()로 체이닝하거나 or getter를 for문으로 하나씩 돌릴 때`, 응답을 받는 순간 `의존성이 추가`된다.** but 의존성을 낮추려고 factory를 쓰는 것이다.
     - **사실상 지역변수로 받거나 or 체이닝하거나 or for문에 돌릴 때, `factory만 알아야할, 보호받아야할, 변하지 않는 [전략객체 사용처]`가 의존성이 추가되므로 `시켜서 = 위임해서` 제거해야한다.**
   - **구상Factory마다 모두 시키는 것이 적용되어야하므로 `구상층 직전의 추상층`이자 `인터페이스 변수`로 사용되는 `PolicyFactory`내부에 `default 메서드`로서 `getter로직을 위임시켜야한다`**
     - getter로직을 위임할 땐, **기존 `객체.getter()`를 안으로 가지고 들어가 `getter()`으로서 사용하면서 `.B()에 해당하는 메서드 내부에 다 포함`시켜야한다**
       - 예를 들어 **객체.getter().B() -> 객체.B()로 변경 뒤, 내부에서는 getter()로 사용**
   - **`전략객체를 내부에서 받아 사용하는 class는, 변화가 없고 의존성이 낮아야한다.`**

10. **discountPolicy내부의 calculateFee()는** `전략객체factory.calculateFee(fee)`만 남기고, **전부 factory의 인터페이스 내부로 위임되어야한다**.

    - **`F6`으로 옮기면, `내부에 사용된 객체class 중 택1`하면서 `통째로 다 옮겨`버리니, `옮기기 전에 복사해서 틀을 짜놓고 옮긴다.`**
      - 응답메서드이므로 `return factory.calculateFee(fee)`를 남겨놓고 옮긴다.
        - **이 때, 옮겨질 defatult 메서드를 활용하는 코드다.**
        - **`default 메서드는 내부에서 구상factory들이 구현해야하는 2가지 전략메서드를 내부에서 사용한 method다.`**
      - **추상체이자 interface로 옮기면 자동으로 `default`메서드로 옮겨진다.**

    ![856e26c3-7a57-4d83-83e4-edaa1308b588](https://raw.githubusercontent.com/is3js/screenshots/main/856e26c3-7a57-4d83-83e4-edaa1308b588.gif)

    ![image-20220626230428536](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626230428536.png)

    - **기존에 사용된 `추상체.추상메서드()`를  `추상체 내부에선 상위추상층의 추상(전략)메서드를 그대로 내수용으로써 사용해도 deafult 메서드라면 괜찮은가보다.`**
    - **discountPolicy의 context를 사용하지 않았는데, F6으로 따라온 파라미터를 제거한다.**

    ![b3a0847f-5b2a-4802-8ecd-28969613b199](https://raw.githubusercontent.com/is3js/screenshots/main/b3a0847f-5b2a-4802-8ecd-28969613b199.gif)

    - **결과적으로 `DiscountPolicy는 전략메서드2개를 내수용으로 사용하는 deafault메서드 1개를 호출`해서 사용하게 된다.**

      ![image-20220626230900836](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626230900836.png)

    - **2개 이상의 전략메서드를 포함하는 `추상체 변수에게 공통 로직을 위임`할 때, `인터페이스의 default`메소드로 위임되며, `내부에서는 내려받은 전략메서드를 내수용으로서 편하게 사용`해서 정의된다.**

    ![image-20220626231934396](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626231934396.png)


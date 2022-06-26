---
toc: true
layout: post
title: Object) [F1]Strategy + Factory 패턴
description: 전략패턴에 생성사용패턴을 적용하기 위해 Factory 도입

categories: [java, 우테코, oop, object, strategy, factory]
image: "images/posts/java.png"
---

##### 실습1-전략패턴에 simplex Factory for 생성사용패턴

- [적용 직전 커밋코드](https://github.com/is2js/OOP-Theater/tree/ea5e328af4a61480e8980c295341a043b81325e5/src/main/java/theater)

1. **전략객체를 사용하는 DiscountPolicy에게, 전략객체를 강제pushed(생성자를 통한 객체주입)하는게 아니라 `전략객체Factory`를 통해 `원할 때 && caching하며 생성`하여 주입할 수 있도록**

   - **Factory를 통해 원할 때  & caching 적용하여 생성하는 것을 `Lazy pulled`라고 한다.**

   1. **`전략객체Factory` 인터페이스를 만든다.**

   2. **전략객체를 반환하는 오퍼레이터**를 가진다.

   ![9d3ad15b-68b8-4833-92ad-b4e85326a968](https://raw.githubusercontent.com/is3js/screenshots/main/9d3ad15b-68b8-4833-92ad-b4e85326a968.gif)



2. 전략Factory를 구현한 **개별전략객체Factory**를 구현한다.

   - **개별전략객체를 사용하고 싶은 곳(DiscountPolicy)에서는 전략객체 대신 전략Factory를 생성자주입한다.**

   1. 전략Factory를 구현하고
   2. 전략객체getter에서는 **`cache(전략객체)가 적용된 생성`을 위해**
      1. `if (cache(전략객체) == null)`: **cache라는 전략객체가 아직 생성안되었다면, new 전략객체()를 생성해서 할당해준다.**
         - **`private 전략형 cache;` 변수는 애초에 null로 생성하는 것이 특징이다.**
      2. `return cache` : 전략객체가 들어갈 cache로 한다.
   3. **전략객체 생성 개별 `구현에 필요한` Money amount는 `필드로 추가해서  생성자`에서 받아온다.**
      - 구현에 필요한 재료는 생성자에서 받아오기.

   ![b8a8f98f-511f-4d70-94a4-3bbb11461680](https://raw.githubusercontent.com/is3js/screenshots/main/b8a8f98f-511f-4d70-94a4-3bbb11461680.gif)

   ![image-20220625224504986](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220625224504986.png)

3. **이제 전략객체를 pushed받던 DiscountPolicy가 `전략Factory`를 주입받아, `사용하던 곳에서 그 때 생성`하는 Lazy pull을 사용하도 DiscountPolicy를 수정한다.**

   1. 생성자로 주입될 놈(CalculatorFactory)은 field를 미리 만들어놓는다.
   2. 생성자에 파라미터를 추가해서 생성자 주입받는다.
   3. 기존 전략객체 주입은 생성자에서 삭제한다(signature)
   4. **기존 전략객체자리에  Factory를 통한 Lazypull된 것을 위치시킨다.**
      - **`Factory를 통해 생성하여 가져올 전략객체는 DiscountPolicy가 모르게 된다. Factory의 마법`이다.**

   ![f372f1a4-0c05-456b-ae4c-bfd067f4e976](https://raw.githubusercontent.com/is3js/screenshots/main/f372f1a4-0c05-456b-ae4c-bfd067f4e976.gif)

   ![image-20220625225028597](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220625225028597.png)



4. **나머지** 전략객체에 대한 **구상Factory들을 생성**해준다.

   - 영상에서는 Percent의 경우 synchronized 달아주는 것을 깜박함.

   ![a49169c7-410c-420e-b23f-a6f88c9d9f65](https://raw.githubusercontent.com/is3js/screenshots/main/a49169c7-410c-420e-b23f-a6f88c9d9f65.gif)

   - Nosale도 마찬가지

   ![9de6064a-85cf-4355-95a5-5523a16ea734](https://raw.githubusercontent.com/is3js/screenshots/main/9de6064a-85cf-4355-95a5-5523a16ea734.gif)

   

   

##### 실습2 - 위임된 Factory 적용 for Factory circulation

![image-20220626232753379](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626232753379.png)

1. 전략Factory에 존재하는 **생성의 추상메서드 `getCalculator`를 추상메서드에서 `제거`**하고, **전략객체의 `전략메서드를 복사해서 위임`받는다.**

   - **생성 추상메서드를 단순제거하고 -> 구상Factory들의 `@Override만 지우고 + 내수용 private 메서드로 바꾼다` .**
     - **구상Factory의 생성책임**이 생성용 추상메서드 개별구현이 아니라, **자기메서드가 되어버리며, public 전략메서드의 내수로  private으로 객체호출없는 메서드로 사용될 예정.**

   ![a1773d55-7ea6-4fc3-aaac-bab4122880bc](https://raw.githubusercontent.com/is3js/screenshots/main/a1773d55-7ea6-4fc3-aaac-bab4122880bc.gif)

   ![image-20220626005535310](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626005535310.png)
   ![image-20220626005644545](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626005644545.png)

   - **내수용 전략객체 생성메서드가 private으로 안바뀐 스샷임.**

2. 각 구상Factory는 **위임된Factory의 전략메서드를 구현하되**

   1. **자신의 전략객체생성메서드(cache+원할때)를 사용해서 전략객체 생성 후**
   2. **전략객체의 전략메서드를 호출**하게 한다.

   ![image-20220626011112869](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626011112869.png)

   ![6e1bec47-e595-4486-882f-1715a80635ce](https://raw.githubusercontent.com/is3js/screenshots/main/6e1bec47-e595-4486-882f-1715a80635ce.gif)

   - **이렇게 되면, Factory가 전략메서드 호출 -> 내장된 구상Factory가 전략객체 생성메서드 호출 후 -> 전략메서드 호출**의 형태가 된다.

3. 전략Factory를 사용하다가 열차전복사고가 일어난 DiscountPolicy에서 **Factor.전략메서드()**호출으로 **구상Factory내부메서드로 내장된 생성메서드를 생략**할 수 있게 된다.
   ![image-20220626011450717](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626011450717.png)

   ![image-20220626011433141](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626011433141.png)





4. **위임된 전략Factory**를 만들었더니, **전략인터페이스와 100%동일하다.**
   ![image-20220626012343087](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626012343087.png)

   1. pushed 주입을 해결하기 위해 getter+캐슁해주는 Factory를 도입했다.
   2. 열차전복을 해결하기 위해 Factory에는 전략메소드책임을 -> 구상Factory에는 getter() 내수용 소유 -> 전략메서드 구현시, getter()된 전략객체.전략메소드()호출해서 최종결과물을 반환해줘야한다.
   3. 그랬더니 Factory는 전략인페와 완전히 동일한 놈이 되었다. -> Factory자리에 전략인페로 교체함

   - **전략Factory인터페이스를 삭제하고, 전략인터페이스를 imple하도록 한다.**

   ![63f0c34c-fb0c-4902-83ae-17a8f09fb9b0](https://raw.githubusercontent.com/is3js/screenshots/main/63f0c34c-fb0c-4902-83ae-17a8f09fb9b0.gif)

   ![image-20220626012944177](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626012944177.png)





5. **깨닮음: `전략인터페이스`는 `전략객체`뿐만 아니라, 전략객체별 `구상Factory`를 구상할 수도 있다.**

   - **구상Factory는 내부에서 caching하여 전략객체 생성메서드를 내수용으로 만들고, 전략메서드 구현시, 전략객체가 전략메서드를 호출하도록 한다.**

   ![image-20220626013312123](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626013312123.png)





6. 전략Factory 주입후 사용처 DiscounPolicy는 

   - 전략객체 주입대신, 전략Factory를 주입했다가, **다시 전략객체를 주입(`전략 인터페이스를 변수로 받는`)하는 형태가 되었다.**

     - **형태는 전략 인터페이스를 받아 `전략객체를 주입하는 것 처럼 보이는` 형태지만**
       ![image-20220626014228621](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626014228621.png)

     - **개별 전략객체의 `구상Factory`들 역시 `전략인터페이의 구상체`이며, `외부에서는 구상Factory`를 실제로 주입한다.**
       ![image-20220626014249694](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626014249694.png)

   - **`전략인터페이스의 구상체가 전략객체가  뿐만 아니라 전략객체별 구상Factory도 같이 있다는 것을 생각`해야한다.**

     - 구상Factory의 전략메서드 구현내용에서
       - **전략객체를 내부에서 생성하고, 전략메서드를 호출** 

   ![feb7be6e-feb5-4475-a020-dae81db57557](https://raw.githubusercontent.com/is3js/screenshots/main/feb7be6e-feb5-4475-a020-dae81db57557.gif)

   - 주입되는 전략인터페이스의 **변수명을 factory로 바꿔주기** 

     ![image-20220626124004887](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626124004887.png)

7. 생각해보기

   - **위임된 전략Factory -> 위임된 구상Factory -> 전략Factory삭제**의 과정에서 **`Factory circulation`(회전풍차)가 사라지게 된다.**

     - 기존
       ![image-20220220181056735](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181056735.png)

     - 위임된 후 추상Factory 삭제

       ![image-20220220181102383](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181102383.png)

   - **보기에는 삼각형의 순환구조 같지만**

     - **생성된 전략객체는 구상Factory의 전략메서드 내용을 실질적으로 담당하기 때문에 `순환표에서 구상Factory는 삭제하여 생각`해도 된다.**

       ![image-20220220181742994](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181742994.png)




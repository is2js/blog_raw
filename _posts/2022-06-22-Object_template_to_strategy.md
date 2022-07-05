---
toc: true
layout: post
title: (Object)TemplateMethod to Strategy
description: 템플릿패턴(추상클래스, 상속)를 전략패턴(인터페이스, 조합)로 변경하기

categories: [java, 우테코, oop, object, templatemethod, strategy]
image: "images/posts/java.png"
---

- 전략패턴: 구상체 확장에 용이하다.(유연성). 
  - **템플릿메소드패턴에서 공통점이 없는 기능이 추가된다면 전략패턴으로 바꾼다.**
  - **조합** -> 전략객체들을 통해 서로다른 구현을 처리한다.
    - 부모-자식들이 없이, 일반class-주입 전략객체 구성
- 템플릿메소드패턴: 중복코드를 제거한다. 
  - **전략패턴에서, 안정화되어 더이상 확장안된다면 템플릿메소드로 바꾼다.**
  - **상속** -> 자식클래스들을 통해 서로다른 구현을 처리한다.



##### 전략패턴 핵심 요약

1. 현재 상태: **추상클래스가 부모로 있고** +  extends한 **자식class에서 훅메서드만 구현(개별구현시 필요한 자신의 상태값을 같기도)** 

   1. **종류별 개별구현을 위해 추상층 `abstract class(부모)`로 올라갔던 class(`DiscountPolicy`)를 `일반 class`로 낮추고 `훅메서드 정의부`를 삭제한다.**

      - 공통로직은 일반class안에 template method로 그대로 소유할 것이다.

      ![image-20220219160315991](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219160315991.png)

2. **개별구현을 상속한자식class의 명칭 + 훅메서드 구현**으로 구현하던 것을

   1. **일반클래스 + 추상화된 명칭(DiscountPolicy)를 유지하되**
   2. **개별구현의 전략객체의 전략메서드에서 할 예정이다. `전략객체들의 추상체 인터페이스`를  `(훅메서드명, 추상회된 메서드명) 전략메서드명`를 바탕으로 `-or`을 붙여 만든다.**
      - 훅메서드명과 동일한 **전략메서드명**: **caclulateFee**()
      - **전략객체의 인터페이스명: Calculator**
      - 개별 전략객체의 명: Amount**Calculator**, Percent**Calculator**
        - 사용: new AmountCalcultor -> `Calculator.calculateFee()`

3. **공통로직을 담은 일반class DiscountPolicy는 `훅메서드만 대신할 전략객체를 생성자에서 받아, 필드로 보유`하고, `훅메서드 자리에 대신해서 전략객체.전략메서드()`를 호출한다.**
   ![image-20220219160315991](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219160315991.png)

4. **상속된 자식이, 개별구현을 위해 필드를 생성자로터 받아 사용**

   ![image-20220219160611514](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219160611514.png) -> **전략객체들도 자기 생성자에서 받아서 쓰면 된다.**
   ![image-20220219161530933](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219161530933.png)

5. **자식class나 전략객체들이나 구상체의 구조는 `extends, impl`빼고 완전히 동일하다.**
   ![image-20220219172401296](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219172401296.png)

   - 사실 훅메서드는 접근제한자 `proected`인데 public으로 오타인듯

     ![image-20220624222615834](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624222615834.png)

   - **사실, 추상클래스의 상속을, `프로토콜(약속)을 가진` 인터페이스처럼 사용하고 있었던 것**

6. 구조적 차이점

   - 상속: **`컴파일러`가 알아서 2객체를 연결한다**
   - 합성: **`직접` 생성자를 통해 공급받아, 필드로 보유하면서 사용한다**





##### 현재코드(템플릿메소드 패턴) 설명

1. 현재코드: 템플릿메소드 패턴으로 작성된 할인정책

   - 추상체

     ![image-20220624215549118](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624215549118.png)

     ```java
     public abstract class DiscountPolicy {
     
         private final Set<DiscountCondition> conditions = new HashSet<>();
     
         public void addCondition(DiscountCondition discountCondition){
             this.conditions.add(discountCondition);
         }
     
         public void copyCondition(DiscountPolicy discountPolicy){
             discountPolicy.conditions.addAll(conditions);
         }
     
         public Money calculateFee(Screening screening, int count, Money fee){
             for (final DiscountCondition condition : conditions) {
                 if (condition.isSatisfiedBy(screening, count)) {
                     return calculateFee(fee);
                 }
             }
             return fee;
         }
     
         protected abstract Money calculateFee(final Money fee);
     }
     
     ```

   - 구상체 중 1 AmountDiscount

     - **훅메서드 구현시 자식class에서 자신만의 상태값도 필요했다.**

     ![image-20220624215829875](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624215829875.png)

   - 사용 in Movie

     ![image-20220624220007579](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220624220007579.png)





##### 실습 (템플릿메소드패턴 to 전략패턴)

0. 클래스

   1. 추상클래스: DiscountPolicy
   2. 구상클래스: AmountPolicy, NosalePolicy, PercentPolicy
      - [step3 github 코드](https://github.com/is2js/OOP-Theater/tree/step3/src/main/java/theater/discount/policy)

1. 추상클래스를 일반class로 내리고, 추상메서드로 정의된 훅메서드도 삭제한다.

   1. **`훅메서드`를 대신하여 그자리에 `(소문자로)전략객체.전략메서드()`를 작성**한다 
   2. **빨간줄로 `전략객체 추상체`를 field로 생성**
      - **전략객체명은 `전략메서드명 + -or`을 통해 생성한다.**
   3. **`전략객체 인터페이스`를 생성**
      - 이왕이면 전략객체명으로 package를 파서 한다?
      - 아니면 정책전략(DiscountStrategy?)
   4. **필드에 적힌 전략객체 필드를 생성자로부터 외부에서 주입**

   ![95c8e408-6bed-4553-85e1-eea3de38329f](https://raw.githubusercontent.com/is3js/screenshots/main/95c8e408-6bed-4553-85e1-eea3de38329f.gif)





3. **템플릿메소드패턴의 구상체 <-> 전략객체 구상체 거의 유사**하므로 

   1. strategy 패키지를 만들어 옮긴 뒤
   2. **기존 추상클래스의 자식 구상체의 `class명`들을**로 `전략객체 명칭`을 뒤쪽에 가지도록 바꾼다.
      - AmountPolicy -> Amount**Calculator**
        - class명을 바꿀 땐 F2로 바꿔야지 다 적용된다.~!
   3. 상속의 extends 추상클래스를 **`implements 전략인터페이스`로 바꾼다.**
   4. **훅메서드 구현시 접근제한자**였던 `proected`를 **`public`으로 바꾼다.**

   ![d9268fe0-bc76-4989-877f-12928eaea111](https://raw.githubusercontent.com/is3js/screenshots/main/d9268fe0-bc76-4989-877f-12928eaea111.gif)





4. Main에서는

   1. 기존 추상클래스DiscountPolicy 구상체 생성 -> **Calculator 전략객체 생성**
   2. 전략객체를 생성자에서 인자로 받는 일반class DiscountPolicy 객체 생성
      - **DiscountPolicy는 개별구상체를 안가지며, 대신 개별 전략객체를 주입받아 가진다.**
   3. 기존 policy구상체에 add condition -> 일반 DiscountPolicy 객체에 add condition
   4. Movie에 개별policy구상체 인자 -> 전략객체를 머금은 discountPolicy객체 

   ![106d5730-bc2c-4bb6-a076-fe5eb0a34615](https://raw.githubusercontent.com/is3js/screenshots/main/106d5730-bc2c-4bb6-a076-fe5eb0a34615.gif)



##### 비교

- 서로다른 구현을 해주는 구상체 AmountCalculator vs AmountPolicy

![image-20220219161636479](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219161636479.png)

- 서로 다른 구현을 위해
  - 좌: 전략객체 impl 전략인터페이스, **hasA모델, compositio모델이 된다.**
  - 우: 자식클래스 extends 추상클래스



- 전략패턴의 장점

  - 의존성관계가 simple해진다.

    - 일반상속: 자식이 공통코드를 가진 부모를 앎

      ![image-20220625111517654](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220625111517654.png)

    - 템플릿매소드패턴 상속: 부모가 자식의 추상메서드만 앎

      ![image-20220625111532049](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220625111532049.png)

    - 전략패턴: 여러 전략객체들을 아는 전략인터페이스만, 1개 클래스가 앎

      ![image-20220625111549733](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220625111549733.png)

      - **DiscountPolicy는 개별전략객체를 모른다.**
        - **상속**이면, 최소 자식class**들**은 알아야했다.
      - **전략인터페이스**가 **중간추상층으로 끼어들어가, 외부공급개별 전략객체는 직접적으로 몰라도 된다.**
      - **상속에 비해, 의존성이 1개 중간에 추가되었지만**
        - **각각이 다 1:1관계로 알고 있다.**
          - policy -> calculator(추상체라 1)
          - **AmountCaculator(N인데 시작점이라 1)** -> calculator
      - **무거웠던 부모의 역할을, 부모는일반클래스로 해방 + 전략객체 1개만 주입**되어 더 가벼워진다.
      - 그러나, 중간에 전략인터페이스 자체를 바꾸는 것은 까다롭다.
        - **`개별구현을 원하는 DiscountPolicy`도, `개별구현을 담당하는 전략객체들`도 1개의 전략인터페이스를 바라보고 있기 때문**
        - **전략인터페이스가 무거워진다.**

- 전략패턴의 단점

  - 처음에는, 확장가능성이 있으니, 템플릿메서드패턴의 상속을 쓰긴 어렵다. 처음에는 중간추상층으로 들어가는 전략패턴을 가지고 시작하여, **`가운데서 각각이 의존`하는 무거운 인터페이스로서 시작을 해야하지만, `수정시 여파`은 존재한다.**

- 전략패턴의 추가 장점

  - 전략인터페이스는 하나의 어댑터로 생각하고, 전략객체들을 바뀔 수 있다.

    - 전략객체들이 업데이트되어도, 업데이트 된 부분만 수정하고, 개별구현을 원하는 DiscountPolicy는 안변한다.

  - **어댑터로서, 외부의 변화에 대한 충격을, 사용처(DiscountPolicy)에게 안전해지게 흡수한다.**

    - 그냥 꽂지않고 어댑터를 끼워 사용하면, 여러변화의 폭을 수용할 수 있다

    - **화살표를 받는 쪽 = 이용당하는 쪽 = 알고 있다 = 의존한다 = `다른 방향에서 오는 여러개의 화살표들을 먹어 충격을 흡수`해준다**

      ![image-20220219231841318](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219231841318.png)

  - 템플릿메소드패턴은 상속으로서 `부모와 자식이 직접 통신`하기 때문에, 방향을 역전시켜놓았어도 **중간에서 여파의 충격을 흡수할 추상층이 없다.**
    ![image-20220219231947820](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219231947820.png)



- **만약 위(-Policy)/아래(-Calculator)로 `변화가 심했다`면?**

  - **변화가 심한 쪽마다 `인터페이스(중간추상층)`로 충격을 흡수시켜야하므로 `인터페이스끼리 대화하게 만든다.`**

    ![image-20220219233330001](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219233330001.png)

- 가운데 생기는 `인터페이스`는 충격을 흡수하는 어댑터이자 전략인터페이스이다. 

  - **하지만,  개별구현 객체들을 다 감당해야하는 `가운데가 무거워`지는 관계.**
  - 템플릿메서드패턴의 상속은 자식이 부모를 알고서 받아 사용하다가, **부모가 자식을 알고서 개별구현을 받아쓰므로, `가벼운 관계`이다.**
    - **훅메서드만 알고있으면 되니, 무게가 가벼운 것들을 여러개 안다.**

  



- 다시 비교
  - 템메(상속 역전):  여러 종류를 가진 class는 추상클래스로 바꾸고, 그것의 **구상체를 사용하여 Type(형)을 만들고, `runtime에 Type을 선택`**만 해주면 된다.
    - **`new AmountDiscountPolicy()`로 runtime시 선택하여 객체 생성 -> `amountDiscountPolicy.calculateFee()`로 사용 runtime내에선 더이상 바꿀 수 없다?**
    - 단순 포인터 1개
  - 전략패턴: 여러 종류를 가질 class는 일반클래스로 유지하되, **중간추상층으로 `의존성을 1개 늘려 runtime에 선택된 구상층을 합성`**하여 개별 구현함.
    - `new AmountCalculator()` -> Calculator ->  `calculator.calculateFee()`
    - **Calculator자리에 다른 전략객체로 언제든지 바꿀 수 있다??**
      - 포인터의 포인터





##### 각각의 단점

![image-20220220110722859](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220110722859.png)

- 템메: (아래층 생길 시 == 훅메서드 2개이상) **조합의 폭발**
  - **자식층이 점점 깊어지**거나, 자식들이 병렬이 아닌 **중첩구조**인 경우
    - 한 레벨 아래 여러개가 있는데 또 **그아래 레벨이 생기는 경우**
      - **추상층 늘어난다 -> 훅이 1개더 추가된다(기존 구상층은 중간추상층이 되어 1개를 가운데서 미리 구현하여 막아줌)**
      - **훅(추상) 메서드가 2개다 -> 자식이 2층이다. -> 조합폭발이다.**
        - ex> theater - [step2의 DiscountCondition](https://github.com/is2js/OOP-Theater/blob/step2/src/main/java/theater/discount/DiscountCondition.java)
          - **중간 추상층(자식1층)** with DiscountPolicy
            - 훅메서드 calculateFee만 먼저 구현
          - **최종 구상층(자식2층)** 
            - 훅메서드 isSatisfiedBy 구현
    - 부모Policy - 자식 3개의 Policy - 자식마다 2개씩 Policy
      - 1 x 3 x 2 =  최소 6개의class
  - **경우의수만큼 계속 늘어난 class를 만들어야한다.**
  - 그에 비해 전략패턴은?
    - **외부에서 1개의 전략객체를 만들어 넣어주고, `필요하다면 바꿔서 넣어주면 된다.`**
      - 전략객체를 만드는 것에 대해.. 생각안하고 있음..
      - 전략객체는 미리 6개 class를 만들었다고 가정한다?
- 전략: (전략메서드 2개 -> 인터페이스를 2개)**의존성 폭발** but **해결 가능**
  - **템매의 조합폭발(훅메서드 2개이상)은 못막는다.**
    - **훅메서드가 2개이상 들어갈 것 같다 싶으면, 전략패턴으로 구현하자.**
  - 반면,  hasA모델 = 합성 = 조합 = 전략패턴의 **의존성 폭발은 해결가능**
    - 외부에서 들어오는 **전략객체 2개의 다중합성** -> **사용처 안에서 알아서 대화함.**
      - 형자체는 외부에서 결정되어 들어오므로 문제가 안될 것이다.
  - **구체적으로는 어떻게 해결할까?**


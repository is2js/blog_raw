---
toc: true
layout: post
title: OBJECT 08 객체설계3_Theater_with_SOLID(코드스핏츠)
description: object 책을 강의한 코드스핏츠 유튜브 요약

categories: [object]
image: "images/posts/java.png"
---

- 참고 유튜브 : https://www.youtube.com/watch?v=navJTjZlUGk
- 정리본: https://github.com/LenKIM/object-book
- 코드: https://github.com/eternity-oop/object
- 책(목차) : https://wikibook.co.kr/object/


### ch8. theater_with_solid





#### Movie

![image-20220205161147187](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205161147187.png)

- 책하고 동일하게 `policy`를 단일로 1개를 가지고 있다. `final`로 가지고 있다?
- calculateFee()할때는 screening, count를 받아서 
    - **`policy에게 calculateFee를 위임`하는데, 추가로 + `Movie속 fee`도 같이 건네줘야한다**





#### DiscountPolicy

![image-20220205161328082](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205161328082.png)

- discountpolicy는 **`hashSet을 통해 다수의 condition을 소유`**하고 있다.
    - **`(중간에 유연하게 더 받아들일 수 있도록)생성자에서 받지 않고`**, addCondition()메소드로 **`빈Set에서부터  추가/삭제`** 하려고 하는 것 같다
        - **runtime context를 중요하게 생각(my) `condition을 유연하게 보유` )**한다면, 책 2장의 형태로는 안된다.

- 2번째 문제는,  중간에 유연하게 원소를 더 받아들일 수 있는 conditions(hashset)을 가지는데,  Movie가 `final`로 선언한 `policy`지만 다른 **policy로 바꿔치울 때, 내부 conditions는 어떻게 유지할 것인가?**

    - 5장에서, Movie가 **policy를 가진 장점** -> **Movie가 중간에 policy를 바꿀 수 있다.**
    - 그러나 policy와 condition은 별도의 객체다 -> 
        - policy를 바꿀 때, 기존 condition을 유지하는 방법은?
            - **내가 AmountPolicy -> PercentPolicy로 바꿀 때, AmountPolicy내부에 있는 conditions를 유지하는 방법은?**
    - **특정policy에게 `"나의 conditions를 물려줄게"` **
        - **물려준다=객체내부정보를 준다=객체내부에서만 가능하다 = 본인 내장을 까는 것이기 때문에 Policy클래스 내부에서 `copyCondition( 새로운policy) -> 새로운policy.addAll(내장conditions)`을 통해 넘겨준다.**
    - conditions은 Movie에 걸려있는 것이다. 근데, conditions를 소유하고 있는 policy를 바꿀 때, movie에게 그대로 넘겨줘야하니, **바뀐policy에게 내장을까서 넘겨주는 메소드 copyConditions를 만드는 것**이다.
        - 원래는 Movie꺼지만, Movie의 지식을 줄이기 위해서 -> policy에게 위임했던 condition
            - **Movie가 policy와 condition을 둘다 아는 것보다 policy만 아는 것이 더 낫다고 판단**했었다. 근데, policy를 교체한다면, 기존 condition을 다시 만들어서 넣어줘야하는 문제가 생겼다. 
                - **다시 만들면 안된다. 객체는 식별자를 통해 구분함. 값만 똑같이 만든다고 해서 기존 condition이 아님.**
                - 기존 condition그대로 유지해야하므로 
                    - 내장에서 **같은Classr객체BUT바뀐policy객체**를 받아서, 빈set에 기존꺼 add해주기
                    - `기존policy.copyCondition`( **바뀐policy** )
                        - **바뀐policy.빈conditions.addAll(`기존conditions`)**
        - **같은종류의 객체끼리는 이렇게 내장까서 복사가 가능하다.**
    - 원래는 Movie가 가져야할 condition다. 
        - 하지만, policy가 condition을 가져왔기 때문에, policy교체시 condition도 옮겨줘야하는 책임이 딸려왔다.
        - 객체에서, 소유권을 함부러 이전하면 그 여파는 반드시 온다.
            - 아무 관계 없는 policy와 condition을 한줄로 엮어놔서... 책에서 문제가 발생한 것.

- calculateFee는 condition을 돌면서 계산하면 된다.

    - NosalePolicy는 condition을 무시하고 계산해야한다.

        - **Set안에 아무것도 안들어와있으면 -> for돌린 것은 무시되니 -> return이 for문 밖에서도 있게한다.**

            ![image-20220205164205144](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205164205144.png)



- **현재까지 `DiscountPolicy`는 추상클래스이고**
    - addCondition, copyCondition은 **템플릿메소드**이고
    - calculateFee같은 **구상클래스에 위임한 훅** 이 발동한다.
- **`템플릿 메소드패턴`은 언제 많이쓰나**
    - **구현층에서 공통적으로 사용되는 로직이나 상태가 있음을 확신할 때**
    - 수많은 policy가 나올 것이지만, **어느정도 형태와 로직이 공통일 때, 형이 한정되어있을 때**
- **`전략패턴`은 인터페이스로서의 메소드 calculateFee()만 존재할 때**
    - **전략패턴은 `인터페이스만 공통` -> 구현이나 상태의 공통점이 없다.(각기 다르게 구현되는 유연함을 가질 때)**
        - 만약 템플릿메소드를 써야할 곳에 전략패턴을 쓴다면..
            - 템플릿메소드부분을 모두 들고 다녀야한다 -> 버그 많아지고 수정할부분도 여러군데

- 만약, 템플릿메소드라고 생각했는데, 공통아닌 policy가 발견되었다? -> 전략패턴으로 바꿔줘야한다.



##### AmountPolicy, PercentPolicy

![image-20220205164846747](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205164846747.png)

- 템플릿메소드패턴의 DiscountPolicy를 상속받았다면
    - calculateFee()에 쓰일 추가적으로 amount만 생성자에서 받아들여주고
    - 자식마다 달라지는 훅의 calculateFee()부분만 @오버라이드 해서 구현해주면 된다.



![image-20220205165027756](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205165027756.png)

- PercentPolicy도 마찬가지다.



##### NosalePolicy

![image-20220205165149271](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205165149271.png)

- percent는 오타

    ```java
    public class NosalePolicy extends DiscountPolicy {
    
        @Override
        public final Money calculateFee(Money fee) {
            return fee;
        }
    }
    ```

    - **그냥 fee를 받으면 그대로 fee를 리턴한 것**이다. 
    -  추상클래스 DiscountPolicy를 보면,
        - **condition을 가지고 있어서, 부모의 로직에서의 for문에 걸려 calculateFee(fee) -> 훅 -> return fee를 하나**
        - **컨디션 없이 return fee를 하나 동일하게 로직이 수행된다.**

    ![image-20220205165549856](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205165549856.png)

    - 리스코프 치환 원칙에 의해서, **if자식형을 찾지않고** 부모형인 DiscountPolicy수준에서 찾는데 성공했다(?)
        - **로직이 <자식객체인지 확인하는> if분기되지 않았다.**
        - conditions이 있든/없든 Nosale이 Amount, Percent와 동일한 로직으로 일반화(return fee만)되었다.

    

    

#### DiscountCondition

![image-20220205170231321](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205170231321.png)

- 추상클래스에선 할일이 없다.
    - 구상클래스에선 **메소드 1개만 구상**하면 되니 **좋은 인터페이스**다.



##### PeriodCondition

![image-20220205170250233](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205170250233.png)

- when받아서 when이 일치하는지 확인해주면 된다.



##### SequenceCondition

![image-20220205170320843](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205170320843.png)

- 마찬가지로 sequence 받아서 일치하는지 보면 된다.





#### TicketSeller

![image-20220205170435907](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205170435907.png)

- TicketSeller는 customer+theater+movie+screening+count 5개를 인자로 받았다.
    - **인자로 받았다 = `Dependency로서 가지고 있다`**
    - 5개의 인자 = 지식들을 형으로 강제하면 좋은데, Seller는 TickeyOffice에게 해당 정보를 넘겨줘야하니, 기존 형을 모르게할 수가 없다.
    - **도메인상, customer는 주문시 seller인 나에게 5개 정보를 다보낸다.**
        - 코드상으로는 5개 정보에 대해 dependency가 있지만, **실제로 이 dependency(theater, movie, screening)를 사용하면 안된다.**
            - 왜? **중개용으로서** Customer -> -> TicketOffice에게 **건네주기 위해 받았을 뿐**
                - **`중개 = 나와 계약한 ticketOffice = 원래 의존성 있던 객체`가 .reserve()에 `사용하도록 전달만`해줌. **
                    ![image-20220205171528941](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205171528941.png)
                - **`나한테 원래 의존성이 없던 , 인자로만 받았던 movie`가 사용해선 인자로 넘어온 dependency들을 사용해선 안된다.**
                    ![image-20220205171509277](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205171509277.png)
            - dependency 받은 **목적이 사용이 아니라 전달하기위함**이었다.
                - java에서는 표현할 능력이 없음.
    - **직접사용하는 코드 `movie.calculateFee( screening, count)`는 movie에게 `중개용, 전달용 dependency를 메세지로 전달`해서 사용하고 있는 `오류`** 를 발생시키고 있다.
        - 도메인상 어쩔수 없이(전달 목적으로) 받은 dependency를 사용하게 되면, 겉잡을 수 없게 된다.



![image-20220205171559206](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205171559206.png)

- 빨간줄이 잘못되었다는 것을 깨달아야한다.

    - **`최소지식의 원칙 = 디미터의 원칙`**으로 바꿔줘야한다.
    - **`나와 직접적인 관계가 있는 TicketOffice에게 맡긴다`**

    ![image-20220205171700434](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205171700434.png)

    - `직접적인 관계가 없던 movie`가 하던 것을 -> `ticketOffice`에게 시킴
        - **`theater, movie, screening의 변화에 안전해졌다.`**
        - 중개용 인자 (= 직접관계 객체라면, 걔만 사용가능)를 중개용으로만 사용되게 했다면, **인자들에 대한 변화에 안전 = dependency가 사라짐**





#### TicketOffice

![image-20220205172025099](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205172025099.png)

- **movie의 대한 지식(직접관계가) 원래 있던 TicketOffice는**
    -  calculateFee()를 내부에서 movie에게 시킬 수 있게 된다.
    - **결국엔 movie.calculateFee()를 할 예정이지만, `직접 관계있는 놈에서 실행시키도록 돌리는 wrapping method`를 TicketSeller에게 적용했던 것이다.**



- 그렇게 수정하다보니, 코드중복이 떠서 하나는 내부메소드를 사용하게 한다.
    ![image-20220205172319775](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205172319775.png)
    ![image-20220205172357474](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220205172357474.png)


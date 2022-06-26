---
toc: true
layout: post
title: OBJECT 05 객체설계2_Theater_with_Reservation(코드스핏츠)
description: object 책을 강의한 코드스핏츠 유튜브 요약

categories: [object]
image: "images/posts/java.png"
---

- 참고 유튜브 : https://www.youtube.com/watch?v=navJTjZlUGk
- 정리본: https://github.com/LenKIM/object-book
- 코드: https://github.com/eternity-oop/object
- 책(목차) : https://wikibook.co.kr/object/


### ch5. Theater with Reservation

![image-20220126223153536](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220126223153536.png)





#### 저번시간까지 모델

![image-20191124163632027](https://raw.githubusercontent.com/is3js/screenshots/main/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f30303679386d4e366779316739393633666b337a766a333131713069676d79372e6a7067)

- 극장이 ticket이나 invitation을 발행할 수 있다( 생성시 theater가 생성자 인자로 )
- ticket의 **판매는 복잡한 책임**이 있기 때문에, 극장은 생성만 하고 -> **TicketOffice에 위임**한다.
    - 대신 TicketOffice는 Fee를 받는다.
- TicketOffice는 Office로서 Fee만 받게하고, **고객상대는** -> 프리랜서인 **TicketSeller를 고용**한다.





#### 이번 시간 모델

![image-20220126224317186](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220126224317186.png)

- ticket 물물교환은 끝났다 -> 인터넷예매의 시대

    - 초대장도 없앴다. 대신 예약을 한다.

- theater가 Reservation을 허용해 줄 수 밖에 없다.

    - 이 책에서는 Screening이 적당한 expert다. ->  `expert pattern`을 따르게 하고 있다.
        - **책임을 누가 질 것이냐? -> `정보 전문가가 책임`을 부여하는게 가장 낫다.**
            - **객체의 특성(`자기의 상태를 외부에 노출하지 않는다`) 때문이다.**
            - 최대한 노출하지않고 수행하려면, **가장 관련있는/정보를 가장 많이 가지고 있는 객체**가 받아서 처리하는게 좋다. **캡슐화가 가장 덜 깨진다.**
        - 하지만 이 패턴에 따르면 말이 안되는 경우가 많다.
            - 팀장, 사장이 정보가 전문가가 아니다. -> 현실세계는 정보 전문가가 그 책임을 수용하진 않는다. 정보 전문가 패턴의 단점이다. 실제 짜보면 그렇게 안된다.

- **책에서는 Screening(상영)이  Reservation을 받는 까닭은?**

    - Reservation관련 정보를 가장 많이 가지고 있기 때문에, `정보전문가 패턴`에 의해 Screening이 Reservation을 책임진다.

        ![image-20220126225218799](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220126225218799.png)

    - **상영 정보가  예매를 한다는게 이해가 되나?**

        - 말이 안된다. 설계가 안된다. `screening.reserveSeat()`가 말이 안된다. 
            - 쓸데 / 안쓸때를 구분해서 써야한다.

    - 진짜 도메인에서는, 상영 정보는 자리번호/순서/시간을 가진 `시간표일 뿐`이다.

        - 여러 영화가 `공유`할 수 있다. 10시 상영객체 -> 인디아나존스도, 스타워즈도 각각 2개 가질 수 있다.
            - 10시-스타워즈 / 10시-인디아나존스를 따로 만들필요가 없다.
            - DB 정규화와 관련되어있다.
            - 어떤 객체가 2개 만들어질 필요가 있는지 고민해볼 필요가 있다.
            - 함부러 객체를 남발한다 = 더이상 객체context 사용X = 값 context를 쓰겟다고 하는 것과 마찬가지다.
                - 1, 2는 값으로 분리될 수 있어서 값을 넣어, 인스턴스를 여러개 만든다?
        - 객체는 객체 고유정보만 들고 있다면, 관계형 DB처럼 참조객체로서 보여주면 되는 것이다.
            - 10시상영 시간표객체는 1개만 있으면 된다. -> 각 영화들이 10시면 참조만 해주면 된다.
                -  굳이 영화를 field로 받아서 각각 영화마다 여러개를 만들 필요가 없다.?
        - 객체지향 설계에 도움되는 개념 = RDB의 정규화(1~3정규화)
            - 객체의 역할과 책임을 분리할 때, 어디까지 얘의 정보로 다룰지 결정할 수 있다.
                - 인디아나존스 영화1, 스타워즈 영화2가 있다면, 
                    - 상영정보(Screening)은 영화제목을 가질 게 아니라, 시간만 있으면 된다. -> 각각의 영화에 참조만 시간별로 참조가 되어주면 된다.
                    - 만약, 상영정보(Screening)에 영화제목을 넣는다면.. 
                        - 상영정보-시간/영화1
                        - 상영정보-시간/영화2
                            - 똑같은 시간에 영화마다 여러개의 객체를 만들어줘야하니.. 그냥 안만들고 참조만 해주면 된다.

- Screening은 시간표일 뿐 -> 알고있는 정보는 시간뿐 -> Reserve를 받기에 적합하지 않다.
    - 정보전문가패턴에서도 그랬다 -> 이번시간 모델에서는 아예 안받게 함.
    - 현실세계에서는 극장도 여러개 / 영화도 여러개 / 영화마다 상영도 여러개





![image-20220126232008885](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220126232008885.png)

- 책에서는 할인정책도 소개하고 있다.
    - `discount policy(어떻게 할인 하는가)` 2가지 정책 타입이 있다. 
        - amount
        - percent
            - 그외 고정금액 확정도 있고 보너스 정립 등 많은 정책들이 있다.
    - `discount condition(언제 할인이 일어나는가)`도 2가지다.
        - time condition: 언제를 기준으로 할인
        - sequence condition: 몇번째인지를 기준으로 할인
            - 그외 몇명이상일 때/소아일때/여성일때 등 많은 condition정책들이 있다.
    - 위에 그림처럼 보는 것처럼 **연관관계가 수직적 성립하진 않는다. 각각 교차조건이다. 서로 부모자식관계 없다.**
        - **Type시스템의 문제: `부모자식 관계가 없는데, 어떻게 소화하지?`**
            - 책에서는 비겁하게 무기에다가 상수값으로 타입을 놓거나
            - 객체간 분리해야할 것을 무기의 속성으로 소화하고 있으나 -> 그러면 안된다.
                - 무기의 속성으로 소화 -> throw return시 -> 중간메소드 2개씩이 생겨남
                    - 자기 타입불렀는데 아니면 죽는 메소드가 2개나 가지고 있게 된다.
                    - 즉 조건분기만큼 하드코딩으로 메세지를 받게 된다?
                        - 쓸대없이 값을 가지고 있기 때문
            - **책 4장까지 나온 movie 클래스로서는 discount 1개만 더 추가되더라도 많은 변화를 시켜야함.**
                - person, amount외 다른 type를 추가하면 다 고쳐야한다. -> **`내부에 type에 의한 if분기`가 있기 때문!!**





#### Main 

- **client코드인 Main코드부터 짠다.**

#### ![image-20220127212251961](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127212251961.png)
![image-20220127220145163](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127220145163.png)

- client코드부터 짜야 
    - class짜기가 쉽다
    - 충분한 도메인 검증(제대로 작동하는지) 검증할 수 있다.



1. 극장을 100원으로 만들어냈다.

    - 극장의 처음 받는인자는 자본금이었다. **책에서 `값 객체` = `Money`를 쓰기 원해서 바꿨다.**
        - `new Theater(100L);`
        - `new Theater(Money.of(100.0));`
            - **값 객체는 값을 대신하는 객체라서 `레퍼런스형 객체`와는 다르다.**

2. 그 담으로 극장 상영을 위해 `영화`를 만들어낸다.
    ![image-20220127212749778](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127212749778.png)

    - **영화의 타입으로 AmountDiscount를 받는다. 왜?**
        - 책에서 내건 제약조건이 `1 영화 = 1 policy` 제약조건을 줘서
            - `AmountDiscount`or `PencentDiscount`  둘 중 1개의 Type조건을 가진다. **값도 객체도 아닌 Type(형)으로 인식해야할 문제다.**
        - **그냥 Movie가 아니라, Type 2개 중 1개의 Movie, 즉, Subtype을 가지는 Movie로 인식해야한다.**
            - 책의 제약조건 `영화란, 단 1개의 할인정책을 갖는 존재`라고 했기 때문이다.
        - **`제네릭`도 두려워하지말고 일단 지르자. 회사에 잘하는 사람이 알아서 짜줄 것이다.**
    - Movie의 Type이 정해졌으면, 나머지
        - 영화제목
        - 지속시간
        - 영화가격(실제에서는 영화에 가격X Screen-상영(시간)에 따라서 가격이 정재짐)
        - 컨디션
            - AmountDiscount 중에 Sequence를 쓰는 것으로 나타냈다.
            - 위에서 지정한 Type을 썼고, 그 중에서도 Time/Sequence 중에 sequence를 쓴 것이다.
                - amount 1000원 할인 + sequence가 1일 때 할인
            - 컨디션도 Type에 맞춰서 넣어줘야한다. 그렇지 않으면 제네릭에 걸려 죽게 된다.

3. theater에 만들어진 movie를 추가

    - **movie는 상영(screen)을 가지고 있지 않기 때문에, 따로 theater-movie(1)-screen(N)매칭시켜줄 필요가 있다.**

4. theater에 Screening을 추가하는데, **각 movie에 맞춰서 Screening을 추가해줘야한다. ** **일별 x (매일마다) 시간별**로 여러개를 추가한다.
    ![image-20220127215027520](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127215027520.png)

    - **2019년, 7월, for** 7일**부터** / 매일(1일**마다(간격으로)**) / 31일**까지**
        - **for 10시부터** / 3시간**간격으로** / 24시 전**까지**
            - **for sequence 1부터 / `시간간격마다`+ 1되면서 **
            - sequence는 **안쪽 for문에서 hour처럼 매일 1로 초기화되서 커짐**
    - Screening을 만드는데
        - 순서=seq(시간마다 1부터 +1씩 증가되서 번호가 생성됨)
        - 상영시간=LocalDateTime으로 2019년 7월 <7일부터 31일까지 매일, 시간은 10시부터 3시간 간격으로>
        - 상영마다 **가용한 좌석 수**(상영관 class(여기에 예매가능한 좌석정보 포함?) 만들어야하는데, 너무 오래걸려서)
            - **무한 예매를 방지**하기 위함.
            - 상영관이 없으면 무한예약이 될 수 있다. 대신 **상영마다** 가용 자석수를 배정하면, 무한예매가 방지된다. (좌석수만큼만 예매가능)

5. TicketOffice를 자본금 0원으로 만들고, **theater <-> TicketOffice가 계약(contract)하게 만든다.**
    ![image-20220127220210626](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127220210626.png)

    - 계약시 TicketOffice에게는 Fee(수수료) 10%를 떼어준다

6. 프리랜서 TicketSeller를 만들고, **TicketOffice <-> Seller와 계약**시킨다.

    ![image-20220127220334538](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127220334538.png)

7. **customer만드는 코드가 빠짐**
   
    ```java
    Customer customer = new Customer(Money.of(20000.0));
    ```
    
8. 마지막 **예약**한다. **예매를 할 때는**
    ![image-20220127220425401](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220127220425401.png)

    1. **theater으로부터 특정 movie에 대한 상영시간표(Screen)들을 얻어온다.**
        - theater는 공개적으로 특정영화 선택시 Screen들에 대한 인터페이스를 외부에 공개할 책임(기능,함수)이 있다.
    2. 지금은 for문 돌리면서 break로  첫번째 Screening만 예약하지만, 실제에서는 특정 Screen을 UI로 선택할 것이다.
        - **customer가 .reserve()예약하는데**
            - 특정seller에게
            - 이 theater에서
            - 이 movie를
                - 이 때(screening)
            - 2명이서 
                - 보고싶다고 예약한다.
    3. customer는 예매후 theater.enter()입장을 한다.
        - customer는 인자로 넘어가면, 내부에 표를 2장 가지고 있을 것이다.
            - 1사람이 표2장을 제시하면서 입장한다.
        - 결과로 입장가능한지 결과를 알려준다.



- 실제 예매과정과 비슷하다
    - **핵심은 class가 아니라 Main의 client코드부터 시나리오대로 짜보는 것이다.**
        - 이것 가지고 회의를 먼저 한다.
            - UI에 넘겨줄 부분.. 등
        - 중요한 건 class가 아니라 **instance, 실체, 진짜 일하는 놈들**
            - 일, 활동의 결과는 return값(isOK)가 되기도 하고
            - 인자로 넘어가기도 한다.(theater.enter(customer))

##### 검토하기

- TicketOffice를 생성할 때, 생성자로 넘기는 인자는 값 `객체`다.
    ![image-20220128002847964](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220128002847964.png)
    - Money를 만들 때는 값(0.0)이 어쩔수 없이 들어갔지만
    - TicketOffice입장에서는 객체(Money)가 들어간다.

- contract할 때도, 객체를 받아들였다.

    ![image-20220128002929648](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220128002929648.png)

    - rate(할인율) 10.0은 좀 아쉽지만, 이것까지 객체로 하긴 귀찮아서 일단 안함.

        - 사실은 이부분은 객체지향 위반 중이다. 값이 들어가면 안된다.

    - 그렇다면, 예매한 좌석 수(사람 수) count 2도 값으로 들어가서 안좋은 것

        ![image-20220128003048335](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220128003048335.png)

        - 실제로는 count객체가 들어와야한다.
        - return값이 isOK도 boolean = 값이라서 위반이다. 원래 객체로 리턴되야한다.

    - 여기도 100대신 seatCount라는 객체로 들어가야한다.
        ![image-20220128003147119](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220128003147119.png)

    - 여기도 1이 아니라 sequenceNumber객체로
        ![image-20220128003206503](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220128003206503.png)

    - spiderman 문자열도 title객체로
        ![image-20220128003235903](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220128003235903.png)

- **농담하는 것 같지만, 고도화된 객체지향 시스템에서는 값이 등장하지 않는다.**

    - **`객체`만이 `역할`을 수행할 수 있고**
    - **`Type`만이 `책임`을 수행할 수 있다.**
        - **형이 아니면, 책임을 질 수 없고, 역할을 수행할 수 없다.**



- **아래 예제에서는 값1을 쓴이유: 작성자는 sequenceNumber라는 객체는 더이상 관여할 일이 없음을 확신하고 씀**
    - 대부분 이러한  착각때문에 시스템을 뜯어고치게 된다.
        - **값은 더이상 확장할 수 없다.**
            - **seat 100석이 더이상 바뀔일이 없다고 확신? 절대 하지마라**
        - 만약, 객체를 썼다면?
            - 포인터의 포인터를 통해 어떻게든 반응할 여력이 생김

 



#### DiscountType

![image-20220129001023447](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220129001023447.png)

- 일단 위의 구조는 틀렸다고 했다. 짜보면 안다.

- **DiscountCondition**할 것은 2가지다. 

    - 대부분의 **최소화된 인터페이스**가 가지는  **2가지 조건(발동 조건+액션)**을 가진다.

    ![image-20220129001057595](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220129001057595.png)

    1. **이 조건들을 만족하는가(hasNext = 발동 조건)**

    2. **Fee를 계산해봐(next = (발동조건 만족시)액션)** 
        - 2가지
        - **`iterator패턴` 중  `hasNext` -> `next` 쓰는 것이 중요한게 아니다. `아래 2쌍의 pair가 더 중요하다.`**
            1. `내가 발동할 조건을 가지고 있는지` 와
            2. `내 action이 무엇인지`
        - **여기서 DiscountCondition은 `실제 액션`하기 위해 쓰인 것이다.**
            - **더 추상화된 개념에서 보면 actor -> action을 할 녀석 -> 발동trigger를 가지고, trigger만족시 action한다.**
        - 하지만 **DiscountCondition는 자율적인 애가 아니라 `외부에 위탁`**한다.
            - **1. 발동 조건을 가지고 있는지 `외부에서 알려주는 인터페이스`**
            - **2. `본인이 진짜 action하는 인터페이스`**
                - **이런 수동적인 객체는 `lazy = 외부사정에 맞춰서 발동trigger를 확인후 -> 나를 action시킴`하기 위해서 작성된다.**
        - iterator패턴을 쓰는 이유 : 
            - **내가 원할 때** hasNext 호출후 trigger 만족하면 -> next로 액션하려고 씀.
            - **while이 아니라 `이 담에도 있어? -> 해`의 `능동형 queue = 내가 원할 때 작동하는 queue`를 만들 때 `while이 아니라 hasNext -> next의 iterator`를 쓰게 된다.**

- DiscountCondition은 **`인터페이스`로 2개의 `역할(책임)`을 동시에 수행하고 있다.**

    - 책임으로 보면, 2개의 책임 -> **<발동trigger>와 <action>을 분리가 가능**하다.
        - **발동 trigger**
        - **action**
    
- **`인터페이스`가 `2개 이상의 메소드(책임)을 가지면, 해체 가능`하다.**
  
    - **`인터페이스는 함부러 2개이상 메소드가지면 안된다!!` **
    - **`기본이 1개를 갖도록 분리시키는 것`이다.**



###### 메세지

- **세상에서 제일 좋은 함수: 인자 없는 함수 = `프로시져`**
    - 자율적으로 업무를 수행함
    - 유틸함수 -> 프로시져
        - 객체 -> 객체 내부 상태만 이용하는 객체
    - **`인자`가 없다  =  UML에서는 `dependency`라 부르는 것이 없다.**
        - 결합이 약해진다
    - **하지만, 우리 수준에선 못짠다.** 
    - **세상 2번째로 `좋은 함수`: `인자가 1개 만 있는 함수`**
        - 3번째로 좋은 함수는 ? 없다 
            - **인자 2개이상부터는 나쁜 함수**
        - **우리가 짤 수 있는 제일 좋은 함수 = 2번째로 좋은 함수 = 인자가 1개만 있는 함수**



- **어떻게 좋은 함수(=`인자가 1개` 뿐인 함수)를 만들까? **
    - 우리의 수준에서는 세상에서 제일 좋은 함수 = 인자 없는 함수 = 프로시져를 만들긴 힘들다.
    - 그렇다면 어떻게 인자 1개인 함수를 만들까?
        - **`모든 인자를 추상화 -> 객체화`시켜서 `객체를 1개의 인자로 받는 함수`를 만들면 된다.**
            - 언제나 옳은 정답이며, **`객체지향에서 메세지`라 부르는 것이다.**
    - 메소드의 인자를 정할 때, **전달된 인자가 2개이상**이다?
        - 메세지가 충분히 추상화 되지 않았다.
        - 메세지가 충분히 **Type(형)**이 되지 않았다.





- 세상에서 **제일 좋은 인터페이스**는? 

    - **메소드가 없는 인터페이스**
        - 진정한 역할은 역할자체만 나타냄 -> 어떻게 해야한다(메소드)를 정의해주지 않는다.

- **세상에서 `2번째로 좋은 인터페이스` = `메소드 정의가 1개만` 있는 인터페이스**

    - 메소드 정의 2개부터는 다 나쁜 인터페이스다

        - 우리의 예제도 나쁜 인터페이스 -> **인터페이스가 충분히 추상화되지 않아서**

            ![image-20220202211756025](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202211756025.png)

        - 하지만 책에서는 이정도 수준으로 만족하고 있다.

            - **메소드 1개짜리 인터페이스로 인도되려면, Layer가 지금보다 더 많아진다. ( 머리에 한계가 찾아옴.. ) -> 너무 깊은 추상화는 팀원들이 화냄**

                ![image-20220202211836162](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202211836162.png)





##### DiscountPolicy

![image-20220202213429164](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202213429164.png)

- policy야말고 마커 인터페이스다.
    - 책에서는 enum으로 정의하고 있지만, **enum은 Type이 될 수 없다.**
        - enum(에넘)을 type으로 generate하는 언어들도 있지만 java에선 허용하지 않는다.
    - **java에서는 interface로 정의한다.**

- **인터페이스를 DiscountPolicy를 상속받는 AMOUNT, PERCENT 등의 type들을 interface로 다시 정의해서 Type을 만든다.**
    - **인터페이스** DiscountPolicy**를 따르는 다양한 마커인터페이스를 정의**할 수 있다.
        - **마커 인터페이스 = 메소드가 없는 (인터페이스만 여러개) 인터페이스 = 세상에서 제일 좋은 인터페이스**





###### 더 못난 인터페이스(DiscountCondition) 공략

- **더 못난 인터페이스 DiscountCondition에 `메소드 2개`가 정의** 되어있다.
    - **`둘 중에 하나의 메소드를 막아야`한다.**

![image-20220202214044462](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202214044462.png)

- DiscountCondition에 있는 **인자 2개인 나쁜 함수 `isSatisfiedBy()를 막도록 공략`하자.**
    - **`방법:` 인터페이스 속 메소드 1개 처리를 `추상체(추상클래스)` SequenceDiscount`로 공략한다.`**
        1. **`생성자 인자로 sequence`를 받아들여, `sequence기반으로 isSatisfiedBy를 final로 더이상 안내려가게 잡아준다.`**
            - **SequenceDiscount를 상속하는 애들은,  만족하는지여부를 Screening의 sequence와 비교하는 로직은 다 똑같아진다.**
                - AMOUNT, PERCENT, COUNT, NONE이든 상관없이 모두 **sequence기반으로 `isSatisfiedBy 만족여부를 추상체에서 커버`하도록 한다.** 
                - 이후 상속받는 애들은
                    - sequence기반의 DISCOUNT가 된다.
                    - **차후 implements를 AMOUNT, PERCENT, COUNT, NONE 각각 분기처럼 나눠서 구현한다.**
        2. **추상체가** DiscountCondition를 **implements해서 isSatisfiedBy를 @ Override `public -> public final로 정의`해서 `이후 상속받은 구현체들은 정의못하게 == 그대로 갖다쓰도록 막아버린다.`**
            - DiscountCondition대신 공략용 추상체 SequenceDiscount를 상속하는 애들은 calculateFee만 정의시켜주면 된다.

- **2개 메소드를 가지는 인터페이스를 처리하는 전략: 1개 메소드를 xxx기반으로 처리되도록 정의하는 추상체(추상클래스)를 만든다.** 





![image-20220202220037834](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202220037834.png)

3. **추상체 SequenceDiscount를 상속받는 애들은 (ex> SequenceAmountDiscount)**
    - SequenceAmountDiscount를 하고 싶다면, extends는 SequenceDiscount로 상속받은 상태에서 **implements를 `DiscountPolicy.AMOUNT(분기처럼 선택해서)로 구현`해서 Amount개념을 입힌다.**
    - **이 때, 생성자에서 `sequence`는 `super()를 이용해서, 부모인 SequenceAmount에게 처리`해달라고 한다.**
        - **`sequence`의 처리는 부모(막은 추상체)에게 맞기고 우리는 현재기반인 `amount`를 생성자에서 받아준다.**
        - **my) `메소드 1개 처리는 추상체`로 막았지만, `인자는 2개 모두 이후 구현체`에서 생성자가 받긴 다 받는다.**
    - 그럼, **Amount기반으로 calculateFee만 정의**해주면 된다.



4. 같은 개념으로 Amount대신 Percent기반으로 sequenceDiscount에 적용해보자.

    ![image-20220202220951941](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202220951941.png)

    - 똑같이 SequenceDiscount받기, 구현implements만 `DiscountPolicy.PERCENT`로
    - 생성자에선, 부모가 처리해줄 sequence도 같이 받는다.
    - Discount정책중
        - Sequence정책 중 
            - PERCENT 정책으로
    - **받은 sequence는 부모(추상체)에게 super()로 맡기고**
    - **caculateFee만 percent를 곱해서 원래fee에서 빼준다.**





5. **추상체를 통해 메소드 1개처리를 막는 이러한 추상화는 4개 자식들의 공통조상이 되어버린다.**
    - **도메인, movie에서는 discountPolicy가 공통으로 묶였으면 했지만, 4개로 분리 되어버렸고, DiscountCondition중에 하나인 SequenceDiscount로 묶였다.**
        - **추상층을 Condition으로 시작하면 안됬다.**
        - **다시 추상화 해야함.**



##### implement inversion

![image-20220202225020093](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202225020093.png)

######  요구 인터페이스(DiscountPolicy) 공략

- 잘못된 공략: 문제가 원하는 인터페이스(DiscountPolicy)가 아닌 Discount**Condition**을 구현한 **추상체를 통해 메소드 1개 처리를 중간에서 추상체가 처리하여 막았다**.
    ![image-20220202221639981](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202221639981.png)
    - **이번에는 문제 `반대방향으로 implementation`하면서 Condition( Sequence -> isStatisfiedBy()막기 )가 아닌 `AMOUNT -> calcuateFee()를 막는 추상체`를 생성해서 1개 메소드를 막아보자.**

![image-20220202221617404](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202221617404.png)

1. **원하는 interface인 `DiscountPolicy.AMOUNT`를 implements하면서, 동시에 `DiscountCondition`도 implements **
    - **AMOUNT계열의 Condition임을 나타내고 있다.**
        - 그러나 **AMOUNT처리가 메인**이며 -> 관련된 메소드인 calculateFee를 
            - public -> **@Override `public final`** 
            - **추상체가 calculateFee()를 커버**함.
                - isSatisfiedBy는 sequence/time의 처리영역
                - amount -> fee.minus(amount)
            - **관심사가 sequnece가 아니라 amount(Money)이므로 생성자에서 amount를 받는다.**





![image-20220202231618756](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202231618756.png)

- 2번째 추상클래스는 AMOUNT대신  `오타` -> Discount`.PERCENT` && DiscountCondition을 상속한 추상체를 만들면
    - calculateFee를 percent단위로 discount하게 추상체를 정의한다.
        - PERCENT계열의 부모가 된다.



- **AMOUNT or PERCENT계열의 부모**
    - 공통적인 처리는 **policy -> calcuateFee()로 묶게 된다.**



- **discountCondition이 2개의 메소드를 가져서 문제 -> 분리가 필요했었다. -> 1개의 메소드를 중간 추상체를 만들어서 정의시킨다.**

    1. isSatisfiedBy 를 추상체에 올려서 정의 -> 아래쪽에 calcuateFee를 각자 정의 
    2. **반대로** calculateFee를 추상체에 올려서 정의  -> 아래쪽에 isSatisfiedBy를 각자 정의
        - **인터페이스 2개 이상(Condition, Policy)이 모이면 경우의 수가 폭증되는 문제점**
            - **도메인이 무엇과 일치(여기선 Policy정책을 기준으로 도메인이 정해짐)하는가를 찾아서 -> 기준이 되도록 추상화 만들면 된다.**

    









![image-20220202232240453](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202232240453.png)

- 이제 Policy가 추상화된 상태에서 -> Sequence + Policy(AMOUNT or ~) + Discount (SequenceAmountDiscount)를 만들어보자.

    - **받은 amount는 `부모(= 1개 메소드 처리해준 추상체)에 보내서 처리`하고**
    - 이제 `is~`쪽을 각자 정의해주면 된다.
        - sequence가 같은지를 비교한다.

- 다른 Policy를 선택해서 sequenceDiscount를 만들어도 똑같이 `is~`만 정의해준다.

    ![image-20220202232539870](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220202232539870.png)



- 이러고 봤더니, `sequence`관련된 로직들은 `어느 policy`든 공통되는 것을 확인했다.
    - **implements는 여러개 가능하지만, `상속은 1개 밖에 안된다.`**
        - 인터페이스 2개메소드 -> 메소드 1개 처리용 중간 `구상체` -> `상속`한 구현체
        - **상속 구현체의 한계 =  `병합 되어있는  여러 경우의 수(자식들)에 걸린   코드중복`을 제거할 방법이 없다.**
            - **`별도의 [전략 객체]를 받아들이는 수 밖에 없다`**
                - cf) 코틀린은 classby를 선언하면 자동으로 해준다?!
            - **`생성자에서 sequence처리 로직을 갖는 전략객체를 외부에서 수입 -> 받아서 해결`** 
    - **우리가 원하던 `추상화` 계열은 `도메인 요구 -> policy`별로** 
        - `AmountDiscount` or`PercentDiscount`를 상속받아서
            - SequenceAmountDiscount or SequencePercentDiscount를 만드는 쪽
                - 원하면 TimingAmountDiscount  or TimingPercentDiscount도 만드는 쪽  
        - **movie에 꽂을 수 있는 `특정 policy`를 따르는 객체들이 된다.**
    - seqeuence이외에 나머지를 직접 구현해보자.



##### Movie

![image-20220203143256759](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203143256759.png)



###### 제네릭

- `제네릭`을 통해 `if문을 완전히 제거`할 수 있다.
    - cf) 라우터: 조건에 맞는 객체를 생성 (객체처리기?) -> if문이 사라짐





- **추상클래스 등이 잘 지원되는 jvm을 쓰는 동안은 `제네릭`의 필요성을 거의 못느낀다.**

    - 제네릭외 해결방법이 많으므로
        - but swift같이 철저히 프로토콜, extends만 지원하는 언어들은 대부분을 제네릭으로 해결한다.
    - **my) `T 제네릭`을 통해, `policy와 condition 2개를 다 만족하는 추상체 -> 의 하위객체`를 movie `T`에 꽂을 수 있따.**

- 제네릭을 깊이 공부하다보면 **상위경계 / 하위경계 라는 개념이 나온다. 그리고 각각을 조합**할 수 있다.

    ![image-20220203143555853](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203143555853.png)

    - **`DiscountPolicy 이자 Condition을 만족하는 T`ype(의 하위)만 올 수 있다.**

        - **여기오는 `T`형은 반드시 `Policy이면서 Condition`이어야만 한다.**

            - **상속은 안되고 `2개 동시에 implement를 통해 만든 것`이어야한다.**

                - **제네릭T의 경계 조건(2개이상 사용가능) -> 필요한 것만 받는다. -> if분기를 대체할 수 도 있다.**

                ![image-20220203143917090](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203143917090.png)



- `제네릭` -> **`형`에 따른 if분기를 컴파일러에게 맡긴다.**
    - if를 제거하는 유일한 방법: 바깥에 if분기를 위임 -> 거기에서 **객체처리기**로 객체 생성 -> 받기
        - **바깥에 위임한 `객체처리기`조차 `형`으로 대체할 수 있다. -> `제네릭`**
            - **if분기 대체를 `객체 -> 형`으로 대체하는 방법**
        - 형으로 대체해서 집어넣는 것만으로도 if를 대체할 수 있으니 제네릭을 사용한다.



- 이 **제네릭 T의 `상위경계(bound)` --->  `if에서의 조건`을 의미**하게 된다.
    ![image-20220203144804236](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203144804236.png)

    - `각 if에서 맞는 객체처리기 객체`---> 상위경계를 만족하는 추상체를 `상속받아서 구현한 자식class들`

- **`if문`을 쓰는 대신 -> `각 case에 맞는 interface를 상속 or impl한 추상클래스를 만들고` ---> `그것(들)을  상위경계로 하는 제네릭<T extends>`를 사용해서 대체할 수 있다.**

    1. **`2중 if문`을 -> 각 조건(policy + condition)마다  interface로 만들고, **
        ![image-20220203145753014](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203145753014.png)

        -  **2개의 인터페이스로 빼낸 뒤**

        - **1개를 막아주는 구상클래스로 바꿈**

    2. **2개 조건을 각각 implementation으로 넘어오게 하는 추상클래스를 구현한다.**

        ![image-20220203150021161](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203150021161.png)

        - 컴파일러가 각 조건에 맞는 코드를 내부에서 구현하게 된다.
            - 컴파일러가 제공해주는 Type의 router로 봐도 된다는 개념

    3. **`class의 이름`에서부터 `이중 if조건`이 걸려있도록 정의한다.**
        
        - A: Sequence & B: Percent인   Discount
            ![image-20220203145649869](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203145649869.png)





- 코드에서 if를 안보이게 하는 방법
    - router도입이 아니라 `제네릭`을 도입하는 것이다.
        - if문 자체가 -> 컴파일러로 숨어진다. (사용자코드에서 존재하지 않게 됨)



- **응용: `if조건이 3개`다? --> `implements에 3개가 오는 추상클래스`를 구현 --> 제네릭이 상위경계로 받으며 구상클래스(Moive) 정의**
    - 조건의 갯수 = 추상클래스가 implements하는 갯수
    - **조건의 분기갯수 = 각 추상클래스마다 정의해놓아야하는 구상클래스**
        - 제네릭은 이 구상클래스들을 `<T extends 조건1구상 & 조건2구상 & 조건3구상>`으로 상위경계를 잡는다.
            - **재귀함수를 loop로 바꾸듯이 익숙해져야한다.**
                - 재귀함수 -> 꼬리물기 최적화 -> loop
            - **if문 -> 제네릭으로 분산시키는 것도 자연스러워야한다!**
                - 제네릭 분산시스템 -> 묶어서 -> if문 객체들로 바꾸는 것도 자연스러워야한다.





##### 다시 movie

![image-20220203151117547](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203151117547.png)

- **movie가 제네릭인 이유?**

    1. **movie에 적용되어야하는 조건이 2가지다.**

        1. 조건갯수만큼의 추상클래스 -> 그 조건의 분기만큼의 구상클래스 中 1개 선택
        2. 그 조건의 갯수만큼 extends로 연결한 제네릭을 사용

    2.  **조건에 대한 경우의수를 받아들이는 조건을 `제네릭 - 상위경계`로 받아들여서 class를 정의한다.**

    3. **생성자에서도 `T`제네릭형에 맞는 condition을 받아들인다.**

        ![image-20220203151416317](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203151416317.png)

        - T형의 `conditions`는 반드시 상위경계의 하위 형으로만 올 수 있기 때문에 **일관된 policy를 가진 condition이 올 것이다.**



- conditions들을 Set으로 만들어서 추가로 집어넣는다.

    ![image-20220203151616732](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203151616732.png)

    - **list를 쓴다? `값 context`를 생각하고 있는 것이다.**
        - **`0번과 1번 element가 반드시 다를 것이라고 생각`한다면, `set`을 안쓸 수가 없다.**
            - **객체 context -> 식별자에 의한 구분만 한다? -> list 쓸리가 없다. set을 쓴다.**
        - list의 index -> 값 -> 이것에 의존해서 <비록 같은 객체라도> 0번과 5번을 구분한다. -> 값에 의존한다.
    - **컨테이너에 값이 아닌 객체로 식별하고 싶다 -> set만 사용해서 집어넣는다.**







![image-20220203152018279](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203152018279.png)

- Movie가 할인정책(`discountConditions`)과 원가(`fee`)를 알고 있기 때문에, **정보전문가패턴으로? Movie만이 calculateFee()를 할 수 있다.**
    - 어떤 상영인지, 관객수 를 외부에서 받아서 계산한다.
    - 할인조건은 여러개일 수 있다.
        - for문으로 돌면서 현재에 맞는 조건을 찾는다.  -> **맞는 조건 = 여러조건들 중 발동조건 = `isSatisfiedBy()`**
    - condition에 내제된 있는 계산방법을 통해, 인원수만큼 multi해서 return해준다.
- 여기서 애매한 점:
    - moive calculateFee에 audienceCount를 곱하는 책임을 가져야하냐?
        - 미래에 fee를 계산할 때, **관객수에 따라 fee에 미칠 것 같았으므로, movie에게 audienceCount를 넘겨서 condition내부의 multi를 수행하는 권한을 줬다.**
            - 역할
                - 권한: 내부 나만이 알 수 있는 것들 (알 책임)
                    - 니가 그런 권한이 있으니, 그것을 할 수 있지로 설명
                - 책임: 외부한테 수행하는 메세지 (할 책임)
        - 맨 처음에는 audienceCount가 isSatisfiedBy()를 판단하는 인자 중 1개로 들어갔다.
            - 시간대 말고, `몇명이 예약했는지에 따라 더 깍아주기 위해서 받아들였었다.`



- if 발동조건에 안걸렸더라면  (`return안되고` 생략된 else 부분쪽으로 빠져나오면 ->) 할인없는 `할인없이 fee를 받환하도록 return`걸어준다.









##### Money

###### value object

![image-20220203154006848](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203154006848.png)



![image-20220203154025289](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203154025289.png)

- 객체처럼 **참조의 참조**가 가능(껍데기 -> 업데이트된 내부 접근)하지만,
    - **값의 특성도 가지고 있는 박쥐같은 녀석**이다.
- **값객체의 참조는 -> 새로운 참조(새 객체)를 return하게끔 되어있다.**
    1. **불변성으로서 값은 불변이다.**
    2. **값이 변할때마다 `새 객체`를 return한다.**
        - 유용하기 때문에, 다른언어들은 내장하고 있다. ex> swift-structure
        - 객체기 때문에 참조의 참조가 되긴한다.
            - **그렇지만 복사본만 return되고 값은 불변**
        - **java에선 지원안해서 직접 구현**해야한다.





- **자세히 보기**
    1. **처음에 `final`로 Double amount가 `확정`된다.** 
        - **모든 필드는 final이어야한다. (불변특성을 확정하고 있다)**
    2. **일반적인 연산에서도 `new Money()`의 새로운 객체를 return하고 있다.**
        - 값을 갱신(변경)할 수 없으니, 새로운 값을 가지는 객체를 만들어 return한다.
            ![image-20220203161047062](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203161047062.png)
            - 뺄셈에 있어서는, 본인amount > 받아온 amount의 크기를 비교하고, 작거나 같으면 하한선 0을 return하게 한다
        - **multi()는 amount(Money)객체가 아니라 Double times인 값을 받아들인다.**
            - **audienceCount를 객체가 아닌 값으로 처리했던 응보를 받는 부분이다.**
            - 어떤 객체context에서라도 단 1개의  primitive를 채용하면... -> 전역적으로 값을 쓰는 이질적 api를 생성하게 되어버린다.
        - **되도록이면 객체로 바꿔야한다.**
            - times는 money와는 다른 것 같지만, times를 객체로 처리했고 & times와 money를 추상화했을 때, **abstractNumbers의 공통 추상체**가 만들어질지 아무도 모른다.
                - 객체로 만들면 추상화될 희망이 있다.
                - 2개의 책임 모으기 -> 1개의 역할 도출의 추상화 할 가능성이 있다.
                - 역할을 상속받은 2개를 만듬 -> 한번에 처리될 수 있음.
            - **primitive가 온 순간 확장(추상화 등)은 없다. 반드시 객체가 오도록 하자.**



![image-20220203161813546](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203161813546.png)

- greaterThan()과 같은 비교연산도 얄짤없이 내부api로 정의해서 은닉화된다.

    - **amount는 private한 속성으로서 외부에 노출안된다.**
    - 외부에 boolean 값을 return하는 경우에도, 내부에서 연산해서 건네준다.

- 값 객체의 장점

    1. **번거로워도 값객체 -> private필드로 내부에 데이터 은닉 -> 외부는 정의한 api를 통해서만 대화할 수 있다. -> `연산의 안전성 보장`.**

    2. **`new 새객체를 return`하기 때문에 -> 기존 상태에 간섭할 수 없음.  -> 객체임에도 `동시성 문제에 자유로워진다`**



- 확정된 도메인, 확정된 연산 -> 값 객체로 바꾸는 것을 고려하자
    - 이유: 동시성 문제에 자유로워지니까







##### Reservation(value object2)

![image-20220203162318753](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203162318753.png)

- 이펙티브 자바에서 나오는 **null대신에 쓰는 static final `NONE`객체 나온다.**
    - NONE 객체는 불변으로final, 돌려쓰기static는 객체인가보다.

- 그외에 **`예약`은 순수한 데이터 정보 -> 다 `final`이다.**
    - 극장
    - 영화
    - 상영시간
    - 명수
- **Reservation도 순수한 `값객체`이다.**
    - **연산을 제공하지 않아서 `복사본 new Reservation()`이 없는 것 뿐이지, `모든 필드가 final`인 값객체다.**
    - **다 `final인 값 객체`는 return새객체가 없어도 `만들고 나면 못건듬=변경불가=다final`이라서  `쓰레드안전`**







##### Screening(value object3)

![image-20220203164740902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203164740902.png)

- **Screening도 다 final로 들어오는 순수 데이터 정보라서 `값객체`**다. 
    - **단지 `seat`만 private이면서 final이 아닌데, `TRIGGER(is~/has~) 와 -> ACTION`을 통해 발동조건에 따라 변동(count보다 많을 때, count만큼 까야지 완성)이 있기 때문이다.**
- **객체 생성시 `trigger`(bool반환 메소드) 와 그 trigger발동조건 만족시 `action`이 있다면?**
    - **`외부 상황(count)`에 사정에 따라 맞춰서 사용되는(`lazy하게 사용`) 것들일 경우, 최소한의 인터페이스 2개 (trigger + action)를 정의한다.**
- trigger: 현재 Screening이 외부 count만큼 seat를 가지고 있나??
    - count만큼 예약해줘 -> 가지고 있는 seat에서 까줘(Screening이 할 수 있는 것은 전체seat수에서 count만큼 빼는 것)
- action: trigger를 만족할 경우에만 처리
    - 안그럼 thr





##### Theater 업글하기

![image-20220203165805132](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203165805132.png)





![image-20220203170013057](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203170013057.png)

- amount -> 값객체 Money로 업그레이드

- 각 Theater마다 계약하고 있는 TicketOffice Set들이 있다.
    ![image-20220203170213819](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203170213819.png)

- **각 Movie마다, movie에 맞는 Screening Set들이 있다.**

    ![image-20220203170121495](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203170121495.png)

    - **TicketOffice처럼 그냥 Set<>으로 정의해도 될 것 같지만, `Movie에 Screening이 없는 경우`에 편하게 `map.empty()`처럼 편하게 메소드를 쓸 수 있도록 `set위에 hasmap을 씌웠다`.**







![image-20220203170438126](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203170438126.png)

- addMovie**(key추가)**시
    - 이미 존재하는 거면, add의 결과로 false를 / 그게 아니라면 **새로운 movie마다 set에 보관할 수 있도록 빈set을 생성**해준다.
- addScreening(**value추가**)시
    - 해당 movie에 screening을 추가해주는 것이므로 2개의 인자를 받는다.
    - movie가 아예 존재하지도 않으면, false로 끝낸다.
    - 있으면, movie마다 빈  set에 있던 곳에 screening을 넣어준다.



![image-20220203201948747](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203201948747.png)

- rate를 가지고 특정 office와 계약한다
    
    - office에서도 계약을 해보고 잘됬으면, 여러 offices들 중 한개로 추가한다.
- remove로 계약을 cancel할 수 도 있다.

- **plusAmount(Money amount)**

    - **ticketOffice가 표를 팔았으면, theater의 재산을 plusAmount**해준다.

    - 가지고 있는 값 객체(amount)를 **plus해주면 -> new Amount새 값객체**가 나오고 -> 다시 **재할당**해준다.

        - **값 객체의 변동은 새 객체 -> 재할당해줘야하는 것이 숙명** 

            ![image-20220203202450918](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203202450918.png)

            - **`this.amount`같은 `값 객체의 할당부분을 외부`에 노출해선 안된다.**

        - 값 객체는 `포인터(Money)의 포인터(amount)`를 깨먹고 있다.

            - `amount의 참조`를 바꾸고 싶지 않았다. 
            - Money객체(외부)는 그대로 있고, double amount(내부)만 바뀌었으면 좋겠는데
                - Money객체(외부)가 새객체로 재할당되었다. 
            - `this.amount` 자체의 `포인터`가 변경된다.( **my) 내부 내용만 바뀌던 것이 `껍데기 자체가 바뀜`** ) 
                - 기존Money -> 새 Money객체 by `this.amount.plus(amount)` - `값객체연산`
            - 따라서, **값객체를 할당하고 있는 field**(`this.amount`) **= 값객체 껍데기 필드**가 외부에서 노출안되게 한다.
                - `this.amount` =  값 객체(껍데기)를 가리키는 포인터
                    - **값객체의 내부 연산시  -> 새객체할당 -> 껍데기 자체가 바뀐다.**
                    - **이 새객체 할당 to `this.amount(값 객체 포인터)` 부분이 외부에선 드러나선 안된다.**
            - `+= `의 증감이 아니라 `=`새객체 재할당부분이 외부에 노출되었다 ?
                - **그 외부에 노출된 부분은 -> `새객체 재할당 이전의 옛날 Money(값객체)`를 가지고 있을 것**

    - **my) 값객체: `객체 사용` = `껍데기->내부필드` =`포인터의 포인터` 과정이 없어지고**

        - **`값 객체 할당부분(this.amount = )` = `껍데기 보단 내부필드나 마찬가지`  -> `외부에서 쓰지마. 내부 부분이나 마찬가지 이므로, 한번 노출되면 업데이트 안됨. 외부노출된 것들은 과거의 것 가지게 됨.` **
        - 값 객체의 할당은 **뒤쳐지기 싫으면, `내부 연산 -> 새객체 할당에서만 사용`**

    - **my) 일반적인 값의 변화**

        - 원래 객체로 쌈 ->  껍데기에 접근 -> 내부에서 원시필드 한번 더 접근
            - **`내부 원시필드 변화` -> 껍데기(포인터1)에 접근하면 -> 업데이트된 내부원시필드(포인터2)를 받아볼 수 있다.**
            - 값 객체는 **`외부 껍데기의 변화` -> 내부는 final로 고정된 값. 아예 외부껍데기 1개밖에 존재 안함 -> 어떻게 접근하든 업데이트는 안됨 -> 외부에서 사용금지**

- **값객체의 외부노출은 -> `값으로 환원해서 외부노출`밖에 없다.**

    - 값객체의 변화는 껍데기->`내부필드 변화`가 아니라,  `껍데기 자체의 변화`이므로 업데이트를 받아볼 길이 없기 때문
    - **값 객체의 숙명**
        - **값객체를 그대로 외부에 노출한다면? -> 값객체연산후 새객체가 들어옴 -> 참조가 깨지게 됨.(옜날 객체에서 업데이트 안됨)**

    

![image-20220203204559699](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203204559699.png)

- getScreening은 movie에서부터 screening Set 리스트를 얻어내는 것
    - movie가 존재하지 않으면 null return아니라 EMPTY객체 return
- isValidScreening: 상영중인지 확인하는 인터페이스
    - 이 movie가 이 screening을 가지고 있는지 감정해주는 것
        - movie를 가지고 && 그 screening을 가지고 있다



![image-20220203204801307](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203204801307.png)

- enter
    - Customer가 오면, 거기에 있는 reservation을 가져와서
        1. NONE이 아니다.
        2. 예약속.theater == this(현재 극장)
        3. 예약속 movie-screening이 유요한지
        4. 예약속 count == 현재 들어온 사람수 맞는지 다 통과하면 true가 반환된다.

- reserve
    - movie, screening, count를 보내면
        - movie, screening이 valid한 것인지 확인
        - screening한테 hasSeat있는지 물어본다
            - 둘중에 하나라도 아니면 -> NONE객체 반환
    - screening한테 seat를 count만큼 까라고 시킨다.
    - 극장(this)를 포함해서, movie, screening, count다 포함시켜서 -> Reservation을 만든다.
        - **요청을 받게 되면 무조건 예약 발급된다? **
            - **`public이 아니다` = 내부만 호출한다 = 계약맺은 애들만 reserve를 호출 할 수 있게 한다. **
    - **`reserve호출 여부 판단`은 변화율이 크다 -> 외부로 ticketOffice한테 뺐다.**





##### TicketOffice

![image-20220203205814111](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203205814111.png)

- ticketOffice는 자본금인 amount를 가지고 있고
- Map을 통해서 <극장마다, 얼마의 할인율Rate>로 계약맺었는지를 가지고 있다.
    - contract()할 때, map에 집어넣는다.
    - cancel()을 통해, map에서 빼낸다.



![image-20220203210611290](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203210611290.png)

- **핵심적인 것은 TicketOffice에게 `TicketSeller`가 `reserve`해달라고 요청한다. **
- 이게 TicketOffice에게 있는 `reserve()`의 역할이다.
    - **궁극적으로는 theater에게 reserve해달라고 한다. 그전에** 
        - office에 계약된 theater인지 확인하고
        - 극장에게 상영중인 movie인지 물어보고
        - 스크리닝한테 seat자리 있는지 물어보고
            - 만족안하면 reservation불가인 NONE객체로 처리한다.
    
- 극장에게 **reserve요청한 결과물  reservation이 NONE만 아니면 -> 제대로 예약된 것이다.**
        - NONE 객체라면, 그 NONE객체를 담고있는 결과물변수를 return해주면 된다.
    - 극장에서 reservation이 되었다면?
        - movie에게 calculateFee()를 때려서 총매출을 구하고
        - 총매출에서 commissionRate를 곱해, 내(Office)가 먹을 커미션을 구한다.
        - 내 재산amount에 commision만큼 더해주고
        - 극장에게는 **총매출 - 커미션**만큼을 매출로 더해준다.
    - 여기까지가 Office에서 하는 reserve





##### TicketSeller

![image-20220203211346707](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203211346707.png)

- tickerOffice와 계약맺는 절차가 있고
- reserve는 Customer와 한다.
    - **일단은 기본적으로 Reservation이 없다고 생각하고 진행한다.(중첩if의 else마다 할당될 값을 else 없이 & 미리 할당해놓음)**
    - 일단 movie에게서 price를 가져온 뒤
    - customer에게 그 돈이 있는지 일단 물어본다.
        - customer가 돈있는게 확인되면
        - Office에게 reserve()로 reservation을 받아온 다음, 
        - 유효한 reservation이라면, customer에게 돈을 뜯어낸다.
    - NONE이든 유효한 reservation이든 -> reservation을 return해서 끝낸다.





##### Customer

![image-20220203212017919](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203212017919.png)

- **일단은 기본적으로 Reservation이 없다고 생각하고 `NONE객체를 가진체` 진행한다.**
    - TicketSeller도 이걸로 시작 -> 받아오기 시도 -> 없으면 그대로 NONE객체 반환
    - Customer도 이걸로 시작 -> 받아오기 시도  -> 없으면 그대로 NONE객체 반환
- seller에게  seller.reserve()를 요청하는 reserve를 정의한다.
- hasAmount와 minusAmount는 값객체를 외부에 공개해야하는 api





#### Main 다시보기

![image-20220203212055140](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203212055140.png)

- Movie를 만들 때

    - **제네릭 <AmountDiscount>를 상위경계 T로 지정했으므로**
        - 그 자식인 Sequence + AmountDiscount를 받았다.
        - XXXX +  AmountDiscount를 다 받을 수 있게 된다.
            - **if문 2개를 대체한 것 ( Amount vs Time ) -> 그 추상체 자식들 중 ( Sequence vs .... ) **
            - Sequence 자리의 XXX는 DI(injection)이 주입해준다.
        - **제네릭과 친해지면 if문이 완전히 없어진다.(router나 외부 객체처리기 없이 if문 다 없어짐)**
            - IF문 -> 제네릭이 대체함.
            - 제네릭에서 인젝션을 DI가 해준다.
                - **우리는 인젝션할 DI의 객체만 바꿔주면 된다.**

    ![image-20220203212309896](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203212309896.png)

    - sequnce가 1일 때, amount 1000원을 깍아주는 discount policy다.

- theater에게 movie집어넣기
- theater에게 movie마다 여러개의 screening들을 map에 집어넣어서 movie-screening들쌍을 만들어준다.



![image-20220203213050132](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220203213050132.png)

- ticketOffice를 만들고-> theater와 계약을 하고

- seller를 만들어서 -> ticketOffice와 계약하고

- **customer만드는 코드가 빠짐**

    ```java
    Customer customer = new Customer(Money.of(20000.0));
    ```

- screening이 돌아가면서

    - customer가 seller와 예약을 함
        - **예약시 customer내부에 reservation객체를 가지게 됨.**
    - reservation을 가진 customer가 theater에 enter입장함
        - 결과를 return받아서 출력







- 4장, 5장 일부까지 정리
    - 실제 워킹하는 코드임.
    - 본인만의 역할모델 짜보기






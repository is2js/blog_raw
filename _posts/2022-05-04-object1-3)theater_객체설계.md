---
toc: true
layout: post
title: OBJECT 03 객체설계1_Theater(코드스핏츠)
description: object 책을 강의한 코드스핏츠 유튜브 요약

categories: [object]
image: "images/posts/java.png"
---

- 참고 유튜브 : https://www.youtube.com/watch?v=navJTjZlUGk
- 정리본: https://github.com/LenKIM/object-book
- 코드: https://github.com/eternity-oop/object
- 책(목차) : https://wikibook.co.kr/object/



### ch3. Theater

- 맛보기로 theater
    - 책에서는 절차지향 -> 객체지향으로 가는 과정을 설명하기 위해 만들어서, 조금 허술함
    - 책 뒤쪽의 역할모델 설명처럼 정교하지 않다. 
    - 책에 없는 역할모델을 옮기는 과정을 해볼 것이다.





- 책에 나온 상호의존적인 표
    ![image-20220122213654603](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122213654603.png)
    - 극장이 seller와 audience를 둘다 알고 있다(화살표를 보면 됨)
        - seller -> ticketoffice -> ticket 를 알고 있다.
        - audience -> bag -> invitation  +  ticket
            - bag은 invitation  +  ticket을 둘다 담아야하기 때문에, 둘 다 알고 있다.
            - ticketoffice는 ticket을 알아야지, seller에 줄 수 있기 때문에 관계를 맺고 있다.



- 책에 나온 엉망인 부분 체크(빨간색)
    ![image-20220122214003688](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122214003688.png)
    - theater가 enter()를 구현하기 위해서,자기(theater)가 가지고 있는 seller에게 audience를 pass해준다.
        - 그럼 theater는 enter()가 중요메서든데, seller에게 audience에게 pass만 한다.
            - 그렇다면, theater는 audience를 건네주기 위해서  seller를 알아야(가지고있어야)한다는 건가?
            - 극장이 seller를 알아도 되나?  tickeroffice는 몰라도 되고?
        - seller는 tickeroffice를 알고, audience도 알고 있기 때문에, 중간역할을 하는 것처럼 보인다.
            - 책에서는 audience하고 office하고 연결하면 연관성이 생겨 더 힘들어진다고 표현한다.
            - 왜 그런 문제들이 생길까 -> 책의 설계가 잘못되었기 때문에 
                - 잘못된 설계를 보여주는 이유: 절자지향 -> 객체지향으로 옮기는 과정을 보여주기 위한 설계일뿐이기 때문



#### diagram

- 책을 고친 표
    ![image-20220122214556326](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122214556326.png)
    - 극장이 표 파는 곳을 모를 수 없다. **객체지향에서 의인화 = 실제 역할을 생각함**
        - theater -> ticketoffice
    - 극장 본인이 표를 발급하니까 표를 모를 수 없다.
        - theater -> ticket
    - theater - ticket - ticketoffice만 두고 얘기를 해보면
        - theater이 발급한 ticket을 ticketoffice에 맡긴다
    - ticketoffice는 쌓인 ticket들을, 팔도록 ticketseller에게 맡긴다.
        - ticketoffice -> ticketseller
    - 극장이 ticket을 발급하듯, invitation도 만들고, audience에게 제공한다.
    - 극장이 audience를 알아야하는 진정한 의미는
        - **audience**를 **입장(enter())**시키기 위해서 알아야한다.
        - audience에게 invitation(초대장)을 넘겨주기 위해서 알아야한다.
    - **theater**은 자기가 발급하는 **ticket** / ticket판매를 위한**ticketOffice**를 알아야하고
        - ticket 을 **--->** ticketOffice에게 맡길 것이고
        - ticketOffice는 **--->** **ticketSeller**를 고용하고
        - ticketSeller는 **--->** ticket을 팔아야하니 알고 있어야한다.(모르는데 팔 수 없음)
        - **audience**(고객)은 **--->** ticket가 존재하는지 / **invitation**(초대장)이 있는지 확인을 위해 알아야한다.(모르면 매진여부/초대여부를 알 수 없음)
        - audience(고객)은 **--->** 실질적으로 ticketSeller에게 표를 사니, 알아야한다.
            - 매진/초대여부를 위해서 ticket, invitation을 알아야하지만, 실질적으로 관계되는 것은 ticketSeller이다.
        - 반대로 ticketSeller는 **--->** audience(고객)에게 표를 팔아야하니, **서로** 알아야한다.
            - 트랜잭션관계로서, 서로 상호관계를 한다. 



- 표에는 시간이 없다고 가정하고 한다.

#### Theater

![image-20220122223342652](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122223342652.png)

- 표(ticket)를 발급하는 곳 = 표의 가격(fee)을 알 수 있는 유일한 곳(가격 책정하는 곳)
    - final로 fee를 확정할 수 있다.
    - getFee()로 fee를 알려줄 수 있다.
    - 이 극장은, 이 fee로 ticket을 발급할 예정이다.



![image-20220122223710967](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122223710967.png)

- theater는 <다수의> ticketOffices들과 거래한다.
    - 인터파크, yes24 .. 등 티켓판매소는 여러군데다. 온라인/오프라인 포함 여러군데다..
        - 인자에 다수의 ticketOffices를 수용(극장에 set)한다.
            ![image-20220122223905940](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122223905940.png)

- ticketOffices들이 ticket을 받아오는 방법은? theater가 주는(set) 수 밖에 없다.

    ![image-20220122224436018](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122224436018.png)

    - theater가 set(준다) + (특정ticketOffice에게 ,  원하는 수량만큼Long num) **주는** 수밖에 없다.
        1. ticket발급해줄 ticketOffice가, 내가 거래하고 있는 **(set된 오피스) 중 1곳인지 확인** 한다.
        2. 발급을 해주기 위해, **발급수량을 1개씩 숫자를 깍으면서 발급**해준다. ( `while (num-- > 0)` )
        3. **발급은** ticketOffice 내부에 addTicket( new Ticket(**this**));로 객체로 발급하는데, 중요한 것은 ticket이 **어느극장 발행인지 this로** 넘겨준다..
            - 원래는 티켓에 new Ticket(this = 발행극장,  상영시간, 영화 편수?, .... )
            - **해당 극장만이, 표를 특정지어 발급할 수 있기 때문에, this를 필수로 건네줘서 발급한다.**
                - 거래하는 오피스에 num수량만큼, 발급해주는데, 발급극장이 유일한 발급의 주체라서... this로 건네준다?
    - 여기까지 theater가  수많은 ticketOffices들을 거느릴수 있게 되었다.







![image-20220123003141459](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123003141459.png)

- theater와 audience사이에도 invitation을 준다(theater.set(고객 고객객체) -> (내부) 고객.set() )의 관계가 있었다.
    - A가 B에게 C를 준다. -> A.setC(B b) { b.set( new C(this) ) }
    - 극장이 특정 귀빈(고객)에게 초대장을 준 것이다.
        - 고객을 인자로 받은 뒤
        - **고객은 (초대장을) 받는 메소드( 자기자신.set( 물건 )  )도 준비되어있어야한다.**
            - **`준다` = `주는사람.set(줄 사람)` , **
            - **`받는다` = `자기자신에게 set` = `자기자신.set( 물건 )`**으로 해결한다.
    - **`theater가 audience에게 뭔가를 줄려면, audience는 받는 서비스(메소드)를 준비하고 있어야` 줄 수 있다.**
        ![image-20220123004841666](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123004841666.png)
        - audience가 setInvitation(  새 초대장 )할 수 있는 메소드를 제공했기 때문에 theater가 줄 수 있다.
    - 초대장 invitation도, ticket처럼 **theater가 발행한 것 = 객체생성시 this를 넘겨줘서 발행극장을 표기하는 것이 중요**하다.
        - **주는 물건을 구분짓는 생성자인자가, 값(시간, 가격)이 아니라 `이 극장 = theater =  this = 객체 = 식별자`가 들어간다는 것이 중요하다**
        - 값인 **`theater.name`이런 것이 들어가면 안된다!**
            - name갔다고 같은 theater인가? (X) 
            - **객체는 식별자로만 구분이 되므로, 식별자가 들어가야한다.**



![image-20220123005252707](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123005252707.png)

- 마지막으로 audience을 입장시키는 enter()메소드가 필요하다.

    - theater는 audience를 enter(입장)시킨다.
        - 입장하는것은 누가봐도 고객

- 극장이 하는 것은, audience에게 ticket을 받아내는 것이 주임무이다.

    - 티켓판매 등은 극장이 해야할 일 아니다... 판매는 seller가 하는 것.

    1. audience에게 .getTicket()으로 자기 표를 받아내고
    2. 유효한지 확인한다.
        - **표가 유효한지는 극장이 검사할 필요가 있나? 표자체로 유효한지 검사할 수 있다.**
        - **표 자체로 유효한지 판단할 수 있으나, 발행극장확인을 위해서는 theater(this)가 필요하다**
            ![image-20220123005710341](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123005710341.png)
            - enter()시 표한테 유효판단을 맡기는데, **현재 극장(this)이 발행한 표인지 확인하려면, 현재 극장의 식별자가 인자로 필요**하다.

    





#### Ticket

![image-20220123210927966](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123210927966.png)

- theater가 알고 있는 3인방 중 ticket에 대해 알아보자. 
- **ticket은 생성될 때부터, 자신의 발급주체인 theater를 알고 있다.**
    - 이 theater에서 발급된 표임을 생성자에서 받아 증명해준다.
    - **따라서 발급주체는 절대 불변하도록 `final`로 theater를 잡아둔다.**
        ![image-20220123212403581](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123212403581.png)



##### 요금

- 표를 보고 fee를 알아낼려면?

    ![image-20220123212340789](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123212340789.png)

    - fee는 표안의 속성이 아니라  theater가 getFee()한 것을 **중개할 뿐**이다.
    - **포인터(`getFee()`)의  포인터(`theater.`)의 포인터(`getFee()`) 를 이용한 것이다.**
        - 지금은 final불변값 theater지만,
        - 만약, **배신을 때려서 theater가 변경되어도** -> 포인터의 포인터 -> **ticket.getFee()는 정상작동(업데이트 자동으로 됨)될 것이다.**
            - 만약, Ticket의 필드로 fee를 잡아 `fee = theater.getFee()`로 값을 잡아놨다면 -> theater의 fee를 바꾸면  ticket.fee에는 반영이 안될 것이다.
            - **값의 변화가 반영되려면, 참조의 참조로 얻어와야한다! 그래야 참조의참조 업데이트가 runtime에 반영(이후에도 반영)** 
    - **포인터의 포인터를 비용으로 치루더라도 -> 변화가 바로 반영되도록 하는 원리를 가지게 한다.**





##### validation

![image-20220123213008079](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123213008079.png)

- ticket의 꽃은 fee가 아닌 validation이라고 한다.
    - valid한 상태를 판단할 때, 값이 아닌 **식별자로 3가지 잘못된 경우를 판단**하는 것을 확인할 수 있다.
- 일단 `ticket.isValid(theater)`로 사용되어 **theater를 받아와서 validation한다**.
    1. **먼저, `isEntered라는 숨겨진 속성`이 있다. -> 자기 상태를 자기가 관리하는 `maintain of status`**
        - 속성 **첫 값은 false;**로서 아직 사용안됬음을 알려준다.
        - **flag속성으로서 내부 토글되지만, 바깥은 아예 모른다.  = 자기가 관리한다**
        - 표가 이미 사용되었으면 -> if절에 true가 되어 out
    2. 받아온 theater가 <-> 내가 알고 있는(ticket생성시 필요한 theater)랑 달라도 out
    3. 자기자신(ticket)이 this == **EMPTY**인 경우 꽝 
        - 첫 줄에 정의된 EMPTY를 보자. **(이펙티브 자바 책에서 나오는 `null 대체 패턴`)**
            ![image-20220123213624856](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123213624856.png)
            - **null를 사용할거면, null을 대체하는 객체나 값을 대체로 사용하는 것**
            - **ticket이 잘못되거나 null쓸 것을 대신해서 `Ticket형 EMPTY`를 대신 만들어놨다. 특히, final로 **
                - ticket이 null이거나 잘못되었다 -> EMPTY로 대체해서 쓴다.

- invalid3가지에 안걸린다면?
    ![image-20220123214232166](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123214232166.png)
    1. **미리 `이건 쓴 표야` 도장을 찍어놓고**
        - **고객이 제시하는 순간, 그 표는 어떠한 경우든 `사용됨 isEntered = true`가 꽝! 찍히고** 
        - true 든 false든 1개가  return된다.
    2. true를 return시킨다.





#### Invitation

![image-20220123214706067](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123214706067.png)

- ticket과 마찬가지로, 발급주체인 theater를 생성자에서 받은 뒤, final로 확정해서 받아놓는다.
- 있다/없다의 판단시 null대신 Invitation형 EMTPY를  final로 만들어준다.
    - 이펙티브 자바..



#### TicketOffice

- my) 받기만 하는 쪽에서는, 주는쪽의 객체를 사용못하는 제약 + 주는쪽에서 [받는 서비스(메소드)]를 제공해줘야 -> 주는 쪽(theater, TicketSeller)이 무엇을 줄 수 있다.
    - theater에게는, theater가 표를 줄 수 있게 -> 표를 받는 메소드 `addTicket`
    - ticketSeller에게는 표를 꺼내갈 수 있게 -> 표를 꺼내주는 메소드 `getTicketWithFee/NoFee`를 제공한다.

![image-20220123214826683](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123214826683.png)

- 의외로 theater만 -> office를 알고, office는 theater를 모르게 되어있다.
    ![image-20220122214556326](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220122214556326.png)
    - TicketOffice는 theater에 관심 없다.(뭔가를 안준다?)
        - theater가 TicketOffice에게 뭐를 줄려고 한다( 안다, 관심있다)
        - (theater가 주는 것에 대해) TicketOffice는 받아주는 메소드(서비스)를 노출해줄 때만, theater가 이용할 수 있다.
        -  **화살표 방향과는 반대로, `받는 쪽이 [받는 메소드=서비스를 제공] 해주지 않으면 사용불가`하여, 더 거만한 것은 TicketOffice쪽이다.**
            - 화살표 나가는 쪽이 많이 알고는 있지만, 받는 쪽이 오픈/제공해줘야 사용가능
            - **`화살표 나간다` = 알고 있다 = (제공되는 서비스가 있어야) 이용한다.**
            - **`화살표 받는다` = 서비스를 제공해줘야 주는 쪽에서 이용가능하다.**



- `addTicket()`은 특정 theater가 -> ticketOffice에게 표 넣어줄 때 사용하는 메소드

    - 화살표를 받는 쪽에서, 주는 쪽(발급주체)인 theater에게 제공하는 메소드

    - 이것이 있어야 theater가 ticket을박아줄 수 있다.

        ![image-20220123215846558](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123215846558.png)

- 그외 변수에는 티켓오피스 생성자(자산amount를 가지고 시작함), 티켓오피스의 재산인 (amount), 받은 총 티켓 리스트(tickets) 이 있다.
    ![image-20220123220011549](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123220011549.png)



- **ticket의 fee는, 직접 받아 가지고 있는게 아니라, 포인터의 포인터의 포인터로.. theater까지 가서 받아와야한다고 했다.**

    ![image-20220123220120708](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123220120708.png)

    - **ticketOffice는 theater를 기본적으로 모른다.**
        - **`정보의 은닉` = 상호참조가 아니라 `단방향 의존성`을 가지게 했다.**
        - **theater를 모른다 = `theater.getFee()`를 못쓴다(my) theater객체를 못쓴다)**
            - 그럼 어떻게 theater에 있는 표의가격을 알수 있을까?
                - 자기가 가진 ticket에서 조회를 한다.

    1. 티켓오피스에 표가 없다? -> 표의 가격을 모른다 -> 0원으로 반환
    2. 안다? -> (한 극장내에서 받았다 가정하면) 1번째 표에서 getFee()를 때린다.
        - my) 받기만 하는 TicketOffice(theater를 모름)에서는, 주기만 하는 theater의 객체를 사용못하므로 그 내부의 getFee()를 이용못하는데, 받은 물건(tickets)을 이용해 fee를 조회한다.



- 이번엔 theater가 아닌 **TicketSeller에게도 2가지 서비스를 제공**하는데 

    - TicketOffice에서 표를 1장 뺀 뒤 -> TicketSeller에게 줘야한다

    - 빼가지고 줄 때 2가지 방법을 준다.

        1. **초대권으로 무료로 준다.**

        2. **돈 받아서 준다.**

![image-20220123220839246](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220123220839246.png)

- **어느 경우든, 빼내서 줄 티켓이 없으면 null대신 Ticket.EMPTY를 준다.**

- `티켓을 돈주고 팔때`는 **List에서 `.remove(0)`으로 `list에서 제거하면서 & 반환된 Ticket을 받은 뒤` 그 ticket.getFee()로 티켓가격을 `재산amount에 보탠다`**
    ![image-20220124011839537](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124011839537.png)
    - 판 표는 **`remove(0)`로 제거가 아닌 `받아오는 빼내기 전략`를 이용한다.**
        - remove되면서 반환되는 ticket 데이터를 이용하여, 참조의 참조로 표 가격을 구한다.
        - return ticket을 통해 표를 건네준다.
    - 판 표에 대한 돈은 재산amount+= 를 한다.
- `티켓을 초대장으로 그냥 줄 때`는 
    ![image-20220124011852616](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124011852616.png)
    - **ticketOffice는 invitation의 존재를 모르는 상황**이다.
        - 존재를 모르더라도, **그냥 공짜로 표를 주는 것 정도로 처리**한다.
            - **.remove(0)로 받아오는 것은 같으나 `ticket.getFee() -> amount += `의 과정이 없다.**
    - **초대장을 몰라도 되는 이유는?**
        - 초대장 이외에 VVIP 등에게도 무료로 표를 줄 수 있기 때문에, **무료로 표를 갈취해가는 경우는 많다. -> 모든 경우를 처리하려면, invitation을 모르는게 정상**
- **ticketOffice가 표를 돈주고 팔지 / 무료로 줄지를 결정하는 것이 ticketSeller다 **
    - Seller의 역량에 따라서, Office에서 무료로 빼줄지 / 돈주고 팔지를 결정한다.





#### TicketSeller

![image-20220124012340287](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124012340287.png)

- 악의축 TicketSeller는 ticketOffice**의 소속**이라, TicketSeller**~~생성자==발행주체~~에 소속기관**(TicketOffice)**을 set메소드**로 박아둔다.
    - ticket의 **`발행 주체`인 theater를 -> 발행담당 `생성자에서 받아, final로 박아두는 것`과는 달리**
        - **`태어나서 ~ 죽을때까지 절대 못바꾸는 나의 소속기관(발행주체)`이다? = `생성자`에서 받는다.**
    - **`프리랜서`로서생각하여, 발행주체=생성기관이 아니라 `중간에 소속을 바꿀 수 있는 소속기관`으로서 `set메소드로 박아준다`**
        - **프리랜서의 소속기관으로서, `바뀔 수 있는 소속기관`이다? == `set메소드로 필요시마다 변경하며 박는다.`**
        - 표를 파는 직원이 종신으로 일할 리 없다. -> setter로 소속을 박는다.
- **`소속`을 `setter인지 생성자에서 받을 것인지`는 신중하게 생각해야한다.**
    - **태어나서 불변하는 발행주체, 소속기관 -> 생성자에서 태어날때부터 받기**
    - **중간에 계속해서 바뀔 수 있는 프리랜서의 소속기관 -> setter로 받기**



![image-20220124212851967](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124212851967.png)

- **중요한 역할은 `getTicket이라는 거래`이다. 이 때 트랜잭션이 성립하기 때문이다.**
    - **seller**의 getTicket은 **audience와 거래**를 한다는 얘기다
    - **누가 갑일까? `거래는 을인 audience가 표를 원할 때` 이루어진다.** 
        - **`갑은 seller`다. `표를 줄 수도 / 안줄 수 도 있다`. 을의 매표소직원의 느낌이 아니라 `출입국 심사원`의 느낌으로 `고객에게 여러가지 검사를 요구`한다.**
        - 을인 audience 표달라고 빈다.
- **거래에 있어서, 판매원은 갑의 위치에 있다는 말이 무슨 말일까**
    1. **판매원은, 일단 기본적으로 표를 안줄 생각(`Ticket.EMPTY`)을 하고 있다(`갑으로서 검사과정에서 여러가지 이유에서 안 줄 가능성이 있음을 시사`)**
        ![image-20220124213448407](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124213448407.png)
    2. **`첫번째 고객자격검사`**로, **고객이 돈은 둘째 치고`초대권` `가지고 있는지( != EMPTY 로 )`** 검사한다.
        ![image-20220124214838257](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124214838257.png)
        - 초대권 있으면, **소속인 TicketOffice에서 무료로 표**를 얻어온다.
        - **TicketOffice에서 `표를 받아왔는데, EMTPY 표일 수 도` 있다!**
            - **`빈 표가 아닐때`, return ticket될 예정이니, audience의 초대장을 (뺏어서)파기(`audience.removeInvitation()`)한다.(`고객의 권한을 침범하는 것 아닐까? 아니다. 을이라서 시키는대로 한다.`)**
                - **트랜잭션은 누가 주체인지 정확하게 알아서 주도해야한다.**
                - 초대장 파기의 순간은, 정상적으로 표를 받아왔을 때인데, seller만이 알고 있다. 그 타이밍에 파기 명령을 갑으로서 내린다.
                    - 고객이 getInvitation()으로 보여주기만 하고, 파기를 안할 수 도 있으니,  파기명령도 거래에서의 갑인 seller가 하게 한다.
            - seller에게 ticket만 return받고, 초대장을 파기안했을지 seller는 모른다 -> 갑의 권한으로 을의 초대장을 파기한다.
        - TicketOffice에서 일단 get한 ticket은 **EMPTY or 정상 ticket이 될 것**이며, 
            - **ticket이 `EMPTY가 아니라면`** -> **곧 return ticket으로 표를 건네줄 것이기 때문**에 ->  **상대방의 돈을 가져오거나 or `초대장을 파기`해야한다**
            - empty라면, 상대 돈뺏or초대장파기없이, 그대로 return ticket(EMPTY)를 하면 된다.
    3. **`두번째 고객자격검사`**로, **고객이 애초에 표를 `살만한 돈`을 `가지고 있는지`  검사를 한다.**
        ![image-20220124215138012](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124215138012.png)
        - 트랜잭션이 성립하려면, 누가 무슨책임을 가지고 있는지 봐야한다?!
        - **`고객`은 getAmount로 내장(amount 값)을 까는 정도는 아니지만, `hasAmount( 표가격 )으로 최소 노출로서 고객자격 증명`을 한다.**
            - 살만한 돈을 가지고 있으면, 트랜잭션으로 표가격만큼 돈을 갈취해해야한다.
            - **`고객의 돈 갈취`는 `실제 표 == EMPTY 표가 아닌 것을 확정`되었을 때 갈취 한다.**
        - **돈을 주고 `ticketOffice에서 표를 받아온 뒤`, 그 표가 `EMPTY가 아닌게 확정 되었을 때 진짜 표를 받아온 것 -> 고객의 돈을 갈취`**한다.
            - **TicketOffice에서 getTicketWithFee()를 하면**
                - **EMPTY 표 일 수 도 있다. 이것은 티켓의 갯수가 0개인 경우였다. -> 티켓 없는 상황이다.**
                    ![image-20220124221051067](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124221051067.png)
                - **EMPTY가 아닌 표를 받아왔다면**
                    ![image-20220124221208345](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124221208345.png)
                    - ticket을 1개 빼온다( remove(0) -> 받아오기)
                    - 그만큼 **`office`는** amount+=  **`돈을 이미 받은 상태`**
                - **`seller`는 audience에게 amount-= 로 `돈을 갈취해야만 한다`.**
                    - audience(고객)이 표만 먹고 나를 수 있으므로, seller가 <책임역할모델에 의해> 갈취해야한다.
                    - 을인 audience가 `갑인 seller`님이 `.removeInvitation()`이나  `.minusAmount()`해서 갈취를 할 수 있게끔 서비스를 제공해줘야 거래가 일어난다.
                        - 현실세계와 다르다. 트랜잭션이 성립하려면, **갑인 seller는** 
                            - **audience에게 초대장(상품권) or 돈있는지 확인후** 
                            - **office에 -  물건을 받아온 뒤,  + amount돈을 늘려놓고**
                            - **return 물건이 EMPTY아니여서 물건 있음 확정되면, -amount돈 갈취 or -초대장 갈취를 주도적으로 한다.**
- **seller**의 주업무는 **getTicket에 대한 비니지스 로직**을 가지고 있게 된다.
    **갑으로서  audience에 대해 검사를 하면서 판매(트랜잭션)** 한다.
    - **초대장 있는지 확인한다.**
        - **있으면 NoFee로 Office에서 티켓을 떼온다.**
            - **티켓이 있으면 (티켓을 주는 코드는 공통이라 맨 나중에) 초대장을 갈취(파기)한다**
    - **(초대장 없으면) 돈이 있는지 확인한다.**
        - **있으면 Fee로 Office에서 티켓을 떼온다.**
            - **티켓이 있으면 (티켓을 주는 코드는 공통이라 맨 나중에) 돈을 갈취한다.**



- **TicketSeller는 아래와 같이 & `계속 바뀌거나 추가될 가능성이 있는 <invitation정책> + <요금정책>의 도메인 지식(로직)`을 가지고 있어서, `대부분 고정된 로직인` ticketOffice와 다르기 때문에 `클래스가 분리`되었다.  **
    1. **ticketOffice에 대해 Fee/NoFee경우 나누기**
    2. **audience에 대해  검사 + 물건 확정시 - 초대장파기/돈 갈취**
- **새로운 invitation정책**이 생기거나 **새로운 요금정책 -> ticketOffice는 안건들이고 ticketSeller만 건들이면 된다.**
    - 격리가 된 파일이 된다.
- **티켓판매 정책은 많이 바뀐다. + 많은 것들이 관여한다. + audience도 다양한 자식클래스로 파생 될 수 있다.**
    - 파생되는 고객종류들이 생길때마다 -> 분리해놓은 ticketSeller만 수정한다.
        - ticketOffice가 변하고 망가지는 일들이 예방된다.
        - **변하는** 등급별 audience 파생 / 요금정책 등을 **분리해놓은 ticketSeller**가 다 받아서 해결한다. **그래야 그 밑에 TicketOffice - theater등을 방어할 수 있기 때문에**
    - **밑으로의 수정 여파를 발생시키지 않기 위해서, TicketSeller라는 객체가 나왔다.**





- **변동 가능성 높은, audience등급 마다 할인정책 / 분기별 바뀌는 요금 정책 / 시즌마다 제휴마다 이벤트마다 발생하는 변동**
    - **`변하는 것과 관계없는 부분만` 따로 모아서 -> `TicketOffice`**
    - **`변하는 부분만` 따로 모아서 격리 -> `TicketSeller`**





#### Audience

 ![image-20220124223050202](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124223050202.png)

- audience(고객)은 동네북이다.
- **고객이 가질 수 있는 ticket과 invitation을 EMPTY로 가지도록 default한다.**
- **태어날때부터, 금수저/흙수저인지 amount를 생성자로 받아 태어난다.**
- **`buyTicket`으로 사려면? `audience는 을`이다. -> `ticketSeller를 찾아가는 수밖에 없다`**
    
    - seller를 찾아간다. = **`seller를 인자로 받아와`서 티켓을 구매한다.**
    - **셀러에게 `"티켓좀 (Office에서 떼와서) 주세요. 저한테this"`의 메소드를 날린다. (seller는 티켓떼오면서 여러가지 을을 검사해서 줬었다)**
        - `ticket = seller.getTicket(this)`
    - 티켓은 EMPTY이거나 있는 표가 올테니, `default EMPTY였던 티켓 갱신`하고, 극장에서 검사받는다.
    
- **`hasAmount`(seller의 충분한 돈 있는지 검사용)과 `minusAmount`(seller의 진짜 표를 떼온 뒤, 돈 갈취)는 seller에 협박에 의해 최소 노출(getAmount가 최고노출)된 것이다. 안해주면, 갑인 seller는 거래안해준다.**

    ![image-20220124224002993](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124224002993.png)



- **`getInvitation`(seller의 초대장 있는지 검사용)과 `removeInvitation`(seller의 진짜 표를 떼온 뒤, 초대장 강제파기) 역시 seller가 요구하는것이다.**
    - **`hasAmount`, `minusAmount`, `getInvitation`, `removeInvitation` 4가지는 모두 seller가 `트랜잭션(거래)를 위해` 요구하는 스펙(`조건`)들**이다.
    - **위 4가지는 `인터페이스`로도 뺄 수 있다.?**
        - 그렇게 되면, **audience를 더이상 audience class가 아닌 `트랜잭션 컨디션이라는 인터페이스`로 인식**할 수 있다.
        - **seller가 사실 원하는 것은 audience가 아니다. 4가지 조건들을 원한다. 이것들만 만족하면 티켓을 줄 수 있다고 seller는 생각한다.**
            - **책에서 나오는 `인터페이스 빼기 = 책임을 분리하는 방법`이다. 어렵다...**



- **getTicket()는 극장이 검사할 때 요구하는 것**이다.

    ![image-20220124224602963](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124224602963.png)

    - 표 있으면 줘봐.

- **setInvitation**은 theater에서 특정 audience들에게 표를 무료로 제공하는 **초대장을 꽂아 주는 행위**

    - theater만 audience를 일방적으로 알고 있다.
        - audience는 받아주는 서비스를 제공해야, theater가 초대장을 줄 수 있다.



- 다 다른클래스 접대 메서드이고, 스스로 행사할 수 있는 메소드는 `buyTicket()`밖에 없다.
    - 바깥에 노출되는 **public**은 buyTicket()밖에 없다
    - 나머지 4가지 -> seller와 **internal**로 내부패키지(seller)끼리 필요한 것에게
    - 나머지 2가지 -> theater에게 internal로 필요한 것





#### Main


![image-20220124225435095](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124225435095.png)![image-20220124225342945](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124225342945.png)

1. Theater를 만드는데
    - 표값을 100원(100L)로 정한 극장이다. theater가 태어날때부터 안바뀌는 것은 표가격(fee)를 생성자로 받았다.
2. Audience 2명을 만드는데, 흙수저(0L)과 은수저(50L) 로 만들었다.
3. TicketOffice는 재산, 자본(amount)를 생성자로 받아서 만들어진다
    - theater를 모르는 놈이다. 자본은 0원으로 만들었다.
4. TicketSeller는 자유의지를 갖는 = 프리랜서로서, 소속기관을 set으로 박아주고 생성자로는 아무것도 안받는다. 
    - 그냥 태어난다



![image-20220124225545022](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124225545022.png)

- Theater가 자기와 거래할 TicketOffice와 계약( by setTicketOffices() )을 맺는다.
    - 계약 맺은 직후, TicketOffice에게, ticket을 10장 지급한다.
    - (추가) 흙수저에게, 무료 표인 invitation을 1장 박아줬다.



![image-20220124225758385](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124225758385.png)

- 프리랜서인 seller는 해당 TicketOffice와 계약을 했다.
    - 변동 가능성 소속기관을,  set으로 박아 계약한다.



![image-20220124225904333](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124225904333.png)

- 고객1과 고객2가, seller에게 표를 사러 갔다.
    - 그냥 사는 것 같아보여도, **갑 & 특정 seller에게 (인자로 받아서) 표를 사러간다.**
        - 내부에서는 갑이 메소드를 실행해서 거래한다.



![image-20220124230931572](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124230931572.png)

- 고객들은 theater에 입장했을 때 -> 티켓을 꺼내 -> 그 티켓이 isValid한지 검사하는데
    ![image-20220124231041159](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220124231041159.png)
    - 고객1 -> invitation이 있으므로 -> 표로 바꿔서 -> 들어가는 데 성공했을 것이다.
    - 고객2 -> 돈 없으므로 -> 들어가는데 실패했을 것이다.



#### 교훈

- 절대로 우리의 수준으로 class들 -> main순으로 이렇게  짤 수 없다.
    - main코드를 먼저 짜고 -> 거기에 맞는 class를 만들 수 밖에 없다.

- 책에서는 다양한 책임을 먼저 구하고 -> 역할들을 찾아보라곤 하지만
    - 실제로는 그렇게 짤 순 없다.
- **내 도메인에 맞는 시나리오(Main)부터 짜고 거기에 맞는 Class를 짜면 된다.**
    - 객체들의 협력상황을 보고 -> class를 만들어야한다.
        - class짜는 것도 팀마다 있는 퀜트백(조영호)같은 사람이 짜면 된다.
- 테스트코드부터 설계에 반영하려면, 테스트코드가 실제 설계에 사용되는 client코드와 닮아있어야하는데 
    - 클래스를 완성되었다고 가정하고, 클래스를 객체로 어떻게 사용할 건지 먼저 짜보는게 훨씬 낫다
        - 상상속으로  class를 짜봤자, 어떻게 사용되는지는 모른다.


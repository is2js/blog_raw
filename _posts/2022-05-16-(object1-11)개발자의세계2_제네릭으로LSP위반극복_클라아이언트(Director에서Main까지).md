---
toc: true
layout: post
title: OBJECT 11 객체설계5_개발자의세계2_제네릭(코드스핏츠)
description: object 책을 강의한 코드스핏츠 유튜브 요약

categories: [object]
image: "images/posts/java.png"
---

- 참고 유튜브 : https://www.youtube.com/watch?v=navJTjZlUGk
- 정리본: https://github.com/LenKIM/object-book
- 코드: https://github.com/eternity-oop/object
- 책(목차) : https://wikibook.co.kr/object/

### ch11. 제네릭으로 LSP위반 극복



- **객체지향의 `추상형` ---> `instanceof가 아닌 if`를 제거**
- **객체지향의 `제네릭` ---> `if instanceof`를 제거할 수 있다.**
    - **2가지를 섞으면** 모든 종류의 if를 제거할 수 있다.





#### instanceof to 제네릭

- 알렉산더르 아저씨가 쓴 책: 수학적 근거로부터 만들었음
- 객체지향적 의미: **LSP위반을 해결할 수 있는 언어적 수단은 제네릭**이다.
    - **`제네릭`: 추상형을 유지하면서 & 구상형은 클라코드에서 결정하게 해주는 것**
        - **if문과의 차이점 : `형`으로 결정함**
        - **즉, `if instanceof`를 제거하려면 `제네릭`을 써야한다**

- **제네릭 사용 판단은 `기계적으로 <내 코드에 instanceof가 있는지>로 판단`해도 된다.**
    - runtime, context에러 -> compile에러로 옮길 수 있는 수단이다.
        - `형` 체크시 compiler를 이용하므로
        - 우리가 할 수 있는 if제거 수단: 형



![image-20220206200613251](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206200613251.png)

![image-20220206200620827](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206200620827.png)

- if `instanceof`가 보이면 -> `제네릭`를 쓰면 되는데
    - if instanceof  -> **다운캐스팅을 하고 싶어서 확인하는 것** -> LSP, OCP 위반
    - **매핑의 과정**은 아래와 같다.  **`Client의 추상층인 Paper인터페이스`에 `<다운캐스팅이 일어나는 것을 추상형으로 제네릭>추가`**
        - **programmer 다운캐스팅(FE or BE)  ->`T`**
        - **programmer 추상형  -> `extends 추상형`** 
- 다운캐스팅한 문법을
    ![image-20220206201755934](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206201755934.png)
    - **제네릭을 이용**해서 **업캐스팅한 문법**으로 바꿨다.
        ![image-20220206201821031](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206201821031.png)
    - **즉, 제네릭을 통해 `<T`형의 규정을 `extends 추상형>` == `업캐스트(upper bound, 상위 bound)를 이용`해서 규정한다.**
        - cf) 다운캐스트 == 바닥의 lower bound == 구상층 == FrontEnd로 정의하는 `instanceof`
- **제네릭을 이용한 순간 -> `LSP, OCP를 회복`했다고 생각할 수 있다.**
    - **my) `instanceof -> lowerbound(구상층)을 사용한 다운캐스트 -> LSP,OCP위반`**
    - **my) `<T extends 추상형> 제네릭 -> upperbound(추상층)을 사용한 업캐스트 -> LSP,OCP회복`**





##### Client 수정

![image-20220206202245219](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206202245219.png)

1. LSP, OCP 위반 = 다운캐스팅이 일어나는 Client 클래스 -> 그것의 **추상층인 Paper 인터페이스에 `제네릭`인 `T형` (extends 다캐일어나는 FE/Programmer의 추상형) 을 받았다고 생각해보자.**

2. **추상클래스 레벨에서 `추상형upperbound 제네릭`의 `T형`을 받았다면 구상클래스 Client가  impl시 단순Paper가 아닌 -> `Paper<FrontEnd>`를 받을 수 있다. **

    - my) 제네릭이 달린 추상층은, 구상클래스 구현시  `impl 추상층`이 아니라 **`impl 추상층<T형의 구상형>`으로 받을 수 있다.** 

        ![image-20220206203059186](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206203059186.png)
        ![image-20220206203108989](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206203108989.png)

3. **`추상층의 메소드정의시 인자`**를 제네릭을 이용한 **`T형`(upperbound의 구상형 입력가능)을 인자로 받았기 때문에 -> `구상층 메소드 정의시 인자`에는 직접적으로 `(구상형으로 메소드 인자)`를 받을 수 있다.**

    - **`추상층에서 제네릭을 통한 T형으로 정의 -> 구현클래스에서 upper bound의 구상형들로 정의 가능`**

    ![image-20220206203440227](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206203440227.png)

    ![image-20220206203451142](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206203451142.png)

    - **instanceof 없이 구상형d()를 가져다 쓸 수 있게 된다.**
        - 제네릭이 해주는 것은 instanceof를 제거하는 것

4. **Client의 setData()의 인자에 구상형으로 정의했다 -> `제네릭을 통한 형으로 좁혀서 막았다` -> compile시 에러가 난다.**

    - 이제 Client 프로젝트에 데이터를 꽂을 때는, 아무것이나 못 준다 -> 제네릭을 통한 형으로 제한하면 -> 형은 compile에러를 내서 오류를 잡을 수 있게 된다.
    - **과거에는 다운캐스팅한 코드 속에서 if backEnd로 갈지도 모르는 가능성이 runtime코드 어딘가에 숨겨져있었는데, 지금은 제네릭을 통해 `하위형으로 확정`시켜서, 형 에러시 compile time에 걸린다**
    - **제네릭을 사용하면, 추상형 -> T자리에 구상형을 지원 -> 형으로 용도를 확 줄임**





- 제네릭은 기계적으로 사용할 수 있다. 매핑하는 것처럼
    - 현재 클래스의 추상층에 제네릭으로서 upperbound로 T형을 꽂아주고, 추상메소드 정의시 T를 사용해서 정의
    - 이 추상층을 -> 구현이나, 메소드정의시 T자리에 구상형을 직접 입력해서 정의
    - instacneof 제거 -> LSP, OCP 위반 제거하게 됨



- **if를 제거하려면, 분기에 해당하는 수만큼 객체를 만들 수 밖에 없고, 선택은 클라코드에 위임**한다.
    - **`Client가 범용`(FE or BE or 추가될 개발자) 이었다가**
    - **`제네릭`을 통해 `if instanceof`제거 -> `1개 분기(FE)에 대한 객체(Class)만 생성`**
        - **`BE용 Client`도 새롭게 정의해줘야한다.**
        - if case 1개(FE)를 담당하도록 **FE전용 class가 정의**되버렸다.
            - **class를 if case만큼 생성해하며, 그 선택은 바깥에서 한다.**
    - **제네릭 / 전략패턴 / 인터페이스  ->  다 if를 이런식으로 제거한다.**
        - **if는 제거되었지만, 그만큼 객체/class가 생성되며 -> 선택은 클라코드에서 한다**





##### ServerClient: instanceof 2개는 제네릭은 기계적 매핑 안됨

![image-20220206204751581](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206204751581.png)

- [Paper쪽]에 시킨 버전은 더 무겁다는 것이 여기서(ServerClient) 드러난다.
    - programmer를 2 case를 if  +  else if로 모두 다운캐스팅해서 받아줘야한다.
    - if 2개를, 제네릭으로 T형+R형으로도 매핑할 수 있을까?
        - if가 3개면,, 그것도 불가능.. 안됨.
    - **if가 2개**(2개이상일 가능성 높음)부터는 **instanceof -> 제네릭 매핑이 불가하다**
- 이것을 보니까 **[Paper쪽] instanceof가 훨씬 무겁다는 것**을 알 수 있다.
    - 그렇다고 다시 [Programmer쪽]으로 옮길수 도 없는 노릇이다.???



#### OCP와 제네릭을 통한 LSP위반 해결

![image-20220207004803787](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207004803787.png)

- 우리는 LSP위반을 해결하기 위해 제네릭을 통해 해결해보려했지만 신통치 않았다
- **그래서 `헐리우드 원칙(묻지말고 시켜=던져)`를 이용해서 `OCP+제네릭`을 결합한다**
    - 물어보다:  `if instanceof` 로 맞는지 물어본다.





##### Paper(N) 및 자식들 원상복구

- 추상층 paper한테 묻지말고 시켰더니, 어떤 구상층에서는 2개를 묻는 더 무거운 경우가 생겨버렸다. **paper는 안되니 다시 롤백**하자.
    - **paper는 programmer와 1:N관계에서 `N`이며, `N쪽에서 구현(시키기)하면 언제나 일반화시킬 수없다` -> 시키면 안된다`시키는 것을 1쪽에 구현해야한다.`**
        - programmer(1)에 시키고 + 제네릭
            ![image-20220206202245219](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220206202245219.png)
        - paper(N)을 **원상복구**(제네릭X + 시키지도X)
            ![image-20220207010809521](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207010809521.png)
- **이상적인 객체관계에서는 항상 `child(N) -> parent(1)을 알게한다`.**
    - **parent(1) -> child(N)을 알게하면 더 불리**하다. **많은 것들을 알아야한다**.
        - 많이 알아야한다 -> 의존성의 무게가 크다.
    - child -> parent를 아는 것은 1:1매핑이 된다.
        - **sqlalchemy에서도 N테이블(FK테이블, `Song`)에서  1테이블(PK, `Album`)reference(relationship변수)를 거는 것이 단수로 나와서 편했다.**
        - 부모가 자식을 알땐 1:N <-> 자식이 부모를 알 땐 N출발이지만 1:1
    - **여기 문제에서는 `Prgorammer(N) -> Paper(1)`를 알게 하는 것이 더 낫다**





![image-20220207010809521](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207010809521.png)

- **`1에 해당하는` Paper에 구현X 시키지X** 원상복구 하니, **메소드 없는 제일 좋은 인터페이스로 돌아갔다.**

    - 1paper당 - programmer - 2종류의 개발자가 매칭되더라. -> if가 1개에서 2개로 늠. -> 원상복구
    - **programmer(N) -> paper(1)쪽에 시키지말고**, programmer가 감당하게 가야한다.
        - N에서 1만 안다 == **시키는 것을 1에게 몰게하고, N은 아무것도 안하고 1만 의존하게 한다.**
    - **Paper는 메소드가 비어있다 -> abc(구상층의 공통로직)이 없는게 정상이다.**
    - **Client(Paper 구상체) 전체가 d다. (paper에 abc자체가 없었다)**
    
    

![image-20220207012516441](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207012516441.png)

- ServerClient도 돌아갔다. Paper(1)에 시키지X ->  구상체들 다 원상복귀함.
    - 마커 인터페이스 이하는 다 d()이다.





##### Programmer(n)에서->Paper(1)를 알게한다 = 그 쪽(N)에 시킨다 = 그쪽에 제네릭을 붙여 instanceof 제거

![image-20220207012641647](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207012641647.png)

- 중간 코드들은 생략한 상태
- **기존 Programmer `인터페이스` ---> FE와 BE의 공통로직을 추상화한 `추상클래스`였다** 
    - Paper쪽에 시킨 **기존 버전**
        ![image-20220207012939048](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207012939048.png)
        - 템플릿메소드패턴자리에  paper에 개발자정보를 .setData 해놓고
        - 그 paper로 makeProgram한 Program반환 -> makeProgram의 만드는 과정은 구상층이 구현
            - my) Paper는 밖에서 입력되는데,  paper에 개발자 정보를 set박아놓은 뒤(**no..**N:1에서 N이 **paper에게 일 시키는 중..**), 각 자식 프로래머들이 알아서 Paper를 받아->프로그램을 만들어서 -> 반환
    - **현재 버전**
        ![image-20220207013003472](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207013003472.png)



- **달라진 점**
    1. **구상클래스들의 instanceof 제거를 위해 -> 추상층에 제네릭을 T형 + extends  Paper(upperbound)로 붙히고**
        ![image-20220207013130246](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207013130246.png)
        메소드 인자를 기존 `Paper paper` ->  **`T paper`로 정의**한다.
        ![image-20220207014808797](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207014808797.png)
    2. **더이상 `paper.setData( 개발자 )`으로 paper에게 시키는 것(책임을 미루는 것)이 아니라 **
        - **`setData()의 책임`을 내가 가져간다.**
            - 타 객체에게 시키는 것이 아니라 **`내가 한다` = (this.)  `그냥 setData(paper) 호출`**
                - **제네릭 T형 paper 정보를 받아서 내가 처리한다 -> 정의는 setData(`T paper`)**
            - T형 paper의 처리 -> **자식(구상층)에게** 알아서 **`if instanceof분기마다 1개씩 <해당 paper구상체>`를 받아들이는 Class생성하도록 맡김**
        - **`내가 해줄 수 있는 것은 makeProgram()호출 전에  setData()를 호출하면서 T형 paper를 넘겨줄게`**
            - **`T paper의 처리는 구상층 자식들아 너네들이 분기별로 한개씩 T를 사용해서 만들도록 해라`**



- 착각해선 안되는 점: **(객체)메세지 -> `Don't ask + tell!`인데**
    ![image-20220207022037068](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207022037068.png)
    - **템플릿메소드를 구성하는 `부모-자식간에도 마찬가지로 적용`된다.**
        - **set**Data( paper ): **tell한 것(물어보지 않고 말해서 시킴)**
            - **getter처럼 값이나 객체를 받아오질 않음**
            - `"내가 줄건 paper고, 나한테 얘기하지말고 알아서해"`
    - **`abstract setData(paper)메소드` = `자식꺼`라고 생각한다.**
        - **자식꺼인 setData(paper)**를 호출할 때도, **부모(템플릿메소드getProgram)은 `묻지않고 = return없이 -> set시킨다`**





##### BackEnd

![image-20220207022203455](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207022203455.png)

- **수많은 종류의 BackEnd개발자**가 있을 것인데, 종류만 FE/BE로만 나눠놓은 상태다.
    - **수많은 BE개발자(instance)들의 공통점을 찾자**라면 
        - Server와 Language는 정해줘야한다.
    - **BE개발자들 중에서도 기존 추상층의 상속 extends  Programmer`<T>`에 추가하여**
        - **`BackEnd에도 <T extends Paper> 제네릭` 붙혀 `어떤 T형의 paper를 받았느냐`에 따라 같은 BackEnd개발자라도 `다른 BackEnd`개발자로 본다.** 
            - 기존(원상복구)
                ![image-20220207121502739](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207121502739.png)
            - **BackEnd -> `제네릭`을 받는 `추상클래스`로 변화 **
                - **그 위 추상층 Programmer 은 이미 제네릭 붙인 상태함**
                    ![image-20220207121613593](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207121613593.png)



- **기존까지 class의 관념**에서 보면 **`수많은 BE개발자(instance)`들 -> BackEnd `일반 class로 추상화`해놓은거**
    - **instance들을 추상화해놓은 class에 `제네릭이 개입`하면  `추상화가 깨지는게 아니라 <if분기에 걸릴 것들이 -> 각 T형`으로 `<제약>`이 걸린다. + my) `추상클래스`가 된다.**
        - class : `BE개발자들`
        - class + **제네릭 ** -> **abstract class** -> 
            - `<a Paper>를 가진  BE개발자` class로 **각각 한정된 class로 여러개 구현**해야함
            - `<B Paper>를 가진  BE개발자` class로 구현해야함
            - `<C Paper>를 가진  BE개발자` class로 구현해야함
            - **`if 대상이 되는 분기들을 T형에다 옮겨놓은 것`이므로 `if case만큼 새로운 class를 만들어야한다`**
- **if를 코드안에서 제거하는 유일한 방법 -> `if case만큼 class를 만들어주는 방법` 밖이다.**
    - 함수도 만들어도 되지만, 우리는 **형(class)으로 만들어야지 compile에러를 낼 수 있다.**
    - **if case만큼 class를 만들어주는 방법은 `제네릭`을 사용하는 것**이다. 
        - **my) 그러려면, 기존 class 1개를 -> 제네릭달린 추상class + 각 if case만큼 구현한 일반 class들로 구현**
        - 일반class BE의 추상화 -> `public abstract class BackEnd` 는 추상화는 깨지지 않았지만
        - `Backend<T extends Paper>`로 인해 **`각 if case 1개(instanceof)로 사용범위가 제한된 class가 각각 구현`된다**

- **각 한정된 BackEnd마다 case마다 다른 값으로 들어올 `T`를 **-> extends **Programmer**<`T`>에 공급해준다
- `T`를 공급받은 Programmer에는 추상화된 공통로직이 올라가있도록 설계되었다.
  
- 이제 **각 case별 BackEnd들의 공통로직(어느 paper종류를 가진 backend라도 똑같은)을 구현**해주면 된다.

    - 대표적인 것이 makeProgram()으로, **어느paper를 받는 BackEnd class일지라도 다 공통되는 로직**으로서 **`추상클래스 BackEnd`에서 구현**해주면 되는데

- **`추상클래스 BackEnd`에는 추상층(Programmer)에 있던 `setData(paper)의 구현`은 빠져있다.**
- 이건 각 case별 = 들어오는 **`T paper별 BackEnd마다 서로 다르게 구현`해야하는 내용이므로  `클라 코드에 위임`시키기 위해 `BackEnd<T>`가 `추상클래스`가 된 것이다.** 
  
- **아까는 Prgrammer -> Paper로 밀어냈는데, `클라코드(Main)방향으로 밀어낸 것이 아니라서 문제가 해결X`(협력관계 도메인 레이어(같은 책임 레이어)에서 문제를 떠넘길뿐이었음)**
    - **`자기보다 클라방향으로 밀어내야지 LSP, OCP위반을 해결`할 수 있다.**
        - if제거를 밀어내기를 클라방향으로..
        - 클라코드에서는 경우의수만큼 더 만들어내게함 -> 객체로 보내주면 내부 객체랑 대화함



- **my) paper마다 다르게 구현해야하는 Programmer의 setData(paper)를 받아버리면**
    - paper마다 다르게 구현해야하는 내용 -> **어쩔수 없이 if문을 써서 구현**해야한다.
    - **setData(paper) -> paper가 다르게 들어오는 것의 결정 -> `클라 코드(main)`으로 넘기고 싶다 **
        - **BackEnd가 Programmer를 상속하면, 어쩔수 없이 setData(paper)를 구현해야하니 -> `FE/BE class를 추상클래스`로 바꾸고 -> setData를 구현안하고 -> `클라코드(main)`에서 `FE/BE를  추상클래스를 구상클래스로 구현 && 객체 생성`하도록 한다.**
- **my) 매번 달라지는 paper -> 추상층에 제네릭붙혀 if instanceof 제거 ->  구상층에 paper땜에 달라지는 paper달린 메소드 -> 구상층도 추상class로 변경해서 paper달린 메소드 구현X 클라코드에 추상class구현을 미룸** 





![image-20220207130309754](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207130309754.png)

- FrontEnd도 **클라방향(외부)로 밀어내서 편안**하다
    - 부모제네릭T지만, 바뀌는 것들을 -> 자식이 구현X -> `안바뀌는 공통로직만 구현한 추상클래스`로 만듬 ->  case에 따라 바뀌는 것을 클라에서 구현하게 함





#### 클라아이언트(주체Director ->  Main)의 변화

![image-20220207144218499](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207144218499.png)

- 여기 배운것에 따르면
    - **가장 위쪽 추상레이어인 Programmer와 Paper를 괴롭히지말고, 더 외부방향인 `Director`를 괴롭혀라**
        - 현실에서도 임원이 시키면
            - 개발자한테 던져서 if로 분기하면 -> context에러로 망한다.
        - 팀장이 다 해체한다음,
            - 개발자들한테는 1개 case들만 각각 정의하도록 던져준다.
                - 너는 aws
                - 너는 유닉스 세팅
                - 너는 버플리쉬
    - **추상층이 아니라 `클라이언트 층 = 서비스를 이용하는 층 = 더 훌륭한/돈많아서 시킬 수 있는/위에 있는 사람`에게 `구체적인 상황을 나누는 분기를 던져줘야한다`**
        - 서비스층이 안정화되고 -> 클라이언트 층은 hell이 되었다고 생각할 수 있다.
- **클라이언트한테 던져줘도 클라이언트에서 hell = 책임이 떡지지 않는다.**
    - **우리는 `역할책임모델`기반으로 책임을 분산하면서 짰기 때문**
    - 코드에 if를 가지지 않고, 형 with 제네릭으로 해결한다



#### 상대적 클라방향 Director

- Director내부에서 `if ServerClient`와 `if Client`를 처리하던 코드를 비교해보자

    - **`기존 코드`**
        ![image-20220207145337724](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207145337724.png)

        ![image-20220207145432491](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207145432491.png)

        - **Server와 Client 프로그램 각각을 따로 만든 뒤, deploy하는 책임을 가지고 있다.**
        - **프로그램 만들어서 deploy하는 책임을 가지고 있었다.**

    - **`기존코드`를 보면, ServerClient와 Client 처리코드가 `중복된 로직`을 처리하는 `중복된 코드`를 가지고 있다**





- **현재는 `if instanceof제거`를 위해 Programmer를 `특정 paper를 알고 있는 programmer`로 구현되도록 `제네릭으로 한정`지은 상태다.**
    ![image-20220207145804141](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207145804141.png)
    - **`기존` Director속 programmer는 `특정paper`를 알고 있는 프로그래머가 아니다.**
        - 범용 프로그래머 = **아무 paper나 받아들이는 frontEnd개발자, BackEnd개발자**인 상태다.
            ![image-20220207150037690](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207150037690.png)
    - **`지금`은 `제네릭으로 -> 특정 paper만 받아들이는 prgrammer로 제약`을 걸었다.**
        - **`Director 속의 코드` ->  ServerClient 처리 코드가 `바뀐다.`**



![image-20220207150327852](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207150327852.png)

- Director속 `ServerClient paper`인 경우,  **처리코드가  어떻게 바뀌었는지 확인**해보자.
    - **범용 FrontEnd 개발자가 아닌, `ServerClient만 아는 FE`를 만들어야한다. `그래야 if가 바깥쪽으로 빠져나온 효과`를 누린다.**
        ![image-20220207150823134](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207150823134.png)
        - **제네릭이 `if없이 case를 [형]으로 한정`지어서 if문이 없다.**
            - **my) `new Class<형으로 확정>`지은 class의 객체는 `if제거하고 외부로 돌린 제네릭`이라고 생각하자**
        - **ServerClient(paper)를 알면**, 아는 것의 내장을 깔 수 있으므로, **그 속의 language만 물어봐서 역할책임에 넣어준다.**
    - BE도 범용이 아닌 `<ServerClient>`만 알 고 있는 BE -> setData시 `<확정된 paper -> ServerClient 문서>`로 여기로부터 확정된 데이터를 가져와 setData
        ![image-20220207151935404](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207151935404.png)
    - 나머지는, project에 FE, BE개발자들 세팅해주고, program 얻고, 정리해서 deploy에 준다
        ![image-20220207152035309](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207152035309.png)





- **기존까지는 Paper or Programmer에 있던 if instanceof를 ---> `if문에 해당하는 수만큼의 class를 정의`하고 -> `외부 클라코드(Direcotr, main)가  어떤class->어떤객체를 만들어낼지 선택하게 한다`**
    - **Director가 선택하는 모습이 안보인다고 할 수 있는데, `[if문 -> 하드코딩]을 통해 <선택한 객체를 새로 생성>하는 것이 if분기 중 1개 선택`한 것돠 동일한 것이다.**
        - if case만큼의 class = 형을 만드는 것으로 대체함.
            - 어려우면 반복하자.
            - 클라이언트에 밀어내자
            - 클라이언트는 if문분기 대신 **본인이 선택(객체 하드코딩으로 new생성해서 선택)**
                - 가장 외부에서 **객체를 새로 생성하는 것으로 선택**





![image-20220207162918373](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207162918373.png)

- 우리는 **FrontEnd개발자를` <제네릭으로 paper -> ServerClient로 확정>` 짓는 것**을 통해서
    - 개발자들이 각각의 개발자들이 어느 paper에 반응할지를 **가장 클라이언트(외부, Director, Main)에 가까운 쪽으로 밀어내서**
        - 클라에 가깝다 = `추상레이어 -> abstract class -> 구상class in Director` = **추상화 레벨 가운데 가장 밑 레벨까지 밀어내서 == `가장 마지막 구상층까지 밀려나와 객체를 new새로생성`으로 if를 대신할 수 있게 된다.**
- Director 프로토콜 수행 전 구간에 걸쳐서 LSP, OCP위반이 없어졌다.
    - **프로그래머 , 페이퍼 -> 추상층?인 FE/BE, Client/ServerClient 전부 다 if가 없어지고 LSP, OCP위반이 없어졌다.**
        - `형`으로서는 `가장 마지막 구상층`까지 밀어냄으로써(**거기서 객체 생성하여 선택하게 함으로써**)



![image-20220207164000581](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207164000581.png)

- Client paper는 더 쉽다. Client형 전용으로 만들어서 if를 없앤다.
    - 범용 FrontEnd가 아니라 `Client paper전용 FrontEnd`를 만들어서, 그 내부에서 받은 client paper로 setData해주고
    - 그 FrontEnd개발자를 project에 set한 뒤 deploy
    - if는 다 사라졌다.



- **아직까지 Director수준의 `instanceof`를 가지고 있어서 아직 남아있다.**
    - **그러나 Director에게 `형`을 주는 것은 불가능하다.**
        - Director가 **N개의 paper를 가지고 있기 때문** -> **N개짜리는 제네릭으로 처리할 수가 없다.**
            - Direct가 2개이상 종류를 가진 paper를 추상화하는 것은 불가능하다.
        - **`의존성 역전`을 시켜야한다.**
            - director가 ---< N개의 paper를 가지고 있다?
            - **반대로 1paper가 ---ㅡ1Director에게 `서비스를 제공해서 추상화`하면 된다.**
                - 아까 1Programmer ---ㅡ<2 paper 에서는 paper선택시 무거웠지만
                - 여기 director ---ㅡ< N paper에서는
                    - **paper---ㅡ director가 더 가벼워진다.**
        - 어려워보이지만, 재귀 <-> loop바꾸듯이 자연스러워야한다.
            - 회사에 가면 거의다 다운캐스팅해서 쓰기 때문에... 연습을 해서 익숙해져야한다.



![image-20220207172412976](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207172412976.png)

- 다시 Director 전체 코드 레이아웃를 한번 살펴보자
    - 기존처럼 addProject, runProject 하는데, **runProject시 가져온 paper(project)에 대해 `if instacneof 분기`를 하고 있다.** 
        ![image-20220207172622965](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207172622965.png)
    - Director 레벨에서도 OCP위반을 하고 있다.
        - **OCP를 해소하는 방법으로 if문을 해소**해보자.
            1. if문이 보이면,**공통로직 추상화** ---> **구상화를 클라이언트 방향으로 옮겨야한다.** 
            2. **거기서 경우의 수만큼 `형`을 생성해놓으면 -> if대신 선택을 `해당 형 객체 생성`으로 하게 한다. **
    
    



##### OCP위반 1: if분기대상에 공통로직 추상화

![image-20220207172903526](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207172903526.png)

- **클라방향으로 옮기기 위해서는 `if분기들 내부 공통로직을 추상화`부터 먼저 해야한다.** 
    - **if instanceof 분기마다 `공통되는 로직들을 추상화`해야 -> `클라쪽에서 구상화`할 수 있게 된다.**

![image-20220207175204927](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207175204927.png)

- **공통로직(빨간박스)**: 특정paper -> project -> project 개발자 ->  project 세팅완성 ->  개발자+project로 get**Program**
    - deploy()를 보면, **앞에 공통로직에서 `programs`를 받는 표준인터페이스이므로 `공통 로직들은 programs를 return하도록 감싸면 된다.`**
- **공통 로직을 구성하는 `programs return로직`으로 ---> `if 분기를 만드는 Paper`를   `감쌀 수 있다.(추상화?)`**
    - 아까도 caseA caseB를 감쏴서 -> abstract class를 만들었다.
    - 코드(공통로직)를 보고서 -> 추상화한다.
        - **빨간박스**는 **programs배열을 토해내면 된다**.(자바 ...은 1개씩 보내도 되고 배열로 보내도 됨)
        - **추상화의 대상: `공통로직(빨간박스, programs를 return)`를 -> `paper (추상층)`에 올리는 것**이다



![image-20220207192138807](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207192138807.png)

- **`if분기를 만들던 Paper`는 추상층에서  `인자 없이 run()`을 때리면 `Program[]배열`을 `return`해야한다.**
    - case A, case B를 본 결과, 추상화하여-> Program[]배열을 return하는 인자없는 메소드run()을 만들면 된다. 실행만해주면 된다.
    - **책임: `Director가 project를 실행하는 것`에서 -> `<여러 if분기를 유발하는 추상층> paper가 project를 run`하도록  `추상화`를 통해 넘어갔다.**
        - 역할책임모델은 도메인과 일치하지 않는다. 적당한 역할을 하는 사람이 가져간다.





##### OCP위반 2: if분기대상의 자식들(특정분기들)을 abstract화 -> 클라에서 new 자식(특정분기) 객체 생성하며 && 분기대상의 공로메소드 case에 맞게 구상화

![image-20220207192718902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207192718902.png)

- `분기속 공통로직`을 분기만드는 paper로 **추상화한 `run()`을 -> `상세 구현`은  client방향으로 밀어야한다.**

    - **`공통로직의 틀` = `paper는 Program[]배열를 return`만 추상화한다**
        ![image-20220207192138807](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207192138807.png)

        - **`기존에는`** ServerClient나 Client paper는 **`완전한` paper(`일반class`)였는데**

            ![image-20220207193001591](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207193001591.png)
            ![image-20220207193011844](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207193011844.png)

    - **이제는 `분기를 만드는 paper에 [공통 로직의 틀]를 <클라이언트에서 구현하면서 객체 생성으로 선택>`**하기 위해 
      
        - **`이제는` paper의 run()을 `외부에서 최종구현할 수 있게 자식들` SeverClient paper, Client paper `을 구상클래스가 아닌 [중간의 추상클래스]`로 만든다. 가장 바깥 = `외부에서는 이 추상클래스를 구상하면서 객체생성하여 선택`**
            ![image-20220207200640949](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207200640949.png)
            ![image-20220207200649745](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207200649745.png)
            - 왜냐면... 외부 최종 선택 -> if case마다 다 하나씩 생성해야함 -> **`구현하면서 객체 생성하여 최종선택`하면 한번에 해결**
                ![image-20220207200718479](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207200718479.png)
                - 나중에 볼 코드지만, `new Client()`로 **if분기 중 1개인 Client특정paper를 선택하면서 && 타 내부정보들도 `Paper의 run() -> 자식인 Client도 추상클래스 -> 객체생성하며 클래스처럼 구현`하여 선택해버린다.**

- if문(paper)을 제거하려한다? 그만큼의 형(class)를 만들어야한다.
    - 그 형을 담는 그릇이 interface Paper이다.
    - 그렇다면 **자식들이** ServerClient / Client는 **구상클래스 같지만, 추상클래스로 남겨놔야한다** 
    - 외부에서는 `new ServerClient() or new Client()`로서, **객체를생성 하면서 case에 맡게 구현하며 추상클래스 구상화를 해버리는 전략이다.**
    - if instanceof에 분기 선택으로 사용되던 Client, ServerClient도  **`class`  ->  `abstact class`된다**. 
        - **`abstact`만 달아줌으로써, Client, ServerClient의 본래 특징을 유지하되 && `if를 유발하는 수행방법(내부다른정보들 입력) -> case에 따라서 외부에서 구현 && 객체 생성(형 생성)`해버리기 위해**





##### OCP위반 3: 외부 선택 by 특정분기 객체생성 with 공통로직 메소드 구상화 ---> 다시 내부로 받아온다.

![image-20220207203105512](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207203105512.png)





##### OCP위반 4:  내부에서 받은 특정분기 객체의 <case에 맡게 구상화된 공로메소드> 호출

![image-20220207201425126](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207201425126.png)

- 위 코드는, Director의 `if instanceof`관련로직을 다 클라이언트 방향으로 밀었다.
    1. if 분기대상(Paper)에 공통로직 메소드(run()) 추상화
    2. Paper자식들 = 특정분기들(Client, ServerClient)의 absrtact class화
    3. 외부에서 특정분기(자식) 1개 new 객체 생성 && 분기대상의 공로메소드run() case에 맞게 구상화

- **deploy의 인자 중 projects.get(name)`.run()`이 `case에 맞게 외부에서 구상화한 공통로직 메소드`를 호출한 것이다. -> `<공로메소드 추상화 직전 틀>에 의하면 programs 배열을 return`할 것이다.**
- **Director의 `if instanceof` 제거 -> `앞으로 수정안해도 됨`**
    - Programmer, paper를 이용하던 Director보다 ---> **더 클라방향인 Main으로 밀렸다.**
    - Director는 상대적으로 Programmer그룹, paper그룹에 비해 host방향이었다.
    - **객체지향 설계를 제대로 했다면 -> Main까지 밀려야 성공한 것**
        - 그 전까지 어딘가에서 다운캐스팅 하고 있을 것이다
- **Main까지 다 밀었으면 `DI`를 한다.** 
    - Main의 코드들을 싹다 일일히 만드는게 아니라 DI를 통해 주입하는 것으로 끝내야한다.
    - Main까지 다 밀어내야지만 DI가 정상작동한다.



- **Director -> 분기대상인 Paper가 책임을 가지게 되었음.**
    - Director는 코드가 줄었다.





##### OCP위반 5: Main에서 여기까지 밀려난 특정분기Paper를 case에 맞게 객체생성&& 익명클래스로 구상화(형 생성)



![image-20220207205412287](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207205412287.png)

- Director를 만든다.
- Director에 project(특정Paper 중 1 case)를 "name"과 함께 add한다.
    - 프론트 작업이라서 -> **특정 paper들(자식들) 중 Client paper를 만들고 있지만, 범용Client paper가 아니다.**
        - **`수행방법을 그 자리에서 구상화`하면서 객체를 생성한 `Client paper`이다.**
            - 구상화(abstract class를 구현)한 것이니 -> `해당 수행방법을 가진 형`이 case에 맞게 생성된 것이다.
            - **() { } 괄호 담에 중괄호 -> `익명클래스`로 형을 선택한 자리에서 새로만든다. **
                - 이게 바로 if를 제거하는 원리
    - 수행방법을 보니
        - Client용 FE개발자 1명 섭외
            - 개발자도 **[객체생성으로 선택]**으로 섭외하는, **익클로 구상화와 동시에 객체 생성하여 `특정 개발자(개발자의 특정자식(중간abstract))면서 && 수행방법(case에 맞게 abstract를) 구상된` 개발자를 선택한다.** 
            - 여기에도 if대신 형을 생성하는 문법으로 바뀌어있음.
        - 개발자한테서 program을 받아냄
        - deploy용으로 program반환
        - runProject

- DI주입은  `객체생성 && 익클 구상화`하는 부분을 주입으로 바꾼다.





##### OCP위반 6: Main(외부)로 밀었다면, 구상화에 있는 setter를 제거해서 구상화시 은닉성을 높이자.



###### Client: No 제네릭(필요없음)

![image-20220207211518367](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207211518367.png)

- FE에 한정된 이야기지만, 중간에 있는 `setProgrammer()`라는 인터페이스를 보자.

    - **`Client(abstract class)` paper에서 `자신의 속성을 갱신시켜주는 [외부 객체 메세지]`를 허용해주기 위해 받은 메소드다.** 
        ![image-20220207212922123](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207212922123.png)

    - **지금은 Client 본인이 `Main(외부)에서` 익클 구상화를 통해` case맞는 형으로 확장된 상태`이기 때문에**

        - **`외부 객체 메세지가 필요 없게 되어` ---> 안쓰면 보안성이 더 높아진다.**
        - 클래스의 은닉성을 높이려면 ---> **외부 객체를 인자를 받는 메소드가 없는게 좋다**.

    - setProgramer( )는 **전형적인 setter의 노출**이다. ---> **클래스의 은닉성을 깨먹는 것**

        - **상태관리를 본인이 아닌 사람에게 맡기고 있다.**

    - **Main까지 밀어낼 수 있다면 == 익클로 구상화하면서 객체 생성하여 선택 할 수 있다면**

        - **`Main속 익클` 내부의 속성은 `setter로 외부에 시킬 이유가 없다`**
        - **내가 내 속성을 갱신하도록 바꾸면 된다.**

        ![image-20220207213639342](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207213639342.png)

        - setter()는 여긴 안보이지만, `abstract class Client`에 선언된 `programmer`변수에 set하는 public 메소드다
            ![image-20220207214008789](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207214008789.png)

        ![image-20220207213644956](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207213644956.png)

        - `programmer`는 말만 private 변수지 **`public method`인 setter()로 공개되어있던 상태**였다.



![image-20220207214406492](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207214406492.png)

- **public과 연동되어있는 getter/setter만 봐도 설계가 잘못되어있음을 알수도 있다.** 
    - 제대로 설계했다면, `안보이지만 추클에 있는 변수에 ---> 익클 구상화(자식)하여 직접 상태(변수)를 갱신시켜주는 class로 설계`
    - **익클 구상화 -> 자식에서 추클의 변수를 사용? -> protected 수준의 보안성을 가진다.**
        - 나만 내 프로그램을 관리한다. : private 수준
        - **나(추클) & 자식(구클or익클)만** 내 프로그램을 관리한다. : **protected 수준**





###### ServerClient: with 제네릭 for 1:N



![image-20220207214436157](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220207214436157.png)

- if분기로 나올 `그냥 ServerClient paper`가 아니라 **`case맞게 수행방식을 가진(익클 구상) && ServerClient paper`를 [객체생성 && 익클구상화]로 선택한다. **
- **`지금까지는` 메소드위임만 했다 -> 제네릭을 안 썼다.**
    - **`Director(구상형, 1) - paper(N=추상형 이지만 화살표 시작점이라, 1)가 1:1관계`라서 제네릭을 안통했어도 됬었다.**
        - **`즉, Director가 추상형이 아닌 ==  구상형 -> 자식으로 분기X 무조건 1` --> `1:1이면 if instanceof를 쓸일 X` --> `제네릭 쓸일X` ---> 자연스럽게 공통로직만 추상화해서 [인터페이스의 츠상메소드]로 올릴 수 있었다.`**
    - **만약 `Director`가 `A Director or B Director로 분기되는 추상형(자식으로 분기가능한 N)`이었다면**
        - **Main에서 로직이 Director별 분기로 인한`if instanceof`를 잡아주기 위해 ---> `제네릭`이 관여해서 `Paper <T extends Director>`로 정의했어야했다.**
            - paper를 받을 때, paper<T Director>를 가지게 될테고(?)
            - T Director에 따라 run()을 바꿔주는 경우의수를 또 나눠야함.(?)
            - 해당 메소드 T에 있는 메소드로 넘어가게 된다고 함(?)
            - **구상형을 받으면, 구상형을 제네릭으로 나눴기 때문에, 나눈 추상층에서는 구상형에 대한 수용방법을 알 수 없다. 형만 알고 있다. 그렇기 때문에 다시 개별형으로 다시 토스를 해줘야한다. 지금은 Director가 구상형이기 때문에, 보다 추상형인 Paper에게 run()의 구현을 넘겼다.**(?)
    - 지금은 **Director:구상형 ---> run()의 구현을 `보다 추상형인 Paper(N)`에게 공통로직 메소드run()을 구현하도록 넘겼다.**
        - **그러나, Director가 추상형이 되면, Director + Paper 둘다 추상형으로 N인 경우, 무게가 가벼웠던 Director에게 run()의 구현을 넘길 것이다.**
        - 설계를 해보면 == 코드배치방법은, 어느쪽이 무거운지 vs 확장적인지에 따라서 바뀐다.
        - 지금은 압도적으로 Paper가 가볍기 때문에 구상클래스에 대한 분기를 Paper쪽에서 구현할 메소드를 올린다. 
            - **진짜 수용해야할 코드는 아래코드이며, 이 코드만 왔다갔다 하는 중이다.**
                ![image-20220208005750463](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208005750463.png)
            - 아까는 Programmer안에 있었다가 -> Paper안에 있었다가 -> 지금은 Director안에



![image-20220208010135742](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208010135742.png)

- 마찬가지로, **익클구상과정에서 은닉성을 깨먹는 public setter를 ---> 자신이 갱신(할당)하도록 바꾸면,** 보안성을 높일 수 있다.



##### 2종류의 if   -> 제거방법

- **객체지향의 `추상형` ---> `instanceof가 아닌 if`를 제거**
- **객체지향의 `제네릭` ---> 1개만 가지는`if instanceof`를 제거할 수 있다.(2개이상일 땐, if를 제거하는추상화)**
    - **2가지를 섞으면** 모든 종류의 if를 제거할 수 있다.





- addProject후 runProject
    - **if는 어디 가지 않는다 ---> 마지막 클라코드, 정확히는 Main까지 밀어내고 싶다.**





#### 반복 복습하면

- 보자마자 미래에 문제가 일어날 것이다.
- 아무코드를 보면, 구조상 분기했을때 버그가 일어날걸
- 이건 코드상 context에러를 내장하고 있는데?
- 단순매핑 + 훈련으로 극복할 수 있는 내용들이다.





1.  LSP위반(추상체로 처리 안됨)을 헐**리웃원칙으로 시킬 땐 확인부터해야한다**.

    1. 추상체에게 일을 시킬 때, 나도 추상체냐?
    2. 추상체vs추상체면 -> 상위(1)에서 하위(N)으로 시키는게 맞느냐? 
        - 현재는 paper가 1이면서 상위라서.. paper에게 시킬 때, 나또한 추상체로 넘겨줘야하고, 그 개별구현체를 확인해야한다.
    3. 헐리웃원칙을 적용하기 전에 추상체vs추상체와 관계까지 확인한 뒤 적용시켜야한다.

    ![cbcf8888-1ea9-49ba-8bea-075afac149f6](https://raw.githubusercontent.com/is2js/screenshots/main/cbcf8888-1ea9-49ba-8bea-075afac149f6.gif)

2. 시행착오를 한번 겪어보기 위해 1쪽인 Paper추상체에게 알아서 하도록 시켜보자.

    1. 일단 `FrontEnd`내부에서 Paper-`Client`에게 시켜본다.

        - 시킬일을 메서드로 추출
        - Client로 메서드 이동
        - **FrontEnd(구현체)가 인자로 잡힘**
            - 넘길 때, this가 FrontEnd가 아닌  추상체Programmer로 넘어가야, BackEnd도 일을 시킬 수 있게 되니, backEnd처리시 처리해본다.

        ![aa9ac4fe-4754-4df5-a3b9-e9af59bff280](https://raw.githubusercontent.com/is2js/screenshots/main/aa9ac4fe-4754-4df5-a3b9-e9af59bff280.gif)

     2. `Backend`내부에서 Paper-`ServerClient`에게 시켜본다.

        - Paper는 이제 추상체Programmer를 받아서 일을 하긴 하지만, 파라미터로 온 `Programmer도 역시 한번에 일시키는 메서드`가 없어서, **각 구현체(FrontEnd, BackEnd)마다 서로 다른 필드 -> 서로 다른 Setter을 가질 수 밖에 없다**

            1. 인터페이스에서 안쓰는 기능도 올려주거나

                ![5c03bba3-7b9d-42e0-abc1-c95e7da7088b](https://raw.githubusercontent.com/is2js/screenshots/main/5c03bba3-7b9d-42e0-abc1-c95e7da7088b.gif)

            2. instanceof로 물어보고 개별setter만 사용하도록 처리할 수밖에 없다.

                - Programmer추상체를 파라미터로 받았는데, 개별setter를 사용해야하니, **물어보고 다운캐스팅 할 수 밖에 없는 구조**

                ![17501407-ae45-48f2-9384-171a5738e4e7](https://raw.githubusercontent.com/is2js/screenshots/main/17501407-ae45-48f2-9384-171a5738e4e7.gif)

        3. paper(1)에게 일을 시키면서, Programer(N)을 건네주었지만, 1:N관계가 유지되는 한, 

            ![image-20220618172839846](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220618172839846.png)

            ![image-20220618172850641](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220618172850641.png)

        

3. 시행착오 끝에

    1. **추상체vs추상체라면, N에게 물어보더라도 , 1에게는 시키지 않아야하는 구나**
        - **N(programmer)에서 1(Paper)를 받은 상태에서  `1:1`관계를 유지한체로 다른처리(`제네릭`)가 되어야하는구나.**
    2. **추상체vs추상체에서 instanceof를 제거하는 방법은 `제네릭`을 써야하는 구나**


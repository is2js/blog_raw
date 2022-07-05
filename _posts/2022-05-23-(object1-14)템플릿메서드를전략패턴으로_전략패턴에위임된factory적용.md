---
toc: true
layout: post
title: OBJECT 14 템플릿을 전략으로 + Factory(코드스핏츠)
description: object 책을 강의한 코드스핏츠 유튜브 요약

categories: [object]
image: "images/posts/java.png"
---

- 참고 유튜브 : https://www.youtube.com/watch?v=navJTjZlUGk
- 정리본: https://github.com/LenKIM/object-book
- 코드: https://github.com/eternity-oop/object
- 책(목차) : https://wikibook.co.kr/object/

### ch6. 합성과 의존성

![image-20220219105609538](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219105609538.png)



디자인패턴에서 가장 중요한 패턴이 무엇이냐 물어보면 **`추상 팩토리 메서드 패턴`**이라고 답할 것이다.

- 그것과 더불어 `커맨드 패턴` 정도 이다. 이 2가지가 가장 중요한 패턴이다.



책 9장부터 이어지는 `템플릿(추상 팩토리 메서드) 패턴`이 왜 복잡하게 일어나는지 `의존성`과 어떤 관계가 있는지 한번 살펴보자.





책에선 합성과 상속에 대해 나와있다.

- 디자인 패턴에서 **`합성`과 `상속`의 차이를 패턴으로 알아보는 방법은 `템플릿 메소드 패턴`과 `전략 패턴`이 문제를 해결해나가는 패턴을 생각**해보면 된다.
    - 합성과 상속의 장단점을 살펴보면 된다
- 합성과 상속을 하던 가장 큰 과제는 **`의존성`**이다.
    - 책에서는 의존성을 3장을 할당한다.
    - **그 이유는 우리는 객체로 문제를 해결할 때, `객체망`을 구성하는데, `다른 객체가 또다른 객체를 안다` = 안다는 `의존성`이 생긴 것**
        - 의존성없이는 객체망이 성립하지 않으니까, **설계상의 핵심은 의존성을 적절하게 관리**하는 것 but **적절한 것은 없다. 오직 관리하는 방법은 `의존성을 양방향이 되지 않게 관리`하는 것**
        - **단 방향**으로 만들자!







#### Template method

##### 개념

![image-20220219105912066](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219105912066.png)

- DiscountPolicy에 대해 `Template method`를 적용해보자.
    - 현재는 discountpolicy를 set으로 소유
    - `calculateFee`라는 메소드가 **템플릿메소드로 작동**하면서  
        - my) `인자1+2`를 받는 메소드가 -> `가지고 있는 list`를 돌면서 `if + 인자1 -> element가 만족` -> `인자2로만 다시 호출하는 같은이름의 추상메소드를 호출`  
        - **calculateFee라는 훅**을 다시 내려주고 있다.
    - **템플릿 메서드 -> 위에서 `내부에 인자가 다른(줄은) abstract메소드를 호출` + 아래서 `사용되는 abstract메소드가 훅`**
- **템플릿메소드를 `좋은 상속의 예`로 소개하는 이유는?**
    - 의존성의 방향이 역전(여러자식들 -> 부모 에서 부모-> 자식들로 역전)되기 때문(보다 추상화된 쪽을?쪽으로? 역전)
    - 책에서 말한 상속의 약점: 부모`<---`자식 : **자식이 부모를 쓴다 = 안다 = 의존한**다.
        - 일반적으로 **상속으로 퍼져나갈 때, 자식이 부모를 안다. 부모는 자식을 모른다.**
        - **객체지향에서 자식이 부모를 안다?( 부모를 다운캐스팅해서 사용) 다운캐스팅이라 부르며 그것은 잘못 된 것.**
            - 다운캐스팅 = 리스코프 치환원칙
            - 업캐스팅을 통해 자식은 부모를 대신할 수 있지만 부모는 자식을 대체할 수 없는 것
            - **상속을 하면 자동으로 자식 `--->` 부모를 가리키게 됨. 의존성의 방향이 자식이 부모를 가리키기 때문에 `상속은 쓰면 문제`가 되는 `나쁜놈`이다.** 
    - 왜 상속이 문제를 일으킬까?
        - **`여러개의 자식들`이 ---> `하나의 부모`를 가리키기 때문에**
        - **부모를 건들이면 여러군데가 영향을 받는다.**
            - 상속을 썼더니 의존성이 넓게 퍼져버린다.
            - 상속구조를 가진 이상, 부모를 건들이면 모든 자식이 영향
            - **부모레이어의 수정여파가 모든 자식한테 가서 `겁나서 부모를 건들이지 못하는 상황`이 된다.**



```java
abstract class DiscountPolicy {
  private Set<DiscountCondition> conditions = new HashSet<>();
  public void addCondition(DiscountCondition condition {
    conditions.add(condition);
  }                          
  public Money calculateFee(Screening screening, int count, Money fee){
    	for(DiscountCondition condition:conditions){
        if(condition.isSatisfiedBy(screening, count)) 
          return calculateFee(fee);
	}
    return fee; 
  }
  protected abstract Money calculateFee(Money fee); 
}
```

- **근데 `템플릿 메소드 패턴`은**
    1. **부모**(DiscountPolicy)**가 자식들**(이 반드시 가질 abstract protected메소드)**을 알고 있다**
        - 자식들의 반드시 가질 수 밖에 없는 추상메소드만 알고=의존하고 있다.(의존 역전)
        - **부모의 수정은 자식에게 영향을 끼치지 않는다.(상속 문제점 해결)** -> **부모와 상관없이 자식만의 독립된 인터페이스만 구현하도록 시키고, 자식의 `메소드명`만 의존**한다.
        - **`일반 상속`은 `자식이` ---> `부모의` ~~메소드명~~ 속성+메소드의 `세부 구현까지 알고서 이용`하도록 구현된다. 하지만 `템플릿 메소드 패턴`에서는 `역전`이되어 `부모가 일처리 + 자식이 미래구현하도록 확정된 오퍼레이터명`만 것을 `의존하여 부모 로직 정리` **
            - **자식은 제한되어있는 자기 책임(abstract메소드)만 구현하고 부모을 몰라도 된다.** 
        - **실제 처리는 부모가** 자식의 **메소드명만 의존한 체로 처리**한다.
    2. **자식은 부모를 몰라도 되며, abstract에 따른 책임만 구현**하면 된다.
        - `추상메서드`로 이름만 정해주니까, 자식의 어떻게 구현/수정하든 여파가 부모로 올라오지 않는다. (`독립된 인터페이스를 구현`하는 자식)
        - 그외 부모의 세부구현은 모름 -> 수정해도 상관없음



- **상속을 사용할 때는, 부모의 변화가 자식에 여파를 일으키지 않는 `스킬=템플릿메소드`을 사용해야 `좋은 상속`이 된다**고 책에서 소개한다.





![image-20220219113941060](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219113941060.png)

- 역전된 상속을 만들어내는 `훅` -> **훅을 사용하는 메서드 = `템플릿 메서드`**
    - **미래에 자식들이 구현해야만하는, 제한된 책임 = `훅`을 이용해서 `부모쪽에서 로직을 구현함`**





##### 이용(자식 구현)

![image-20220219115233188](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219115233188.png)

- 템플릿메소드를 사용한(훅을 가진)  추상체를 **상속한 자식들을 보자.** 어떻게 관계의 역전을 사용했을까
    1. **훅을 가진 부모를 상속**해서 자식을 구현한다
    2. **자식은 `부모가 처리해줄테니까 이것만 구현해라고 지어준 책임`을 구현한다.**
        ![image-20220219115506778](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219115506778.png)
    3. **자식의 `나머지 모든 구현`은 super(X) 부모속성(X) 부모메서드(X)  **
        ![image-20220219115515368](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219115515368.png)







##### 부모 -> 상속 자식으로의 여파가 없는 이유

![image-20220219120055439](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219120055439.png)

- 부모에 있는 `훅 제외`의 나머지를 보자. 

    - **부모 private** 변수 -> **자식이 부모 속성에 접근할 수 없다.**
    - **부모 public** 메서드 -> 자식(protected)가 아니라 **자식포함 대외적 아무나 쓸 수 있다.**
        - 부모가 외부에 공개하는 것
        - **부모 public 은 자식들만 사용하고 있는 부모만의 유일한 것이 아님.**
        - **`부모 public을 수정하는 안건` ---> 자식에 여파주는 안건에 포함이 안됨 ---> `자식포함 public을 사용하는 모든 애들에게 여파를 각오하고 수정하는 안건`**
            - 부모수정 -> 자식들 모두 수정 되는 일반상속과는 조금 다른 안건이다.
    - **`외부공개 인터페이스(public 메서드)외 모두 private`으로 만든 부모 -> 자식은 부모의 내용을 사용할 수 없다 -> `부모의 수정이 자식에게 여파를 끼치지 않는다.`**

- 근데, **자식도 다 private으로 구현**하면?

    - 부모는 원래 자식을 모름(=사용안함. 그리고 자식이 public으로 외부공개 아닌이상 못씀)

    - **근데 `템플릿메소드`는 부모가 자식을 알아야함(protected추메를 주고 자식public구현메만 당겨쓴다.)**

        - **`미리 약속된 protected추메`는 `자식들이 나한테 public으로 제공해줄 의무`도 포함되어있다.**

        ![image-20220219123846899](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219123846899.png)

    - 자식들은 

        - **`자기의 사정(필드, 생성자, 메소드)`을 구현**하면서 + 
        - **`부모와의 통신`은 `부모의지식사용X 미리 확정된 프로토콜(protected abstract)만 이용해서 대화를 주고 받는다.`**
        - 그 덕분에 헐리웃 원칙(tell, don't ask)을 지키고 있다. 
            - **자식은 훅을 사용해서 말하기만 함**
                - 통신대상인 부모에게서 **getter로 뭐 읽어들이지 않음**
            - **부모가 원하게 있으면 말을 검**
                - 통신대상인 자식에게서 **getter로 뭐 읽어들이지 않음.**





- **상속을 쓰는데 `템플릿 메소드가 아닌 [부모가 상속한 메서드]`이나 `private이 아닌 [protected/public부모속성들]`을 쓰고 있다면 이미 꽝**
    - 상속 = **`상속된 부모클래스를 안 건들여야 한다.`** 
        - 건들이면, 여러 자식들이 와르르 다 무너지니까
        - cascading으로 여파가 안미치려면, 자식들이 부모의 상속한 속성+메서드를 쓰지말고 약속된 메서드만 제공해주고, 부모가 일을 처리하도록 역전



- 객체망에서는  부모객체와 자식객체는 서로 다른 객체일 뿐이다. 부모객체 만들고 자식객체 만든다.
    ![image-20220219153540946](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219153540946.png)

    - 원래는  `new AmountPolicy`하면, 자식객체 뿐만 아니라 부모 객체도 먼저 만들놓고, 부모객체-자식객체 2개를 묶어서 포인터로 관리된다.

        - 캐스팅이 가능한 이유는.. 부모 객체를 먼저 만들기 때문. 어느 형으로 갈지만 선택해주는 것

    - 하지만, **객체망에서는 부모객체, 자식객체 서로 다른 것이고 따로 통신을 해줘야한다**

        - **`템플릿 메소드`는 부모-객체의 대화 패턴 중 `서로 약속되어 있는 abstract메서드`를 통해서 프로토콜(인터페이스)만 가지고 대화하도록 도아주는 패턴이다.**

            ![image-20220219154310743](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219154310743.png)

    - **`부모를 사용하면 할수록, extends한 수많은 자식들에게 여파를 끼치니`**

        - **`상속 모양은 갖추되,  부모의 속성+메서드는 하나도 쓰지마! 꺼꾸로 부모가 자식을 알아서 쓸게!`**

        - 단일 의존 포인트

            - 하나의 객체를 너무 많은 객체가 알고 있으니, 생기는 문제

            - 여파가 너무 쌔니까 이제 더이상 부모를 못 고친다.
            - 부모를 복사해서 새로운 class를 만들기도
            - 상속말고도 의존하는 것이 많아지는 것 자체가 문제

        - **부모를 (부모꺼 상속받아 써서) 많이 알고 있는 자식보다, 자식의 1개 프로토콜만 아는 부모로 관계를 가볍게 만든다.**
- 부모 ----- 자식들의 선 5개   에서 
            - 자식들의 공통 <훅> - 부모
                - 자식이 늘어나도 메소드 1개만 가지면 관계를 유지함.
        
- 템플릿메소드를 잘쓴다 = 상속의 요령을 익히는 것





똑같은 코드를 전략패턴으로 짜보자.

**상속 -> `템플릿 메소드 -> 전략`으로 짜는 것은 밥 먹듯이 할 줄 알아야한다.**

- **보통 `확장 가능성`으로 유연하게 더 연결될 것 같다? -> `전략 패턴`**
- **안정화되어서 더이상 `확장안될 것 같다`? -> 상속 구조를 가진 `템플릿 메소드`로 바꾼다.**

#### Strategy

![image-20220219155354751](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219155354751.png)



```java
public class DiscountPolicy {
	private final Set<DiscountCondition> conditions = new HashSet<>();
    private final Calculator calculator;
	public DiscountPolicy(Calculator calculator){
    this.calculator = calculator;
  } 
  public void addCondition(DiscountCondition condition { 
    conditions.add(condition);
  } 
  public Money calculateFee(Screening screening, int count, Money fee){
    for(DiscountCondition condition:conditions){
      if(condition.isSatisfiedBy(screening, count)) 
        return;
    }
    return fee; 
  }
```



- 전략패턴은 우리가 계산을 할 때 **상속이 아닌 `합성의 원리`**를 이용해야하므로 **`외부공급 객체를 받아줄 변수를 소유`를 해야한다.**
    - 부모였던 DiscountPolicy가 `private final Calcuator calcuator`라는 **객체를 소유**하게 되는데 
    - DiscountPolicy를 `상속해서 처리해줬던 자식의 구현`을 가지고 있는 **`외부의 객체`의 도움을 받게 된다.  해당 `외부객체가 템플릿 메소드의 자식구현 역할`을 해줄 것이니까 받아준다.**
        ![image-20220219160315991](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219160315991.png)



- 상속을 이용했던 이유?

    - **서로 다른** 구현을, **자식들에서 구현**하기 위해

    - **하지만, 서로 다른 구현을 하는 자식클래스가, `서로 다른 구현을 하기 위해 자신만의 상태도 가지고 있어야함`**
        ![image-20220219160611514](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219160611514.png)

        

- 전략 패턴은?

    - **`서로 다른 구현을 위한 상태`**를, 자식클래스가 아니라, **상속이 아닌 `다른 클래스에서 -> 외부 객체로 제공`**로 제공받으면 되지!
        - 객체 통신을 위해 메모리에 객체 2개가 생길 것인데, 꼭 상속으로 만들어야 하나?
            - `상속`의 장점: 두 메모리간의 연결을 `컴파일러`가 해준다.
            - `합성`:  두 메모리간의 연결을 `우리가 직접` 해야한다.

- 외부에서 서로 다른구현을 공급해줄 `객체`를 생성자를 통해 공급받고
    ![image-20220219161018228](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219161018228.png)

- **공급받은 객체로, 서다구현을 직접 구현**한다. 이 때, 필요한 상태도 상속계층이 아닌 외부객체의 클래스 내부에 위치되어있다.

    ![image-20220219161203719](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219161203719.png)



- 이 `외부객체Calculator`는 사실 `인터페이스`였으며, 서다른구현 메소드를 구현할 `추상메서드`를 가지고 있으며, **`상속이 아닌 impl구현`으로 `서로다른구현`이 가능해진다.**

    - Calculator.java

        ```java
        public interface Calculator {
            Money calculateFee(Money fee);
        }
        ```

    - 서로다른구현1: AmountCalculator.java

        ```java
        public class AmountCalculator implements Calculator {
        
            private final Money amount;
        
            public AmountCalculator(Money amount) {
                this.amount = amount;
            }
        
            @Override
            public Money calculateFee(Money fee) {
                return fee.minus(amount);
            }
        }
        ```

        ![image-20220219161530933](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219161530933.png)





##### 코드 비교

템플릿메소드가 했던 일을 전략패턴으로 바꿀 수 있다. 2개를 비교해보자.



![image-20220219161636479](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219161636479.png)

- **우: 템플릿 메소드 패턴을 이용하기 위해 `상속레이어`사용**
- **좌: 전략 패턴을 이용하기 위해 `hasA모델` = `composition모델`을 사용**
    - **`impl` 과 `extends`를 제외하고 `코드가 완전히 동일`하다.**
        ![image-20220219172401296](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219172401296.png)
        - **부모를 상속 `extends DiscountPolicy`는 `Calculator라는 인터페이스 역할`을 수행하고 있었던 것이다.**
        - **`추상클래스`를 `인터페이스처럼 프로토콜로 사용`하고 있었다.**는 말이다.
        - **상속을 잘쓰려면,  처음 부모를 짤 때  `프로토콜의 근간을 두고 있는 인터페이스`처럼 짜자!!**
- 추상레이어**(부모)를 잘짰는지 확인하는 방법 -> 전략패턴으로 바꿔본다.**
    - `추상층(부모)`을 `hasA모델(전략패턴)`로 바꿀 시 잘바뀐다
    - 잘 안바뀐다? -> 뭔가의 context를 넣어줘야한다 -> 부모 오염시 자식들 다 오염되 구조의 상속
        -  말이 부모지, 인터페이스에 준하게 상속을 짜야한다.





![image-20220219172446631](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219172446631.png)
![image-20220219172551510](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219172551510.png)

- 의존성 관계가 simple해진다. (관계도가 작다)

    - 등장인물이 2명 밖에 없다.

    - 의존성을 역전시켰다.

    - 만약, hasA모델(전략패턴)으로 바꾸면 아래와 같다

        ![image-20220219172658921](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219172658921.png)

        - 의존성을 해결하는 방법이 서로 다르다.
        - **전략패턴:** 
            - `DiscountPolicy`가 `AmountCalculator`는 모르도록 하기 위해**(순환참조X 단방향으로 만들기 위해)**
            -  **`중간 레이어(전략 인터페이스?)를 끼워넣어` (공급받을 외부객체가 될 예정) `단방향 의존성으로 해결`해버렸다.**
                - 템플릿 메소드(상속)에 비해서는  `의존성이 1개 추가`되었지만
                    - 하지만 `의존관계는 다 1:1`이다. `각각의 라인은 가벼워졌다.`
                - `끼워넣은 중간레이어Calculator(전략 인터페이스)`는 **무거웠던 `템메패턴(상속)에서 일처리하던 부모역할 DiscountPolicy`가 짊어지던 짐을  -> `전략패턴 인터페이스 객체 Calculator`가 짊어지게 된다.** 
                    ![image-20220219230026823](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219230026823.png)
                -  **따라서, 전략패턴을 사용하면 `중간에 끼워넣은 전략인터페이스`를 바꾸기는 굉장히 힘들어진다.**
                    ![image-20220219230037484](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219230037484.png)
                - 추가적으로 **전략인터페이스를 구현한 구현체자식들도 다 이쪽으로 의존하게 된다. **
                    - 양쪽으로 무거워진다.
            - **전략패턴 주의점: 프로토콜에 대한 확신이 없다면(전략 인터페이스 수정 가능성 있다면), 한번 전략패턴 도입후 수정시 엄청난 여파가 생긴다. (위/아래로 다)**
                - 모든 곳에 여파가 생기는 전략패턴
                - 상속의 부모자식관계보다 더 무거워 위험해진다.





- **전략패턴의 주의점(단점)을 생각**해보면, 제한된 목적으로 꼼짝못하게 할 때는 

    - 상속이라는 위험이 있음에도, 템플릿 메서드를 통해 `관계 단순화` + `방향성 고정`을 시키려고 하지만, **처음에는 고정시킬만큼 도메인파악이 안되니, `가운데 전략인터페이스 수정의 위험`을 가지고 있지만, 처음에는 관계도를 분리해서 여파를 흡수해주는 놈으로 먼저 쓴다.**

- **`전략패턴을 통해` 우리는 `의존성 1개 끼워넣을 때마다 어댑터를 끼워넣었다고 생각`해야한다.**

    - 벽면에 있는 콘센트 <--- 노트북을 충전

        - 콘센트 --- `어댑터` --- 노트북 충전
            - 여러 Volt 대응가능
            - 추가 기능으로서 충전시간 조절가능
            - 추가 기능으로 밧데리파워 어댑터 추가 꼽을 수 있음.

    - **그냥 콘센트에 꽂지 않고 `어댑터`를 끼워서 사용하면 할 수록 `여러 변화의 폭을 수용`할 수 있다.**

        - 스프링을 그냥 사용하는게 아니라 래핑된 클래스를 사용함 
        - jquery를 그냥 사용X -> 한번더 감싼 함수를 사용함.
        - **스프링, jquey가 업데이트되면, 래핑클래스의 대부분내용은 그대로고 업데이트 된 부분만 수정해서 그대로 사용**

    - 위아래의 **변화(여파)충격**를 `어댑팅`해줄 수 있다. **얼마나 변화가 많냐에 따라 충격흡수해줄 중간층을 몇개 끼워줄 지 결정된다.**

        - 가운데 Calculator(`전략인페`)가 충격을 흡수해준다.

            위아래 모두 `전략인페` 만 바라보고 있다 = 알고 있다 = 의존한다 -> **변화에 대한 충격은 `이용당하는 = 화살표받는`쪽에서 흡수**한다. 
            ![image-20220219231841318](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219231841318.png)

    - 반면에 템플릿메소드 패턴은 부모-자식이 직접통신하기 때문에, 상속의 방향은 역전시켜놨지만, **중간에 여파 충격을 흡수할 층이 없다.** 
        ![image-20220219231947820](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219231947820.png)

    - 위/아래 각각의 변화가 서로 심하다

        - 1층의 쿠션(`Calculator`)으로는 충격완화가 안될 것 같다.
        - Policy의 변화를 잡아줄 `Policy`전략인페 + 각종 Calculator의 변화를 잡아줄 `Calculator`인페 -> **인페끼리 서로 대화하게 만든다.**

        ![image-20220219233330001](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220219233330001.png)

- 전략패턴을 쓰면, 템메(상속역전)과는 비슷해보이지만, 2가지 차이점이 생겼다

    - 프로토콜로서  **`공용 인페`가 가운데 생긴다 -> 무거워진다.** 
        - 템메에서는 자식들과의 관계가 가볍다. 
        - 템메는 자식을 늘려도, 프로토콜에 의해 부모가 -> 자식을 거꾸로 알게되는 가벼운 관계 1개씩 생긴다 
    - **템메의 무게 = `본인(DiscountPolicy) 클래스에 존재하는 추상메서드(calculateFee)`에 대해 -> 무게가 아주 약간씩 느는 것**
        - **전략의 무게 = `중간에 끼어드는 인터페이스`가 다 감당해야하는 무게**

    





#### 비교

![image-20220220103904576](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220103904576.png)

- 템메(상속 역전): 런타임에 **타입 선택**
    - **if를 통해** `Amount`DiscountPolicy냐 `Rate`DiscountPolicy냐 **형을 선택** 
        - Type을 선택하는 순간, 그 안에 종합선물세트를 가지고 있게 된다. 해당 형인 동시에 `DiscountPolicy`이기도 하니까
    - **원리: 추상메소드를 통해서 의존성을 역전**
-  전략: 런타임에 합성
    - `DiscountPolicy`는 그 자체(인페)로 존재하고, **새로운 인페 구상체를 붙인다** = **합성한다.**
    - **원리: 추가 인터페이스로 (중간에 끼워넣어서) 의존성을 분산**



- 클라입장에서는 `템메` if를 통해 원하는 class선택해서 생성 /  `전략`  if없이 class를 생성후 -> 합성하고 싶은 객체를 if를 통해 선택하여 생성
    - if의 대상이 서로 달라진다. `세트 전체 컨택하여 if` vs `추상체 - if각구상체`
- **DiscountPolicy의 안전성은 전략패턴에서 유지**된다.
    - **초반부터 확정되어있거나 안정화되어야하는 객체다? (반대인듯?) -> 선택의 여지없이 `전략패턴`을 사용해야한다.**
    - **중간에 교체할 수 없는, 초반부터 물고 있어야하는 것이라면 -> 상속 계층을 가지고 있더라도 무조건 전략패턴으로 바꿔야한다.(전략패턴은 런타임시 포인터의 포인터 연산으로 선택하게 할 수 있으므로)**
        - runtime = 포인터의 포인터 연산이 가능한 곳 = **전략패턴 -> `Rumtime시` calculator자리에 ` 구상체로 언제든지 바꿀 수 있다`.**
        - **전략패턴: DiscountPolicy.calculator는 런타임시에도 포인터의포인터로 계속 바꿀 수 있다.**
            - 포인터의 if포인터
        - **템메패턴: new AmountDiscountPolicy()로 만들어버려서 더이상 바꿀 수 없다.**
            - 포인터의 포인터 전략X  if 포인터로 끝남



##### 각각의 단점

![image-20220220105856902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220105856902.png)

- **템메: `조합 폭팔`이 일어난다.**
    - 상속계층이 깊어지거나, 역할수행하는 자식들이 병렬구조가 아니라 중첩구조인 경우, 조합의 경우의가 계속 늘어난다.
        - 책에서는 요금제의 문제가 있다. 요금이 결정될 때, 중첩이면서 병렬이다. 같은 레벨에 여러개가 있음에도 불구하고 그 밑에 또 다른레벨이 있어서, 경우의수 계산시 조합폭팔이다.
    - 템메의 경우, 조합 폭팔(경우의 수)만큼 자식의 class를 만들어야한다.
        - 상속을 이용한 세트를 만들었기 때문에 조합 폭팔이 일어난다. 3가지의 박스 -> 각각에 120가지 과자를 선택해야한다면? -> **`그만큼 class만들어야한다.`**
        - **그에 비해 `전략`은 그 경우의 수만큼 들어갈 수 잇게 `비워두고 외부에서 선택해서 1개를 받으면` 된다.  `런타임에서 바꿔 꽂아주기만` 하면 된다.**



![image-20220220110257061](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220110257061.png)

- **전략: `의존성 폭팔`이 생긴다.**
    - **선택해서 넣어줄**  120개의 과자를 **미리 알아서 내부에서 받아줘야한다.**
        - class를 만들진 않았지만, **만들어진 class들을 알고서 내부에서 받아서 사용**









![image-20220220110722859](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220110722859.png)

- 그럼에도 불구하고, **템메는 역전된 상속임에도 `상속의 근본적인 문제`가 해결되지 않는다.**

- **`hasA모델  = 합성 = 조합 = 전략패턴`을 사용하라고 추천하는 이유는 `의존성 폭팔은 해결가능`하기 때문이다.**

    - 조합 폭팔을 일으키지 않을 곳 -> 템플릿메소드 패턴 or 전략패턴  사용가능
    - **내부에 여러 개의 (자식의존)구성요소**를 가지고 있고, 그 만큼 class를 분산해야한다면 -> 전략패턴 사용
        - calculateFee메서드 **뿐만 아니라 다른 메서드도 추상메서드를 사용해야하는 템메**라면? 조합 폭팔
        - 조합 폭팔은 막을 수 없다.
        - 세상에서 제일 좋은 팩토리메서드 패턴 -> 훅이 1개만 있는 것( **훅 2개이상부터는 이미 조합폭팔로 들어가고 있는 것**)
            - 클래스로 따지면 XXX DiscountPolicy -> 훅 2개부터는 AAA BBB DiscountPolicy
            - **오직 `calculateFee() 추상메서드 1개` 때문에 -> `Amount`DiscountPolicy가 됬었음.**
            - **훅 늘어날때마다 `이름에 훅이 들어가면서 조합폭팔로 class가 폭팔`**
            - **템메패턴 주의점: `훅 메서드 2개이상 사용하지마!`**

    - **템메는 `훅(자식에게 구현시키는 추상메서드)`을 `1개만` 가져야하고, `2개이상 들어어가야할 것 같다 싶으면 전략패턴`으로 바꿔야한다.** 

- `템메의 2개의 상이한` asbtract method(`훅`)를 정의하는 대신 -> **2개의 전략객체가 나타났다면?**

    - 2개의 다른 인페를 가졌으며, **조합을 짜도록 2개가 `안에서 서로 대화`**할 것이다.
    - 그러나 **형이 밖에서 확정되서 들어오기** 때문에 **대화는 문제가 안된다.**

    

- **의존성폭팔 차악으로 선택**했는데, 어떻게 해결할 것인가?

    - 템메 훅의 갯수 == 내부 if의 갯수 -> 코드안에 if속에 if를 해결한 것
        - 상속구조에서는 if AAA if BBB들을 각각 따로 떼어서 AAABBBDiscountPolicy로 class를 만드니 **class 내부는 단순하다.**
    - **외부 전략객체 갯수 == 외부 if의 갯수** 
        - 외부에서 선택된 객체 -> **`내부`에서 받아줄 때, `각 경우를 어떻게 쓸지를 작성하는 복잡한 코드`.** == **`의존성 폭팔(코드가 각 객체에 따라 복잡해짐.)`**
    - **너무 많은 전략객체는 어떻게 해결할 것**인가?
        - 합성모델 사용했는데, 다중 합성 객체를 가질 경우의 의존성폭팔은 어떻게 해결할 것인가?
        - 객체지향 목적의 1/2

    

    

    

    

#### 생성사용패턴과 팩토리

책 9장에서 나오는 의존성 폭팔을 해결해보자.

![image-20220220113150656](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220113150656.png)

- **코드를 자세히 바라보면, `객체 생성코드`가 있고 `만들어진객체 사용코드` 따로 있다.**
    - 이 2 코드를 병행해서 쓰지마라.**분리해서 관리하는 것이 훨씬 더 유지보수에 좋다.**
        - 생명주기가 다르다.(만드는 것은 1번만 만들고 끝. 사용은 계속)
        - 쓰는 타이밍도 다르다.(지금 만들었는데, 쓰는 것은 10년 뒤 )





![image-20220220115116170](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220115116170.png)

- `변화율에 따라 따로 코드관리`를 하는 것이 객체지향의 목표다.
    - 객체지향이 변화율을 나눠주는 것이 아니라 **`책임기반 프로그래밍이 변화율을 나눠준다.`**
        - **생성(책임)코드  vs 사용(책임)코드 : `그 사이에 38선을 그어라`**
- **둘을 나누자.**
    - 책에는 `생성코드 -> client쪽`으로 밀어라
        ![image-20220220115144457](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220115144457.png)
    - `사용코드 -> service쪽`으로 들고와라





![image-20220220115212929](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220115212929.png)

- **clinet에서 -> service쪽으로  `객체를 injection(주입)`할 수 밖에 없다.**
    - 원래는 service안에 뭉쳐있던 **생성코드를 무조건 client쪽으로 더 밀어내자.**
        - 상대적인 것이기 때문에, main메서드가 아니더라도 보다 더 밀어내면 된다.
        - 안타까운 얘기지만, 생성코드 밀어내는 것은 실력에 비례한다.
    - 사용쪽은 점점 service안쪽으로 데려오자. **생성코드를 client쪽으로 밀어내는 것은 어렵다.**
- `생성사용패턴`을 이용해서 코드를 리팩토링해야한다.
    - **알고리즘이라 생각했던 것(생성코드가 없던 것)**-> 
    - **Type(형)으로 바꿔서 생성한다.(생성 코드로 바꿔서 형을 만든다.)** (by 인페 -> 구현한 class?) -> 
    - **client를 밀어내서 생성하도록 알고리즘을 바꾼다.**(인페를 받도록 하고 인페호출하도록 코드를 짠 뒤, 외부생성한 전략객체를 받아준다?)



- **나의 떡진 알고리즘 -> 일부를 형(Type)으로 바꾸는데 성공해야한다. -> 생성해서 이용하는 코드로 바꾼다. -> 생성한만큼 바깥으로 밀어낸다.**
    - 책 11장에서 설명함.







#### 전략의 Injection

![image-20220220121356777](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220121356777.png)

- **(전략객체는) `생성자`를 통해 외부에서 생성되어 `주입`**되고 있다.
    - my) 전략객체는 외부에서 선택생성하여 주입되는데 -> **생성자를 통해 외부에서 주입**된다
- **주입되는 것은 실무적으로 불만이 많다**고 한다.
    ![image-20220220123326297](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220123326297.png)
    - 주입 = **바깥(Client생성코드)에서 주도적**으로 여기(Service사용코드) 다 넣었다.
        - clinet는 자기가 원해서 주입했으니 능동적
    - **하지만, 독립된 책임과 역할을 가진 `DiscountPolicy`입장에서는 `달갑지 않다`**
        - `왜 바깥에서 내 입에 단팥빵을 확 쑤셔넣지?`
        - `내 역할과 책임은 어디갔지?`
    - **injection은 외부에서 쑤셔넣는 것 -> `생성자로 받아주는 쪽의 역할과 책임은 약화`된다.**
    - **나는 `pushed`를 당하고 싶지 않다. 내가 원할 때만 `pull` 해줘** 
        - 헐리웃 원칙 위배. `""내가 단팥빵 먹고 싶을 때 말할 거야. 니가 왜 쑤셔넣어"`
        - **my) 생성자를 통해 주입하는 방식 = pushed하여 제어권을 잃는 방식**
        - 나쁜 코드



##### 전략의 Factory

![image-20220220152122531](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220152122531.png)

- **`제어권 역전`을 위해 `Factory`를 만들어보자.**

    - 함수형 프로그래밍에서 `지연 함수`와 같은 역할을 한다.

    - **`Factory`: 외부에서 생성코드를 받을 때, `내가 원할 때 pull해올 수 있도록`해주는 `인터페이스`를 가지고 있다.**

        - **외부에서** setter와 유사하게 **new 생성자()로 `강제로 <이미 외부에서 생성된 전략객체>를 pushed하게 주입`**하는 경우 -> `무조건 받아놔야한다..` = pushed

            ![image-20220220154524964](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220154524964.png)

        - **`Factory`을 `미리 알고 있은 체`  내가 원할 때 `pull`하여 `주입 받기(단팥빵 먹기)`**

            - `전략인페 + 전략객체-메소드호출용 추상메소드` -> 외부에서 전략객체 생성하여 공급
                `전략 Factory인페 + 전략객체-반환용 추상메소드` -> 외부에서 전략객체 supplier 공급



![image-20220220152959772](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220152959772.png)

- 재료인 money는 생성자를 통해 미리 받아야하지만, (Calculator를 사용하는 DiscountPolicy입장에서)**`원하는Calculator(전략객체)`를 `원할 때 + 캐쉬정책까지 사용`해서 `싱크로나이즈까지 걸어서 멀티쓰레드도 대처`하여 전략객체 반환**한다.

    - 전략 객체가 pushe당하면 ->그에 따른 메서드 호출만 정의해준 중간 전략인페 ->  메서드 구현만 달리해주던 전략객체의 구상체class

    - **`Factory`는 기본적으로 구상클래스를 모르게 감춰주는 역할 뿐만 아니라**
        (DiscountPolicy는 전략패턴 사용하면 client에서 만들어서 공급되므로 어차피 구상클래스를 모름)

        - **pushed가 맘에 안들어서, 외부에서 어캐 공급하든 간에 `get전략객체()`메소드를 `사용처(DiscountPolicy)에 재공하여 필요할 때 외부객체를 pull`하도록 한다.**

        

- my) **전략패턴의 `전략객체 외부공급`은 손(Factory) 쓰지 않으면 `사용을 위해 <이미 외부생성>-> 생성자를 통해 무조건 <전략객체를> 받아쳐먹어야`하는 pushed 상황이었다.**
    ![image-20220220160411281](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220160411281.png)

- **Factory를 사용하는 이유**는, `전략객체`를 받아서 바로 사용해야 하는**미리생성된 전략객체 pushed 공급**이 맘에 들지 않기 때문에  **`Factory를 먼저 pushed`해주는 작전이다.**

    ![image-20220220160634824](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220160634824.png)

    - **물론 주입 `pushed`되긴 한다. 하지만 `호출하는 전략객체(단팥빵)`을 입에 쑤셔넣는게 아니라 `원할 때 전략객체(단팥빵)을 생성` 할 수 있는 `supplier(리모콘) 인페객체`을 생성자를 통해 pushed받을 뿐이다.** 

        - my) `전략객체용-메서드호출 전략인페`만 가지고 있다가 -> `전략인페 반환 supplier 인페`를 추가 구현후 -> 구상체`supplier`를 외부에서 pushed받는다. 
        - my) `전략객체 생성을 사용처(DiscountPolicy) 필요시 내부 supplier.getter().전략객체용메서드호출`로 사용한다.

        ![image-20220220161243400](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220161243400.png)
        
    - Factory를 통해서, 조금 더 늦게 +  원할 때 + 캐슁까지 써서 pushed되는 것이 `Lazy pulled`라고 한다.
    

    

    

    
    

![image-20220220161402094](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220161402094.png)

- **`Factory로 pushed`**됬으면, 이제 **`Lazy pull`할 찬스가 생긴다.**

    - 원할 때, supplier를 통해 원하는 **전략객체(XXXCalculator)를 얻을 수 있었다. 그 전까진 메모리에 생성X** +  그이후론 **`supplier.getter() 정의부 = 해당전략 class의 변수에 캐슁`된 전략객체를 사용하게 된다.**
    - **`Factory`의 `.`체이닝을 이용한 `포인터의 포인터 getter()`는**
        - **`supplier.getCalculator()`만 보면, 캐슁된 것(?) 다른 전략객체(?) 어느 것을 공급받을지 모른다.** -> **DiscountPolicy는 Calculator만 알게 되는 `Factory`마법**
        - **`lazyPulled`는  `지연연산을 통해` 런타임에서 다른 Calculator를 받을 수 있는 가능성을 가진다.**
            - supplier.메서드()가 아니라 **supplier(포인터)   `.(의 포인터)`를 통해 얻어서 .메서드()호출**
            - 포인터의 포인터는 정적바인딩이 아니라서, 런타임에서 얼마든지 바뀔 수 있다.

- **Factory**는 의존성을 역전하진 않지만, **`push -> lazy pull의 사용성을 역전`시킨다.**

    - Factory 사용이유는 다양한데, **구상XXXX는 모르게 하는 이점도 있긴한데,**

        **현재 상황(전략패턴)**에서 DiscountPolicy는 Calculator는 알지 **`구상Calculator`는 원래 모르는 상황**이다.

        - **먼저, 사용성 역전(`전략객체 바로 pushed` -> Factory의 supplier로 `캐슁+필요시 생성하는 lazyPull`이 가능해졌다.**

    

    

##### 단순객체return Factory의 문제점(열차전복)

![image-20220220164010525](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220164010525.png)

- **클래스는 `field에 있는 지식만 알아야한다`. 즉, `포인터의 포인터에서 사용`하면 안된다.**

    ![image-20220220164101216](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220164101216.png)

    - **`열차전복사고 = 디미터의 법칙 위반`이다.** 내 필드에는 `factory= supplier`팩토리만 있고 팩토리만 사용해야하는데, getter()로 **구상(포인터의포인터)Calculator**를 불러와 그것을 사용하고 있기 때문에
        - 사용가능한 지식: 필드 / 필드들의 형 / 자기가 만든 객체 / 인자로 넘어온 객체 / 지역변수들
        - **이렇듯, `단순 객체 return 팩토리`는 무조건 `디미터 법칙을 위반`한다.** 
            - 단순 객체 return하는 팩토리는 `팩토리.getter() ` .메서드()를 사용하니까..





![image-20220220165032196](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220165032196.png)

- **디미터법칙 위반 상황에서 2가지 선택지**가 있다.

    - factory뿐만 아니라...  **`field` or `지역변수`로 Calculator를 선언하여** Calculator까지도 알도록 한다.
        ![image-20220220165139585](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220165139585.png)
- 열차전복사고는 어쩔 수 없이 포인터의 포인터도 필드or지역변수로 알게 명시해줘야한단다. 근데 그렇게 되어도.. 순환참조가 발생할 수 밖에 없다.
            - **`CalculatorFactory`는 아래쪽의 `구상CalculatorFactory`가 알고 있고**
            - `CalculatorFactory`는 객체return할 때, 추상클래스인 `Calculator`를 return한다.
            - `구상CalculatorFactory`는 객체return할 때, 구상클래스인 `구상Calculator`를 을 알고 있다.
        - **팩토리 - return객체간에 `회전풍차(Factory Circulation)`도 무조건 생긴다.**
            ![image-20220220165614239](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220165614239.png)
        
- 요약:  **Calculator도 알게 하면 답이 없다. simple Factory는 무조건 사용하지말라는 이유가 된다.**
  
- **사용처가 `factory만 아는 것을 고수해도 열차전복사고가 안나도록 설계`를 바꾸는 방법??**





#### 위임된 팩토리



##### simple Factory의 전복사고 해결

- simple Factory의 전복사고 해결 방법
    - Factory에 전략메소드()를 위임 -> 
    - 구상Factory에  getter()정의 -> getter()사용 -> 전략객체가 됨 -. 전략메서드호출()까지 하여 최종결과물을 반환
    - 사용처(discountPolicy)에서 더이상 getter().전략메서드()의 연쇄는 안해도됨

![image-20220220172123653](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220172123653.png)

- 사용처(DiscountPolicy)가 `Factory`만 알아도, 열차전복이 안일어나는 방법이 있을까?

    - **get으로 물어보지 않고 `factory에게 <최종 호출메서드 calculateFee>를 내부로 위임`을 하는 것이다.**

        - 사용처 내부에서 -> factory(supplier)가 `getter로 꺼낸 뒤, 사용처 내부에서` 메서드호출

            ![image-20220220172902891](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220172902891.png)

        - `factory에서 getter로 꺼내지않고 factory 내부에서` 메서드 호출

            ![image-20220220173112960](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220173112960.png)









![image-20220220173329333](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220173329333.png)

- my) **전략Factory도 추상인페라서.. 구상Factory가 있으며, `구상Factory에서 해당 전략객체를 반환하는 getter가 정의`되어있었다.**
    ![image-20220220152959772](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220152959772.png)



- **이제는 `구상Factory에게 메서드호출()의 책임`이 위임되었다. **
    **my) `중간에 getter로 토해내지말고` -> `뒤에 연결된 것도 다 해결해서 반환`**

    ![image-20220220174409904](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220174409904.png)

    - **`구상Factory 내부`에서  `Factory의 기본역할인 캐슁+getter`이외에 `전략메서드()까지 호출한 결과값을 반환하도록 수정`** 
        - my) getter()로 중간값을 내뱉던 (구상Factory)놈에게, **니가 정의한 getter를 내부에서 사용 -> (runtime에선 전략객체 상태) 전략객체의 전략 메서드호출()하여 최종결과물까지 반환 (하도록 전략메서드를 Factory에서 정의하여 책임부여)**
        - **구상Factory안에서  `getter->(전략객체)->전략메소드까지 호출` 되도록 `Factory에  getter가 아닌 전략메소드의 책임 위임` 이 되어야, DiscountPolicy는 Factory만 알고 Calculcator를 몰라도 되는 상황이 된다.**
            - Factory에 getter의 위임만 보통있다. -> **`getter는 구상Factory로 옮기고` 거기서 사용하게 한다.**







![image-20220220175850784](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220175850784.png)

- 사용처(DiscountPolicy)는 Factory만 알면된다. 
    - factory.전략메서드()호출로 정의해서 끝난다.
        ![image-20220220175918219](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220175918219.png)



- 위임된 팩토리가 아니면 디미터의법칙을 해결할 방법이 없고, 
- 만약에 어겨서 2개(Factory + 전략인페)를 다 알게 해도 무조건 순환참조의 상황이 되게 한다. 



- `전략패턴`에 + pushe주입해결을 위한 `팩토리`를 쓰는 순간  -> `위임된 팩토리`를 쓸 수 밖에 없다.







##### 위임된 팩토리의 정체

![image-20220220180205155](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220180205155.png)





![image-20220220180538421](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220180538421.png)

- **`위임된 팩토리`의 정체는  원래 `전략 인페(Calculator)` 그 자체구나!!**
    - 위임된Factory -> `전략인페`로 수정해야한다.
- **`전략 인페`는 `Factory`로 구상할 수 도있고, `구상클래스`들로 구상할 수 도 있던 것이었다.!**
    - pushed 주입을 해결하기 위해 getter+캐슁해주는 Factory를 도입했다.
    - 열차전복을 해결하기 위해 Factory에는 전략메소드책임을 -> 구상Factory에는 getter()이외에 전략메소드()호출해서 최종결과물을 반환해줘야한다.
    - 그랬더니 Factory는 전략인페와 완전히 동일한 놈이 되었다. -> Factory자리에 전략인페로 교체함



![image-20220220181021716](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181021716.png)

- Factory대신 전략인페를 넣어주면 된다.
    - **사용처 DiscountPolicy는 `Factory`대신 `전략인페`를 아는 것으로 돌아가버렸다.**
    - **`위임된 팩토리`의 특징은  팩토리가 안보인다**는 것
        - **전략인페의 책임(전략메소드)을 위임받았다면 -> 전략인페 == 위임된팩토리 똑같기 때문**





![image-20220220181056735](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181056735.png)



![image-20220220181102383](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181102383.png)

- **보기에는 삼각형의 순환참조가 남아있는 것 같다.**

    - 하지만, 구상Calculator인 `AmountCalculator`가  `AmountCalculoatrFactory`의 역할을 대신할 수 있기 때문에 `AmountCalculoatrFactory`는 사라진다고 한다.

    - **`위임된 팩토리`를 사용하면 기존 3개층만 남게된다. -> `원래 인터페이스를 수정하여, 회전풍차 4각형을 쳐내서 기존 형태로 만들면서 구현하게 된다.`**

        ![image-20220220181742994](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181742994.png)







#### 추상 팩토리 메소드 패턴 for 의존성폭팔

![image-20220220181900274](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220181900274.png)

- **전략 -> `의존성 폭팔을 해결`하기 위한 패턴**
    - 위임되든, 위임되지 않은 팩토리건 간에 **여러 계층의 객체를 반환할 수 있는 능력**을 가지게 되는 패턴이다.
    - 위에서는 객체 1 종류만 반환해도 디미터법칙 위반 했었다.
        - **객체 2종류를 반환하면 어떻게 될까**





##### Factory에 의존성폭팔 만들어보기(2종류 전략 공급으로 늘리기)

- 아직까지 **의존성 폭팔**이 발생하지 않았다. **Factory에서 공급받는 외부`전략의 종류` 1개(Calculator) 뿐이기 때문**



![image-20220220182921161](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220182921161.png)

1. **`conditions`들을 Factory에 위임해서 -> Factory에서 받아올  예정**이다.
    - 변수 + 기능을 일단 없애준다.
    - 우리는 (전략인페 ->) 전략인페동급Factory만 일단 알고 있으면 된다.



![image-20220220183112073](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220183112073.png)

2. 기존에 의존성 1번째 전략객체getter -> 전략메소드인 `getCalculator -> 전략메소드 calculateFee()`에 추가하여 **`2번쨰 공급 전략객체getter인 getConditions()`를 받아온다.**

    - Conditions도 getter()로 팩토리에서 공급받게 되는데,
        Calculator도 원래는 getter()로 공급받으나 위임된팩토리로서 메서드까지 호출하게 되었다. 그래도 2종류를 공급하는 팩토리가 되었다.

        - 더이상 Calculator가 될순 없다. -> **PolicyFactory라는 별도의 `추상레이어`를 만들어  `기존 전략인페(위임된팩토리)` 이후 `2번째 전략을 공급받아줄 2번째 전략인페`만들어서 중간에 끼워넣을  필요가 있다**

            ![image-20220626141255493](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220626141255493.png)


![image-20220220184244911](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220184244911.png)

3. Calculator이면서, getConditions() - set객체를 반환해주는 getter를 가진 Factory여야한다.
    - `전략1종류 - 전략2종류를 가진 구상체`의 코드를 (아래 그림에서)보면, 지금은 괜찮아보이긴한다. 외부에서 2종류만 받으니까...
    - 하지만 **여러 종류의 객체를 반환하는 경우가 되면, 그 반환 객체마다의 `추상(전략)인페`가 존재하게 되고, 그만큼 구상 조합의 수가 많아지게 된다.**





![image-20220220185128201](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220185128201.png)

- **`conditions`의 상태**가, 원래는 사용처 `DiscountPolicy`에 편하게 있다가 ->**`Factory`가 공급받는 식**으로 바뀌어서 -> **`구상Factory`로 이동** 
    - my) Factory는 getter() or 전략메서드명만 명시하는 인페일 뿐, 상태+기능은 구상Factory에서
- VO가 아니라 포장만 해서 그런지, add/remove기능도 구상Factory에 정의
- 구상Factory의 주 목적인 getter `getConditions`를 제공한다. 

- 이제 Factory의 구상Factory마다 수출품은 2개가 된다. 
    - Calculator -> 직접 getter로 제공하지 않고, 메소드위임받아서 결과값을 주고
    - conditions(set) -> getter로 직접 제공
        - **2개 이상 수출품을 가지는 Factory가 `추상 팩토리메서드 패턴`의 대상들이다.**







**우리는 `추상 팩토리메서드 패턴`를 왜 쓸까?**

- 사용처(DiscountPolicy)가 **의존성 화살표를 줄일려고 -> 의존성을 `구상Factory로` 몰아줄려고** 
    - Set
    - DiscountCondition
    - calculator
        -  **-> 의존성이`Factory`  1개 아는 것**으로 바뀌게 되었다.
        -  **-> 내부if에 해당하는 case를 `구상Factory`로만 구현만 할 수 있으면, 생성사용패턴으로 바꿀 수 있게 되었따.** 



- DiscountPolicy -> 구상Factory로 몰빵된 의존성 -> 
    - **구상Factory를 계속 찍어내면**, `알고리즘(if , if의 조합)`을 바꿀 수 있게 된 것이다.
        - **안에서 if로 처리하던 것을 class로 바깥으로 밀어낸 뒤, 바깥에서 공급** 받을 수 있게 된 것
        - 생성사용패턴으로 바꾸게 된 것
        - Factory패턴의 진정한 의미
            - 내 원래 코드 -> 인터페이스로 바꿈 -> 각 case마다 형(class)로 만들고 -> 바깥에서 생성하도록 밀어냄
    - 우리들의 코드에서 `추상팩토리` or `팩토리`가 등장하지 않는 이유:  코드를 형으로 바꿔서 바깥에서 공급받도록 개조하지 않았기 때문 -> 형을 통해서만 공급받아야한다.
    - 그냥 주입하는게 아니라 `Factory`를 통해 주입 -> `lazy Pull 땡기는 타이밍을 정할 수 `있게 되었다.



![image-20220220200300130](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220200300130.png)

- 이제 Factory만 받아오면 된다. 
    - factory에게 getConditions
        - **`new HashSet의 생성코드`도 `Factory`에게 다 넘어갔다.**
    - factory에게 Calculator역할의 메서드 호출
- 하지만, 문제가 남아있다.



![image-20220220200724416](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220200724416.png)

- **forEach**를 쓰고 있다. -> **`열차 전복사고`가 안에 내장**되어있다.

    - `set.iterator() 호출이 생략`된 for문 -> 열차전복사고 중

- DiscountPolicy는  `<Set>Conditions`가 무엇인지 모른다 -> 무엇을 반환하는지도 모른다.

    - **만약, 뭘 가지고 있는지 안다? == 디미터 법칙에 의해 필드로** 잡아서 받아줘야한다.

- **`<Set>Condition`뿐만 아니라, `DiscountCondition`도 뭔지 모르는 상태 -> 디미터법칙이 2중으로 일어나고 있다.**

    ![image-20220220201355471](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220201355471.png)

    - **디미터법칙은 위임을 통해 해결**한다.![image-20220220201423238](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220201423238.png)





![image-20220220201438499](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220201438499.png)

- `Factory`에게 `하늘색 박스 == DiscountPolicy가 모르는 부분`을 모두 위임시켜야한다.
    - 모든 Factory마다 동일한 policy의 공통로직이다.
    - **`인터페이스의 공통 로직`은 Factory라는  `인터페이스의 default메소드`로 구현하면 된다.**



![image-20220220201633329](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220201633329.png)

- 상속한 Calculator 것들은 구상Factory에서 구현하도록 두고
    - 공통로직 위임받은 것을 default메소드로 새로운 calculateFee()를 정의해준다.
    - 이 때, **`getConditions()로직은 본인안에서 쓰게 된다 == getter()를 자기가 써서 최종결과물을 반환하는 디미터 해결`**
    - Factory는 원래 DiscountCondition을 알고 있었다.(원래 공급해야할 놈)  -> 사용해도 괜춘 디미터법칙위반X
        - DiscountPolicy는 몰라서 문제가 됬었음.
    - `return calculateFee( fee )`의 부분은  이 인페를 구상할 구상클래스가 만들어주는 `템메(상속역전)`패턴도 사용된다.





전략 2종류를 공급하기 때문에, 2개의 훅이 생겼을 것이다.

1. Calculator 상속으로 인해 -> 구상Calculator에서 받아오는 템메 훅
2. getCondtions라는 훅

   



원래 `훅 2개의 문제점 == 템메의 조합폭팔`의 문제점들을 ---> Factory에게 밀어버렸다. ---> 외부에 밀어놓으 전략+Factory

![image-20220220202835752](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220202835752.png)

- 최종 `DiscountPolicy`를 보면
    - Factory를 받아서, factory에게 calculateFee()를 위임하는 끝난다.
    - 진정한 `사용코드` 밖에 없다
    - `생성코드`밖에 없다?







![image-20220220203241684](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220220203241684.png)

- **DiscountPolicy에 `생성`이 관여 or `중복 로직` or 더큰 문제는 `의존성 폭팔을 일으키는 <내가 모르는 것>`이 있나? 살펴보자.**
    - Factory입장에서, 내가 알고 있는 애들끼리의 관계 -> **의존성폭팔(공급받아 가지고 있지만, 애들 사용법 지식을 다 알아야한다)**
    - DiscountPolicy입장에서, 
        - Factory로 부터 공급받은 `<Set> Conditions`와  `Calculator`에 대해 **사용법 지식을 알려줘야한다.**
            - 보통은 공급받았다? -> field(변수)에 잡아놨다. -> 사용하는 지식은 어떻게 알아야하나?
            - DiscontPolicy가 `set conditions -> loop -> if 걸리면 -> calculateFee()`의 지식을 다 알아야할까? 
        - **Factory를 만든 이유(지식 줄이고 Factory만 의존시키기)를 위해서 `의존하던 모든 것들을 Factory에 이동`시켜야한다.**
        - **Factory -> 공급하는 2종류의 사용법도 Factory가 지식을 알도록 거기서 다 사용해서 최종결과물만 공급하도록 -> 이사시킨다.**



- `추상 팩토리 메서드 패턴`도 `위임된 팩토리`패턴을 가지게 된다.
    - 팩토리 패턴이 어려운 이유: 위임의 시작 + 1종류보다 더 많은 것(공급하는 것들)을 가져오게 됨.
    - calculator와 condition만 공급해서 위임받을려 했는데, screening에 대한 책임까지 떠넘겨졌다.
        - Factory가 screening에 대한 의존성도 생기게 됨
        - DiscountPolicy의 의존성을 낮추려는 것이 목적. 보호하게 되었음.



- 따라서, 아키텍쳐 상으로 가장 먼저 결정해야할 것은 **변화에 가장 강하게 대응할 수 있게끔 `보호해야할 객체`가 누군지 알아야한다.**
    - 가장 변화가 생기는 부분부터 챙긴다. -> 추가/변화/늘어나는/미정의 부분을 먼저 찾고 -> **상속도 받지않는 `안변하는 클래스`를 먼저 만든다. -> 이것을 기준으로 `책임, 역할이 이 class로 향하도록` 짠다.**
    - 설계요령: **의존성을 주입할 때, `변하지 않는 class`에다가 의존성(`factory` 등)을 주입한다**
        - DiscountPolicy가 변하지 않는 이유 -> 모든 DiscountPolicy의 변하는 부분을 Factory(다른 놈)이 받아가서
    - **왜 DiscountPolicy를 보호할까?**
        - **`포인터`(DiscountPolicy)의 `포인터`(변하지 않는 놈에게 주입하는, 향하는 모든 것들, `Factory포함`)를 이용하려고**
            - Factory는 포인터(DiscountPolicy)의 포인터기 때문에, runtime시 바꿔끼워넣을 수 있다.
            - 하지만 `변하지 않는놈을 -> 확정포인터`로 기준 코드를 쫘났기 때문에, 내가 알고 있는놈은 `절대 변하지 않게 된다`.
            - 변하지 않는 사람에게 <-- 가짜 가면을 제공하고 <-- 가면을 계속 바꿔준다.





- **코드를 짤 때, `의존성`을 생각해서 상대적으로 짠다.**
    - 내가 어떤 코드A를 수정하는데, 의존하는 객체B가 있더라
    - **변하지 않으면서 의존되는 객체B부터 빨리 확정**지어야 -> **my) 거기에 `전략인페orFactory만`만 걸어놓고** 변하는 코드A를 짤 수 있다.
        - 도움을 받아야하는 객체들 -> 모두 변하지 않게 만들어버려야한다. by 팩토리로 다 넘기고, 포인터의 포인터로 이용하게
        - 포인터의 포인터(Factory)에게 싹다 넘기자.
        - **포인터(DiscountPolicy)는 변하지 않으니 -> 이놈을 의존하는 코드들은 (내가 짤 코드 포함) 안전해질 것이다.**
        - 내가 의존하는애들부터 확정시키자.


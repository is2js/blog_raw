---
toc: true
layout: post
title: OOP2) 객체지향프로그래밍(python)
description: 유튜브 python 객체지향 tip 5
categories: [oop, 객체지향, python]

image: "images/posts/python.png"

---

- [참고 유튜브](https://www.youtube.com/watch?v=-ghD-XjjO2g&t=1s) 

#### 01 You can combine FP and OOP



- `processor.py`에는 class(OOP) 뿐만 아니라 class내 메서드가 아닌 **bottom에 function(FP)도 정의해서 사용한다** 

  ![image-20220615234551447](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220615234551447.png)
  ![image-20220615234636728](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220615234636728.png)![image-20220615234426679](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220615234426679.png)

- `payment.py`에서도 class + function을 사용함

  ![image-20220615234726568](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220615234726568.png)







#### 02 Make classes either behavior-oriented or data-oriented

- 같이 돌아다니는 데이터의 묶음도 class `CreditCard`의 **`명사`로 class명을 뽑는다.**

  - 꼭 method가 아니어도 된다.
  - **acess하기 편하기 위해서다!**
    - **나같으면, 상위도메인과 묶어서도 쓸 것 같다.**

  ![image-20220615234918419](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220615234918419.png)



- 이 class `PaymentProcessor`는 data-oriented하진 않다. **behavior-oriented하여 클래스명도 `~or`로 지은 것 같다.**

  - api-key를 생성자로 받아서  체크하는 기능만 가진다.

  ![image-20220615235121865](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220615235121865.png)





- `Order` class는 데이터 지향적이면서, 상태값을 바꾸는 메서드가 있다.

  ![image-20220615235403039](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220615235403039.png)





#### 03 Be careful with inheritance

- 상속해서 새로운 level을 만들면, 부모의 변화가 자식에게 번지게 되어 복잡해진다.

  - **인터페이스를 위한 Protocol (typing)상속이나 추상클래스(ABC, abstract based class)를 제외하곤 상속을 사용하지 말자.**

- 인터페이스를 Protocol을 상속해서 만든 뒤, 어느 라이브러리나 class가 확장성있게 사용하도록 한다.

  ![image-20220616000143188](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616000143188.png)









#### 04 Use dependency injection

- 의존성 주입하지 않고, 의존해서 사용하면?
  - 사용되는 객체가 바뀌면 -> 사용하고 있는 놈에게도 변화가 미친다. (**상속(부모)도, 의존(구현클래스)도 영향 미치는 것의 문제**)
- **자바에서는 인터페이스로 추상화한 뒤 주입받는다.**
  - 다양한 의존성이 주입될 수 있음.
    - 테스트하기 좋아짐
  - 구현클래스와의 결합도가 낮아짐.
  - 가독성 좋아짐

- 사용하지 않은 예

  ![image-20220616000356005](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616000356005.png)

- 의존성 주입으로 바꿈

  ![image-20220616000847278](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616000847278.png)

  - **복잡한 클래스를 test시 쉬운 fake객체로 대체해서 한다.**





#### 05 Don't abuse Python's power features

- python 내부에서 작동을 컨트롤할 수 있는 메서드를 사용할 수 있다는 장점을 가지는데, 예를들어, `매직 메서드`나 `인스턴스변수` 등이다.
  - **전문가가 아니라면, 재정의해서 쓰지말고, factory method를 통해 객체를 생성하자**



- **잘못된 예시**: 들어오는 string인자 값에 따라, 각 Class객체를 만들도록 한다.

  - 호출은 `Payment("string_payment_type)`으로 Payment의 생성자를 호출하지만, **Payment객체는 아예 생성이 안될 것이다.**

  ![image-20220616001248118](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616001248118.png)

  ![image-20220616001744744](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616001744744.png)







- **잘된 예시**

  1. 제한된 종류의 string은 **Enum을 상속한 class로 정의여 각각을 객체로 만든다.**
     ![image-20220616001953146](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616001953146.png)

  2. 생성될 제한된 종류의 객체class들을 **Protocol을 상속한 하나의 인터페이스로 만든다.**

     ![image-20220616002100529](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616002100529.png)

  3. 각각의 class들은 **인터페이스를 구현(상속)해서 정의한다.**

     ![image-20220616002128616](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616002128616.png)

  4. **Enum (key) - 구현Class(value)의 쌍을 dict로 정의한다.**

     - class를 ()없이 value로 넣어놨다면, **dict에서 get한 뒤 `()`만 붙여주면 생성자가 호출된다.**

     ![image-20220616002217679](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616002217679.png)

  5. 사용할 때는, string대신 enum을 -> dict에 넣어 -> 원하는 구현체를 꺼낸다.
     ![image-20220616002344317](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220616002344317.png)
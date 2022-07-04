---
toc: true
layout: post
title: Object)Generic for remove instanceof+@(개발자의세계)
description: 추상체를 확인해야할 경우->자신이 여러형을 알아야할 경우

categories: [java, 우테코, oop, object, 개발자의세계, generic, instanceof]
image: "images/posts/java.png"
---


#### 추상층 + 제네릭  -> 특정형을 아는 구상층을 만들어 1:1관계의 instanceof 제거

- 현재 `Programmer`의 구상체 `FrontEnd, BackEnd`의 개별구현 훅메서드(@Override, protected)들에서 추상체 `Paper`를 인자에 받았음에도 `instanceof`로 Paper의 구상체(Client, ServerClient)를 다운캐스팅해서 확인한다.
   ![20220619225828](https://raw.githubusercontent.com/is3js/screenshots/main/20220619225828.png)


1. **instanceof 제거 전에 `확인`해야할 사항**

   1. **추상체에게 각 구상체들을 물어보지말고 시키면 안되는가? `관계` 확인하기**

      - Paper에게 알아서 하라고 this와 함께 시키면?
      - Paper : Programmer =  1: N관계
        - **N(Programmer)이 1(Paper)을 받아 처리하는게 최적화된 상태로 현재 잘 맞는 상태다.**

   2. **제거할 `instanceof 가 1개`**가 맞는가?

      - **instanceof가 2개이상 사용**되었다는 말은, 1에게 N을 넘겨주어서 일이 많아진 것이다. 
        - 1:N관계가 생긴 이유는, **1에게 N을 넘겨줘서 일이 복잡해진 것**이다.
        - N에게 1을 넘기도록 바꿔야한다. (**차후 Director - paper간 나옴**)
      - **제네릭은 instanceof 1개만  가진상태에서 제거 가능**하다. 
        - 추상층에는 T extends 타추상형 
        - 구상층이 T대신 1개의 타구상형만 알게할 수 있다. -> instanceof 대신 구상형을 사용한다.

      ![f4691c82-5893-4865-a3c1-60e84e0b5ff0](https://raw.githubusercontent.com/is3js/screenshots/main/f4691c82-5893-4865-a3c1-60e84e0b5ff0.gif)

   3. 추상체를 **왜 구상체로 다시 확인하는지 -> 추상층 정보는 충분한가**?

      - 추상체로 받았는데, 또 구상형으로 확인하는 이유는, **추상체에 정보가 작기 때문**
        - 확인해보니, 추상체에 **공통로직으로 처리되는 템플릿메소드로 묶을 정보자체가 구상체들에게 존재하지 않는다**.

      ![4a36fb95-f81b-45ff-b3dd-3ddaddd6ade5](https://raw.githubusercontent.com/is3js/screenshots/main/4a36fb95-f81b-45ff-b3dd-3ddaddd6ade5.gif)

      
      

2. **`제네릭`을 통해 `각 구상층들`이 instanceof 대신 `추상형의 특정형을 미리 알게` 하기**

   - 구조
     - 추상층(Programmer) - `<T extends Paper>`
       - 추상체 Paper를 받는 곳에서 추상체Paper 대신 -> `메서드 인자(or변수)에 T형`으로 받기
     - 구상층들(BackEnd, FrontEnd) - **T형 추상층 -> 특정형 추상층으로 상속**하여 `T자리에 특정형을 사용하는 자식으로 태어나기`
       - **my) `T형 추상층`을 `특정형으로 상속` -> `특정형을 아는 구상class`로 태어나기**
       - my) T형 추상클래스 -> 자신class를 특정형을 아는 자신으로 분신술 (차후 나옴)

   - **적용 순서**

     1. **구상층 1개에서 **

        - **파마리터의 사용을 추상형(Paper) -> `특정형(Client)`으로 변경하여 특정형만 아는 구상층**으로 만든다.

        - 추상층에서 T extends Paper를 사용했다고 가정하고, **추상층(Programmer) 상속시 `특정형 추상층`인 `Programmer<Client>`을 `상속`한다**

        ![161f415f-1cf0-4d4a-8f1c-693091d39389](https://raw.githubusercontent.com/is3js/screenshots/main/161f415f-1cf0-4d4a-8f1c-693091d39389.gif)

     2. **추상층은 구상층이 특정형을 사용할 수 있도록,  **

        - **사용 메서드를 추상형 파라미터(Paper) 를` T형`으로 변경하고**
        - **기존 추상형의 구상층들로 `제약 걸린 T형 파라미터 with <T extends Paper> 제네릭`**을 사용한다.

        ![6a7fc472-bdb8-41c5-9887-6bb2f5139a00](https://raw.githubusercontent.com/is3js/screenshots/main/6a7fc472-bdb8-41c5-9887-6bb2f5139a00.gif)

        

     3. 나머지 구상층(BackEnd)도

        1. 구상층에서는 특정형을 사용 + **\<특정형\> 추상층을 상속**
        2. (이미 되어있음)추상층에서는 T형 사용 + **\<T extends 추상형\>제네릭을 달아주기**

        ![267a6f6e-9eb3-41ff-8065-a35ab2b7c626](https://raw.githubusercontent.com/is3js/screenshots/main/267a6f6e-9eb3-41ff-8065-a35ab2b7c626.gif)

   

#### 구상층에 제네릭+추상클래스화  -> 필요한 여러 특정형을 아는 자신의 개별구현분신술(익명클래스)을 만들어  1:N관계의 instanceof 제거

1. **문제상황:** 

   - Client형을 아는 FrontEnd외에, ServerClient를 아는 FrontEnd도 필요하다. 
     - 특정형을 안다? -> 개별구현로직이 다른 구상층이다.
   - 매번 구상class를 생성할 수 없으니, **제네릭 + 추상클래스화를 통해, `익명클래스로 분신술로 생성하면서 개별구현로직을 구현`한다.**

   ![image-20220619222127837](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220619222127837.png)



2. **적용순서 in FrontEnd**

   1. 분신술(여러형을 알아야하는) 구상층(FrontEnd) class를 **abstract class화** 하여 추상층으로 만들고

   2. 중간추상층이지만, **추상층으로서 `제약걸린 T 제네릭`을 걸어서**, 구상층(익명클래스, 분신술)이 특정형을 알 수 있게한다.
      ![5263744d-829e-4c08-aab0-6fd55c9531a7](https://raw.githubusercontent.com/is3js/screenshots/main/5263744d-829e-4c08-aab0-6fd55c9531a7.gif)

   3. **상위 추상층(Programmer)**의 `특정형 추상층` 상속되던 부분을 -> **`T형 추상층`으로 바꿔, 중간추상층인 FrontEnd가 T를 아는 구상층(중간추상층)이 되게 한다.**

      ![8a0dfb6e-923b-4ffb-a653-bb226d361430](https://raw.githubusercontent.com/is3js/screenshots/main/8a0dfb6e-923b-4ffb-a653-bb226d361430.gif)

   4. **구상층이 -> 중간추상층이 되었으므로, `특정형마다 개별구현 로직 = 훅메서드 setData(@Override + protected)`를 구상층이 구현하도록  `사용처에 로직을 복붙 보존`해놓고 `추상층에서는 훅메서드를 삭제`한다**

      ![3e47db7e-0c85-431f-99ab-5c7acb1d5e72](https://raw.githubusercontent.com/is3js/screenshots/main/3e47db7e-0c85-431f-99ab-5c7acb1d5e72.gif)

   5. 기존에 구상층으로서 객체 생성하여 기능하던 곳에서 **알 필요한 특정형마다 개별구현 분신술인 익명클래스를 구현해서 하나의 구상체로 사용**

      - 익명클래스는 **일반 구상층이 상속한 `extends 특정형 추상층`상속 없이 `바로 특정형 제네릭을 사용`하여 `implements로 훅메서드 개별 구현`**하기만 하면, 해당 특정형을 아는 구상층 익명클래스가 된다.
        - 제네릭을 선택안하면 upperbound 추상형(Paper)를 아는 구상층이 되기 때문에, 제네릭을 정해줘야한다.
      - **익명클래스(추상클래스 구상층)에서 추상클래스내 필드를 안보이는 상태로 물려받기 때문에, `구상층에서 물려받은 필드 할당시 protected수준으로 만들어`주기만 하면 편하게 자식이 사용해도 된다.**
      - **`FrontEnd`**의 특정형을 아는 개별구현 분신술 만들기

      ![eb8bf6b4-a25c-4241-bed7-fcf675b9d391](https://raw.githubusercontent.com/is3js/screenshots/main/eb8bf6b4-a25c-4241-bed7-fcf675b9d391.gif)

   6. **`BackEnd`**의 역시 여러특정형을 알 수 있는 훅메서드 개별구현 분신술을 만들어간다.

      1. abstract 화+ 제약걸린 제네릭 + 상위추상층(제약T제네릭)을 `T형 추상층`으로 상속
      2. 추상형 파라미터 대신 T형으로 사용(분신술에서 특정형을 선택해서 앎)
      3. 개별구현로직(훅메서드)를 삭제한 뒤, 분신술들이 개별 구현
      4. 추상클래스 필드를 자식이 사용시 proected로 사용

      

      

      

   


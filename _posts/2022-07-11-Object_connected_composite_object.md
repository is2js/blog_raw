---
toc: true
layout: post
title: Object) Connect to Strategy Object (Plan2)
description: next필드를 이용하여 전략객체들이 다음 타자를 가지고 있도록 연결

categories: [java, 우테코, oop, object, strategy, plan, context ]
image: "images/posts/java.png"
---

#### context를 인자로 공유하는, 연결될 합성객체는 개별로 [생성자 주입받는 다음타자 next필드] + [return null터미네이닝 재귀호출]로 연결된다.(실습)

- **연결합성객체는 재귀처럼 다음 연결된 합성객체를 호출한다.**
  - 재귀처럼 호출하려면, **if 종착역 끝 + return 종착역 아니면 다음타자 호출**한다.
    - 종착역은 다음타자가 null으로서 없을 때 누적변수를 반환하는 것이다.
  - 전략객체는 전략메서드로서 시그니처가 통일되었으므로 **주체만 next필드에 미리 담아놓아야한다 -> 생성자 주입 받는다.**
  - **만약, 생성자 주입에서 null이 입력되면, 다음타자는 없는 것으로 한다.**





- PricePerTime

  ![image-20220707171845735](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220707171845735.png)

  1. **생성자로 동급의 다음 합성객체(전략객체)를 주입**받는다.
     - 이후 client에서 사용은 데코레이터 패턴처럼 포장하는 모양을 가지게 된다.
  2. 나의 할일이 끝나고, **return문에 재귀함수처럼 next필드에 있는 다음타자의 전략메서드를 호출**하되, 다음타자가 없으면 `== null` 현재의 결과누적변수를 반환한다.

  ![341c54db-f1df-4b69-8ed3-d9124a9f2b18](https://raw.githubusercontent.com/is3js/screenshots/main/341c54db-f1df-4b69-8ed3-d9124a9f2b18.gif)

  ![image-20220711093826610](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711093826610.png)

 - **early return + 바로 return은 3항연산자**로 변경할 수 있다.

   ![9f2dc63a-45fb-4567-a466-30a8685ef3ef](https://raw.githubusercontent.com/is3js/screenshots/main/9f2dc63a-45fb-4567-a466-30a8685ef3ef.gif)

   ![image-20220711094223741](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711094223741.png)



- 나머지 `NightDiscount`는 같은 처리
  ![a8d6258e-c96a-4a29-b1ab-feae7c3b97c9](https://raw.githubusercontent.com/is3js/screenshots/main/a8d6258e-c96a-4a29-b1ab-feae7c3b97c9.gif)

  ![image-20220711094601810](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711094601810.png)

  

- `Tex` 전략객체는 새로  추가해준다. ratio만큼 세금만 뗀다.

  ![0f3771ab-3bac-44d4-8db9-5a62d48476c6](https://raw.githubusercontent.com/is3js/screenshots/main/0f3771ab-3bac-44d4-8db9-5a62d48476c6.gif)

  ![image-20220711095044853](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711095044853.png)



- `AmountDiscount`도 추가한다.

  ![e43a798d-ea63-4f8b-aa40-15a0f826911f](https://raw.githubusercontent.com/is3js/screenshots/main/e43a798d-ea63-4f8b-aa40-15a0f826911f.gif)

  ![image-20220711100037327](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711100037327.png)





- Main에서 사용법

  1. **각 전략객체를 `데코레이터 패턴으로 생성자 주입`하면, 콜백 지옥의 장풍형태가 만들어진다.**

  2. **다음 타자가 없을 경우, `생성자에 null을 입력하여 터미네이팅하는 재귀호출`형태다.**

     ![4cb1e78b-d85e-4232-915d-b48588c35498](https://raw.githubusercontent.com/is3js/screenshots/main/4cb1e78b-d85e-4232-915d-b48588c35498.gif)

  ![image-20220711100535473](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711100535473.png)





#### 유사 데코레이터 패턴의 Clinet코드 및 내부 중복로직을 추상클래스로 제거하기(설명)

- client에서 사용하는 유사 데코레이터 패턴의 **생성자 체이닝** (문제점)

  ![image-20220707173025499](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220707173025499.png)

- 내부에 존재하는 유사 데코레이터 패턴 로직

  ![image-20220707173430634](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220707173430634.png)

  

- **문제점 파악하기**

  - **전략객체들이 직접 다음타자 주입 + next필드 + 재귀호출부를 가져**  `코드중복`이 발생한다. **중복코드는 템플릿메소드형태의 `추상클래스`로 제거한다.**

    ![image-20220711104815044](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220711104815044.png)

    ![image-20220707174221254](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220707174221254.png)

  - **중복제거를 위해 올린 생성자 주입 ->  `부모 추상클래스`는 `좋은 상속조건에 의해 생성자 주입을 받지 않는다.`에 의해 `생성자 주입 대신 setter받기기능`을 사용하되, `return this를 이용한 [setter 체이닝 받기기능]`를 이용한다.**

    - **setter체이닝받기는 `받기기능의 주체 = 받고난 뒤 return되는 객체`가 동일해야한다.**

    ![image-20220707174311364](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220707174311364.png)
---
toc: true
layout: post
title: Object) TemplateMethod to Strategy2(Plan1)
description: 훅메서드 -> 전략메서드 -> context공유로 책임커진, 연결되는 합성객체

categories: [java, 우테코, oop, object, templatemethod, strategy, plan, context]
image: "images/posts/java.png"
---

## 템플렛 메소드 패턴 -> 전략패턴

### 요약

- 전략적용대상의 class가 추상클래스로 개별로직을 가지는 상태 

1. 전략적용대상 추상class를 일반클래스로 변경 및 훅메서드 정의부 삭제
2. 내수용 private훅메서드 호출부를, 주입된 전략객체.전략메서드()호출로 변경
3. 전략 인터페이스 만들면서, 전략객체 주입 (생성자 or 확장(좋은 상속 가능성)있으면 setter로 받기)
4. 전략메서드 올리기 
5. 훅메서드구현 개별자식들을 [구상내용+전략인페명]으로 변경하고, 전략인터페이스 구현
6. @Override 개별구현 proected훅메서드 ->  public전략메서드로 시그니쳐 변경
7. 부모의 템플릿메소드 공통로직 with 내부 context변수들 -> 전체를 전략메서드에 인자로 context위임
    - 내부context변수들(보라색)을 지역변수로 추출 이후 -> 제외하고 메서드 추출 -> 인라인으로 메서드인자로 context 넘겨주기
8. delegate로 추출한 전체로직을 전략메서드에 default메서드로 위임 
9. 위임된 default메서드 로직에서, default를 지우고 로직을 개별 전략객체들에게 구현->전달후, 기존 전략메서드(context인자X)는 삭제


### 좋은 상속(템플릿메소드패턴) -> 합성객체(전략패턴)

- [시작 커밋](https://github.com/is2js/object2/tree/252f7b91d886eb9ce04edc6f75e26eec3441339c/src/main/java/goodComposition)

- [참고 내 블로그 정책적용 글](https://blog.chojaeseong.com/java/%EC%9A%B0%ED%85%8C%EC%BD%94/oop/object/policy/side/hospital/2022/07/01/(side)%ED%8A%B9%EC%A0%95%EA%B0%9D%EC%B2%B4%EC%97%90-%EC%A0%95%EC%B1%85%EC%A0%81%EC%9A%A9.html#method2-2-%ED%85%9C%ED%94%8C%EB%A6%BFpolicy%EB%A5%BC-%EC%9D%BC%EB%B0%98class--%EC%A0%84%EB%9E%B5policy%EB%A1%9C-%EB%B3%80%ED%99%982%EA%B0%9C%EC%9D%98-%EC%A0%84%EB%9E%B5%EA%B0%9D%EC%B2%B4%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%BC%EB%B0%98policy)

`템플릿메서드`는 **기계적**으로 **전략패턴의 전략객체**로 만들 수 있으며, 그 방법이 `상속모델` -> **합성모델**로 바꾸는 것과 마찬가지다

- 판단은 **조합폭팔이 일어날 것 같을 때 기계적으로 바꾸면 된다.**
- 연습은 **상속이 보이면 조합으로 바꾸기(합성)**를 해보면 된다.



1. 템플릿추상클래스를 **일반클래스로 내리고, 훅메서드 정의부 삭제**하기

   - 훅메서드가 내려가지 않고, 전략객체로 정의된 내용이 외부에서 주입된다.

2. **훅메서드() 호출부를 전략객체.전략메서드()호출로 변경**하기

   - 추상클래스의 훅메서드 구상 자식클래스들을
     - 전략객체가 대신한다.

   ![f401af7f-90a8-430d-a4fd-25b646351870](https://raw.githubusercontent.com/is3js/screenshots/main/f401af7f-90a8-430d-a4fd-25b646351870.gif)

3. ~~전략객체를 생성자 주입하기~~ **전략객체를 생성자주입이 아닌 `void setter받기기능`으로 받기**

   - **템플릿 추상클래스는 `전략객체 주입 일반클래스`로 바뀌어도 `여전히 추상층으로서 확장(상속) 가능성이 있다면, 생성자 주입하면 안된다.`**

   ![47be155d-07a9-4f68-a8a0-87585cdfdbcf](https://raw.githubusercontent.com/is3js/screenshots/main/47be155d-07a9-4f68-a8a0-87585cdfdbcf.gif)

4. 전략메서드 만들어서 올리기

   ![60c8b15d-181a-4a9f-afc4-85cf52ee4799](https://raw.githubusercontent.com/is3js/screenshots/main/60c8b15d-181a-4a9f-afc4-85cf52ee4799.gif)

5. **훅메서드 구현체자식들을 전략객체**로 변경

   1. 클래스명을 `구상내용 + 전략인페명`으로 바꾸기
   2. **extedns `템플릿추상체` -> implements `전략인터페이스`** 로 바꾸기
   3. **protected**훅메서드들 **public**전략메서드명으로 바꾸기

   ![1b8b8eb1-f27e-4e89-a5a2-8c0df357b859](https://raw.githubusercontent.com/is3js/screenshots/main/1b8b8eb1-f27e-4e89-a5a2-8c0df357b859.gif)



6. **전략객체를 받기기능으로 받아, `상속(확장)가능성이 있다면 class에 final을 달지말고 3가지 원칙 지키는지 확인`하기**
   1. private변수
   2. final or protected abstract 메서드로 구성
   3. 생성자 주입 말고 받기기능으로 받을 것







### 최소 책임 전략 -> context를 공유하는 연결되는 합성객체

- **훅메서드의 최소 책임 합성 -> 연결되는 합성객체는 context를 공유받아 메서드단위(SRP)의 책임을 위임받을 수 있음.**

- 현재 전략객체는 `1개의 call`만 유틸메서드처럼 입력받아  값을 반환해주며 **실질적으로 연산 전체의 상태(누적변수result, 초기값 Money.Zero)인 context를 위임받진 못하고 있다.**

  ![image-20220706225207429](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220706225207429.png)

  - **하지만, 연결되는 합성객체가 되려면 `해당 메서드내 context를 공유받아서 처리`해줘야 `제대로 책임위임`**받는 것이다.
    - 훅메서드는 상속 특성상 최소의 구현만 맡고 있었고, 그것을 합성으로 변경하려다 보니, **합성객체는 context공유받아 관련 SRP를 전체로 책임질 수 있는데 못하고 있는 상태다.**



- 기존 코드(Plan - 전략객체 적용(주입) class)

  ![image-20220706232831912](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220706232831912.png)

1. 기존 전략객체의 작은 책임을 포함하여 **전략적용 메서드 전체를 전략메서드로 빼기 위해 `인자가 잡힌 내수용 메서드`로 추출한다.** 

   1. 현재 적용객체 **context의 시작 값들을 지역변수로  메서드 추출 위로 뺀다.**


      
      ![869188b5-22af-4abb-9992-7e4d13f6b54a](https://raw.githubusercontent.com/is3js/screenshots/main/869188b5-22af-4abb-9992-7e4d13f6b54a.gif)


      
      ![image-20220706232733176](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220706232733176.png)

   2. 전략 인터페이스로 move시키기 위해 **내수용으로 추출 후, 위에 빼놓은 지역변수를 인라인 으로 인자 자리에 넣어준다**

      - 미리 사용되는 변수들의 선언을 지역변수로 위에 빼놨기 때문에, **내수용메서드로 추출하여도 인자로 잡힌다.**

      ![cc3eafe1-b155-4363-ab77-f94237b0e4a6](https://raw.githubusercontent.com/is3js/screenshots/main/cc3eafe1-b155-4363-ab77-f94237b0e4a6.gif)

      ![image-20220706233118121](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220706233118121.png)



3. 인자잡힌 내수용 메서드를 **move를 통해 전략 인터페이스로 이동시킨다.**

   - 구현부가 존재하니, default 메서드로 이동된다.
   - **내부context로서 사용된 변수들이 인자가 이미 잡혀있으므로 잘못생긴`this 파라미터는 제거`한다**

   ![f5eb59ba-ba28-44b9-ba1c-db1e4e91a798](https://raw.githubusercontent.com/is3js/screenshots/main/f5eb59ba-ba28-44b9-ba1c-db1e4e91a798.gif)

   ![image-20220706233435516](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220706233435516.png)

   ![image-20220706233508424](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220706233508424.png)



4. **새로운 전략메서드에 기존 전략메서드가 내수용으로 사용되고 있다. -> `인라인 리팩토링으로 제거`될 예정이다**

   1. 새로운 전략메서드로서 default를 지우고, 구상체들이 구현하게 한다.
   2. default내용을 구현부를 복사해서 전략객체들이 구현부에 넣어준다.
   3. 기존 전략메서드는 inline refactoring을 이용해서 지운다.
   4. 기존 전략메서드를 인터페이스에서 지운다.
   5. 모든 전략객체에 구현부 복붙이 끝났으면, 전략메서드의 구현부도 삭제한다.

   ![050eb1da-1f0f-422c-b14e-809b2f3c33d0](https://raw.githubusercontent.com/is3js/screenshots/main/050eb1da-1f0f-422c-b14e-809b2f3c33d0.gif)
---
toc: true
layout: post
title: 제이) 중간피드백 Service와 Controller
description: 리뷰어 제이가 해준 MVC 피드백 강의

categories: [java, 피드백, mvc, dto, service, controller, 우테코]
image: "images/posts/java.png"
---
### Service

1. 일반 코드로 작성하는 `안티 패턴 싱글톤`이 아닌 **스프링에서는 `전용 싱글톤`으로 유지되어야한다.**
2. **상태(인스턴스 변수)를 가지면 안된다.**
    - 스프링의 service는 **유저별 객체생성하면 감당이 안되므로 `싱글톤`으로 관리 되는데, `수많은 유저가 -> 1개 싱글톤의 상태를 오염`시킬 것이 뻔하기 때문이다.**
    - **controller -> 재료들을 service 메소드 파라미터로 받아서 `지역 변수`로 처리되어야한다.**



3. 원래 service를 사용한다면?
    - Controller -> inputDto -> 도메인을 return하는 **toDomain** -> service는 **domain을 받는다. 상태로 유지는 X** -> 레포지토리
        - dto를 service에 넘기는 것을 지양해야한다 dto -> toDomain -> service
            - controller는 **클라이언트 레이어**쪽 <---> service는 **도메인이 포함된 레이어** 쪽
        - 레포지토리는 지엽적인 구상체들이 다 올 수 있는 Interface로 쌓여있다.
        - 도메인 생성에 필요한 데이터를 가져다 주는 것이 레포지토리 인터페이스
        - 도메인은 레포지토리에게 질의만(findByName, findById)



4. **input -> 도메인으로 받은 controller에서 `service.흐름로직( 도메인1, 도메인2)`로 service로 흐름을 가져가되, input/output 흐름이 잘 잡히도록 짜면 된다.** 



5. **`검증`**이 **controller**에 있어도 되긴 되지만, 최대한 **controller에서의 로직은 걷어내서 `Factory or 도메인 로직`에 넣어준다. `도메인을 풍부하게` 해주자** 



6. 서비스에서 전략객체 바로 생성사용 가능?
    - spring -> bean에서 언제든지 사용가능하게 관리해줄 것이므로 **주입없이 바로 사용해도 **



### Controller와 의존성

1. `의존성 개념`에 따르면 **중요하지 않은 = 잘 변하는= `view와 맞닿은 controller`가 `중요한 도메인을 생성->사용`해도 된다.**

    ```
    중요한 것 ---의존=생성/(인자로)사용/리턴 (X)---> 중요하지 않은 것(쉽게 변하는것) (X)
    - domain내부에서 dto를 return하는 toDto()  하면          안된다.
    - domain내부에서 view(String)과 관련된 메소드를 작성하면    안된다.
    
    중요하지 않은 것 ---의존=생성/(인자로)사용/리턴(O)---> 중요한 것
    - controller에서     도메인을    (인자로)생성후     사용해도 된다.
    - dto에서            도메인을    from인자로        사용해도 된다.
    - dto에서            도메인을    return하는 toDomain()해도 된다.
    
    ```

    



2. **controller**에서 **도메인을 생성/인자로사용**해도 되며 -> **service에는 도메인or값**으로 넘겨준다.





### Service to Controller

1. **Controller는 중요하지 않은 것**으로서 중요한 **도메인을 받아 써도 된다**.
    - **프로젝트가 커지면 Dto로 반환해준다.**
        - jpa를 쓰게 되면, 영속성 관리 때문에, 도메인을 view단으로 보내면 안된다.
    - **view에서는 도메인에 있는 정보를 짤라 일부만 써야하는데, 필드를 날릴 순 없으니 `민감/불필요한 필드가 짤려진 dto`로 보낸다.**
    - **`도메인의 정보가 다쓰인다면 도메인객체를 그대로 넘겨도 된다.`**
        - view에 `map`을 넘겨줘도 된다. 알아서 변환되서 내려간다.
        - `map`을 각 `key`필드 + `value`필드로 쪼갠 뒤 -> `dto클래스`를 만들어서 -> `객체dto`를 넘겨도 된다. 



### Method시 들어갈 때 필요한 것들 -> 뭐가 나오는지 명확하게



**나중에는 `returnType -> 메서드명 -> 파라미터`의 시그니쳐만 보고 메서드를 갖다쓰기 때문에, 잘 정의되어 있어야한다.**



- 안 좋은 예 -> 잘못 메서드 추출한 예

    ```java
    lottoService.calculateResult(inputView.getBonusNumber());
    ```

    - `보너스`를 넣으면 -> `당첨 결과`가 나온다? -> (X)
        - **결과는 `산 로또 + 당첨번호` + `보너스`가 들어가서 -> `당첨결과`가 나와야한다.**

    ```java
    final Money totalMoney = getMoney();
    final List<Lotto> issuedManualLotto = buyManualLotto(totalMoney);
    
    final Result result = issueLotto(totalMoney, issuedManualLotto);
    ```

    





### xxxxFactory

- **xxxxFactory 는 <생성과정이 복잡하고 중요해서 따로 분리해주는> 판매원이자 xxxxMachine**
    - Lotto를 용지로만 본다면, **복잡한 생성과정은 판매원/Machine인 Factory에게**

- 생성로직이 복잡하고 중요하기 때문에 클래스로 분리한 것

    - 간단하면 따로 Factory로 분리할 필요가 없다.

    ![image-20220303225421237](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220303225421237.png)


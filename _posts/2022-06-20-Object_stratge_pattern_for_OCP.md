---
toc: true
layout: post
title: Object)StrategyPattern for OCP(개발자의세계)
description: 추상체를 넘겨받았음에도 여러개의 if -> 추상체 정보 강화

categories: [java, 우테코, oop, object, 개발자의세계, strategy, OCP]
image: "images/posts/java.png"
---

#### N개의 instanceof는 N개의 if -> 1:N을 1:1로 만드는 전략패턴 준비와 적용 

- **전략패턴 준비**: if내에서 구상체들이 단독으로 하는 일을 찾아, 파라미터로 추출 후, 구상체내부로 옮겨주기
- **의존성 역전**: 
  - **`1:N`** : 일반class가 N개의 구상체를 알고 있는 관계
    - 추상체로 받지만, 1:N관계라서, N개를 모두 다 확인하고, 개별로직을 수행한다.
  - **`1:1`: 추상체로 받고, 추상체에 시켜서 내부적으로 N개의 개별구현 구상체가 작동 후  1알아서 결과물을 제공하는 `N관계가 1개의 전략(추상)메서드로 기능 제공`하게 된 상태**
    - 추상체를 의존하는 것은 그대로이나, N개 구상체를 다운캐스팅해서 직접 까보지 않아도 되어, 1:1의 관계가 됨. **이것도 의존성 역전**이라고 한다.
    - **Director가 직접 N개 다 까보던 것** -> **Paper(추상체)가 전략메서드로 일을 하고 상황에 맞는 결과물을 반환해주는 일을 건네받음.**
      - 역할의 역전



1. **문제상황**

   ![image-20220619233740650](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220619233740650.png)

   - `Director`는 `Paper`(추상체)에 대한 **instanceof가 2개**가 생겼다.

     - instanceof가 1개가 아니라면 제네릭으로 제거 불가다**. 의존성 역전이 필요한 N개의 if로 봐야한다**

   - 의존성 역전을 하려면, **`Paper(N, 추상체에).시킨다( Director(1) )`의 형태**가 되어야하는데, 

     - **추상층의 정보가 적어** 한번에 일을 못시켜 if로 구상층들을 확인해야하는 OCP위반(부모형이 자식형들 대체를 못함)이 생긴다. 
       - **구상층의 정보들을 모아, 추상층에 올리기 위해 `N개의if를 제거하는 전략패턴`을 적용해야한다**
         - 구상층의 정보가 없다면? 구상층의 은닉화/역할책임 분배가 잘못된 것으로  **`구상층을 확인하고 사용하는 class내에서` -> `구상층이 단독으로 사용되는 로직을 묶어 구상층 내로 가져와야` 한다**.

   - **추상체에 정보를 채울 `구상체들의 공통로직+개별로직`이 `구상체 내부에도 없는 상황`**

     - **각 if마다`Director내에서 확인된 구상체들이 단독으로 하고 있는 일들을 구상체 내부로 일단 가져와야`한다.**

       - **단독으로 하는 일은, 구상체로 가져와도 서로 다를 인자가 안생겨, 전략메서드가 될 수 있다.**

       - 확인해보면, Paper의 구상체들 Client, ServerClient들은 getter/setter밖에 없다. 특별한 공통로직이 안보이는 상황

         ![image-20220619234928202](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220619234928202.png)

         ![image-20220619234948841](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220619234948841.png)

         

       

     

   

2. **적용순서**

   1. **`if마다각각의 구상체`가 현재Class내에 있지만, `단독으로 하는 일들`**을 살펴보고 **`메서드 추출 with 구상체만 필요한 인자` 후 -> 구상체 내부 public (예비전략)메서드**로 옮긴다.

      - **구상체가 현재 속한 class(Director)정보 없이 단독으로 하는 일** -> Director안에서 안 펼쳐져있어도 된다. 은닉화해서 내부로 들고와야
      - 구상체**만**의 일을 찾아서 -> 구상체내부로 넣어줘야 -> 구상체들만의 공통로직을 추출할 수 있다.
        - **new 객체를 생성하는 일**: 섭외한다고 표현하고, 의존하는 것이므로 Director와 무관하게 **구상체가 단독으로 하는 일에 포함되는 일로서 다른 객체를 섭외(의존)**할 수 있다.
        - **객체없는 private메서드 추출된 메서드(deploy)** : 현class 정보를 사용해서 하는 일 -> **Director의 일**이다.
      - **공통로직을 추출하는게 아니라 구상형들의 단독일을 찾아 올려 `추상체.시킨다()`할 `통째로 1개의 메서드 추출` 목적이므로 템플릿메소드 패턴은 생각하지 않고, `인터페이스의 전략메서드 1개`로 만든다**
        - 전략메서드는 Director와 무관하게 인자 없이 정의되는 것이 좋으므로, 단독으로 하는일을 찾아서 -> 추출후 -> 추상체로 이동시킨다.
      - 단독으로 하는 일 정리
        1. 특정 Paper구상형를 아는 개발자(들)을 섭외
           - **2개의 지역변수가 추출되는 순간 -> 메서드 추출이 안된다. -> `객체없는 private메서드로 생성 후 개별 로직 -> 수동 이동 -> 구상체만 필요해야하므로 구상체만 Parameter로 추출 -> 구상체로 이동`**
           - 가능하면, **먼저 1개의 변수 체이닝을 하는 if를 찾아가서 메서드 추출**하고 **해당 구상체로 move한 뒤, 복잡한 경우(2개이상 지역변수, 결과물 생성)하는 부분은 수동으로 처리하자**
        2. Paper구상형에 개발자들을 set
        3. 개발자들이 Paper구상형을 받아 Program(들) 생성
           - **return 1개이상일 경우, new 형[]{ 1, 2}의 배열을 생성해서 반환한다**
        4. ~~Director가 Program(들)을 deploy~~

      - **추출할 메서드명은 모든 구상체가 같아야할텐데, getProgram/makeProgram은 개발자들의 메서드라 되었기 때문에, 1~3의 과정을 (Paper의) generateProgram으로 하자.**

        - **각 if마다 순차적으로 `구상체단독일 추출 -> 구상체로 이동`을 `반복`해서 Move시켜준다.**

        - Client

          ![39dcdb0b-1c72-4df1-a3b6-208c26ab1767](https://raw.githubusercontent.com/is3js/screenshots/main/39dcdb0b-1c72-4df1-a3b6-208c26ab1767.gif)

        - ServerClient : 지역변수 2개 생성 체이닝 -> **구상체만 사용하는 메소드의 자동추출이 안되므로 -> 전략메서드와 동일한이름으로 메서드 생성 -> 복붙 -> `단독사용된 구상체만 파라미터로 추출` -> `Move시 파라미터의 사용된 구상체로 이동`**한다.
          ![f0581c6f-c1ad-442f-b414-2588eb68ca15](https://raw.githubusercontent.com/is3js/screenshots/main/f0581c6f-c1ad-442f-b414-2588eb68ca15.gif)

          - **예비 전략메서드들의 `returnType을 1개이상 받을 수 있는 배열로 변환하여 통일`하도록 `구상체들을 Compare Files`을 이용한다.**

          ![3cba378f-6cc8-4cb9-9ad1-4f4e763de7e4](https://raw.githubusercontent.com/is3js/screenshots/main/3cba378f-6cc8-4cb9-9ad1-4f4e763de7e4.gif)

          

   2. **구상체 1개로 `input/output이 통일된 예비전략메서드`를 올린 뒤, 나머지 구상체들이 추상메서드를 구현하도록 수정한다**

      ![cb42ca01-9461-49ff-9ed0-79949ace9d58](https://raw.githubusercontent.com/is3js/screenshots/main/cb42ca01-9461-49ff-9ed0-79949ace9d58.gif)

   3. `Director내` if별로 존재하는 구상체들.예비전략메서드() -> **if를 제거후 추상체.전략메서드()**로 변환한다.

      ![ad98d75b-524a-44a3-b7f6-ee881840eea6](https://raw.githubusercontent.com/is3js/screenshots/main/ad98d75b-524a-44a3-b7f6-ee881840eea6.gif)

      

   



#### 전략패턴의 완성: 전략구상체들 역시 추상클래스화 -> 분신술이 전략메서드 실시간 개별구현

####  이론

- 훅메서드 / 전략메서드는 추상메서드로서, **if분기를 유발하던 로직**이다. if분기마다 **외부에서 구상class가 익명클래스로 class(java)파일 생성없이 생성될 수 있게 한다.**

  - **객체지향 설계를 제대로 했다면 -> if 유발 개별로직이 전략/훅메서드 -> 구상class -> abstract 화 -> 익명클래스에서 구상하여 Main까지 밀려야 성공한 것**

    - if instanceof에 분기 선택으로 사용되던 Client, ServerClient도  **`class`  ->  `abstract class`된다**. 
      - **`abstract`만 달아줌으로써, Client, ServerClient의 본래 특징을 유지하되 && `if를 유발하는 수행방법(내부다른정보들 입력) -> case에 따라서 외부에서 구현 && 객체 생성(형 생성)`해버리기 위해**
    - 그 전까지로만 밀렸다면,  어딘가에서 다운캐스팅 하고 있을 것이다

  - **Main까지 다 밀었으면 `DI`를 한다.** 

    - **Main의 코드들을 싹다 일일히 만드는게 아니라 DI를 통해 주입하는 것으로 끝내야**한다.
    - Main까지 다 밀어내야지만 DI가 정상작동한다.

  - `외부에서 익명클래스로 실시간 구현 && case에 맞는 구상형 생성`시 **개별구현되는 전략메서드/훅메서드**는 

    - 내부에서 분기문을 유발하던 곳에서  전략/훅메서드로 사용된다.

    ![image-20220620013639335](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620013639335.png)

- **템플릿메소드 패턴**의 Programmer의 구상층 FrontEnd와 BackEnd는 **`더이상 구상불가능한 하위도메인인 자신이 알아야하는 형이 많아져서`  `제네릭+추상클래스화`를 통해 분신술마다 `특정형알게+익클 분신술로 훅메서드 실시간 개별구현`**
  - case마다 훅메서드(들)의 개별구현을 외부에서 하도록
  - (더이상 추가 하위구상층 만들기 불가한 자신class로) case마다 특정형 선택할 수 있게
- **전략패턴**의 Paper의 구상층 Client, ServerClient는 **분기를 유발하던 개별구현로직 `전략메서드`를 `외부에서 필요에 따라 실시간 개별구현`하도록 분신술**
  - case마다 전략메서드의 개별구현을 외부에서 하도록
  - 하위 구상층의 추가 가능성??



#### 적용

- **기존 구상체를 추상화(중간 추상층화)**시키는 것은 먼저 작성한 [generic관련 글](https://blog.chojaeseong.com/java/%EC%9A%B0%ED%85%8C%EC%BD%94/oop/object/generic/instanceof/2022/06/19/Obejct_generic_for_remove_instanceof.html#%EA%B5%AC%EC%83%81%EC%B8%B5%EC%97%90-%EC%A0%9C%EB%84%A4%EB%A6%AD%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4%ED%99%94----%ED%95%84%EC%9A%94%ED%95%9C-%EC%97%AC%EB%9F%AC-%ED%8A%B9%EC%A0%95%ED%98%95%EC%9D%84-%EC%95%84%EB%8A%94-%EC%9E%90%EC%8B%A0%EC%9D%98-%EA%B0%9C%EB%B3%84%EA%B5%AC%ED%98%84%EB%B6%84%EC%8B%A0%EC%88%A0%EC%9D%B5%EB%AA%85%ED%81%B4%EB%9E%98%EC%8A%A4%EC%9D%84-%EB%A7%8C%EB%93%A4%EC%96%B4--1n%EA%B4%80%EA%B3%84%EC%9D%98-instanceof-%EC%A0%9C%EA%B1%B0)에서 참고하자.

1. 구상층을 **abstract class**화

   - 익명클래스(분신술)이 생성과 동시에 여러형을 선택해서 알게하려면 제네릭 추가(여기선 X)

   ![f17ef41d-62fd-4a4a-8cbd-5f6d23212e04](https://raw.githubusercontent.com/is3js/screenshots/main/f17ef41d-62fd-4a4a-8cbd-5f6d23212e04.gif)

2. **개별구현한 훅메서드(@Override+proected)/전략메서드(@Override+public)들을 새로운 구상층(익명클래스)**에게 자동 양보하기 위해 

   1. **`로직 복사후 보존`할 곳에 옮겨두기**

      - alt+F7로 객체로 생성되어 사용되던 곳에 붙여넣기

   2. **훅/전략메서드 `삭제(해야 익클에서 imple가능) 후`하여 `새로운 구상층(익클-분신술)이 실시간 훅/전략메서드 외부 구현`하는 곳에 `implements하여 로직 양도`**

      - Client

        ![0431e0db-f1ba-4ec3-9b49-8f5a87c7a521](https://raw.githubusercontent.com/is3js/screenshots/main/0431e0db-f1ba-4ec3-9b49-8f5a87c7a521.gif)

      - ServerClient

        ![eecc2212-32b9-48a2-9c7a-cf4583a88bab](https://raw.githubusercontent.com/is3js/screenshots/main/eecc2212-32b9-48a2-9c7a-cf4583a88bab.gif)

        

3. 현재`if분기를 유발하던 개별구현 훅메서드/전략메서드들`이 구상체 추상화를 통해 **Main에 익명클래스(구상체의 추상화된 classe들의 분신술) 실시간 구현** 되도록 **외부로 모두 밀렸다.**

   - 이는 나중에 DI를 통해 코드를 수정할 수 있게 된다.

   ![image-20220620155818699](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220620155818699.png)
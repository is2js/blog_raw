---
toc: true
layout: post
title: Object)하루 시간대별 계산(TimeOfDayCalc)을 prev데코객체로 처리(Plan7)
description: 구간처리에 사용되는 prev데코객체를 시간대별 계산에 적용하기

categories: [java, 우테코, oop, object, decorator, prev, section, timeofday ]
image: "images/posts/java.png"
---

### TimeOfDayCalc를 암묵적 index로 묶인 컬렉션필드 -> prev데코객체로 처리하도록 변경하기


- [시작커밋](https://github.com/is2js/object2/tree/b940b38244e68e03bf68f7b6899962b5e1544f9a/src/main/java/goodComposition)



#### 구간처리를 해줄 prev데코객체 생성 및 필드 1개로 소유하기

![image-20220719171653377](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719171653377.png)

![image-20220719171730734](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719171730734.png)



#### prev데코객체는 시작특이점객체로 필드초기화하니, 생성자는 정의해줄 필요 없이 체이닝setter로 받되, 매번 다음타자로 업데이트하고, 현재 rule을 prev필드에 저장하게 한다.

- prev객체를 필드로 소유하는 클래스는, 생성자에서 받지 않고, 이미 내부에 시작특이점 객체를 가지고 있어서, setter를 정의해준다.

- 테스트코드가 돌아가도록 기존필드를 받는 생성자를 둔 상태에서, **생성자는 추가 선언이 되므로, 빈 생성자를 하나 추가해준다.**

  ![image-20220719172253910](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719172253910.png)





- 테스트코드에서 (컬렉션은 아니지만 버금가는 연결 데코객체므로) **addRule( )을 사용 생성하되, `다음 타자를 외부에 생성후 넣는게 아니라 다음타자 생성에 필요한 prev제외 나머지 정보`** 를 주어서 **`prev소유  클래스 내부에서 필드로 가지고 있던 prev를 필드로 넣은 다음타자`생성한 뒤, `다음타자를 필드로 가지고 있도록 setter를 정의`한다. **

  - test코드에서 사용 생성하면된다.

    - Rule 생성에 필요한 정보를 **생성자 정의부 파라미터 복사해서 가져와 prev를 제외하고 넣어줘야한다.**

      ![image-20220719174124317](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719174124317.png)

      ![image-20220719174202753](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719174202753.png)

- 다음타자를, 60초를 단위시간으로 10원씩 부과하며, 다음구간은 **0시 출발이므로** **2시까지**로 한다.

  ![image-20220719174401859](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719174401859.png)





- 다음구간의 끝은, 무조건 직전구간보다 커야한다.

  ![image-20220719175105522](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719175105522.png)

  ![image-20220719175803103](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719175803103.png)

  ![image-20220719180017200](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719180017200.png)





#### 기존 전략메서드는 놔두고, public 기본메서드로 새로 작성후, 다음타자로 업데이트까지 필요한 틀을 잡는다.

![image-20220719182614143](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719182614143.png)



![image-20220719184610957](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719184610957.png)



#### prev데코객체의 개별처리 메서드는,  자신의to와 prev의 to로 인해  정해놓은개별구간만 처리하지만, 전체 처리할 구간을 duration으로 변형된 인자를 받는다. (Duration.ofSecconds()나 기존 start+end (localdatetime) -> between()활용)

![image-20220719185144906](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719185144906.png)

![image-20220719185130799](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719185130799.png)

![image-20220719184901537](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719184901537.png)



#### [추가] 처리해야할 구간이 처음부터 시작안할 수 있다. 그럴 경우, a-b 구간을 = 0-b구간 처리 - 0-a구간처리로 나눠서 처리해야한다. -> 물어보고 중간구간이면, 2번 계산후 차이를 구해야한다. (데코객체 자체가 0부터 시작하는 구간으로 인지하고 비교 등 연산하기 때문)

- 먼저 **처리해야할 구간을 가진 interval**한테, **0부터 시작하는 구간인지 vs 중간구간인지 물어본다**.
  ![image-20220719233235699](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719233235699.png)

- **중간 구간이라면, `0~큰구간` 과 `0~작은구간`을 가지고와서, `각각을 계산후 차이`를 내야한다.**

  ![image-20220719233319241](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719233319241.png)



#### prev데코객체들이 개별구간을 처리하기 전에 확인해야할 2가지

##### 01 내 자신이 시작특이점은 아닌지 확인한다.(prev필드 == null) -> 차후에는 외부 반복문 돌릴 때, 조건에 걸려서 없어진다. 처음 Rule객체 작성 및 테스트시에 넣는다.



##### 02 처리할 구간이 나보다는 작아도 되지만, 내 하한인 (prev의 to)보다는 커야 일을 한다. -> prev데코객체 생성시에는 내 처리구간의 끝(to)를 LocalTime으로 받더라도, 연산은 0(시)부터 시작하는 구간으로 변환해야한다.

![image-20220719215606852](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719215606852.png)

![image-20220719215644437](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719215644437.png)

##### 03 쪼개놓은 모든 구간은 반복문으로 돌아가므로, 외부에선 값을 누적하고 있다면, 내가 일을 하지 않을 땐 누적0의 값을 반환하게 한다(thr X)

![image-20220719215749765](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719215749765.png)





##### 04 내가 처리해야할 구간이라면, 내 상한 vs 처리구간 상한을 비교하여, 실제 처리할 구간의 upperbound를 찾는다. 하한은 이미 prev의 상한으로 정해져있다. 이 때, localtime으로 상한을 받았으니, 메서드를 이용해서 0부터 시작하는 duration으로 변경해준다.

![image-20220719234021769](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719234021769.png)

![image-20220719234058582](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719234058582.png)

##### 05 [추가]  localtime.of의 입력한계는 23, 59, 59인데, (1) 내 구간이 23, 59, 59를 담당하고 있고 (2) 처리해야할구간과 비교하여 정한 상한upperbound가 23, 59, 59까지 실제 처리해야한다면, Duration.ofSeconds(1)로 1초를 더해준다.

![image-20220719233809584](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719233809584.png)

![image-20220719233820454](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719233820454.png)





##### 06 실제상한 ~ prev상한으로 만든 하한의 구간 차를 duration으로 계산후, getSeconds()로 환산후 단위초당 요금을 부과한다.

![image-20220719235241782](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719235241782.png)





##### 07 백업한 전략메서드를 지우고, 구간처리로 바꾼 메서드로 대체한다.


---
toc: true
layout: post
title: Object)요일(묶음)별 계산(DayOfWeekCalc)을 Set<rule> rules로 구현(Plan8)
description: 구간처리에 사용되는 prev데코객체를 시간대별 계산에 적용하기

categories: [java, 우테코, oop, object, rule,  dayofweek ]
image: "images/posts/java.png"
---

## 연속된 구간이 아닌 범주별처리 or 범주묶음별 처리도 Rule객체 소유

### 요일별 요금 계산(DayOfWeekCal)

- call -> interval -> 특정요일 -> **요일별로 따로 계산**
- call -> interval -> 특정 시간대구간 -> **미리 시간대구간별로 계산법을 쪼개놓은 prev데코객체를 for문을 돌면서 -> 자기구간에 걸리면 처리하도록(연결되어있는 것이 마치 컬렉션)**처럼 
  - **`미리 요일별로 계산법을 쪼개놓은 class -> 객체들(rows)`을 만들어놓고, 반복문을 돌면서, `자기 요일이면 계산해서 반환`하도록 한다.**
  - **이 때, `단일 필드 1개 요일` 하나하나 쪼개놔도 되지만, `Set<요일> = `을 통해, `미리 쪼개놓기을 주중/주말`로 짤라놔도 된다.**





#### 이미 정해져있는 범주별로 계산해줄 Rule객체를 만들고, 계산클래스는 Rule컬렉션(Set) 1개만 필드로 보유한다. Rule들은 자신이 처리할 대상이 맞는지 확인해야한다.

- 이미 정해져있는 구간 / 범주별 처리는 **개별처리해줄 rule객체 컬렉션or prev데코객체**를 두고, **`반복문`을 통해 돌리고, 해당하는 처리들이, 자기몫만 계산하고 반환하게 한다. 이 때, `해당하지 않는 놈들`도 있으니 `ZERO반환`하면서 `누적`되는 계산법이 필수다**

  - 필드로 연결되는 데코객체라면 -> 단일 객체필드 1개만 소유한다.
  - 범주별 개별 처리하는 객체라면 -> **모든 범주별처리 객체를 Set필드에 모아서 이것을 소유한다.**
    - 동적으로 변하는 것이 아니기 때문에 **final필드 + 생성자 주입으로 Set필드를 초기화**한다.
  - **미리 모든 경우의수를 다 쪼개놨기 때문에, `반복문을 돌며, 각Rule자신은 자신의 처리대상이 맞는지 확인기능도 포함`되어야한다.**

  

1. Calc계산기와 이미 정해져있는 범주들을 처리해줄 모든 경우의수 rules 필드를 만든다.

   ![f6a2b26c-a8ff-44e2-a4d9-9ca85a35b6a8](https://raw.githubusercontent.com/is3js/screenshots/main/f6a2b26c-a8ff-44e2-a4d9-9ca85a35b6a8.gif)

   ![image-20220721012129583](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721012129583.png)



2. 기존 Calculator를 구현해서, 계산기능을 넣는다.

   ![image-20220721012305914](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721012305914.png)





#### Rule객체의 class는 자신의 처리구간(요일) + 처리시 필요정보(단위시간과, 단위시간당 부과할 요금)를 필드로 가진다


![798b43a1-2484-4adc-846f-d55b405fe2f5](https://raw.githubusercontent.com/is3js/screenshots/main/798b43a1-2484-4adc-846f-d55b405fe2f5.gif)

![image-20220721012714610](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721012714610.png)



#### Rule객체는 처리할 대상이 범주1개가 아니라 범주묶음이라면, Set컬렉션필드를 가지면 된다. 자신이 처리할 대상이 1개의 범주라서 contains로 확인하면 된다.

![aff90b0a-9b61-4fbb-92a9-8660bf0af7a5](https://raw.githubusercontent.com/is3js/screenshots/main/aff90b0a-9b61-4fbb-92a9-8660bf0af7a5.gif)

![image-20220721014926126](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721014926126.png)





#### Rule들을 소유한 Calc는 처리대상을 만들어주고, rule를 반복문 돌리되, 개별계산은 해당없을시 ZERO반환하여 바깥에서 누적계산이 되도록 한다.

![image-20220721020647515](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721020647515.png)

![image-20220721031303713](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721031303713.png)



#### 주의) 시간 구간처리시, 23, 59, 59의 가능성이 있다면, +1초 예외처리를 해줘야한다.

![image-20220721031239280](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721031239280.png)

#### rule 테스트

![image-20220721023122666](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721023122666.png)



#### calc 테스트

![image-20220721031324763](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220721031324763.png)
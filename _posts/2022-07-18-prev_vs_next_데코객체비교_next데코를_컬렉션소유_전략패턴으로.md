---
toc: true
layout: post
title: Object) prev vs next 비교 및 next를 컬렉션소유 전략패턴으로(Plan6)
description: next데코객체는 동적 추가처리가 안되어 컬렉션 add로 동적기능 추가

categories: [java, 우테코, oop, object, decorator, prev, next, section ]
image: "images/posts/java.png"
---

# prev데코객체 vs next데코객체 비교



## 공통점

### 같은형의 다음타자를 필드로 가지고 있어, 필요하다면 객체class내에서 꼬리재귀 호출(필드검사 터미네이팅 + 다음타자 꼬리재귀 호출) 가능하다.

- 다음 타자를 필드로 가지고 있어서 **데코객체 내부에서 꼬리재귀 호출로 다음 것으로 넘어가는 것이 가능**하다.

  - 구간처리 prev

    ![image-20220718235553256](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220718235553256.png)

  - 기능추가 next
    ![image-20220718235532171](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220718235532171.png)



- **하지만, 재귀호출은 stackoverflow를 발생시키므로, `데코객체 소유 클래스에서 반복문 + 다음타자로 업데이트`를 통해 실행한다.**



### 데코객체 소유 클래스는 1개의 필드만 가지고 있어도 연결된 모든 객체를 호출할 수 있다.

- 데코객체 특징 자체가 **다음타자를 필드로 소유하기 때문에 `연결된 여러객체를 알 필요가 없다`**
  - prev
    ![image-20220719000552063](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719000552063.png)

  - next

    ![image-20220719000524469](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719000524469.png)



## 차이점

### 

### 다음 타자를 업데이트 방식과 주체가 다르다.

- 데코 소유 클래스는 필드 1개로만 소유하고 있으며, **add(prev)나 void setter(next) 메서드**를 통해, 다음 타자를 붙여야하는데

#### prev

##### prev는 마지막부터 실행될 예정이며, 소유클래스에는 시작특이점객체를 공유한다. prev를 소유하는 데코객체 소유 클래스는 시작특이점을 알고 출발하며, 이미 존재하는 prev를 prev필드로 넣으면서 다음 타자가 생겨나므로, prev를 들고 있는 [데코객체 소유 클래스]가 내부에서 다음 타자를 생성 + prev객체필드를 다음타자로  업데이트시키며, 최종에는 마지막 타자만 알게되어, 뒤에서부터 시작점까지 돌아간다.

- 데코 소유 클래스는, prev데코객체의 **시작특이점 객체로 초기화된 prev데코객체를 가지고 태어난다.**

- 데코 소유 클래스는, **생성된 prev를 들고 있으므로, 자신의 내부에서 prev를 저장하는 다음타자를 생성하여, 알고있는 데코객체를 업데이트(재할당) 한다.**![image-20220719001041913](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719001041913.png)

  ![image-20220719001501252](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719001501252.png)

- **그로 인해, 맨 마지막 타자부터 -> 시작특이점까지 순환하게 된다.** 

  ![image-20220719001614978](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719001614978.png)



##### 요약: 다음타자 업데이트 주최는 데코객체 소유 클래스 + 애초에 시작특이점 객체를 prev로서 들고서 시작됨.

#### next

##### next는 순서대로 실행되기 위해, 외부에서 미리 다 연결된 상태로 시작데코객체만 공유하므로, 시작객체 생성시 모든 연결이 완료가 되어야한다. next라는 것은 아직 존재하지 않기 때문에, 생성자에서 받더라도, 실제 생성시 생성자 내부에서 next가 생성되어야하므로 생성자 체이닝으로 한번에 다 연결된 뒤, 시작객체가 반환되어야한다. next를 소유하는 데코객체 소유 클래스는 시작부터 끝(null)까지 아무것도 모른다. 또한, 넣어주는 시작next데코객체만 받아서, 처음부터 -> 마지막까지 순서대로 순환한다. [next데코객체 자신이]  생성자or setter로 다음 타자를 받아들이되, 시작객체가 반환되어야한다.

- next데코객체는 **순서대로 시작되기를 원해, 시작데코객체만 반환되어 공유되므로, 소유클래스가 아닌 `데코객체 자신`내부에서 생성자체이닝이나 `체이닝setter(next필드에 다음타자를 채우되, 반환은 나 자신 return this;`가 되어야한다**

  ![image-20220719003203194](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719003203194.png)

- return this의 **체이닝 받기는 setter를 호출한 나 자신이  반환되므로, 시작객체가 반환된다.**

  - 데코 소유 클래스에서 들어가는 것은 `시작데코객체인 BaseCalc`만 들어간다.

  ![image-20220719003331699](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719003331699.png)

- **체이닝으로 한번에 받지만, 아는 것은 시작데코객체 뿐 -> `마지막 연결된 놈을 모르며 접근이 불가능`하므로 prev때처럼의 동적기능추가가 안된다.**

  - **시작객체가 반환된 상태인데, 또 next를 추가할 순없다. 미리 한번에 추가됬어야 하며, null로 끝내놓은 상태일 것이다.**
  - **null까지 모든 게 결정되어있을 때만 next데코객체를 써야하나보다.**

- next데코객체 소유클래스는 아무것도 모르고, **이미 다 연결된 데코객체만 받는다. **

  - 이로 인해, **동적으로 추가가 안된다.**

  ![image-20220719001929191](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719001929191.png)

  ![image-20220719002419839](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719002419839.png)



##### 요약: 다음타자 업데이트는, 객체자신 내부에서, 시작부터 끝까지 다 받기 + 데코 소유 클래스는 시작next데코객체만 공유받음.



### 실행순서가 다르니 용도도 다르다?

#### prev

##### prev는 끝을 모르지만, 시작은 아는 구간처리를 했었는데, 각 데코객체들이 개별구간을 정해놓고, 그곳만 처리하면 되니, 마지막부터 실행되어도 상관없다(게다가 필드의 prev정보가 있어 마지막부터 개별구간 처리해도 문제없다)



##### 마지막이 정해지지 않는 & 순서대로 적용 안해도 되는 기능(계산 자체가 순서와 상관없이 특정금액 누적?) 추가 or 마지막이 정해지지 않는 구간을 미리 쪼개놓고 해당부분만 개별처리 하는 곳에 사용한다. -> 마지막부터 실행되므로 순서대로 누적적용은 못하고, 개별구간 처리만 하되 for문에서 누적시키는 것을 생각하자.





#### next

##### next는 처음부터 시작하니 [직전까지의 결과값을 메서드 인자로 들고다니며 누적된 계산]을할 때 좋을 것 같다.  -> for문에서는 누적하면 안된다.



##### 처음부터 순서대로 마지막까지 누적 적용하고 싶은 곳에 [직전결과값을 들고 있는 메소드]로 사용한다. 대신 for문에서는 누적안하고 업데이트만 하게 한다.



### 구상next데코객체 1개 소유 대신, 컬렉션Set필드에 전략객체 소유하기

- [시작커밋](https://github.com/is2js/object2/tree/78c5ceed27bd8a1f60b77bac8889a24f09512c32/src/main/java/practice_next_deco_for_adding_cumulative_func)

#### 순서대로 누적적용 연산을 반복문(do-while)으로 할거면, 동적 기능 추가 안되는 구상next데코객체 소유 대신, 동적 추가 가능한 Set컬렉션 필드 소유와 for문으로 해결

- 반복문내에서 `동적 기능 추가 안되는 next데코객체`를 부를 거면
  - **`동적 기능 추가가 가능한 컬렉션(Set)`으로 소유하고,`객체 소유 클래스에서 add로 동적 추가`가 가능하며, 순차적으로 누적적용 시킬 수 있다.**

##### 01 next데코객체의 추상체인 AbstCaclculator추상클래스를 전략 인터페이스로 변경하면서, next와 객체자신이 다음타자를 받는 체이닝setter 등 전략메서드만 남기고 다 삭제한다.

- 더이상 다음타자를 next데코 객체 자신(시작객체가 전를 체이닝으로)이 안받는다 ->  소유객체가 Set으로 받을 예정으로 순차적으로 동적기능 추가가 가능해진다.
- 각 데코객체들은 다양한 기능만 가지므로 전략객체로 변경한다.
- **[템플릿메소드패턴 <-> 전략패턴 전환 정리글 참고](https://blog.chojaeseong.com/java/%EC%9A%B0%ED%85%8C%EC%BD%94/oop/object/strategy/templatemethod/convert/2022/07/12/Object_strategy_templatemethod_convert_text.html)**

![19b67bb9-c192-43ae-83c2-c0d09eeb8c81](https://raw.githubusercontent.com/is3js/screenshots/main/19b67bb9-c192-43ae-83c2-c0d09eeb8c81.gif)

![image-20220719122746173](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719122746173.png)



##### 02 전략인터페이스명으로 변경하고, 훅메서드 구현자식들(next데코객체class들)은 전략인페를 구현하도록 수정한다.

![4cbf6ec2-1886-43fb-a54f-db81a948f50a](https://raw.githubusercontent.com/is3js/screenshots/main/4cbf6ec2-1886-43fb-a54f-db81a948f50a.gif)

![image-20220719123257723](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719123257723.png)

![image-20220719123306401](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719123306401.png)

![image-20220719123317919](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719123317919.png)



##### 03 전략주입 구상클래스(구, next데코 1개 소유 클래스)에서 필드를 Set컬렉션으로 변경후 -> 실행기 반복문을 do while + getNext() + null터미네이팅로직을 -> for문으로 바꾼다.

![15b61506-2af8-4777-b386-0af8bd9f52eb](https://raw.githubusercontent.com/is3js/screenshots/main/15b61506-2af8-4777-b386-0af8bd9f52eb.gif)

![image-20220719123806076](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719123806076.png)



##### 04 전략주입 구상클래스가, Set필드에 동적으로 다음타자를 추가 + 체이닝 할 수 있도록 `컬렉션필드에 체이닝setter`를 만든다.

- next데코객체의 체이닝setter는 필드가 1개 뿐이라 return this하는 순간, next의 추가는 어려워졌지만
  - **`컬렉션필드로 들고 있는 소유클래스`의 체이닝setter**로 **동적으로 객체(기능)을 추가**할 수 있다.

![302ed38f-a3f2-422c-87d6-c3de5db41a83](https://raw.githubusercontent.com/is3js/screenshots/main/302ed38f-a3f2-422c-87d6-c3de5db41a83.gif)

![image-20220719124310723](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719124310723.png)





##### 05 client에서는 전략주입 구상클래스의  필수로 1개를 넣어서 생성후 -> set필드에 체이닝해서 편하게 전략객체들을 동적으로 받는다.

![f41205e5-0185-4c71-98d8-16efc40629e8](https://raw.githubusercontent.com/is3js/screenshots/main/f41205e5-0185-4c71-98d8-16efc40629e8.gif)

![image-20220719124847905](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220719124847905.png)


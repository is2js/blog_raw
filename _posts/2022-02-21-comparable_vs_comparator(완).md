---
toc: true
layout: post
title: comparable vs comparator
description: java에서 final로 불변 값/변수를 사용

categories: [java, comparable, comparator, 우테코]
image: "images/posts/java.png"
---

- [참고 블로그1](https://creampuffy.tistory.com/141?category=986887)
- [참고 블로그2](https://gmlwjd9405.github.io/2018/09/06/java-comparable-and-comparator.html)

### 세팅

```java
public class Car {
    private final String name;
    private int position;

    public Car(final String name, final int position) {
        this.name = name;
        this.position = position;
    }

    public String getName() {
        return name;
    }

    public int getPosition() {
        return position;
    }
}
```





### 기준없이 정렬



#### 기준없이 문자열 리스트 정렬

- 문자열은 값이므로 알아서 **기준 없이 알아서 정렬된다.**

    ```java
    @DisplayName("정렬기준 없이 문자열 리스트 정렬")
    @Test
    void noStandard_stringList_sort() {
        final List<String> strings = Arrays.asList("acd", "abc", "dca");
    
        // Collections.sort() 와 .reverse()
        Collections.sort(strings);
        System.out.println(strings); // [abc, acd, dca]
    
    
        Collections.reverse(strings);
        System.out.println(strings); // [dca, acd, abc]
    }
    ```

- **String은 `이미 내부에서 Comparable을 구현 -> compareTo 오버라이딩 하여 정렬기준을 제시`하고 있기 때문**

    ```java
    public final class String
        implements java.io.Serializable, Comparable<String>, CharSequence {
        
        //...
        
            public int compareTo(String anotherString) {
            byte v1[] = value;
            byte v2[] = anotherString.value;
            if (coder() == anotherString.coder()) {
                return isLatin1() ? StringLatin1.compareTo(v1, v2)
                    : StringUTF16.compareTo(v1, v2);
            }
            return isLatin1() ? StringLatin1.compareToUTF16(v1, v2)
                : StringUTF16.compareToLatin1(v1, v2);
        }
    ```

    



#### 기준없이 객체 리스트 정렬

- 객체를 넣은 List는 아예 정렬 대상이 안되고 컴파일 에러가 난다.

- **반드시 먼저 정렬기준을 정해줘야한다.**

    ```java
    @DisplayName("정렬기준 없이 객체 리스트 정렬")
    @Test
    void noStandard_carList_sort() {
        final List<Car> cars = Arrays.asList(
            new Car("재성", 1),
            new Car("재경", 2),
            new Car("아라", 3)
        );
    
        //2.
        // no suitable method found for sort(java.util.List<domain.Car>)
        // method java.util.Collections.<T>sort(java.util.List<T>,java.util.Comparator<? super T>) is not applicable
        Collections.sort(cars);
        System.out.println(cars);
    
        Collections.reverse(cars);
        System.out.println(cars);
    }
    }
    ```

    

    





### 객체에 정렬 기준 주기

#### 01 sort()에 안넣어줘도 되는 수동 기준 Comparable

- 수동적인 내부 기준인 만큼, **까봐야알고, 수정할려면 다 도메인 내부를 다 바꿔야한다.**



1. 자기자신 Type의 Comparable을 구현한다.

    ```java
    public class Car implements Comparable<Car> {
    ```

2. **비교할 원시값Type에 맞게 `compareTo`를 재정의**한다.

    - int면 `Integer.compare( this.xxxx , other.xxxx )`
    - string이면, 내부 자기가 구현한 `this.compareTo(other)`를 사용

    ```java
    @Override
    public int compareTo(final Car otherCar) {
        return this.name.compareTo(otherCar.name);
    }
    ```

3. **`.sort()`만** 해서 오름/내림차순 확인해보기

    ```java
    @DisplayName("comparable -> name 기준으로 객체 리스트  정렬")
    @Test
    void noStandard_carList_sort() {
        final List<Car> cars = Arrays.asList(
            new Car("재성", 1),
            new Car("재경", 2),
            new Car("아라", 3)
        );
    
        //3.
        Collections.sort(cars);
        System.out.println(cars); // [아라 3, 재경 2, 재성 1]
    
        Collections.reverse(cars);
        System.out.println(cars); // [아라 3, 재경 2, 재성 1]
    }
    
    ```

    



##### compareTo

- [참고 블로그](https://mine-it-record.tistory.com/133)

1. 숫자형 비교

    - **기준 값과 비교대상이 동일한 값일 경우 0**

    - **기준 값이 비교대상 보다 작은 경우 -1**

    - **`기준 값이` 비교대상 보다 `큰 경우 1`**

         ```java
         Integer x = 3; 
         Integer y = 4; 
         Double z = 1.0; 
         
         System.out.println( x.compareTo(y) ); // -1 
         System.out.println( x.compareTo(3) ); // 0 
         System.out.println( x.compareTo(2) ); // 1 
         System.out.println( z.compareTo(2.7) ); // -1
         ```

2. 숫자형 중 Int의 비교

    - compareTo() 대신 Integer.compare(, ) 사용

        ```java
        int x = 4;  
        int y = 5;
        
        Integer.compare(x,y);
        ```

        

3. 문자열 -> String클래스가 이미 오버라이딩 해놓은 `compareTo()`이용

    - (첫글자 같으면서) 비교대상이 기준에 포함되면 -> 길이의 차이를 return한다.

    - (첫글자가 다르면서)  비교대상이 기준에 포함되면  -> 서로 다른 문자열이 시작되는 위치(첫위치)에서 아스키코드 값 차이

    - 포함이 안되는 문자열끼리 비교는 그냥 첫글자의 아스키코드 차이

        ```java
        String str = "abcd"; // 1) 비교대상에 문자열이 포함되어있을 경우 
        System.out.println( str.compareTo("abcd") ); // 0 (같은 경우는 숫자나 문자나 0을 리턴) 
        System.out.println( str.compareTo("ab") ); // 2 
        System.out.println( str.compareTo("a") ); // 3 
        System.out.println( str.compareTo("c") ); // -2 
        System.out.println( "".compareTo(str) ); // -4 // 2) 비교대상과 전혀 다른 문자열인 경우 
        System.out.println( str.compareTo("zefd") ); // -25 
        System.out.println( str.compareTo("zEFd") ); // -25 
        System.out.println( str.compareTo("ABCD") ); // 32
        ```

        



##### in stream: .집계(  ::compareTo) 기준으로 바로 사용 가능

- **`.집계(  기준이 되어줄 메서드 )`로 `집계기준`으로 바로 사용할 수 있다.**

```java
list.stream()
    .max(Car::compareTo)
    .collect(Collectors ... );
```







##### 단점: 정렬기준 변화시  모두 수정 + 까봐야 앎 + 네이밍

- **다른 개발자들이 `implements Comparable<Car>` 만 보고도**어떤 기준으로 정렬될지 모른다. -> **지식으로서 compareTo를 다 까봐야한다.**
- 그리고 이 **기준이 바뀌지 않을 거라 장담**할 수 없다. 구현부 compareTo를  하나하나 수정해줘야한다.
- **네이밍 수정을 못함. compareTo보다 더 직관적인 네이밍 필요**





#### 02 .sort( , ) 2번째 인자에 넣어주는 능동적 기준 Comparator

- `list.sort()`의 첫번째 인자 혹은 `Arrays.sort(, )`나 `Collections.sort(, )`의 **두번째 파라미터로 comparator를 받는 메소드가 오버로딩** 되어있다.
    - 따라서 수동적 기준인 natural ordering 으로 정렬하기 원할 경우, compareTo만 정의해서 기준으로 넣어주면 되고
    - **정렬 기준을 동적으로 할당**하고 싶을 경우, **`.sort( , )`1~2번째 파라미터로 Comparator를 정의해서 전달**하자.





##### comparator 직접 구현 for 네이밍

1. `new Comparator`롤 작성한 뒤-> `<Car>의 클래스`만 입력해주고  -> implements메소드 -> `compare`만 선택한다.

    ```java
    new Comparator<Car>()
    ```

2. **getter()도.. 들어가서.. 불러와서.. 비교시킨다. **

    - 문자열이면, compareTo()  String클래스꺼
    - 인트면, Integer.compare()

    ```java
    new Comparator<Car>() {
        @Override
        public int compare(final Car o1, final Car o2) {
            return o1.getName().compareTo(o2.getName());
        }
    };

3. 변수로 받아서 **정렬기준 객체의 `네이밍`을 완성**한다.

    ```java
    final Comparator<Car> carNameComparator = new Comparator<>() {
        @Override
        public int compare(final Car o1, final Car o2) {
            return o1.getName().compareTo(o2.getName());
        }
    };
    
    
    final Comparator<Car> carPositionComparator = new Comparator<>() {
        @Override
        public int compare(final Car o1, final Car o2) {
            return Integer.compare(o1.getPosition(), o2.getPosition());
        }
    };
    ```

    

4. **.sort( , )의 2번째 인자에 능동적으로 기준을 넣어준다.**

    ```java
    Collections.sort(cars, carNameComparator);
    System.out.println(cars); // [아라 3, 재경 2, 재성 1]
    
    Collections.sort(cars, carPositionComparator);
    System.out.println(cars); // [재성 1, 재경 2, 아라 3]
    ```

    





##### Comparator.api + getter()  for 가장편함.

- **따로 impl하거나 new 구현할 필요 없이, `sort()의 2번째 인자`에 바로 넣어준다.** 
- **Comparator.comparing() `.reversed()`를 붙혀 역순 지정가능**

```java
Collections.sort(cars, Comparator.comparing(Car::getName));
System.out.println(cars); // [아라 3, 재경 2, 재성 1]

Collections.sort(cars, Comparator.comparing(Car::getName)
                 .reversed());
System.out.println(cars); // [재성 1, 재경 2, 아라 3]

Collections.sort(cars, Comparator.comparing(Car::getPosition));
System.out.println(cars); // [재성 1, 재경 2, 아라 3]

Collections.sort(cars, Comparator.comparing(Car::getPosition)
                 .reversed());
System.out.println(cars); // [아라 3, 재경 2, 재성 1]
```





##### Comparator.api + getter()  in stream

```java
max ( Comparator.comparingInt ( 클래스::getter ))
```







### 정리

- Comparable 보다는 `Comparator`를 사용하여 **정렬 기준만큼은 `도메인에서 제외시켜 외부에 위치`**시켜 **변경에 유연하게 만든다.**
- 외부에서는 comparable -> compareTo -> .sort() **수동적 내부 기준으로 정렬**시 **`밖에서 보면 무엇을 기준으로 정렬되는지 모른다.`**

- **Comparator 는** 
    - **도메인 클래스에 변경을 가하지 않고도 변경에 유연해진다.**
    - **정렬 기준을 코드 자체로 나타내기 때문에 협업에도 더 좋은 것 같다.**


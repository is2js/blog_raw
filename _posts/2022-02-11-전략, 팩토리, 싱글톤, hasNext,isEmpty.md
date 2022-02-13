---
toc: true
layout: post
title: 전략,팩토리,싱글톤,hasNext,isEmpty
description: 일부 패턴들 + 자바상식 

categories: [java, 우테코]
image: "images/posts/wootech.png"
---



### 전략패턴 for 대체를 [테스트에서 상수]로

- 전략패턴은 전략을 **사용자가 선택해서 상황에 맞는** 행동을 하게 할때 주로 사용
    - **필요시 다른 환율적용**한 계산 반영
    - 해쉬값 생성시 알고리즘을 **다르게 적용**
    - **`다른 적용 전략으로서 [랜덤값 포함 조건문] -> [()- > true] 테스트에 적용`**
        - 여기서는 랜덤값을 -> 테스트용 상수로 적용하기 위해서, 랜덤값 포함 조건문에 전략패턴을 건다
    - [예시 코드 블로그(노션)](https://catsbi.oopy.io/344dbe7b-9774-48fc-9c95-b554e9c1c4bc)





1. **바뀔 수 있는 부분을 잡아서 `상태에 맞게 메소드로 추출`해놓는다.**

    - 해당메소드는 인터페이스 impl 구상클래스로 갈 것이다.

        ```java
        public void move() {
            if( Util.generateRandomNumber() >= 4 ){
                this.position++;
            }
        }
        ```

        ```java
        public void move() {
            if(isMovable()){
                this.position++;
            }
        }
        
        private boolean isMovable() {
            return Util.generateRandomNumber() >= 4;
        }
        ```

        

2. **`인터페이스 XXXXStrategy` + `이름만 가진 추상메서드`를 만든다.**

    - `랜덤한 조건문`이  -> `테스트용으로 대체`될 예정이라면 -> **`boolean등 반환형` +  `추출한 메서드를 추상메서드로 이름만` 정의한다**

    ```java
    public interface MoveConditionStrategy {
        boolean isMovable();
    }
    ```



3. **`특정Strategy`의 이름을 정해서 `impl구상클래스`를 `추출메소드를 복붙`해서 구현한다.**

    ```java
    public class RandomMoveConditionStrategy implements MoveConditionStrategy{
    
        @Override
        public boolean isMovable() {
            return false;
        }
    }
    ```

    ```java
    public class RandomMoveConditionStrategy implements MoveConditionStrategy{
    
        @Override
        public boolean isMovable() {
            return Util.generateRandomNumber() >= 4;
        }
    }
    ```

    

4. **`특정전략의 메소드`가 사용된 곳마다 ---> 다른전략을 허용하는 `추상화된 인터페이스 객체`를 `인자`로 받은 뒤 -> `추상체.추출메소드()`호출로 수정한다.**

    ```java
    public void move() {
        if(isMovable()){
            this.position++;
        }
    }
    ```

    ```java
    public void move(MoveConditionStrategy moveConditionStrategy) {
        if(moveConditionStrategy.isMovable()){
            this.position++;
        }
    }
    
    ```

    

5. 이제 에러난 부분을 타고가면서, **`내부 매개변수` 정의에 사용된 `추상 인자`를 호출하는   `외부 인자 자리`에 `new 특정전략()`를 넣워준다.**

    - **여기서는 `매 car`마다 랜덤 전략이 `각각 적용되어야하는 부분` ->  `특정전략을 인자로 넣어준다.`**

    ```java
    public void move() {
            this.cars.forEach(car -> car.move());
        }
    ```

    ```java
    public void move() {
            this.cars.forEach(car -> car.move(new RandomMoveConditionStrategy()));
        }
    ```

    

6. **테스트에서 다른전략으로 `조건부의 상수`인 람다함수로 `() -> true`를 대입하여 움직임을 확인한다.**

    - **`랜덤이 섞인 기능`은 테스트하기 힘들다** -> **`전략자리로 바꿔서 항상 같은 값인 상수`**를  들어가게해서 다른 기능을 테스트한다.

    ```java
    @Test
    void 자동차_움직임() {
    
        Car js = Car.of("js");
        // +1 움직임의 조건부를 <다른전략 사용가능한 전략패턴>으로 정의했으니 -> 특정전략을 넘겨 결정한다.
        RandomMoveConditionStrategy randomMoveConditionStrategy = new RandomMoveConditionStrategy();
        js.move(randomMoveConditionStrategy);
        js.move(randomMoveConditionStrategy);
        js.move(randomMoveConditionStrategy);
        js.printPosition();
    
        // 전략자리-랜덤조건부 ---대신---> [다른전략으로서 람다함수 상수로서 true]로 줘버릴 수 있다.
        // -> 테스트용으로 항상 움직임을 확인할 수 있다.
        Car jk = Car.of("jk");
        jk.move( () -> true);
        jk.move( () -> true);
        jk.move( () -> true);
        jk.printPosition();
    
    }

    js의 위치: 0
    jk의 위치: 3
    ```

    





### 정적 팩토리 메소드 네이밍 컨벤션

- **`from`: 매개변수를 하나** 받아서 해당 타입의 인스턴스를 반환하는 형변환 메소드
    ex) Date d = Date.from(instant);
- **`Of`: 여러 매개변수를 받아** 적합한 타입의 인스턴스를 반환하는 집계 메소드
    ex) Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
- `valueOf`: from과 of의 더 자세한 버전
    ex) BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);;
- `instance` 혹은 `getInstance`: 매게변수를 받는다면 매게변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.
    ex) StackWalker luke = StackWalker,getInstance(options);
- `create` 혹은 `newInstance`: instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.



- **참고: 생성자내부에서만 `this.필드 = `할당이 가능함. 팩토리 메서드는 `변환or검증`만**









### 싱글톤 패턴(관리자Machine, InputView스캐너)

```java
//1. class내부에 default생성자로 static finale 객체를 미리 생성해놓는다 -> for getInstance시 반환할 수 있게 미리 생성
private static final $CLASS_NAME$ $VALUE_NAME$ = new $CLASS_NAME$();

//2. 차후 입력시, 싱글톤이 받아서 [관리하는 데이터]를 쌓아둔다.
//private Cars cars;

//3. 싱글톤 private생성자는 미리 생성해야해서 매개변수 없이 default생성자로 만든다.
private $CLASS_NAME$() {
}

//4. 관리자로서 [관리 데이터]를 매 구동시 초기화 할 수 있게 한다.
public void init() {
	// this.cars = new ArrayList<>();
}

//5. 싱글톤은 언제든지 부르면 나갈 수 있게  public static getInstance()메소드르 ㄹ정의한다.
public static $CLASS_NAME$ getInstance() {
    return $VALUE_NAME$;
}
```



```java
//1. class 내부에 default? 생성자로 미리 static final 객체 생성
private static final Scanner scanner = new Scanner(System.in);

//2. 싱글톤scanner(getInstance) + .nextLine() ---> getInput()으로 싱글톤 + 메서드까지 사용
public static String getInput() {
    return scanner.nextLine();
}
```





### 값객체.hasNext()패턴

1.  `if 남은수 0보다 큰지 물어본다`
    - 0보다 크면 `1빠진 값객체`로 재할당받고
    - true를 return해서 `바깥 반복문이 계속 돌아가게` 한다.
2. 남은수가 0보다 같거나 작으면
    - false를 return해서 바깥 `반복문에 진입을 막는다`.

```java
public boolean hasNextGame() {
    if (this.gameCount.moreThanZero()){
        this.gameCount = this.gameCount.next();
        return true;
    }
    return false;
}


//...

public boolean hasNextGame() {
    return count > 0;
}

public Count next() {
    return new Count(this.count - 1);
}
```







### isEmpty  < isBlank

- isEmpy: `""`의 빈값
- isBlank: `""`를 포함하여 `" "`까지 다 True되는 넓은 범위


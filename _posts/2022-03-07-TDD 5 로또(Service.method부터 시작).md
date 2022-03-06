---
toc: true
layout: post
title: TDD 5 로또(Method부터 시작)
description: Service.method부터 시작하여 파라미터+응답값(enum) 포장

categories: [java, enum, 우테코, TDD, 강의]
image: "images/posts/wootech.png"
---


## 메서드의 파라미터(값->일급) 및 응답값(제한된 종류의 분기 응답) 포장시 TDD 



### 메서드의 파라미터(값->일급) 및 응답값 포장시 TDD [메서드 내부 포장후 ->새 기준메서드 호출] 및 [Client 배려 by 메서드 파라미터 포장 완성된 후 client일급생성 내부(List.of)부터 가변인자 생성자 추가]

#### 01 메변1) 검증 등 필요에 의한 값 포장시, [메서드복붙]후 [파라미터 수정] -> 에러해결 -> client 인자 변경

##### `기존 테스트들이 인자를 안바꿔도 유지`되도록 `전체 복사후 인자만 바꾼 새 기준 메서드`를 만들고 `에러 해결`후 `포장 객체로 메서드 호출 변경`



1. **기존 테스트는 유지한 체** 값List를 받다가

    ```java
    public static int start(final List<Integer> userLotto,
                            final List<Integer> winningLotto,
                            final int bonusNumber) {
    ```

2. **새 테스트에서 검증 등** 필요에 의해 **값을 1개 포장**

    ```java
    final int rank = LottoService.start(
            List.of(1, 2, 3, 4, 5, new LottoNumber(100))
    ```

3. 포장 Class, ClassTest생성

    ```java
    public class LottoNumber {
    
        private final int value;
    
        public LottoNumber(final int value) {
            this.value = value;
        }
    }
    ```

    ```java
    class LottoNumberTest {
    
    }
    ```

4. 객체비교를 위해, equals,hC 오버라이딩

5. **메서드 복붙 -> 파라미터 정의** 변경

    ```java
    public static int start(final List<LottoNumber> userLotto,
                            final List<LottoNumber> winningLotto,
                            final LottoNumber bonusNumber) {
    ```

6. **검증 요구 새 테스트에 한해, 복붙한 새 메서드 인자를 포장**시키도도록 노가다

    ```java
    @Test
    void over() {
        assertThatThrownBy(() -> LottoService.start(
            List.of(new LottoNumber(1), new LottoNumber(2), new LottoNumber(3),
                    new LottoNumber(4), new LottoNumber(5), new LottoNumber(100)),
            List.of(new LottoNumber(1), new LottoNumber(2), new LottoNumber(3),
                    new LottoNumber(4), new LottoNumber(5), new LottoNumber(7)),
            new LottoNumber(6)
        )).isInstanceOf(IllegalArgumentException.class);
    }
    ```

    

7. **검증, 필요에 의한 포장 테스트 -> thr 나야 통과하는 테스트**
    - 포장 생성자에 검증을 안 넣어준 상태므로 테스트를 통과못한다.
    - 원래 포장 객체 생성자 1개로만 테스트를 해야하는데, **여기서는 메서드 전체 호출로 퉁친다.**



#### 02 도메인 속 검증 등 포장에 의해 thr나는 메서드() 테스트 작성

1. 생성자 쪽에서 validate해준다.

    ```java
    public LottoNumber(final int value) {
        validate(value);
        this.value = value;
    }
    
    private void validate(final int value) {
        if (value < 1 || value > 45) {
            throw new IllegalArgumentException("로또 번호의 범위가 아닙니다.");
        }
    }
    ```

    

2. 테스트해서 thr나게 된다.

    ```java
    @Test
    void over() {
        assertThatThrownBy(() -> LottoService.start(
            List.of(new LottoNumber(1), new LottoNumber(2), new LottoNumber(3),
                    new LottoNumber(4), new LottoNumber(5), new LottoNumber(100)),
            List.of(new LottoNumber(1), new LottoNumber(2), new LottoNumber(3),
                    new LottoNumber(4), new LottoNumber(5), new LottoNumber(7)),
            new LottoNumber(6)
        )).isInstanceOf(IllegalArgumentException.class);
    }
    ```

    

#### 03 (필요에 의해 포장이 발생된) 메서드 테스트를 넘어서서 도메인 전용Test에서 경계값들 테스트

```java
class LottoNumberTest {

    @ParameterizedTest
    @ValueSource(ints = {0, 46, -1, 100})
    void range(final int value) {
        assertThatThrownBy(() -> new LottoNumber(value))
            .isInstanceOf(IllegalArgumentException.class)
            .hasMessageContaining("범위");

    }
}
```



#### 04 메변2) [`기존 rawInput메서드`]는 [파라미터만 유지]한체 내부에서 포장 -> 새 기준 메서드 호출 하는 `[포장후 호출메서드]로 변경`



1. rawInput을 받는 곳의 **파라미터명에 `raw`를 달아 -> `원래 네이밍은 포장후 변수`로 받아줄 준비를 한다**

    ```java
    public static int match(final List<Integer> rawUserLotto,
                            final List<Integer> rawWinningLotto,
                            final int rawBonusNumber) {
    ```



2. 내용물에 대해선 **포장된 것으로 처리되는 메서드가 남아있으니, `rawInput은 받아서 stream().map(포장생성자).collect()의 List<포장>`만해서 그쪽으로 넘겨주기만 한다.**

    ```java
    public static int match(final List<Integer> rawUserLotto,
                            final List<Integer> rawWinningLotto,
                            final int rawBonusNumber) {
    
        final List<LottoNumber> userLotto = rawUserLotto.stream()
            .map(LottoNumber::new)
            .collect(Collectors.toList());
    
        final List<LottoNumber> winningLotto = rawWinningLotto.stream()
            .map(LottoNumber::new)
            .collect(Collectors.toList());
    
        return match(userLotto, winningLotto, new LottoNumber(rawBonusNumber));
    }
    ```





##### 요약: 값을 단일객체 포장한다면, 메서드복붙후 포장인자 -> 에러처리 -> rawInput메서드는 내부포장용으로 변경



#### 05 메변3) 객체간 검증(갯수, 중복) 등 필요에 의해 List.of앞에 [new 일급()]으로 포장



##### [메서드 수정]은 포장후 기준호출해주는 메서드가 아니라 `포장인자를 받는 기준 메서드`로





##### 기준메서드에 List.of( 단일, 객체, 들) ->  갯수 초과 유발(필요에 의한 검증)

1. **rawInput을 받아 포장후 기준메서드 호출해주기도 하지만, `제깍적인 수정`을 위해서 `포장된 인자를 직접 받아서 내부에서 바로 처리`하는 메서드를 수정해줘야한다.**

    - 하나하나 다 쏴줘야, 메서드는 그렇게 오는지 알 수 있다(노가다)

    ```java
    @Test
    void lotto7() {
        assertThatThrownBy(() -> LottoService.start(
            List.of(new LottoNumber(1), new LottoNumber(2), new LottoNumber(3),
                    new LottoNumber(4), new LottoNumber(5), new LottoNumber(6), new LottoNumber(7)), // 갯수 초과
    ```

    - 갯수를 초과해서 넣었다. **에러가 나야 통과인데, 현재는 에러가 안날 것이다. 포장해야한다**.





##### 파라미터의 갯수 검증은, 받는 메서드 내부에서도 해줘도 될 것 같지만.. [검증은 다 쏴서 포장한 도메인 생성자에서]

- 관련 로직도 아닌데, if가 계속 쌓이게 될 것이다.
    - **메서드 내 파라미터를 받아 검증하는 것을 지양하자.**
    - **검증이 필요하면, 포장 -> 도메인 분리 -> 도메인 생성자에서**





##### 복수개 검증을 위해 `List.of( ) 앞`에 `new 일급() 생성자`를 씌워서 포장 시작

1. `객체List`인  List.of()를 new일급() 생성자를 씌워 일급객체를 만든다.

    ```java
    @Test
    void lotto7() {
        assertThatThrownBy(() -> LottoService.start(
            new Lotto(List.of(new LottoNumber(1), new LottoNumber(2), 
    ```

    

2. 일급객체도 상태가 1개 라면, `value`로 네이밍 -> 나중에 `.getValue()`로 getter를 쓸 생각

    ```java
    public class Lotto {
        private final List<LottoNumber> value;
    
        public Lotto(final List<LottoNumber> value) {
            this.value = value;
        }
    }
    ```



3. 검증의 순서는 중복검사보다는 갯수가 있다면 갯수가 먼저다

    ```java
        public Lotto(final List<LottoNumber> value) {
            // 중복검사(distinct.count != )보다 갯수검사(size != )가 선행되어야 한다.
            // (1) 일단 6개가 들어오고 -> 거기서 중복검사 해야한다.
            // (2) 7개가 들어왔는데 -> 중복검사 해봤자 의미 없다.
            if (value.size() != 6) {
                throw new IllegalArgumentException("로또 번호는 6개여야 합니다.");
            }
            if (value.stream().distinct().count() != 6) {
                throw new IllegalArgumentException("로또 번호는 중복해서 입력할 수 없습니다.");
            }
            this.value = value;
        }
    ```

    







##### 또다시 기존 메서드 유지되도록 `전체복사후 인자만 바꾼(list->포장) 새 기준메서드`만들고 -> `에러해결(.getValue())`  -> `client 호출 변경`

1. 복붙후 파라미터 변경

    ```java
    public static int start(final Lotto userLotto,
                            final Lotto winningLotto,
                            final LottoNumber bonusNumber) {
    ```

2. **내용물들은 일단 `list에 난 빨간줄`을 `.getValue()`를 붙여서 해결한다. **

    - 나중에 도메인 로직으로 들어갈 후보들이다. 

        - 도메인에 넣으면 getter없이 바로 value를 쓰게 된다.

        ![image-20220305003505785](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305003505785.png)
        ![image-20220305003513722](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305003513722.png)
        ![image-20220305003902488](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305003902488.png)
        ![image-20220305003913582](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305003913582.png)
        ![image-20220305003918375](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305003918375.png)

    - 앞에 getter를 붙이면 빨간줄이 사라진다.

        ![image-20220305004044030](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305004044030.png)
        ![image-20220305004054296](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305004054296.png)

    

    

    

    `List.of(단일, 객체, 들)`을 모두 `new일급()` 다싼 뒤 -> **`List<단일>`메서드의 파라미터**



#### 06 새 기준(일급) 메서드 에러 제거(getValue()) 후, 도메인(일급)에 [필요에 의한 검증(갯수)]시 에러나도록 도메인 검증 작성 -> 메서드 테스트로 퉁치기(일급자체 테스트는 client배려로 test도 배려되면)

```java
public class Lotto {
    private final List<LottoNumber> value;

    public Lotto(final List<LottoNumber> value) {
        if (value.stream().distinct().count() != 6) {
            throw new IllegalArgumentException("로또 번호는 6개입니다.");
        }
        this.value = value;
    }
```



```java
@Test
void duplicate() {
    assertThatThrownBy(() -> LottoService.start(
        new Lotto(List.of(new LottoNumber(1), new LottoNumber(1), new LottoNumber(1),
                          new LottoNumber(1), new LottoNumber(1), new LottoNumber(1))),
        new Lotto(List.of(new LottoNumber(1), new LottoNumber(2), new LottoNumber(3),
                          new LottoNumber(4), new LottoNumber(5), new LottoNumber(6))),
        new LottoNumber(7)
    )).isInstanceOf(IllegalArgumentException.class)
}
```





##### 아직!) 일급자체Test `List.of() 제거된 가변인자` 등의 client 배려(내려가며 생성자 추가) 끝나고 일급 도메인 테스트 하자!





#### 07 새 기준 메서드(일급이 파라미터, 수정 등 적용 대상) 완성 -> 기존 메서드의 내부에서 포장해서 새기준을 호출하도록 변경

- 변경 전: **파라미터는 포장** 전인데, 모든 로직이 다 있는 상태

- 변경 후: 일급을 파라미터로 받는 **이후 수정 등이 적용될`new 기준 메서드`**을 내부에서 호출하도록, `파라미터만 유지해서 기존 테스트코드들 유지`

    ```java
    public static Rank match(final List<LottoNumber> userLotto,
                             final List<LottoNumber> winningLotto,
                             final LottoNumber bonusNumber) {
    	// 이제 로직은 새 기준메서드에 양보됨.
        // 새 기준 메서드(포장객체)를 내부에서 호출만 해줌.
        return match(new Lotto(userLotto),
                     new Lotto(winningLotto),
                     bonusNumber);
    }
    ```

    





#### 08 client배려1) 메서드 이전 [`단일... 가변인자 일급 생성자를 추가`]하는데, client List.of ()입력 제거 -> `List<단일> to 단일...` 파라미터 -> 내부에서 list포장후 기본생성자 호출

- 현재 client 호출

    ```java
    LottoService.start(
        new Lotto(List.of(new LottoNumber(1), new LottoNumber(1), new LottoNumber(1),
                          new LottoNumber(1), new LottoNumber(1), new LottoNumber(1))),
    ```





##### 일급의 `기본 생성자`는 그대로 `단일List` + `검증`로직을 보유하고 있는다.

- 기존 **일급 `기본 생성자`**:  단일List + 검증 로직이 모여있다. 계속 유지되어서 호출되어야한다.

    ```java
    public Lotto(final List<LottoNumber> value) {
        if (value.stream().distinct().count() != 6) {
            throw new IllegalArgumentException("로또 번호는 6개입니다.");
        }
        this.value = value;
    }
    ```

    

##### client배려 1번은 `호출시 clinet가`List을 제거하고 -> `파라미터에` 가변인자를 받도록 한 뒤 -> `내부에서`는 가변인자(배열)을 List로 포장해서 this()로 호출하도록 `일급 생성자 추가`





![image-20220305083654611](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305083654611.png)
![image-20220305083703228](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305083703228.png)
![image-20220305083816209](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305083816209.png)
![image-20220305083956293](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305083956293.png)
![image-20220305084022308](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305084022308.png)



- 추가된 **일급의 `가변인자 생성자`**

    ```java
    public class Lotto {
        private final List<LottoNumber> value;
    
        //단일객체를 가변인자로 받도록 생성자 추가
        public Lotto(final LottoNumber... value) {
            this(Arrays.stream(value).collect(Collectors.toList()));
        }
    
        public Lotto(final List<LottoNumber> value) {
            if (value.stream().distinct().count() != 6) {
                throw new IllegalArgumentException("로또 번호는 6개입니다.");
            }
            this.value = value;
        }
    ```

    

##### 요약: 기본생성자(단일객체List + 검증) 외에 `가변인자(단일객체...) 생성자`를 만들어 `client의 List.of()호출부터 제거`하자.

- 가변인자로 내부에서 List화 -> client List.of()가 제거된 모습

    ```java
    LottoService.start(
        new Lotto(new LottoNumber(1), new LottoNumber(1), new LottoNumber(1),
                  new LottoNumber(1), new LottoNumber(1), new LottoNumber(1))),
    ```

    





#### 09 client배려 2)  `단일... 가변인자 일급생성자` 직후 바로 `값... 가변인자 일급생성자`를 `추가`한다.

- new 일급( `List.of( 단일, 객체)` )
    - new 일급( `단일, 객체` ) :  by `단일... 가변인자`  +  stream List포장 후 this기본생성자 호출
        - new 일급( `값1, 값2` ) by `값.. 가변인자`(int라면 Integer...)  + stream **map단일객체생성 -**> List포장후 this기본생성자 호출

- **단일... 가변인자 -> `단일객체stream` -> 단일객체List**가운데 **`단일객체stream`을 `값... 가변-> 값 stream + map`으로 만들수 있으니 값... 가변 생성자도 추가**

    ```java
    public class Lotto {
        private final List<LottoNumber> value;
    
        //2. new Lotto( 원시값, 가변으로) -> List.Of() + new단일()생성자 제거
        public Lotto(final Integer... value) {
            this(Arrays.stream(value).map(LottoNumber::new).collect(Collectors.toList()));
        }
    
        //1. new Lotto( 단일객체, 가변으로) -> List.Of제거
        public Lotto(final LottoNumber... value) {
            this(Arrays.stream(value).collect(Collectors.toList()));
        }
    
        public Lotto(final List<LottoNumber> value) {
            if (value.stream().distinct().count() != 6) {
                throw new IllegalArgumentException("로또 번호는 6개입니다.");
            }
            this.value = value;
        }
    ```

    



![image-20220305084817763](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305084817763.png)
![image-20220305085319446](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305085319446.png)
![image-20220305085359886](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305085359886.png)
![image-20220305085457733](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305085457733.png)
![image-20220305085526086](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305085526086.png)
![image-20220305085606702](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305085606702.png)

stream에서 `mapToObj`가 뜨는 이유는 integer가 아닌 `int -> 객체`로 가기 때문 -> **애초에 파라미터로 들어오는 원시값들을 int... 대신 `Integer...`로 box된 타입으로 받을 수 있다.**
![image-20220305085731026](C:\Users\cho_desktop\AppData\Roaming\Typora\typora-user-images\image-20220305085731026.png)





![image-20220305085911955](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305085911955.png)

**이제 `원시값으로 일급`을 만들 수 있게 된다.**

![image-20220305090007614](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220305090007614.png)





#####  요약: `값... 가변-> 값 stream + map`으로 `단일... -> 단일객체stream`을 만들수 있으니 `값.. 가변인자 생성자 추가`해서 `clinet의 단일...가변입력도 값... 입력으로 대체하자`





- 메서드 테스트로 퉁

```java
@Test
void duplicate() {
    assertThatThrownBy(() -> LottoService.start(
        new Lotto(1, 2, 3, 4, 5, 6),
        new Lotto(1,2,3,4,5,6),
        new LottoNumber(7)
    )).isInstanceOf(IllegalArgumentException.class);
}
```







##### 요약2: `메서드 파라미터 포장`이 끝난 직후부터,  `client배려1 List.of 제거 by 단일...` -> `client 배려2 new단일() 제거 by 값...`







#### 10 일급자체(경계값)Test는 client배려1,2가 끝난 뒤 -> new 일급 ( 값 가변인자) 로 하면 편하다

##### 개수(count) 검사 ->  중복(duplicate) 검사 순으로

```java
@Test
void count_valid() {
    assertDoesNotThrow(() -> new Lotto(1, 2, 3, 4, 5, 6));
}

@Test
void count_under() {
    assertThatThrownBy(() -> new Lotto(1, 2, 3, 4, 5))
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessageContaining("6개");
}

@Test
void count_over() {
    assertThatThrownBy(() -> new Lotto(1, 2, 3, 4, 5, 6, 7))
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessageContaining("6개");
}

@Test
void duplicate() {
    assertThatThrownBy(() -> new Lotto(1, 1, 3, 4, 5, 6))
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessageContaining("6개");
}
```







#### 11 메서드 내부 `일급.getValue()`는 `메서드화`하여 `도메인`내부 로직으로 옮겨갈 후보들



##### 일급.메서드( 객체 )로 메세지를 던질 때, `일급`을 좌항(메서드 호출하는 놈) `우항`에는 `내부 찢어질 일급 or 단일`(파라미터)을 배치하여 `value.list메서드()`를 사용하며, `재활용 가능성을 생각`하자.

- `일급.getter vs 일급` or `일급.getter vs 단일` 로직이면 `메서드화`해서 도메인 로직으로 옮겨가는데, **일급vs일급부터 처리**한다.

    - **`일급`.메서드( 찢일 or 단일) -> `value(일급내부 list).`입장에서 `넘어온or넘어와돌고있는 단일`을 List(value)의 메서드로 컨트롤할 수 있다.**

    ```java
    public int match(final Lotto other) {
        int matchCount = 0;
        for (LottoNumber lottoNumber : other.value) {
            //일급내부list.  list메서드 (  단일   ) 
            if (value.contains(lottoNumber)) {
                matchCount += 1;
            }
        }
    
        return matchCount;
    }
    ```

    

- **`일급.getter()` vs`단일`이 외부에 존재했다면** 메서드화 하면

    - **`value. list메서드( 단일 )`로 내부정의** 되므로 **`일급vs일급` 중 `value.list메서드( 돌고있는 단일)`로  메서드가 재활용 될 가능성이 있으니 잘 확인해서 대체해주면 된다.**

    ```java
    public boolean contains(final LottoNumber number) {
        return value.contains(number);
    }
    ```



##### 일급.list메서드( 단일 )의 메서드가 추가된다면, -> 내부 상태(value)를 사용하는  내부 정의 메서드인 `객체없이 호출되는 메서드`가  value.list메서드( 단일 )을 대체한다.

- `value.contains( lottoNumber )`  -> **`contains(lottoNumber)`**

    - **`객체없이 호출되는 메서드`가 있다면 `내부에서 정의된, 내부 상태(value)를 이용하는 메서드`로 생각하자. `(this.)value가 쓰이는 메서드구나`**

    ```java
    public int match(final Lotto other) {
        int matchCount = 0;
        for (LottoNumber lottoNumber : other.value) {
            //if (value.contains(lottoNumber)) {
            // 객체없이 호출된다? 내부상태가 쓰이는 내부 정의 메서드구나~!
            if (contains(lottoNumber)) {
                matchCount += 1;
            }
        }
    
        return matchCount;
    }
    ```

    





### 응답값 포장



#### 12 `응답값도 포장`할 수 있다. 만약, `제한된 종류 + if분기마다 달라지는 응답값`라면 `ENUM응답 포장`후  응답에 필요한 input들로 `정펙매` 를 호출한 뒤 내부로 분기 옮겨 응답한다. 이후 `if분기를 처리해줄 < input->필드들 - 응답값>을 매핑`한다.

- `int`를 포장한 뒤 -> **관련된 if분기 로직을 포장한 도메인으로 넘기면, `메서드 if문이` 사라진다.**, 

![image-20220306183403967](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306183403967.png)



##### `제한된 종류의 응답 값`포장은 Enum을 활용한다.  메서드 응답을 빨간줄로 바꿔 생성하자.

- 만약, 제한되지 않았다면? `class Rank`로, **현재는 `제한된 종류`의 값 응답이므로 `Enum`으로 매핑해서 `if를 대신`하게 해보자.**

    ![image-20220306183754213](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306183754213.png)

    



- 연속된 상수라면, `Enum명_ 순서`를 활용하면 된다.

    - 1,2,3,4,5등 -> `등`을 Enum으로 만들고 등_1,2,3,4,5; 로 선언해보자.

    ```java
    package lotto;
    
    public enum Rank {
    
        RANK_1,
        RANK_2,
        RANK_3,
        RANK_4,
        RANK_5;
    }
    
    ```

    



#### 13 응답에 대한 분기로직을 어떻게 응답class/응답Enum으로 넘길까? 

![image-20220306184557340](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306184557340.png)



##### `메서드 응답부`에서 원하는 포장된 응답값인 Enum객체을 뽑아 응답해주는  `return Enum.정펙매of (,)`완성하기

- **결과적으로 `정펙매`인 `Enum.from()` or `Enum.of( , , )`로 Enum객체를 응답해야한다.**

    - 원하는 Enum객체가 응답될 수 있도록 **필요한 인자들을 생각해서 받아들인다.**
    - **실제로 `입력값이 될 것들을 가지고 있는 사용부인 메서드 응답부에서 부터 시작`**

    

    

1. 응답부에서 **Enum.정팩메로 해당하는 `포장응답값`을 `return`해 줄 예정이다.**

    - 필요한 input들이 뭐가 있는지 살펴본다.
    - **메서드호출의 결과값이 있다면, `input`이 명확하도록 따로 변수로 뽑아주자.**

    ![image-20220306190059628](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306190059628.png)
    ![image-20220306190115387](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306190115387.png)

    - **정팩메로는 `Enum`이라는 `포장된 응답값`을 가져올 것이기 때문에 `return`과 같이 작성한다.**
        ![image-20220306190225323](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306190225323.png)

    ![image-20220306190457221](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306190457221.png)

    





##### 값 응답 로직을 ->  포장응답값 정펙매로 잘라내서 처리하기

1. 이제 값 응답로직을 그대로 잘라붙혀넣기 한다.
    ![image-20220306190736124](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306190736124.png)

    - 이동후

    ![image-20220306190810106](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306190810106.png)

    



#### 14 정팩메 내부로 옮긴 분기에 대해, 응답[값]들을 [Enum객체]로 수정해주기 -> 없는 값(0, None)에 대해서도 OUT,NONE객체 만들어서 응답해주기

- 아직 바꾸기 전 응답로직

    ```java
    if (matchCount == 6) {
        return 1;
    }
    if (matchCount == 5 && matchBonus) {
        return 2;
    }
    if (matchCount == 5) {
        return 3;
    }
    if (matchCount == 4) {
        return 4;
    }
    if (matchCount == 3) {
        return 5;
    }
    
    return 0;
    ```

- 수정 + NONE객체 추가후

    ```java
    package lotto;
    
    public enum Rank {
    
        RANK_1,
        RANK_2,
        RANK_3,
        RANK_4,
        RANK_5,
        OUT;
    
        public static Rank of(final int matchCount,
                              final boolean matchBonus) {
    
            if (matchCount == 6) {
                return RANK_1;
            }
            if (matchCount == 5 && matchBonus) {
                return RANK_2;
            }
            if (matchCount == 5) {
                return RANK_3;
            }
            if (matchCount == 4) {
                return RANK_4;
            }
            if (matchCount == 3) {
                return RANK_5;
            }
    
            return OUT;
        }
    }
    
    ```

    ```java
    public static Rank match(final Lotto userLotto,
                             final Lotto winningLotto,
                             final LottoNumber bonusNumber) {
    
        final int matchCount = userLotto.match(winningLotto);
        final boolean matchBonus = userLotto.contains(bonusNumber);
    
        return Rank.of(matchCount, matchBonus);
    }
    
    ```

    



#### 15 메서드 [포장된 응답] 테스트 작성하기

##### 여러분기므로 1case에 대한 -> 1예상값 마다 테매를 작성해야한다.

```java
@Test
void match_1() {
    //given, when
    final Rank rank = LottoService.match(
        new Lotto(1, 2, 3, 4, 5, 6),
        new Lotto(1, 2, 3, 4, 5, 6),
        new LottoNumber(7)
    );

    //then
    assertThat(rank).isEqualTo(Rank.RANK_1);
}
```





#### 16 if분기를 enum정팩매 내부 values().stream+filter( 정펙매input vs 매핑값과 비교 조건식)으로 제거하기



##### 정팩메 input으로 `들어올 데이터`들 中  1개를 골라 `(매핑 예정인) Enum객체의 필드`로 넣어준다.

- **정펙매로 `여러 데이터`가 들어와 사용**가 들어오지만 `of( matchCount, matchBonus)` 

    ![image-20220306222111985](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306222111985.png)

    - **`매핑에 사용될 쉬운 변수 1개`만 선택하여, `정펙매 내부 values().stream.filter 분기`에서 비교할 것이다. Enum객체가 필드로 가지고 있으면 되며, 그 필드 기준으로 매핑값도 차후 배정된다.**



1. 쉽게 **Enum의 필드에 넣기** 위해서는 **일단 ENUM`(1)` 객체 옆에 빨간줄로 `원시형 예제`작성해준다.**
    ![image-20220306222323949](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306222323949.png)

    ![image-20220306222358375](C:\Users\cho_desktop\AppData\Roaming\Typora\typora-user-images\image-20220306222358375.png)



2. **`생성자를 생성`해주면서 `input과 똑같이 네이밍`후   `빨간줄 this.필드명 = 필드; 초기화`를 이용해서 필드를 생성한다.**

    ![image-20220306222528608](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306222528608.png)


    
    ![image-20220306222623598](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306222623598.png)

    ![image-20220306222631104](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306222631104.png)



4. 나머지 객체도 데이터를 넣어준다.

    ```java
    public enum Rank {
    
        RANK_1(6),
        RANK_2(5),
        RANK_3(5),
        RANK_4(4),
        RANK_5(3),
        OUT(0);
    
        private final int matchCount;
    
        Rank(final int matchCount) {
            this.matchCount = matchCount;
        }
    
        public static Rank of(final int matchCount,
                              final boolean matchBonus) {
    
            if (matchCount == 6) {
                return RANK_1;
            }
    ```





##### Enum정팩메 내 [제한된종류의 모든 if분기]를  values().stream + filter with input vs 매핑field로 분기 없애기

##### 특수한 경우, 정팩매에서 ENUM매핑값 뽑기도 전에,  early return해준다.

- 특수한 경우로서 2등과 3등을 먼저 **필드는 안됬지만 사용해야하는 변수 matchBonus**로 **홀로 true인 2등을 early return한다.**

    ```java
    public static Rank of(final int matchCount,
                          final boolean matchBonus) {
    
        if (matchBonus && matchCount == 5) {
            return RANK_2;
        }
    ```





##### Enum내에서 values()는 Enum객체배열을 응답하며 거기에 `배열.stream자동완성` 사용하여 `EnumStream 만들어 객체전체를 돌리기`

![image-20220306223256714](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306223256714.png)

![image-20220306223352829](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306223352829.png)



![image-20220306223432081](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306223432081.png)





##### `제한된 종류의 if분기`를 `values().stream (for)  + filter(it ->  input vs 매핑field 비교)` 대체하기 

```java
public static Rank of(final int matchCount,
                      final boolean matchBonus) {

    if (matchBonus && matchCount == 5) {
        return RANK_2;
    }

    Arrays.stream(values())
        .filter(it -> matchCount == it.matchCount)
        .findAny()
        .orElse(OUT);
```



- 기존 if분기들 이제 삭제
    ![image-20220306224423339](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306224423339.png)







##### enum sense: 각 enum객체끝은 마지막도 콤마(,) 그 다음라인(;)  +  .findAny()후 .orElse( NONE객체 )

```java
public enum Rank {

    RANK_1(6),
    RANK_2(5),
    RANK_3(5),
    RANK_4(4),
    RANK_5(3),
    OUT(0),
    ;
```



```java
return Arrays.stream(values())
    .filter(it -> matchCount == it.matchCount)
    .findAny()
    .orElse(OUT);

```



#### 17 `Bi(비.아이.) Predicate< , >`로 `input된 2개 값을 다 사용하는 1개의 람다식필드 && filter속 예비 조건식(.test(a,b)로 실행)`을 만들어 ->  [enum의 1 필드 매핑]에 사용되는 Type을 `함수형인터페이스`로 사용하여 -> 정펙매 내부 들어오는 2개값 모두 사용하는 enum 만들기

- **BiPredicate Type은  `1개 함수를 가진 인터페이스 = 함수형 인터페이스` 중 `2개 인자를 받아 실행대기 중인 조건식`을 제공해주고 -> `.test( a, b)`를 통해 외부인자를 받아서 조건식을 실행한다.**
    - `.test(a, b)`에 정팩메로 들어온 input2개를 넣어주면 된다.



##### 아직 BiPredicate 적용안한 기존 코드 (상금도 매핑 안된 상태)

```java
public enum Rank {

    RANK_1(6),
    RANK_2(5),
    RANK_3(5),
    RANK_4(4),
    RANK_5(3),
    OUT(0),
    ;

    private final int matchCount;

    Rank(final int matchCount) {
        this.matchCount = matchCount;
    }

    public static Rank of(final int matchCount,
                          final boolean matchBonus) {

        if (matchBonus && matchCount == 5) {
            return RANK_2;
        }

        return Arrays.stream(values())
            .filter(it -> matchCount == it.matchCount)
            .findAny()
            .orElse(OUT);
    }

}
```



##### Enum필드에 2인자 람다식인 BiPredicate를 넣어서 생성하기



1. 이번엔 `Enum()`객체안에 input 중 1개 6,5,4.. 대신에 **`input 2필드 다 써서 1개 람다식`을 만들어 예제로 넣어주자.**
    - 이왕이면 다중커서로 한번에 다 입력해주자.
        ![image-20220306231741730](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306231741730.png)
        ![image-20220306231807230](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306231807230.png)
        ![image-20220306231833577](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306231833577.png)
        ![image-20220306231947811](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306231947811.png)





##### 아쉽게도 BiPredicate는 `직접 필드입력 -> private final 필드 변수 생성 -> 생산자 자동완성`로 가야한다. (원래는 예제 -> 생산자 -> 빨간줄 this.필드 생성)

- **원래 객체( `원시값 등 쉬운 Type`)을 빨간줄 필드로 넣으면, 생성자에 파라미터 공짜 -> this.로 필드 공짜 생성**





1. BiPredicate 작성

    - **받은 인자 2개는 `if분기에 쓰였던 input값 2개`다.** -> `원래 Enum의 매핑 쉬운 상수 응답값 6, 5, 4...`와  비교해야한다.

    ```java
    public enum Rank {
    
        RANK_1(((matchCount, matchBonus) -> matchCount == 6)),
        RANK_2((matchCount, matchBonus) -> matchCount == 5 && matchBonus),
        RANK_3((matchCount, matchBonus) -> matchCount == 5 && !matchBonus),
        RANK_4((matchCount, matchBonus) -> matchCount == 4),
        RANK_5((matchCount, matchBonus) -> matchCount == 3),
        OUT((matchCount, matchBonus) -> matchCount < 3),
        ;
    ```



2. 필드 직접 입력 `private final`

    - 제네릭 타입은 **들어올 인자들의 Type(정팩메로 들어올 Type)을 Boxing한 타입 + `네이밍은 condition`으로 실행될 조건식임을 인지시킴**

    ```java
    private final BiPredicate<Integer, Boolean> condition;
    ```

    ![image-20220306233622030](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306233622030.png)





3. 생성자 자동완성
    ![image-20220306233747924](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306233747924.png)
    ![image-20220306233758792](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220306233758792.png)



##### values().stream + filter분기에서 `.test( , )로 정펙매 인자`를 받아 조건식 실행



```java
public static Rank of(final int matchCount,
                      final boolean matchBonus) {
    return Arrays.stream(values())
        .filter(it -> it.condition.test(matchCount, matchBonus))
        .findAny()
        .orElse(OUT);
}
```



#### 18 응답값의 포장(리팩토링)이 다 끝났으면, 자체 도메인Test(Enum은 정펙매 테스트)



```java
@Test
void rank_1() {
    final Rank rank = Rank.of(6, false);

    assertThat(rank).isEqualTo(Rank.RANK_1);
}

@Test
void rank_2() {
    final Rank rank = Rank.of(5, true);

    assertThat(rank).isEqualTo(Rank.RANK_2);
}

@ParameterizedTest
@CsvSource({"5,false,RANK_3", "4, false, RANK_4", "3, false, RANK_5"})
void rank_3_5(final int matchCount, final boolean matchBonus, final Rank expected) {
    final Rank rank = Rank.of(matchCount, matchBonus);

    assertThat(rank).isEqualTo(expected);
}
```




---
toc: true
layout: post
title: assertj 및 @annotation 모음
description: assert시리즈와 @어노테이션들
categories: [java, 우테코, 테스트, assertj, TDD]
image: "images/posts/wootech.png"
---



## 학습 테스트 with Junit5



- **`학습테스트`: 모듈 사용법 읽히기**  -> **다른사람이 만든 코드(api)를 검증  =  학습 테스트**
- **`단위테스트`: 내가 짠 코드를 검증하기 위해 만드는 테스트**
    - 단위테스트부터 모든 코드를 설계하는 -> **TDD**





### StringTest 

#### dependency

1.  **build.gradle에 `assertj`추가 -> depenecies `run` or 우측에 gradle > `Reload All Gradle Project`**

    ```java
    testImplementation 'org.assertj:assertj-core:3.22.0'
    ```

    - `assertThat()`메서드 사용안될 시

        ```java
        import static org.assertj.core.api.Assertions.*;
        ```
        


#### assertThat



2. `given(data)` -> `when(actual값)` ->  `then(expected넣어서 비교)` 나눠서 코드를 짠다.

    - **배열 Test: `.contains( ) or .containsExactly()` + 콤마로 개별원소 입력**

    ```java
    @Test
    void 숫자2개_분리() {
        //given
        String data = "1,2";
        //when
        String[] actual = data.split(",");
        //then
        //        assertThat(actual).contains("1", "2");
        assertThat(actual).containsExactly("1", "2");
    }
    
    @Test
    void 숫자1개_분리() {
        //given
        String data = "1";
        //when
        String[] actual = data.split(",");
        //then
        //        assertThat(actual).contains("1", "2");
        assertThat(actual).containsExactly("1");
    }
    
    
    @Test
    void 괄호제거() {
        String data = "(1,2)";
        String actual = data.substring(1, 4);
        assertThat(actual).isEqualTo("1,2");
    }
    ```




#### assertThatThrownBy

**지정한 에러가 나타나야 통과**



3. **에러 내는 테스트는 `assertThatThrownBy( () -> {} ).isinstanceof();`에다가 `예외이름.class`를 인자로 줘서 해당 예외일때 통과된다.**

    - **필요하다면, `해당에러 + 에러메세지까지 확인`하기 위해 `.hasMessageContaining( "메세지" );`까지 체이닝한다. **

    ```java
    //원하는 에러을 안줬더니... 테스트 통과 못한다.
    @Test
    @DisplayName("문자열 인덱스 에러 테스트1")
    void 문자열_인덱스_테스트() {
        assertThatThrownBy(() -> {
            "abc".charAt(4);
        }).isInstanceOf(IllegalArgumentException.class);
    }
    
    //    java.lang.AssertionError:
    //    Expecting actual throwable to be an instance of:
    //    java.lang.IllegalArgumentException
    //    but was:
    //    java.lang.StringIndexOutOfBoundsException: String index out of range: 4
    
    @Test
    @DisplayName("문자열 인덱스 에러 테스트2")
    void 문자열_인덱스_테스트2() {
        assertThatThrownBy(() -> {
            "abc".charAt(4);
        }).isInstanceOf(StringIndexOutOfBoundsException.class);
    }
    ```
    
    



#### assertThatThrownBy + hasMessageContaining

- 해당 에러 + 해당 메세지까지 내어야 통과

    - **도메인 생성자에 포함된 로직에서 메세지까지 같이 검사하자**

    ```java
    @DisplayName("로또 번호가 유효하지 않는 경우 예외를 발생시킨다.")
    @ParameterizedTest
    @ValueSource(strings = {"0", "46", "-1"})
    void input_lottoNumber_invalid(final String number) {
        assertThatThrownBy(() -> new LottoNumber(number))
            .isInstanceOf(IllegalArgumentException.class)
            .hasMessageContaining("로또 번호가 유효한 범위");
    }
    ```

    

### 자동차경주

#### assertDoesNotThrow

- assertThatThrownBy와 달리 **에러가 안나야 통과**

    ```java
    @ParameterizedTest
    @ValueSource(strings = {"o", "brown"})
    @DisplayName("1이상 5이하의 자동차 이름의 길이 확인")
    public void car_name_length_of_positive_name(String name) {
        assertDoesNotThrow(() -> new Car(name));
    }
    ```

    





#### assertThat().isNotNull();

- inputView에서 하는 null or Empty검사시 할 것 같다.

```java
final Car car = new Car("재성");

assertThat(car).isNotNull();
```



#### assertThat( service ).extracting("내부 인스턴스 변수")

- **service 내부에** 존재하는 일급or객체List를 toDto로 가져오는 반면, **VO나 단일 값 변수는 직접 가져와 객체-예상객체 / 값-예상값**으로 비교할 수 있다.

```java
@Test
void get_round() {
    racingService = new RacingService(carNames, 5);

    //when, then
    assertThat(racingService).extracting("round").isEqualTo(new Round(5));
}
```





### 범위검사

#### 값 1개: 랜던뽑기를  여러번 @RepeatedTest(100) + assertThat().isBetween(a,b) 랜덤응답의 범위 검사

```java
//사용할 객체 만들고
RandomNumberGenerator randomNumberGenerator;

@BeforeEach
void setUp() {
randomNumberGenerator = new RandomNumberGenerator();
}


//응답값의 범위 확인을 여러번
@RepeatedTest(100)
void range_test() {
assertThat(randomNumberGenerator.generate()).isBetween(0, 9);
}
```





#### 랜덤값 List 원소들 : 매번범위 검사 : listIterator()를 만든 뒤, while( .hasNext() )동안 next()하면서, 체이닝으로 .크거나같(최소값 객체) .작거나같(최대값 객체)  

```java
@Test
void Random_숫자범위_검사() {
    // 원래는 랜덤으로 차있는 list -> iterator
    final ListIterator<Integer> numbers = Arrays.asList(1, 2, 3, 4, 44, 45).listIterator();

    // 랜덤값들을 돌면서 검사
    while (numbers.hasNext()) {
        assertThat(numbers.next())
            .isGreaterThanOrEqualTo(1)
            .isLessThanOrEqualTo(20);
    }
}
```



### SetTest

#### @BeforeEach

1. **매번 초기화해야하는 변수는 `Test안에서의 class <빈> 변수` + `@BeforeEach`로 정의한 `setUp`메소드에서 `초기화 + 데이터삽입`해준다.**

    ```java
    public class SetTest {
    
        // [빈 변수] -> 매 TEST마다 초기화+데이터 삽입을 반복해준다.
        private HashSet<Integer> numbers;
    
        @BeforeEach
        void setUp() {
            numbers = new HashSet<>();
            numbers.add(1);
            numbers.add(1);
            numbers.add(2);
            numbers.add(3);
        }
    
        @Test
        void 사이즈_확인() {
            //given, when
            int size = numbers.size();
            //then
            assertThat(size).isEqualTo(3);
        }
    }
    ```

    

#### @ParameterizeedTest

##### @ValueSource

**`@ParameterizedTest`로 중복해서 테스트할 필요없이, `given으로 들어갈 메소드인자` or `given + expected`로 들어갈 메소드 인자들**을 지정해서 여러 케이스를 테스트한다.

- `@ValueSource`(ints = {1,2,3,4})

    - **한가지 값만 뿌려줄 때**

    ```java
    @ParameterizedTest
    @ValueSource(ints = {1,2,3,4})
    public void set_contains(int number) {
        assertThat(numbers.contains(number)).isTrue();
    }
    ```





##### @CsvSource

- `@CsvSource`(value = {"1:true", "2:true", "3:true", "4:false"}, delimiter = ':')

    - **여러가지 값 -> `여러개의 인자`로 뿌려 줄 때**
    - delimiter 옵션을 안쓰려면, 그냥 `,`콤마로 데이터들을 구분해주면 된다.
        - `@CsvSource`({"1:,rue", "2,true", "3,true", "4,false"})

    ```java
    @ParameterizedTest
    @CsvSource(value = {"1:true", "2:true", "3:true", "4:false"}, delimiter = ':')
    void set_contains2(int number, boolean expected) {
        assertEquals(numbers.contains(number), expected);
    }
    ```

    





#### @NullAndEmptySource

- 테스트 메서드의 인자에다가 @어노테이션으로는 null을 넘길 수 없다.

    - **`@NullAndEmptySource`를 달아주면, 인자에 1,2번째로 null과 emtpy가 전달된다.**
    - 추가로 `@ValueSouce`를 주면 3번째 인자로 넘어간다.
        - `"이 름"`를 주게 되면 **blank**까지 테스트가 된다.

    ```java
    @ParameterizedTest
    @NullAndEmptySource
    @ValueSource(strings = {"이 름"})
    void nullAndEmpty_or_blank(String name) {
        System.out.println(name);
        assertThatThrownBy(() -> new Car(name))
            .isInstanceOf(IllegalArgumentException.class);
    }
    ```

    





### 그외 Assertj 메소드 모음

| **isEqualTo**(obj)                          | obj 와 같다                                     |
| ------------------------------------------- | ----------------------------------------------- |
| **isNotEqualTo**(obj)                       | obj 와 다르다                                   |
| **isEqualToIgnoringCase**(str)              | str 와 같다 (대소문자 무시)                     |
| **contains**(str)                           | str 를 포함한다                                 |
| **containsIgnoringCase**(str)               | str 를 포함한다 (대소문자 무시)                 |
| **doesNotContain**(str)                     | str 를 포함하지 않는다                          |
| **startsWith**(str)                         | str 로 시작한다                                 |
| **doesNotStartWith**(str)                   | str 로 시작하지 않는다                          |
| **endsWith**(str)                           | str 로 끝난다                                   |
| **doesNotEndWith**(str)                     | str 로 끝나지 않는다                            |
| **matches**(regex)                          | regex 정규식과 같다                             |
| **doesNotMatch**(regex)                     | regex 정규식과 같지 않다                        |
| **isLowerCase**(str)                        | str 은 소문자로 이루어져 있다                   |
| **isUpperCase**(str)                        | str 은 대문자로 이루어져 있다                   |
| **isZero**(n)                               | n 은 0 이다                                     |
| **isNotZero**(n)                            | n 은 0 이 아니다                                |
| **isOne**(n)                                | n 은 1 이다                                     |
| **isPositive**(n)                           | n 은 양수 이다                                  |
| **isNegative**(n)                           | n 은 음수 이다                                  |
| **isNotPositive**(n)                        | n 은 양수 또는 0 이다                           |
| **isBetween**(start, end)                   | start 와 end 사이의 값 이다                     |
| **isStrictlyBetween**(start, end)           | start 와 end 사이의 값이 아니다                 |
| **isCloseTo**(n, within 또는 offset)        | 주어진 within 또는 offset 에 가까운 값이다.     |
| **isNotCloseTo**(n, byLessThan 또는 offset) | 주어진 within 또는 offset 에 가까운 값이 아니다 |
| **isCloseTo**(n, withinPercentage)          | 주어진 백분율 내에서 주어진 숫자에 가깝다       |
| **isNotCloseTo**(n, withinPercentage)       | 주어진 백분율 내에서 주어진 숫자에 가깝지 않다  |
| **isTrue**()                                | 참이다                                          |
| **isFalse**()                               | 거짓이다                                        |
| **isNull**()                                | null 값 이다                                    |
| **isNotNull**()                             | null 값 아니다                                  |
| **isBlank**()                               | 빈 값 이다 (공백 미포함)                        |
| **isNotBlank**()                            | 빈 값 아니다 (공백 미포함)                      |
| **isEmpty**()                               | 빈 값 이다 (공백 포함)                          |
| **isNotEmpty**()                            | 빈 값 아니다 (공백 미포함)                      |
| **isNullOrEmpty**()                         | null 값 이거나 빈 값 이다 (공백 포함)           |
| **isLessThan**(str)                         | str 보다 낮은 문자열 이다 (Ascii 코드)          |
| **isIn**(...obj)                            | 여러개의 obj 중 1개와 같다                      |
| **isNotIn**(...obj)                         | 여러개의 obj 와 모두 다르다                     |
| **filteredOn(...)**                         | list 필터                                       |
| **extracting(...)**                         | list 프로퍼티 값                                |

 





#### assertj string 예제	

```java
assertThat("Hello, world! Nice to meet you.") // 주어진 "Hello, world! Nice to meet you."라는 문자열은
				.isNotEmpty() // 비어있지 않고
				.contains("Nice") // "Nice"를 포함하고
				.contains("world") // "world"도 포함하고
				.doesNotContain("ZZZ") // "ZZZ"는 포함하지 않으며
				.startsWith("Hell") // "Hell"로 시작하고
				.endsWith("u.") // "u."로 끝나며
				.isEqualTo("Hello, world! Nice to meet you."); // "Hello, world! Nice to meet you."과 일치합니다.
```



#### assertj number 예제

```java
assertThat(3.14d) // 주어진 3.14라는 숫자는
				.isPositive() // 양수이고
				.isGreaterThan(3) // 3보다 크며
				.isLessThan(4) // 4보다 작습니다
				.isEqualTo(3, offset(1d)) // 오프셋 1 기준으로 3과 같고
				.isEqualTo(3.1, offset(0.1d)) // 오프셋 0.1 기준으로 3.1과 같으며
				.isEqualTo(3.14); // 오프셋 없이는 3.14와 같습니다
```


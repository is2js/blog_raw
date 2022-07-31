---
toc: true
layout: post
title: Assertj 및 @annotation 모음
description: assertj시리즈와 @어노테이션들(Assertion)
categories: [java, 우테코, test, assertj, TDD]
image: "images/posts/java.png"
---



## assertj 및 annotation with Junit5



- **`학습테스트`: 모듈 사용법 읽히기**  -> **다른사람이 만든 코드(api)를 검증  =  학습 테스트**
- **`단위테스트`: 내가 짠 코드를 검증하기 위해 만드는 테스트**
    - 단위테스트부터 모든 코드를 설계하는 -> **TDD**


## 의존성 추가
- `@Test` 대신 `@ParameterizedTest` for 여러case 를 쓰기 위해서 아래 의존성을 추가해야한다.
    ```java
    // Assertions
    testImplementation 'org.assertj:assertj-core:3.22.0'
    testImplementation 'org.junit.jupiter:junit-jupiter-params:5.4.2' 
    ```
## Live template

- `@Test` 대신 `@ParameterizedTest` for 여러case 를 쓰기 위해서 아래 의존성을 추가해야한다.
    ```java
    // Assertions
    testImplementation 'org.assertj:assertj-core:3.22.0'
    testImplementation 'org.junit.jupiter:junit-jupiter-params:5.4.2' 
    ```

- `as+all`
    ```java
    assertAll(
        () -> assertThat(actual).isEqualTo(expected$END$),
        () -> assertThat(actual).hasSize()
    );
    ```
- `as+that`
    ```java
    Assertions.assertThat(actaul).isEqualTo(expected$END$);
    ```
- `as+notthr`
    ```java
    Assertions.assertDoesNotThrow(() -> $WHEN$)
    ```
- `as+thr`
    ```java
    Assertions.assertThatThrownBy(() -> $WHEN$)
        .isInstanceOf($EXCEPTION$.class)
        .hasMessage("$MESSAGE$");
    ```
- `given` : **option에 있는 `reformat ~ style` 체크할 것**
    ```java
    //given

    //when
    $END$

    //then
    ```

- `methodSource`: **option에 있는 `reformat ~ style` 체크할 것**
    ```java
    return Stream.of(
            Arguments.of($END$),
            Arguments.of()
        );
    ```

- `dynamicTest`: [예시 블로그](https://blog.kingbbode.com/52?category=737337)
    ```java
    @DisplayName("큰테스트이름")
    @TestFactory
    Stream<DynamicTest> collectionsOfDynamicTest() {
        return Stream.of(
                dynamicTest("테스트케이스1", () -> {
                    // given
                    

                    // when
                    $END$
                    

                    // then
                    assertThat(actual).isEqualTo(expected);
                }),

                dynamicTest("테스트케이스2", () -> {
                    // given


                    // when
                    

                    // then
                    assertThat(actual).isEqualTo(expected);
                })
        );
    }
    ```
    - 예시
        ![20220728175610](https://raw.githubusercontent.com/is3js/screenshots/main/20220728175610.png)




## 용례 

### VO

#### 숫자 VO

##### 2개를 뽑아서 일치 or 대소비교

```java
@Test
void 로또_번호_일치_여부_검사() {
    LottoNumber lottoNumber = LottoNumber.getInstance(1);
    LottoNumber lottoNumber2 = LottoNumber.getInstance(1);

    assertThat(lottoNumber).isEqualTo(lottoNumber2);
}
```

```java
@Test
void comparable() {
    final LottoNumber lowerLottoNumber = LottoNumber.valueOf(2);
    final LottoNumber largerLottoNumber = LottoNumber.valueOf(20);

    assertThat(lowerLottoNumber).isLessThan(largerLottoNumber);
}
```





##### 캐싱 -> 메모리 주소까지 같은지

```java
@Test
void create2() {
    final LottoNumber lottoNumber = LottoNumber.of(1);
    final LottoNumber lottoNumber2 = LottoNumber.of(1);

    // 정펙매를 이용한 [같은 객체 다시 부를 땐] 완전히 동일한 캐싱 객체 가져오기
    assertThat(lottoNumber).isSameAs(lottoNumber2); // true
}
```





#### COUNT VO

##### 0까지 허용하여  음수 생성자 에러

```java
@Test
void create() {
    assertAll(
        () -> {
            assertDoesNotThrow(() -> new LottoCount(1));
            assertDoesNotThrow(() -> new LottoCount(0));
            assertThatThrownBy(() -> new LottoCount(-1));
        }
    );
}
```



##### 1개 뽑 + 감소된 것 뽑 ->  감소된 로직후 동일한지 비교

```java
@Test
void decrease() {
    final LottoCount lottoCount = new LottoCount(1);
    final LottoCount expected = new LottoCount(0); // 감소된 것 미리 뽑기

    final LottoCount actual = lottoCount.decrease();

    assertThat(actual).isEqualTo(expected);
}
```



### 단일객체

#### 상수단일객체 .isSameAs( 실시간생성 단일객체 ) 



**상수객체가 정상 단일객체럼 행동하는지 `해당 객체 테스트`에서 `객체상수` isSamaAs `생성한 객체` 테스트를 해보자**

- 기존 테스트

    ```java
    @Test
    void of() {
        final Card card = Card.of(Suit.HEARTS, Denomination.ACE);
    
        assertThat(card).isSameAs(Card.of(Suit.HEARTS, Denomination.ACE));
    }
    ```

- 사용되는 단일객체를 -> 상수객체로 뽑아서 옮겨놓고 상수로 가져오자.

    ![image-20220321003729070](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220321003729070.png)|![image-20220321003745221](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220321003745221.png)

    ![image-20220321003805797](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220321003805797.png)

    ![image-20220321003815981](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220321003815981.png)

    ![image-20220321003822240](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220321003822240.png)
    
    ![image-20220321003840933](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220321003840933.png)



```java
@Test
void of() {
    assertThat(HEART_ACE).isSameAs(Card.of(Suit.HEARTS, Denomination.ACE));
}
```

- 나머지 사용되는 단일객체들도 다 상수화 -> Fixutre에 public 상수로 옮기기 해주자







### 일급

- **getValue()**로 뽑아내고 한다.
    - extracting은 assertThat() .**extracting**("value") **이후 추가 메서드를 못쓴다. list 그대로 비교해야됨.** 

#### 숫자VO List 

##### 갯수 + (수동) 개별원소 포함검사 +  (랜덤)제한된 범위 경계값 대소 검사

- **갯수** -> 꺼내서 size()

    ```java
    @Test
    void size() {
        //given & when
        final Lotto lotto = LottoFactory.generateLotto();
        final int lottoSize = lotto.getValue().size();
    
        //then
        //일급 재료list구성에 랜덤이 있다면, 갯수 + 제한된 범위 -> 경계값 대소 검사
        assertThat(lottoSize).isEqualTo(6);
    }
    ```



##### assertThat(   list   ) .hasSize(n) or .isNotEmpty()

```java
@DisplayName("4. findAll _by_select")
@Test
void select_findAll() {
    final MemberDao memberDao = new MemberDao();// 1. dao.crud()중에
    final List<Member> members = memberDao.findAll(); //2.findall 메서드는?

    //3. list의 size를 검사할 때, 빈 것(null같은)에 대핸 비교는 isNotEmpty();로 해주면 된다.
    assertThat(members).isNotEmpty();
}
```



##### list의 순서까지 검사 .containsExactly ()




- 한번에 생성한 일급 -> **개별원소를 안다면, 제대로 포함하고 있는지 테스트**

    - contains -> 일부 포함 (중복가능, 순서틀려도 가능)
    - containsExactly -> 순서+갯수 모두 일치해야하는 거의 equal

    ```java
    @Test
    void contains() {
        final Lotto manualLotto = new ManualLottoIssuer(1, 2, 3, 4, 5, 6).issue();
    
        assertThat(manualLotto.getValue()).containsExactly(
            new LottoNumber(1),
            new LottoNumber(2),
            new LottoNumber(3),
            new LottoNumber(4),
            new LottoNumber(5),
            new LottoNumber(6)
        );
    }
    ```

    

- **제한된 범위 -> 경계값 대소 검사**

    ```java
    @Test
    void range() {
        //given & when
        final Lotto lotto = LottoFactory.generateLotto();
        //일급 재료list구성에 랜덤이 있다면, 갯수 + 제한된 범위 -> 경계값 대소 검사
        // 1. list Iterator 만들고
        final ListIterator<LottoNumber> lottoNumberIterator = lotto.getValue().listIterator();
    
        //then
        // 2. hasNext()로 돌면서
        while (lottoNumberIterator.hasNext()) {
            // 3. actual란에 next()로 하나씩 떨궈주기
            assertThat(lottoNumberIterator.next())
                // 4. Comparable 먼저 impl안하면 아래 문장 2개 에러남
                .isLessThanOrEqualTo(LottoNumber.getInstance(45))
                .isGreaterThanOrEqualTo(LottoNumber.getInstance(1));
        }
    }
    ```

    

#### list

##### 일부 필드만 추출해서 검사 .extracting("").containsExactly()

- hasSize()에 이어서 chaining해서 검사할 수 있다.

- **해당 필드값 list로 변하게 되니 -> `.containsExactly( 1값, 2값, ...)`로 검사한다**

    ```java
    assertAll(
        () -> assertThat(response.statusCode()).isEqualTo(HttpStatus.OK.value()),
        () -> assertThat(response.body().jsonPath().getList("stations")) // 최단거리 1-2-3-4-5(50) <  1-5 (55)
        .hasSize(5) // list의 size와 숫자 비교 (완료됨에도 이어서 list 검증을 chaining할 수 있음)
        .extracting("name") // list의 특정필드 비교 -> 콤마를 통해 2개를 뽑아낼 수 도 있음. -> 이 땐, t
        .containsExactly("강남역", "잠실역", "선릉역", "사당역", "신림역")
        // contains -> 일부 포함 (중복가능, 순서틀려도 가능) vs containsExactly -> 순서+갯수 모두 일치해야하는 거의 equal
    );
    ```





##### 일부 필드 2개이상 추출 containsExactly + tuple

- 필드를 1개만 추출할 땐, containsExactly()의 힌트가 `Object...` values로  `콤마로 값 1개`만 입력하면 되었는데

- **필드르 2개 추출하는 순간, 힌트가 `Tuple`... values**로 바뀐다.

    ![image-20220524214646548](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220524214646548.png)

    - 2개이상의 값을 `tuple()`에 씌우면 된다.

    ![image-20220524214725474](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220524214725474.png)

    - **특이한 점은 id가 long -> json(string) -> 뽑아오니 다시 int가되어있어서, int로 비교해야한다는 점**

        

```java
assertAll(
    () -> assertThat(response.statusCode()).isEqualTo(HttpStatus.OK.value()),
    () -> assertThat(response.body().jsonPath().getList("stations")) // 최단거리 1-2-3-4-5(50) <  1-5 (55)
    .hasSize(5) // list의 size와 숫자 비교 (완료됨에도 이어서 list 검증을 chaining할 수 있음)
    .extracting("id", "name") // list의 특정필드 비교 -> 콤마를 통해 2개를 뽑아낼 수 도 있음. -> 이 땐, t
    .containsExactly(
        tuple(1, "강남역"),
        tuple(2, "잠실역"),
        tuple(3, "선릉역"),
        tuple(4, "사당역"),
        tuple(5, "신림역"))
    // contains -> 일부 포함 (중복가능, 순서틀려도 가능) vs containsExactly -> 순서+갯수 모두 일치해야하는 거의 equal
);
```



#####  일부 필드 제외 검사 usingRecursiveComparison().ignoringFields("")

1. entity(id필드 배정 전) -> save -> domain(id 배정후) -> **id를 제외한 필드를 비교해서 객체 생성 확인**

    - 그 전에는, id와 같은 기능을 하는 unique field를 꺼내와 검사함

    ```java
    @Test
    @DisplayName("노선을 저장한다.")
    void save() {
        //given
        final Line 이호선_그린_entity = new Line("2호선", "green");
    
        //when
        final Line 이호선_그린_domain = lineDao.save(이호선_그린_entity);
    
        //then
        Assertions.assertThat(이호선_그린_domain).usingRecursiveComparison()
            .ignoringFields("id") // isEqualTo전에 객체에서 무시할 field를 지정
            .isEqualTo(이호선_그린_entity);
    
        lineDao.deleteById(이호선_그린_domain.getId());
    }
    ```

2. 객체안에 **비교하기 힘든 list필드 등을 제외하고 객체간 비교**

    - 다른 list까지 짬뽕된 reponseDto vs 예상값으로 핵심 필드(값)만 뽑은 reponseDto 비교

    ![image-20220525105219686](https://raw.githubusercontent.com/is2js/screenshots/main/image-20220525105219686.png)

    

### ENUM



##### 응답값인 상수Enum 객체 종류별로 정펙매 case Test -> 혹은 정펙매로 찾은 Enum.추가로직()

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

- 추가로직까지 더해진다면

```java
@DisplayName("1등의 상금을 계산할 수 있다.")
@Test
void calculateFirstRankMoney() {
    final Rank first = Rank.FIRST;
    final long count = 3;

    assertThat(Rank.calculateMoney(first, count)).isEqualTo(6000000000L);
}

@DisplayName("2등의 상금을 계산할 수 있다.")
@Test
void calculateSecondRankMoney() {
    final Rank second = Rank.SECOND;
    final long count = 2;

    assertThat(Rank.calculateMoney(second, count)).isEqualTo(60000000L);
}

@DisplayName("3등의 상금을 계산할 수 있다.")
@Test
void calculateThirdRankMoney() {
    final Rank third = Rank.THIRD;
    final long count = 4;

    assertThat(Rank.calculateMoney(third, count)).isEqualTo(6000000L);
}

@DisplayName("4등의 상금을 계산할 수 있다.")
@Test
void calculateFourthRankMoney() {
    final Rank fourth = Rank.FOURTH;
    final long count = 2;

    assertThat(Rank.calculateMoney(fourth, count)).isEqualTo(100000);
}

@DisplayName("5등의 상금을 계산할 수 있다.")
@Test
void calculateFifthRankMoney() {
    final Rank fifth = Rank.FIFTH;
    final long count = 2;

    assertThat(Rank.calculateMoney(fifth, count)).isEqualTo(10000);
}

@DisplayName("맞은 갯수와 보너스 여부를 가지고 랭크를 반환할 수 있다.")
@Nested
class CalculateRank {

    @DisplayName("1등 반환")
    @Test
    void firstRank() {
        final Rank rank = Rank.calculateCurrentRank(6, false);

        assertThat(rank).isEqualTo(Rank.FIRST);
    }

    @DisplayName("2등 반환")
    @Test
    void secondRank() {
        final Rank rank = Rank.calculateCurrentRank(5, true);

        assertThat(rank).isEqualTo(Rank.SECOND);
    }

    @DisplayName("3등 반환")
    @Test
    void thirdRank() {
        final Rank rank = Rank.calculateCurrentRank(5, false);

        assertThat(rank).isEqualTo(Rank.THIRD);
    }

    @DisplayName("4등 반환")
    @Test
    void fourthRank() {
        final Rank rank = Rank.calculateCurrentRank(4, false);

        assertThat(rank).isEqualTo(Rank.FOURTH);
    }

    @DisplayName("5등 반환")
    @Test
    void fifthRank() {
        final Rank rank = Rank.calculateCurrentRank(3, false);

        assertThat(rank).isEqualTo(Rank.FIFTH);
    }

    @DisplayName("Nothing 반환")
    @Test
    void NothingRank() {
        final Rank rank = Rank.calculateCurrentRank(2, false);

        assertThat(rank).isEqualTo(Rank.NOT_THING);
    }
}
```









### 결과값 포장 (새응답) Map

#### [toMap로직의 생성자] &&  getCountByKey( 객체key) 테스트

- Result( Enum Count Map ) 준비사항

```java
public class LottoResult {

    private final Map<Rank, Integer> value;

    public LottoResult(final List<Lotto> lottos, final WinningLotto winningLotto) {
        this.value = lottos.stream()
            .collect(Collectors.groupingBy(
                lotto -> match(lotto, winningLotto),
                Collectors.summingInt(count -> 1)));
    }
```





1. `결과값 Map`을 **만들 재료 중 일부만** setup에 준비한다. **여러 재료라면 `가변쉬운것을 테스트에서` -> `많고 복잡한 것을 setup`에 고정**

    ```java
    class LottoResultTest {
    
        List<Lotto> lottos;
    
        @BeforeEach
        void setUp() {
            //new LottoResult(lottos, winningLotto);
            //새로운 응답값 생성(결과값 포장 클래스 생성자)에 필요한 재료 먼저 선언
            // -> case별 변동이 쉬운 winningLotto는 놔두고, lottos를 미리 만들어놓자.
            lottos = List.of(
                new Lotto(1, 2, 3, 4, 5, 6),
                new Lotto(1, 2, 3, 4, 5, 7),
                new Lotto(1, 2, 3, 4, 7, 8),
                new Lotto(1, 2, 3, 7, 8, 9)
            );
        }
    ```





2. **count Map이라면, 맘 편하게 `.getCount( 객체key )`를 맘편하게 만들자**

    - 뷰에서 쓸 일 있으면 쓰고, 없으면 삭제하면 된다. default를 0으로 해서 count인 value값을 가져오자.
        ![image-20220310161607226](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310161607226.png)

        ```java
        public Integer getCountByRank(final Rank rank) {
            return value.getOrDefault(rank, 0);
        }
        ```





3. **해당 key의 Count를 꺼내와서 정확한지 확인하기**

    - 할수 있다면, debug활용해서 내부값 까보면서 같이하면 될 듯.
        ![image-20220310162521006](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310162521006.png)

    ```java
    @Test
    void create() {
        //given -> 1등이 1개 있도록 가정인자를 만들어줌
        final LottoResult lottoResult = new LottoResult(lottos,
                                                        new WinningLotto(
                                                            new Lotto(1, 2, 3, 4, 5, 6),
                                                            new LottoNumber(7)
                                                        ));
    
        // map이 세고 있던 객체key를 넣어주고 -> Count를 반환하는 메서드 개발
        //when
        final Integer actual = lottoResult.getCountByRank(Rank.RANK_1);
    
        //then
        assertThat(actual).isEqualTo(1);
    }
    }
    ```

    





### [객체 응답]하는 메서드 Test

#### 추상체 변수로 받았어도 -> 해당 구현체 객체 맞음 확인 .isInstanceOf( 특정객체의클래스.class)

```java
@Test
void blackjack() {
    // given & when : hit
    final State state = Game.start(Card.of(Suit.SPADES, Denomination.ACE),
                                       Card.of(Suit.SPADES, Denomination.JACK));

    // then
    assertThat(state).isInstanceOf(Blackjack.class);
}
```



### 예외발생이 통과인 호출 -> assert문내부에서 예외명시하며 호출

#### 생성자 by assertThrows( 일레갈Argument익셉션.class, () -> 메서드호출 )



#### 메서드 by assertThrows( 일레갈State익셉션.class, () -> 메서드호출 )

```java
@Test
void blackjackDraw() {

    final Blackjack state = (Blackjack) Game.start(Card.of(Suit.SPADES, Denomination.ACE),
                                                   Card.of(Suit.SPADES, Denomination.JACK));

    assertThrows(IllegalStateException.class,
                 () -> state.draw(Card.of(Suit.SPADES, Denomination.TEN)));
}
```





## 사용법



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
        



#### assertThat for 동일한지 검사



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



##### contains, ~~containsOnly~~, containsExactly

- [참고블로그](https://bcp0109.tistory.com/317)

1. **contains: 순서틀려도, 중복해서 나열해도(갯수파괴해도) `포함만 되어있으면 통과`** 

    ```java
    containsvoid containsTest() {
        List<Integer> list = Arrays.asList(1, 2, 3);
    
        // Success: 모든 원소를 입력하지 않아도 성공
        assertThat(list).contains(1, 2);
    
        // Success: 중복된 값이 있어도 포함만 되어 있으면 성공
        assertThat(list).contains(1, 2, 2);
    
        // Success: 순서가 바뀌어도 값만 맞으면 성공
        assertThat(list).contains(3, 2);
    
        // Fail: List 에 없는 값을 입력하면 실패
        assertThat(list).contains(1, 2, 3, 4);
    ```

    

2. **containsOnly: 중복하더라도 `순서는 틀려도, 중복 제외한 총 갯수가 일치해야 통과(contains가 아님)`**

    ```java
    /*
     * containsOnly 실패 케이스
     *
     * assertThat(list).containsOnly(1, 2);       -> 원소 3 이 일치하지 않아서 실패
     * assertThat(list).containsOnly(1, 2, 3, 4); -> 원소 4 가 일치하지 않아서 실패
     */
    @Test
    void containsOnlyTest() {
        List<Integer> list = Arrays.asList(1, 2, 3);
    
        assertThat(list).containsOnly(1, 2, 3);
        assertThat(list).containsOnly(3, 2, 1);
        assertThat(list).containsOnly(1, 2, 3, 3);
    }
    ```





3. **containsExactly : `순서+갯수+중복불허   순서대로 모두 같아야 통과(contains가 아니라 일치)`**

    ```java
    /*
     * containsExactly 실패 케이스
     *
     * assertThat(list).containsExactly(1, 2);       -> 원소 3 이 일치하지 않아서
     * assertThat(list).containsExactly(3, 2, 1);    -> list 의 순서가 달라서 실패
     * assertThat(list).containsExactly(1, 2, 3, 3); -> list 에 중복된 원소가 있어서 실패
     */
    @Test
    void containsExactlyTest() {
        List<Integer> list = Arrays.asList(1, 2, 3);
    
        assertThat(list).containsExactly(1, 2, 3);
    }
    ```

    



##### isSameAs

- **메모리 주소까지 같은지 -> 한번 생성했으면 `캐싱된 것`가져오는지**

    ```java
    @Test
    void create() {
        final LottoNumber lottoNumber = new LottoNumber(1);
        final LottoNumber lottoNumber2 = new LottoNumber(1);
    
        // isSameAs: 메모리 주소가 같은지, 오버라이딩된 VO도 같다고 안나옴. 더 엄격한 완전히 같은 객체일 때, True
        assertThat(lottoNumber).isSameAs(lottoNumber2); // false
    }
    
    @Test
    void create2() {
        final LottoNumber lottoNumber = LottoNumber.of(1);
        final LottoNumber lottoNumber2 = LottoNumber.of(1);
    
        // 정펙매를 이용한 [같은 객체 다시 부를 땐] 완전히 동일한 캐싱 객체 가져오기
        assertThat(lottoNumber).isSameAs(lottoNumber2); // true
    }
    ```

    

#### assertThrows  for  더 진행시 게임종료

**지정한 에러가 나타나야 통과**

- 여기선 에러메세지 등 확인은 없다.

- 예외발생시 통과하는 메서드호출을 테스트한다.

    ```java
    @Test
    void blackjackDraw() {
    
        final State state = Game.start(Card.of(Suit.SPADES, Denomination.ACE),
                                       Card.of(Suit.SPADES, Denomination.JACK));
    
        // 블랙잭상태에서 -> 카드받는 메서드 호출시 예외발생하며 종료해야한다.
        assertThrows(IllegalStateException.class,
                     () -> state.draw(Card.of(Suit.SPADES, Denomination.TEN)));
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

    

#### @ParameterizedTest for 여러case

- @Test 대신 쓰려면 아래 의존성을 추가해야한다.
    ```java
    // Assertions
    testImplementation 'org.assertj:assertj-core:3.22.0'
    testImplementation 'org.junit.jupiter:junit-jupiter-params:5.4.2' 
    ```

- local varaible로 뽑힌 변수들을 `ctrl+alt+p`를 통해 테스트메서드의 인자로 올리면서 수행하면 된다.
- CsvSource의 delimeter는 1개의 char기 때문에 `":"`가 아닌 `':'` 작은 따옴표를 활용한다.
    - 알아서 변환되기 때문에 `"1L"`대신 `"1"`을 주면 파라미터 Long이 알아서 받아준다.

##### @ValueSource: `인자 1개` 변수 + `expected 고정`

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





##### @CsvSource: 인자 1개이상 변수 + `expected 가변` -> "arg1:arg2:expected"같이 주되 delimiter로 끊음

- `@CsvSource`(value = {"1:true", "2:true", "3:true", "4:false"}, delimiter = ':')

    - **여러가지 값 -> `여러개의 인자`로 뿌려 줄 때**
    - delimiter 옵션을 안쓰려면, 그냥 `,`콤마로 데이터들을 구분해주면 된다.
        - `@CsvSource`({"1:,true", "2,true", "3,true", "4,false"})

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

    



### 로또

#### .isSameAs : 캐싱된 객체는 식별자 감별 메서드로 확인한다.

```java
@Test
void of_정적팩토리메서리를_캐싱된_객체를_생성한다() {
    final LottoNumber actual = LottoNumber.of(5);
    final LottoNumber expected = LottoNumber.of(5);

    Assertions.assertThat(actual).isSameAs(expected);
}
```


#### @MethodSource: 인자 list or 객체 or string으로 표현할 수 없는 인자 + expected 가변

```java
public static Stream<Arguments> provideOtherLottoAndExpected() {
    return Stream.of(
        Arguments.of(Arrays.asList("1", "2", "3", "4", "5", "6"), 6),
        Arguments.of(Arrays.asList("1", "2", "3", "4", "5", "7"), 5),
        Arguments.of(Arrays.asList("1", "2", "3", "4", "7", "8"), 4),
        Arguments.of(Arrays.asList("1", "2", "3", "7", "8", "9"), 3)
    );
}

@ParameterizedTest
@MethodSource("provideOtherLottoAndExpected")
void compare_lotto_by_lotto(final List<String> otherLottoInput, final int matchedCount) {
    //given
    final Lotto lotto = Lotto.fromInput(Arrays.asList("1", "2", "3", "4", "5", "6"));
    final Lotto otherLotto = Lotto.fromInput(otherLottoInput);
    //when
    final int actual = lotto.compare(otherLotto);
    //then
    assertThat(actual).isEqualTo(matchedCount);
}
```



1. **주로 `일급 재료 1개`에 대해서 `비교할 <<여러>> 일급 case + expected`를 테스트할 때**

    ```java
    //given
    final Lotto lotto = Lotto.fromInput(Arrays.asList("1", "2", "3", "4", "5", "6"));
    final Lotto otherLotto = Lotto.fromInput(otherLottoInput);
    ```



2. **@ParameterizedTest + @MethodSource 붙이면서 `"함수명"`을 string으로 적어준다.**

    - 만약, **expected의 case도 나누고 싶다면 함수를 2개를 제공하면 된다**

        `@MethodSource({ "provideOther일급AndExpected1", "provideOther일급AndExpected2"})`

    ```java
    //1. 정해진 일급 1개에 대해서 vs <비교에 들어갈> others일급-정답의 비교가 필요한 순간에 MethodSource를 쓴다.
    //2. 쓰는 순간 "provideOther일급And정답" 메서드명만적어주고 -> 생성 -> 파라미터로 받아주자.
    @ParameterizedTest
    @MethodSource("provideOtherLottoAndExpected")
    ```

3. **`함수명을 주지 않고 @MethodSource`만 붙인 상태에서 `generate하면, 테스트메서드명과 동일한 명칭으로 생성된다.`**

    ![464f4fb0-7e6a-4347-8beb-19ac58a53035](https://raw.githubusercontent.com/is2js/screenshots/main/464f4fb0-7e6a-4347-8beb-19ac58a53035.gif)

4. string함수명에서 `generate`시키면 함수를 만들 수 있다.

    - 응답 형이 `Stream<Arguments>`가 자동으로 작성된다.

    ```java
    //3. 메서드를 공짜로 만들면, 응답값이 자동으로 Stream<Arguments>다.
    public static Stream<Arguments> provideOtherLottoAndExpected() {
    ```

    



4. return은 Stream으로 만들어줘야하니 **method body를 `return Stream.of( )`형태로 일단 작성**한다.

    - **테스트함수에 전달될 파라미터 갯수대로 `Arguments.of( , , )`에 순서대로 작성해준다.**

    ```java
    public static Stream<Arguments> provideOtherLottoAndExpected() {
        return Stream.of(
            Arguments.of(Arrays.asList("1", "2", "3", "4", "5", "6"), 6),
            Arguments.of(Arrays.asList("1", "2", "3", "4", "5", "7"), 5),
            Arguments.of(Arrays.asList("1", "2", "3", "4", "7", "8"), 4),
            Arguments.of(Arrays.asList("1", "2", "3", "7", "8", "9"), 3)
        );
    }
    ```

    

5. **테스트 함수에서 `파라미터 정의`를 위해, Arguments.of()에 들어간 인자들을 1개 복사해와서 변수추출로 작성하면 쉽다.**

    ```java
    @ParameterizedTest
    @MethodSource("provideOtherLottoAndExpected")
    void compare_lotto_by_lotto(final List<String> otherLottoInput, final int matchedCount) {
        //Arrays.asList("1", "2", "3", "4", "5", "6"), 6
        //final List<String> otherLottoInput = Arrays.asList("1", "2", "3", "4", "5", "6");
        //final int matchedCount = 6;
        //final List<String> otherLottoInput, final int matchedCount
    ```

6. **`String표현불가 객체/list 등의 1개이상의 변수 파라미터`를 `actual에서 미리 빨간색으로 작성 -> 파라미터 추출(ctrl+alt+P)로 파라미터를 채우면 쉽다`**

    ![95fb732d-4ea2-479f-a39f-f19971ddebb2](https://raw.githubusercontent.com/is2js/screenshots/main/95fb732d-4ea2-479f-a39f-f19971ddebb2.gif)

7. 완성본

    ```java
    @ParameterizedTest
    @MethodSource("provideOtherLottoAndExpected")
    void compare_lotto_by_lotto(final List<String> otherLottoInput, final int matchedCount) {
        //given
        final Lotto lotto = Lotto.fromInput(Arrays.asList("1", "2", "3", "4", "5", "6"));
        final Lotto otherLotto = Lotto.fromInput(otherLottoInput);
        //when
        final int actual = lotto.compare(otherLotto);
        //then
        assertThat(actual).isEqualTo(matchedCount);
    }
    ```








### 블랙잭

#### @Nested

1. **setUp할 예시가 달라지는 경우 `서로 다른 @BeforeEach`를 가지게 할려고 `기존 @beforeEach - setUp`를 `Nest`클래스에 넣어주려고 사용한다.**

    - 즉, 같은 상태값을 setup하는 경우만 묶어서 처리해준다.

2. **`@Nested 명시이후 @디플("")이후` `ClassName이Test`로 끝나야한다.**

    ```java
    class GameResultTest {
    
        //1. 가변으로 셋업에서 초기화하는데
        Dealer dealer = new Dealer();
        Gambler gambler = new Gambler("돌범");
        CardDeck cardDeck;
        PlayingCard card1;
        PlayingCard card2;
    
    
        //3. @beforEach를 Nest에 넣어준다.
        @Nested
        @DisplayName("버스트가 없는 경우에 한하여")
        class GameResultWithoutBurstTest {
            @BeforeEach
            void setUp() {
                //given
                card1 = new PlayingCard(Suit.CLUBS, Denomination.FIVE);
                card2 = new PlayingCard(Suit.CLUBS, Denomination.SIX);
                Deque<PlayingCard> rawCardDeck = new ArrayDeque<>();
                rawCardDeck.push(card1);
                rawCardDeck.push(card2);
                cardDeck = new CardDeck(() -> rawCardDeck);
            }
    
            @DisplayName("카드 점수에 따라 승에 해당하는 객체를 반환하는지 확인한다.")
            @Test
            void win() {
                //given
                cardDeck.drawTo(dealer); // six to dealer
                cardDeck.drawTo(gambler); // five to gambler
    
                //when
                final GameResult result = GameResult.of(dealer, gambler);
    
                //then
                assertThat(result).isEqualTo(GameResult.WIN);
            }
        }
    
    
    	//2. 갑자기 예시가 셋업데이터가 달라져야할 경우,
        @DisplayName("딜러가 버스트인 경우, 패배 결과 객체를 반환하는지 확인한다.")
        @Test
        void burst_dealer_lose() {
            //4. 원래 이후 새로운 예시도 -> nest -> 서로 다른 @beforeEach를 지정해서 사용해야한다.
            //given
            PlayingCard card = new PlayingCard(Suit.CLUBS, Denomination.KING);
            PlayingCard burst1 = new PlayingCard(Suit.HEARTS, Denomination.KING);
            PlayingCard burst2 = new PlayingCard(Suit.HEARTS, Denomination.JACK);
            PlayingCard burst3 = new PlayingCard(Suit.HEARTS, Denomination.QUEEN);
            Deque<PlayingCard> rawCardDeck = new ArrayDeque<>();
            rawCardDeck.push(card);
            rawCardDeck.push(burst1);
            rawCardDeck.push(burst2);
            rawCardDeck.push(burst3);
            cardDeck = new CardDeck(() -> rawCardDeck);
    
            cardDeck.drawTo(dealer);
            cardDeck.drawTo(dealer);
            cardDeck.drawTo(dealer);
            cardDeck.drawTo(gambler);
    
            //when
            final GameResult result = GameResult.of(dealer, gambler);
    
            //then
            assertThat(result).isEqualTo(GameResult.LOSE);
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


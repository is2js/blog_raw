---
toc: true
layout: post
title: TDD 6 로또(캐슁,Factory,전략)
description: 캐슁->팩토리->전략 수동로또추가->마무리

categories: [java, 우테코, 캐슁, Factory, 전략, TDD, 강의]
image: "images/posts/wootech.png"
---


### 캐슁



#### 19 싱글톤을 이용한 [단일객체 CACHE] 적용과 TDD

##### 싱글톤을 이용한 CACHE 생성 2가지 방법(정리용)

1. 싱글톤 생성 방법

    1. `static final`으로 `클래스 CACHE변수;` 선언 

        - 바로 옆에서  `= ` 할당하며 초기화 or **길어서** `static { 클래스변수 = }` **블럭(final도 가능)에서 초기화**
            - 바로 초기화하든, static블럭에서 초기화하든 **null이 아닌 상태의 캐쉬변수**
        - **이후 `정팩메`로만** `생성된 CACHE객체` or `생성된 CACHE자료구조에서 1개만 get`하여  제공
            - **이후 기본생성자는 CACHE 최초 초기화하며 채울때만 사용됨**

        ```java
        private static final Map<Integer, LottoNumber> LOTTO_NUMBER_CACHE;
        
        static {
            LOTTO_NUMBER_CACHE = IntStream.rangeClosed(LOTTO_NUMBER_MIN, LOTTO_NUMBER_MAX)
                .boxed()
                .collect(Collectors.toMap(number -> number, LottoNumber::new));
        }
        
        private final int number;
        
        public LottoNumber(final int inputNumber) {
            this.number = inputNumber;
        }
        
        public static LottoNumber from(final String inputNumber) {
            final int number = Integer.parseInt(inputNumber);
            validateLottoNumberRange(number);
            return LOTTO_NUMBER_CACHE.get(number);
        }
        ```

        

    2. `static`으로 `클래스 CACHE변수;` 선언 (**no final**)

        - **`이후 정팩메에 synchronized`달아주고**

        - **`정펙매`내부에서** 초기화 한번도 안된 **최초 `if (CACHE == null) 상태의 캐쉬변수`일 때만,** `기본생성자로 CACHE변수 초기화`
        - **`정펙매`로만**  `생성된 CACHE객체` or `생성된 CACHE자료구조에서 1개만 get`하여  제공

        ```java
        private static Map<Integer, LottoNumber> LOTTO_NUMBER_CACHE;
        private final int number;
        
        private LottoNumber(final int number) {
            checkNumberRightRange(number);
            this.number = number;
        }
        
        public synchronized static LottoNumber valueOf(final int number) {
            checkNumberRightRange(number);
            if (LOTTO_NUMBER_CACHE == null) {
                LOTTO_NUMBER_CACHE = IntStream.rangeClosed(MIN_LOTTO_NUMBER, MAX_LOTTO_NUMBER)
                    .boxed()
                    .collect(Collectors.toMap(lottoNumber -> lottoNumber, LottoNumber::new));
            }
            return LOTTO_NUMBER_CACHE.get(number);
        }
        ```

        



##### Test에서부터 빨간줄로 넘어가 -> 캐쉬 적용하기



1. **캐쉬로 받아먹으려면 `싱글톤 받아주는 정펙매(getInstance())`로만 가져와야한다.**

    - 네이밍을 그렇게 했어도 **`기본 생성자에 넣어줄 파라미터`도 받아야한다.**
        ![image-20220308230247397](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220308230247397.png)

2. 싱글톤의 **동시성 잇슈를 처리하기 위해 `synchronized`를** 접근제한자 <-> static 사이에 넣어준다.
    ![image-20220308230318968](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220308230318968.png)

    ```java
    public synchronized static void getInstance(final int value) {
    
    }
    ```





3. **앞으로 `정펙매`로만 제공되어야하므로 `도메인 검증`을 여기로 옮기자.**

    - 기존 도메인테스트가 `new 기본생성자()`로 진행되어있으니, 그냥 두고 -> 
        - **`맨 나중에 도메인검증을 정팩메코드로 + 기본생성자는 private + 검증은 삭제하도록 수정하자.`**.

    ```java
    public LottoNumber(final int value) {
        // 지금은 임시로 살려둔다. 테스트를 나중에 바꿀 때 private로 변경 + 검증삭제
        validate(value);
        this.value = value;
    }
    
    public synchronized static void getInstance(final int value) {
        //2. 검증은 여기로 옮긴다. 왜냐면, 앞으로 CACHE정팩메로만 제공될 것이기 때문.
        //   - 정적안에 들어가므로 검증메서드가 static으로 바뀐다. 
        validate(value);
    }
    ```

    

4. `CACHE`의 **static변수 선언을 직접 안하고 빨간줄생성** 하기 위해, 

    - 빨간줄로 `if ( == null )`부터 `static 초기화` 를 작성해줬다. 
        - **`기본생성자`는 이제 여기 캐쉬생성에서만 이용되어야한다.**
            - 원래는 private으로 바꾸고, TEST다 처내야함.
    - **static 메서드 내 자동 생성시 constant = static final이므로 그냥 `field -> static`필드로 생성하자.**

    ![image-20220308232128686](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220308232128686.png)
    ![image-20220308232148140](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220308232148140.png)
    ![image-20220308232253457](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220308232253457.png)
    ![image-20220308232419786](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220308232419786.png)

    ```java
    public class LottoNumber {
    
        private static final int MIN = 1;
        private static final int MAX = 45;
        // 자동 생성한 CACHE field (static)
        private static Map<Integer, LottoNumber> CACHE;
    
        private final int value;
    
        public synchronized static void getInstance(final int value) {
            validate(value);
            //3. 캐쉬로 제공될 static 변수;  만 선언하고 -> if null일 때 = 최초에만 초기화 시켜준다.
            // - Map으로 만들거면
            // - 중간에 .map( number -> )없이 맨 나중에 toMap으로 만들면 된다.
            // - intStream은 Integer가 아니다. toMap에 들어가려면 .boxed() 먼저 해주고 들어가야한다.
            // - toMap의 2번쨰 인자는 method reference만 사용 가능하다. new LottoNumber(Number하니 안되더라)
            if (CACHE == null) {
                CACHE = IntStream.rangeClosed(MIN, MAX)
                    //.map( number -> )
                    .boxed()
                    .collect(Collectors.toMap(number -> number, LottoNumber::new));
            }
        }
    ```

    

    

    

5. **`정팩메`에서 `캐쉬객체` or `캐쉬 자료구조.get()`한 것을 응답해준다.**

    ```java
    public synchronized static LottoNumber getInstance(final int value) {
        validate(value);
        if (CACHE == null) {
            CACHE = IntStream.rangeClosed(MIN, MAX)
                .boxed()
                .collect(Collectors.toMap(number -> number, LottoNumber::new));
        }
    
        // 제공은 이제 기본생성자가 아닌, CACHE에서 제공해준다.
        return CACHE.get(value);
    }
    ```

    



##### 검증이 넘어간 캐쉬 정팩메로 생성 테스트로 마무리

```java
@Test
void cache_create() {
    assertDoesNotThrow(() -> LottoNumber.getInstance(6));
}
```







### 갯수만큼 `일급<List> 생성` -> `메인로직이 도메인 생성`이라 static `일급Factory `를 거치도록  기능 분리

- static Service는 **재료를 받아 메인로직의 흐름을 호출 및 처리한다.**
    - **일급생성은 일급 생성자에서 하면 되지만, `일급List 생성 with Money만큼`은 단순히 한번 더 포장하는 것보다 복잡하다. **
        - **도메인 생성 로직을 서비스가 아닌`도메인Factory.메서드()`를 거친 뒤 ->  `그 응답값(일급의 리스트)`을  필요에 따라 포장**하던지 해야한다.
    - **`도메인 생성`의 역할이므로 `service로직`이 아니라 `도메인 로직`쪽이라 서비스는 호출만 해준다.**
- 앞으로 **생성의 방법이 여러갈래 (Manual/Auto)로 외부에서 주입될 가능성이 높아도 `도메인Factory`에서 외부 생성타입 주입을 받아주는 역할**을 해준다.





#### 20 Service에서 복잡한 도메인 생성( `갯수만큼 일급List`  생성)을 호출한다면, service에서 호출해주는  정적Static한 `도메인Facotry`가  도메인로직으로서 생성을 담당한다.

##### Service에서 생성로직을 호출하되, 메서드는 Factory가 가져간다. 가장 정제된 원시Type을 인자로 받는다.

- 사정상 여기선 정제된input으로 **`new LottoMonety(3000)`의 포장도메인 대신 `장제된 원시값`인 `3000`(원)을 인자로** 넣어준다. 

    - **원래는 new LottoMoney(도메인)을 넣어줘야하는데, `도메인 클래스 포장은 나중`에 하고 `정제된 input을 가정하고 원시값으로 호출`해서 점점 포장해나가므로 **
        - **`가장 원시값을`  [포장도메인VO 입력  -> 도메인.getter를 내부에 호출하는] 대신  `바로 쓸 수 있는 원시값을 파라미터로 일단` 제공해서 생성한다.**

    ![image-20220309202144837](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309202144837.png)
    

    ```java
    @Test
    void issue_lotto_by_money() {
        LottoService.issueByMoney(3000);
    }
    ```



##### 실제 Serivce내 `도메인 생성관련 모든 로직`은 `도메인내부or도메인Factory에 일임`시킨다. 



1. Service내에서 `class`부터 생성해야한다면

    1. class -> classTest -> **`classTest에서 메서드 빨간줄 생성` -> 메서드생성**

    ![image-20220309202906763](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309202906763.png)

    ```java
    public static void issueByMoney(final int money) {
        // input으로 money -> money에게 횟수로 치면 몇번이냐 물어볼 예정임.
        final int count = money / 1000;
    
        LottoFactory.issueByCount(count);
    }
    ```

    

##### 메서드 생성이 [컨셉과 파라미터가 잡힌 경우]는 프로덕션 빨간줄로 생성하되 `ClassTest 빨간줄`까지 가서 `메서드 생성 완료`하기!

- **메서드 생성을 프로덕션**에서 해야할 경우, **컨셉과 파라미터가 잡힌 경우, Test에서 메서드 생성하기가 쉽지 않다.**

    - 프로덕션에서 `메서드의 컨셉과 파라미터가 잡힌 경우` -> **`프로덕션 빨간줄로 메서드 생성`하되 `ClassTest->해당 메서드호출`까지 작성 완료한다.** 

        ![image-20220309203014315](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309203014315.png)

    - **`클래스 생성후 바로 ClassTest`생성 -> `생성 원하는 메서드 호출까지 완료`한 뒤 로직 작성할 것**

        ![image-20220309203222763](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309203222763.png)
        ![image-20220309204021371](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309204021371.png)

        





##### [갯수(raw숫자)만큼 반복로직]은 VO로 바뀔 것 대비 [틀을 잡아 while로 돌리기]

- **차후 미래에** VO로 포장시

    ```java
    public static void issueLottoByCount(final int count) {
        Count count = new Count(number);
    
        while (count.isNotFinished()) {
            count = count.decrease();
            // 반복되는 단일로직 issueLotto
    
        }
    
    }
    ```

- 현재 

    - VO count를 0까지 허용하면서, 깍아내려가다가, 0일 때, 튕겨나가게 할 것이다. 
    - 그전까진 내부에서 1개씩 깍으면서 반복하기

    ```java
    public static void issueLottoByCount(int count) {
        while (count != 0) {
            count-=1;
            // 반복되는 단일 로직 issueLotto
        }
    }
    ```

    ![image-20220309204339531](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309204339531.png)
    ![image-20220309204427219](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309204427219.png)



##### 내부에서 정의될,  [1개당 도메인 생성]로직도 도메인Factory내 메서드로 정의

1. `프로덕션내 메서드 정의` 해야한다면, **`빨간줄 생성후 ClassTest에서 메서드 호출까지 완료`해라고 했다.**

    ![image-20220309204551443](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309204551443.png)
    ![image-20220309204622940](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309204622940.png)



##### 프로덕션빨간줄 생성이라면 -> private이라도 public수정후 `Test`Generate로 ClassTest 생성 -> 메서드호출까지 완료 시키기

1. 메서드의 접근제한자를 private -> `public`으로 수정

    - 나중에 다 public 메서드들 조사하면 될 듯 -> 테스트에서만 사용시 삭제or Private화 시키면 될 듯

    ![image-20220309204855596](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309204855596.png)
    ![image-20220309204953373](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309204953373.png)
    ![image-20220309205001262](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309205001262.png)

2. private -> public 수정후에는 Test메서드로 자동생성 됨.
    ![image-20220309205032341](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309205032341.png)

3. 생성된 테메에서 **메서드 호출까지 완료**하자

    ![image-20220309205133875](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309205133875.png)


    ![image-20220309205149786](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309205149786.png)





#### 21 CACHE된 단일 객체는 -> Static한 [도메인Factory]의 Static {} 블럭에서 미리 뽑아놓을 수 있다.

##### 재료가 되는 도메인은 의존/갖다써도/상수는 public으로 바꿔써도 된다.

![image-20220309210020472](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309210020472.png)

```java
public class LottoFactory {

    static {
        //1. Factory는 재료도메인을 의존해서 사용해도 된다.
        // -> 재료 도메인내 상수를 public으로 바꿔서 Facotry에서 같이 쓰자.

        //2. 캐쉬된 것들을 static 블록에 다 모아놓는다. (랜덤구현은 [메서드 호출시 shffle + subList]
        IntStream.rangeClosed(LottoNumber.MIN, LottoNumber.MAX)
            .mapToObj(it -> LottoNumber.getInstance(it))
            .collect(Collectors.toList());
    }
```



##### suffle과 subList를 통한 랜덤구현을 위해 []`미리`CACHE된 재료만 다 `static{}블럭`]에서 준비한다.

1. 준비물들을 미리 뽑아놓고 -> **`상수 추출`을 한다.**
    ![image-20220309210612366](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309210612366.png)
    ![image-20220309210617465](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309210617465.png)

    

2. `split  선언 <-> 초기화`시키면 알아서 **`static블럭`에서 초기화시켜 미리 준비**되도록 한다.
    ![image-20220309210740340](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309210740340.png)

    ![image-20220309210648772](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309210648772.png)

    ```java
    public class LottoFactory {
    
    
        private static final List<LottoNumber> LOTTO_NUMBERS;
    
        static {
            LOTTO_NUMBERS = IntStream.rangeClosed(LottoNumber.MIN, LottoNumber.MAX)
                .mapToObj(it -> LottoNumber.getInstance(it))
                .collect(Collectors.toList());
        }
        
    ```





#### 21 정해진 갯수에서 일부 랜덤이다? -> 정해진 갯수 셔플 + subList(or .limit)으로 랜덤을 구현



1. CACHE(static 싱글톤)을 이용한 **static {}블럭에 준비된 랜덤준비물들을** `랜덤이 필요한곳`에서 `shuffle + subList or .limit`으로 구현하기

    ```java
    public static Lotto issueLotto() {
        //3. [랜덤한 6개 뽑기] 구현을 [미리 준비된 cache 들] -> shuffle && 일부뽑기로 구현한다.
        Collections.shuffle(LOTTO_NUMBERS);
        return new Lotto(LOTTO_NUMBERS.stream()
                         .limit(6)
                         .sorted()
                         .collect(Collectors.toList()));
    }
    ```







##### random한 일급 테스트 ->  getter후 size + 제한된 범위(단일객체의 경계값) 검사

```java
@Test
void issueLotto_count() {
    final Lotto lotto = LottoFactory.issueLotto();

    assertThat(lotto.getValue().size()).isEqualTo(6);
}

@Test
void issueLotto_range() {
    final Lotto lotto = LottoFactory.issueLotto();
    final ListIterator<LottoNumber> lottoNumberIterator = lotto.getValue().listIterator();

    while (lottoNumberIterator.hasNext()) {
        assertThat(lottoNumberIterator.next())
            .isGreaterThanOrEqualTo(LottoNumber.getInstance(1))
            .isLessThanOrEqualTo(LottoNumber.getInstance(45));
    }
}
```





#### 22 다시 [횟수만큼 반복되는 도메인생성] 메서드 처리

- 단일 생성의 테스트를 마쳤다.

    ```java
    public static void issueLottoByCount(int count) {
        while (count != 0) {
            count -= 1;
            LottoFactory.issueLotto();
        }
    }
    ```

##### 2번 이상의 add라면, 지역변수 가변 new ArrayList에 add해서 반환

- count만큼 반복해서 로또 발급후 add해서 반환해야한다.

    ```java
    public static ArrayList<Lotto> issueLottoByCount(int count) {
        final ArrayList<Lotto> lottos = new ArrayList<>();
    
        while (count != 0) {
            count -= 1;
            lottos.add(LottoFactory.issueLotto());
        }
    
        return lottos;
    }
    ```

    

- 테스트에서, 랜덤한 3개 로또 발급에 대해 갯수정도만 테스트한다.

    - 단일 생성 테스트보다 더 밑으로 테스트를 옮겨주자.

    ```java
    @Test
    void issue_by_count() {
        LottoFactory.issueLottoByCount(3);
    }
    ```

    ```java
    @Test
    void issue_by_count() {
        final List<Lotto> lottos = LottoFactory.issueLottoByCount(3);
    
        assertThat(lottos.size()).isEqualTo(3);
    }
    ```

    

#### 23 다시 Service로 돌아와 응답값 만들고 -> 메서드 퉁 테스트

```java
public static List<Lotto> issueByMoney(final int money) {
    // input으로 money -> money에게 횟수로 치면 몇번이냐 물어볼 예정임.
    final int count = money / 1000;

    return LottoFactory.issueLottoByCount(count);
    }
```

```java
@Test
void issue_lotto_by_money() {
    final List<Lotto> lottos = LottoService.issueByMoney(3000);

    assertThat(lottos.size()).isEqualTo(3000 / 1000);
}
```



### 서비스의 메인로직 진행 중 `추가 로직(비교&검증)이 발생`하는데, `도메인의 영역`이라면? -> 대상들을 `포장에 포장이라도 새로운 도메인`으로

#### 24  Service메서드 [파라미터들의 검증로직에 의한 개별 포장] 끝났는데, 또 [추가 검증 로직]이 서비스에  늘날려고 한다?

##### 포장된 `일급과 단일`의 검증인데도 또 `포장`?

1. **당첨로또와 보너스볼 번호는 `중복되면 안되며`므로 `추가 검증 로직이 필요`하고, 항상 붙어다닌다 -> `2개 상태값을 가진 포장체` **
    - Lotto인데 굳이 포장해야하나? -> 추가 검증이 필요하므로 **`일급vs단일의 검증`인데도 `2개 인변가지는 도메인으로 새로 포장`** 해야한다. **안그러면 서비스 ( or 컨트롤러)에 로직이 늘어난다.**
        ![image-20220309215710414](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309215710414.png)







##### 포장하기 전, [ service 테매 복붙]하고 case반영한 테메이름 바꾸고 -> 메서드 추가해주기

1. 테메 복붙해서 case반영해서 이름 바꿔주기
    ![image-20220309220236299](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309220236299.png)



2. 포장해서 만들어나간다.
    ![image-20220309220329113](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309220329113.png)



3. 포장 도메인 만들어놓고, 생성자만 처리해준다.

    - 일단 하나는 `value` + 하는 구체적인 이름으로 적었다.

    ```java
    public class WinningLotto {
        private final Lotto value;
        private final LottoNumber bonusNumber;
    
        public WinningLotto(final Lotto value, final LottoNumber bonusNumber) {
            this.value = value;
            this.bonusNumber = bonusNumber;
        }
    }
    ```





##### 새롭게 포장된 파라미터를 받는 (차후 새 기준이 될)메소드도 추가 -> 가장 위로 올린다.

- 메서드 이름은 같고... 파라미터만 다른게 쌓여나간다.

    - 이놈이 새로운

    ![image-20220309220440359](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309220440359.png)





- 새 기준이 될 메서드를 가장 위로 올려보자.

    ![image-20220309220541237](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309220541237.png)





##### 직전 기준 메소드의 내용을 복붙 ->  포장된 파라미터에 맞추어 빨간줄을 없앤다. (주로 getter로 임시처리)

1. 직전의 파라미터명과 비교해서 맞춰준다.

![image-20220309220645140](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309220645140.png)



2. 포장전 객체앞에다가 **새롭게 포장.getter()을 통해 에러를 없앨 수 있다.**

    ![image-20220309221227872](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309221227872.png)
    ![image-20220309221332836](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309221332836.png)









##### 포장 + 포장파라미터메서드 생성 -> 새 기준 메서드로 지정하기- > `전 메소드는 전 파라미터 -> 포장후 호출하도록 변경`

![image-20220309221614169](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309221614169.png)



![image-20220309221631428](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220309221631428.png)


```java
public class LottoService {

    public static Rank match(final Lotto userLotto,
                             final WinningLotto winningLotto) {
        final int matchCount = userLotto.match(winningLotto.getValue());
        final boolean matchBonus = userLotto.contains(winningLotto.getBonusNumber());

        return Rank.of(matchCount, matchBonus);
    }

    public static Rank match(final Lotto userLotto,
                             final Lotto winningLotto,
                             final LottoNumber bonusNumber) {

        return match(userLotto,
                     new WinningLotto(winningLotto, bonusNumber));
    }
```







##### 포장에 원하던 [생성자에 검증 로직 추가]후 테스트하기

```java
public WinningLotto(final Lotto value, final LottoNumber bonusNumber) {
    if (value.contains(bonusNumber)) {
        throw new IllegalArgumentException("당첨 번호와 보너스볼은 중복될 수 없습니다.");
    }
    this.value = value;
    this.bonusNumber = bonusNumber;
}
```



- 서비스에서 



##### 서비스로 퉁 치던 테스트를 [도메인Test] 생성시 [경계값Test]하기

1. WinningLottoTest 만들어서 경계값 테스트하기

    ```java
    @ParameterizedTest
    @CsvSource({"1", "2", "3", "4", "5", "6"})
    void duplicate(final int bonusNumber) {
        assertThatThrownBy(() -> new WinningLotto(
            new Lotto(1, 2, 3, 4, 5, 6),
            new LottoNumber(bonusNumber)))
            .isInstanceOf(IllegalArgumentException.class)
            .hasMessageContaining("중복");
    }
    
    @ParameterizedTest
    @CsvSource({"7", "8", "45"})
    void valid(final int bonusNumber) {
        assertDoesNotThrow(() -> new WinningLotto(
            new Lotto(1, 2, 3, 4, 5, 6),
            new LottoNumber(bonusNumber)));
    }
    ```







##### 새 기준 메서드에 `임시처리(포장.getValue) 부분` 메세지로 보내기 (과정)

- 기존 임시처리

    ```java
    public static Rank match(final Lotto userLotto,
                             final WinningLotto winningLotto) {
        final int matchCount = userLotto.match(winningLotto.getValue());
        final boolean matchBonus = userLotto.contains(winningLotto.getBonusNumber());
    
        return Rank.of(matchCount, matchBonus);
    }
    ```



1. `getter()를 제거`해서 빨간줄을 낸다.

    ![image-20220310113434321](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310113434321.png)

2. **`기존 사용처`에 대해 `바로 변경이 아니라`  `메서드 생성  -> 내부 내용 복사`를 통해 백업한 체로 진행한다.**

    - 이 경우, 새로운 메서드를 정의해주고, **기존 메서드 바로 밑으로 가져와서 비교하며 처리한다.**


    ![image-20220310113828004](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310113828004.png)
    
    ![image-20220310113838224](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310113838224.png)
    ![image-20220310113950666](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310113950666.png)



3. **새로운 기준을 만들어 나가며, 완성대면 완전히 대체한다.**
    ![image-20220310114108485](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310114108485.png)

    ```java
    //public int match(final Lotto other) {
    //    return (int) other.value.stream()
    //        .filter(this::contains)
    //        .count();
    //}
    
    public int match(final WinningLotto winningLotto) {
        return (int) winningLotto.getValue().value.stream()
            .filter(this::contains)
            .count();
    }
    ```





4. 나머지도

    ```java
    //public boolean contains(final LottoNumber number) {
    //    return value.contains(number);
    //}
    
    public boolean contains(final WinningLotto winningLotto) {
        return value.contains(winningLotto.getBonusNumber());
    }
    ```

    



##### 새 기준 메서드로 바꿨음에도 [기존메서드]가 다른데서 사용중인지 보고 가서 처리한다.

![image-20220310114514409](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310114514409.png)
![image-20220310114519868](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310114519868.png)

- 못바꾸는 부분이 있다면 그대로 둔다.
    ![image-20220310114618954](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310114618954.png)
    ![image-20220310114635551](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310114635551.png)








## `단일 결과의 메인로직(도1 vs 도2)`을 여러번 돌리며 `[결과값을 모으는 자료구조]도 포장`하는 로직

- **`match()`를 내부에서 여러번 호출하여 발생**하는 것들을 **`map`에 모아서 `controller 응답`**한다.

- 결과값 map에 대한 **`계산로직`도 포함될 예정**이므로 **`생성될 자료구조(map)을 생성자에서 생성후 -> 변수 포장`하도록 class로 뺀다.**

    

#### 25 `단일결과by메인로직(도메인1 vs 도메인2)`을 `바깥에서 여러번 호출`해서 발생하는 `결과값을 저장할 새로운 자료구조`가 나온다. -> `[결과값 저장 로직]을 생성자`로 넘겨 ->  `[최종 결과값 자료구조를 시작 상태값]`으로 가지면서 + `관련로직을 메서드`로 가지는 도메인3(Result도메인) 만들기



#####  `단일결과 로직(도메인1 vs 도메인2)`인 `메인 메서드의 파라미터 포장까지 완료`된 상태여야만 -> 바깥에서 여러번 돌릴 때, 포장된 것을 받을 수 있음

- 내부 단일결과를 유발하는 메인로직 **메서드의 파라미터가 이미 포장**이 끝났다. -> **바깥에서 `개별 결과값을 모으는 메서드`도 포장된 값을 받는다.**

    - **단일 결과**(Enum)을 응답하는 메서드는 **파라미터를 포장된 것**을 받는다.
        ![image-20220310104707889](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310104707889.png)
    - **바깥에서** 결과값들을 모을 **메서드 -> 포장된 것을 받아야 편하다.**
        ![image-20220310110208060](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310110208060.png)

    ```java
    @Test
    void get_result() {
    
        //1. 메인로직(match)를 여러번 호출해서 결과값을 받을 Service.메서드
        // -> 메인로직에 필요한 파라미터(Lotto여러개 + WinningLotto)는 넘겨줘야한다.
        final List<Lotto> lottos = List.of(
            new Lotto(1, 2, 3, 4, 5, 6),
            new Lotto(1, 2, 3, 4, 5, 7),
            new Lotto(1, 2, 3, 4, 7, 8)
        );
    
        //0. 내부 메인로직(도1vs도2)의 파라미터 포장까지 완료되었을 때,
        // -> 바깥에서 복수의 포장을 받아, 여러번 돌려 -> 결과값을 저장할 수 있다.
        LottoService.getResult(lottos,
                               new WinningLotto(
                                   new Lotto(1, 2, 3, 4, 5, 7),
                                   new LottoNumber(6)));
    
    }
    ```

    

##### `일단은 Service.Method내부에서 [메인로직을 돌리기]를 정의`를 하다가 -> 필요에 의해 옮기고 포장하자.

1. 현재 Service.getResult( , )에서 자료를 받아서, `현재는 서비스에서 호출하고 있던 메인로직(도1vs도2 비교)를 호출`도 한다.

    ```java
    public static void getResult(final List<Lotto> lottos,
                                 final WinningLotto winningLotto) {
    
        //2. 일단 service내부에 도메인 계산로직을 작성한다.
        final List<Rank> results = lottos.stream()
            // 3. Service를 통해 돌리다보니, 1:1 단일결과(메인로직)을 호출할 수 밖에 없는데
            // -> 돌리는 이것 역시 서비스에서 호출하는 메서드였다.
            // -> 만약, 결과값 모으는 곳으로 간다면 바뀌어야할 것 같다.
            .map(lotto -> match(lotto, winningLotto))
            .collect(Collectors.toList());
    }
    
    ```

    



#### 26 `[결과값을 모은 List를 카운팅하는 새로운 응답(자료구조)]` 만들기

1. 단일 결과값들을 돌아가며 **호출결과값 List를 모은다.**

![image-20220310122951652](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310122951652.png)





#### Enum List ->  EnumMap에 카운팅하기(정리용)

- **`List<객체>`의 갯수를     객체를 key로 하는 hashMap `Map<객체, Integer>`** 에 세야한다.



##### 빈 Map 선언 및 초기화



1. `객체`의 `Count`을 원한다면  **<`객체key`, `Integer`>로 Map을 선언한다. ** (cf) 원시적으로는 직접 0으로 초기화 한다.)

    - **EnumMap이라면 `( )`생성자에 안에 `Enum클래스.class(클래스 자체)`를 지정해준다.**

    ```java
    final EnumMap<Rank, Integer> result = new EnumMap<>(Rank.class);
    ```

    - 참고)

        - **(원시적) `모든 객체key들 stream화`해서 돌면서 -> forEach -> map.put(객체key, 0)으로 초기화해준다.**

            ```java
            private final EnumMap<Rank, Integer> result = new EnumMap<>(Rank.class);
            
            {
                Arrays.stream(Rank.values()).forEach(rank -> result.put(rank, 0));
            }
            ```

        

    - **EnumMap는 넣고난 뒤, 자동정렬되어있지만**

        - **일반map넣고 난 뒤, key순으로 정렬되어야하면(순서가 중요하면)**  hashMap대신 ->  linkedHashMap을 사용한다.

        ```java
        private final LinkedHashMap<Rank, Integer> value = new LinkedHashMap<>();
        ```





##### `일급vs단일 로직`을 먼저 수행한 -> `응답결과List`를 카운팅하는 3가지 방법

2. **이제 갯수를 셀  `결과값List(Enum List or 객체 List)`**들을 돌면서 카운팅한다.

    1. **결과List(객체List)를 먼저 계산** -> 돌면서 -> 

        - 이미 0으로 초기화해놓고 넣어놨어야한다.
        - map.put( `객체Key`,   `map.get( 객체key )`의 value `+ 1` )

        ```java
        {
            Arrays.stream(Rank.values()).forEach(rank -> result.put(rank, 0));
        }
        ```

        ```java
        result.put(rankPrize, result.getOrDefault(rankPrize, 0) + 1);
        ```

        

        

    2. **결과List(객체List)를 먼저 계산** -> 돌면서 -> 

        - **미리 0 초기화 없이 `알아서 직전value(없으면 0부터) 누적`해주는 ->  `map.merge( 객체key, 누적해넣을값, Integer.sum()으로 기존value와 누적방법 Bifunction )`**


        ```java
        result.merge(rank, 1, (before, after) -> Integer.sum(before, after))
        ```
    
        ```java
        final List<Rank> results = lottos.stream()
            .map(lotto -> match(lotto, winningLotto))
            .collect(Collectors.toList());
        
        final EnumMap<Rank, Integer> result = new EnumMap<>(Rank.class);
        
        results
            .forEach(rank -> result.merge(rank, 1, (before, after) -> Integer.sum(before, after)));
        ```
    
        - 결과값을 찍어보니 merge로 카운팅이 1개씩 잘 된다.
            ![image-20220310150556475](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310150556475.png)





##### EnumMap은 아니지만, 일급vs단일 --stream--> 단일vs단일 `결과값(객체key)을 뽑아낼 로직` + `카운팅`을 동시에 해주는 `일급.stream().collect(Collectors.groupingBy(` 객체key를 뽑아낼 로직 , summingInt( value -> 1)` )`

1. 결과값List를 뽑는 로직 조차 `일급.stream -> 단일vs단일로 객체Key뽑기`로직이 내부에 포함됨

    - 결과값List를 미리 뽑지 않는다. -> `Collectors.groupingBy( , )`의 **첫번째 인자에 ** 

    ```java
    // (1)결과값 List를 미리 뽑는 과정 -> 생략됨.
    //final List<Rank> results = lottos.stream()
    //    .map(lotto -> match(lotto, winningLotto))
    //    .collect(Collectors.toList());
    
    // (2) map을 미리 선언해두기 -> 생략됨
    //final EnumMap<Rank, Integer> result = new EnumMap<>(Rank.class);
    
    // (3) 결과값List를 돌면서 -> merge로 1씩 카운팅 -> 생략됨
    //results.stream()
    //    .forEach(rank -> result.merge(rank, 1, (before, after) -> Integer.sum(before, after)));
    
    // 일급을 돌면서 -> groupingBy(단일vs단일의 [결과값 = 객체key]를 뽑는 로직 , 1씩 누적합하는 로직)
    return lottos.stream()
        .collect(Collectors.groupingBy(
            lotto -> match(lotto, winningLotto),
            Collectors.summingInt(value -> 1)));
    
    ```

    





#### 27 결과값을 모은 List를 카운팅하는 `새로운 응답(자료구조)을 포장`하는데  그 `생성 과정을 생성자에 넣어서 시작부터 상태값으로 가지자`

- 현재 상황

    ```java
    public static Map<Rank, Integer> getResult(final List<Lotto> lottos,
                                               final WinningLotto winningLotto) {
        return lottos.stream()
            .collect(Collectors.groupingBy(
                lotto -> match(lotto, winningLotto),
                Collectors.summingInt(value -> 1)));
    }
    ```

    

##### 응답값 포장하기

1. returntype 빨간줄 수정 -> 생성
    ![image-20220310153018402](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310153018402.png)





##### 결과산출 로직은 포장결과값 생성자로 가져가되,  `new 포장결과( ) `로 생성되도록 `재료추출`은 있어야함. 

![image-20220310153445775](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310153445775.png)

##### 대박) `메서드추출`로 `new 포장( , ) 생성자` or  `결과class 정펙매`  생성자 호출과 동시에 `재료 추출`하기

![image-20220310153620211](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310153620211.png)
![image-20220310153706044](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310153706044.png)
![image-20220310153721980](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310153721980.png)





![image-20220310153741892](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310153741892.png)
![image-20220310153808598](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310153808598.png)



- 밖으로 나가서 `아무거나 타자치면 invalid해도 완성`된다.

    ![image-20220310154107874](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310154107874.png)





2. `새로운 응답값` 을 **`시작부터 상태` 관리되도록 `생성자에서 처리해주고 초기화`하도록 잘라내서 가져가기**

    - 기존에 추출된 `새로운 응답값 자료구조  추출 로직`을 -> 결과값 포장클래스의 생성자로 가져간다.

        ![image-20220310154211758](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310154211758.png)

    - 가져와서 파라미터 부분 + 내용부분만 잘 가져다 생성자로 만든다.

        ![image-20220310154327007](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310154327007.png)

        

        

    - **서비스 정의된 에서 `단일vs단일 메인 결과값`match 로직을 서비스에서 ->  `결과 도메인인 여기로 가져오자`**

        ![image-20220310154421973](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310154421973.png)

        ![image-20220310154615494](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310154615494.png)

        ![image-20220310154719594](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310154719594.png)

    - 이제 생성자에서 뽑아진 result를 상태값으로 가지기

        ![image-20220310154834798](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310154834798.png)

        ![image-20220310154846864](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310154846864.png)

        

        ```java
        public class LottoResult {
        
            private final Map<Rank, Integer> value;
        
            public LottoResult(final List<Lotto> lottos, final WinningLotto winningLotto) {
                final Map<Rank, Integer> value = lottos.stream()
                    .collect(Collectors.groupingBy(
                        lotto -> match(lotto, winningLotto),
                        Collectors.summingInt(count -> 1)));
                this.value = value;
            }
        
            public static Rank match(final Lotto userLotto,
                                     final WinningLotto winningLotto) {
                final int matchCount = userLotto.match(winningLotto);
                final boolean matchBonus = userLotto.contains(winningLotto);
        
                return Rank.of(matchCount, matchBonus);
            }
        ```

        



##### 새 결과값 포장이 끝났으면, Serivce 메서드 테스트 다시 해보기

```java
@Test
void get_result() {

//1. 메인로직(match)를 여러번 호출해서 결과값을 받을 Service.메서드
// -> 메인로직에 필요한 파라미터(Lotto여러개 + WinningLotto)는 넘겨줘야한다.
final List<Lotto> lottos = List.of(
new Lotto(1, 2, 3, 4, 5, 6),
new Lotto(1, 2, 3, 4, 5, 7),
new Lotto(1, 2, 3, 4, 7, 8),
new Lotto(1, 2, 3, 4, 7, 8),
new Lotto(1, 2, 3, 4, 7, 8)
);

final LottoResult result = LottoService.getResult(lottos,
new WinningLotto(
new Lotto(1, 2, 3, 4, 5, 7),
new LottoNumber(6)));

System.out.println(result);
System.out.println();
}
```

![image-20220310155100157](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310155100157.png)





#### 28 결과값 포장Result class의 자체 테스트



##### [새로운 응답값 생성로직]이 담긴 [생성자 자체 테스트] with [view에서 쓰일 수도 있는? 삭제될? Count getter(객체key)개발]

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

    - 할수 있다면, debug 활용해서 내부값 까보면서 같이하면 될 듯.
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

    



#### 29 새응답 결과값(map)에 계산 로직 더하기

##### Service에서 controller로 반환되므로 자체 ResultTest에서 시작

1. **ServiceTest가 아니라 `ResultTest` 추가 로직 메서드 개발 시작**
    ![image-20220310165832532](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310165832532.png)



##### countMap 처리 deafult : value.entrySet()  .stream() .map()

2. **CountMap을 돌 때, 기본적인 으로 작성해서 돌아보자**

    ```java
    public void getPrize() {
        value.entrySet()
            .stream()
            .map()
    }
    ```

##### map( -> ) 로직에서 getter등장으로 보내야한다면 -> 옆에 값도 같이 보내자.  `enum.getter`  \* `count(it.getValue())` -> enum.메세제( it.getValue() )

3. map안에서 `enum.getXXX()  * count숫자` 를  계산해야하는데, getter대신 메세지보낼 때, 같이 보내버리자.

    ![image-20220310171121105](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171121105.png)
    ![image-20220310171142345](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171142345.png)



##### 없던 Enum필드도 `this.필드명`을 바로써서 빨간줄로 바로 생성



4. 아직 상금필드를  enum에 정의 안해뒀다. `this.필드`명을 기입해서 빨간줄로 생성해버리자.
    ![image-20220310171321759](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171321759.png)


    ![image-20220310171339698](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171339698.png)


    ![image-20220310171428501](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171428501.png)


    ![image-20220310171434398](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171434398.png)


    ![image-20220310171550145](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171550145.png)


​    
​    ![image-20220310171544606](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171544606.png)



5. `it.getKey()`의 enum으로 메세지 보내도록 작성완료 
    ![image-20220310171624587](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171624587.png)









##### 누적한다면, matToInt( ) .sum() 일까?  map().reduce( )일까?

- **map()으로 반환받은 int(`stream<Int>`)**는 `.sum()`이 안된다.
    ![image-20220310171908172](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171908172.png)

- **matToInt()로 반환받은 `IntStream`만 `.sum()`의 누적함수 적용된다.**
    ![image-20220310171937994](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171937994.png)


    ![image-20220310171833537](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310171833537.png)





##### reduce의 불편함과 초기값장점

- `mapToType` - `sum()`이 아닌 경우 -> map의 returnType에 따라 **reduce(누적)에  type에 맞는 합(누적할연산자)메서드를 직접** 넣어준다.

    ![image-20220310173353936](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310173353936.png)
    ![image-20220310173431208](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310173431208.png)

- 직접 메서드와 초기값을 지정해주지만, stream이 비었어도 에러가 안나고 defaul값이 나온다.

    ![image-20220310173859208](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310173859208.png)









##### mapToType -> .sum()의 편리함과 불편성(?-> 없다) -> 편한 것 쓰면 될듯

- mapToInt를 써서**, `.sum()`할 stream이 없을 때에도 `0`이 default**로 전달된다.

    ![image-20220310174223857](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310174223857.png)

    



#### 30 countMap에 NONE객체 존재 + orElse(NONE)가 있어야하는 이유 -> 전체 갯수`도` 셀 수 있다.

- 당첨못함 : 14명 / 1등: 1명 / 2등: 2명 -> **총 17명**의 갯수..
    - **만약 당첨 못했다고 안셌으면.. ㅠ_ㅜ 전체 로또 갯수를 셀 수 없게 된다.**



1. NONE(여기선 `OUT`)객체는 존재해야하며, 다른 것이 해당 없을 때도 반환되어야한다.

    ```java
    public enum Rank {
    
        RANK_1(2000_000_000, ((matchCount, matchBonus) -> matchCount == 6)),
        RANK_2(30_000_000, (matchCount, matchBonus) -> matchCount == 5 && matchBonus),
        RANK_3(1_500_000, (matchCount, matchBonus) -> matchCount == 5 && !matchBonus),
        RANK_4(50_000, (matchCount, matchBonus) -> matchCount == 4),
        RANK_5(5_000, (matchCount, matchBonus) -> matchCount == 3),
        OUT(0, (matchCount, matchBonus) -> matchCount < 3), // 1.
        ;
    
    //...
    
    public static Rank of(final int matchCount,
                          final boolean matchBonus) {
        return Arrays.stream(values())
            .filter(it -> it.condition.test(matchCount, matchBonus))
            .findAny()
            .orElse(OUT); // 2. 
    }
    ```

    



2. **로또 전체 갯수를, `NONE객체 포함한 COUNTMAP`을 통해 구할 수 있다.**

    - values()에 모든 로직이 counting 포함되어있따.

        ![image-20220310180349901](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220310180349901.png)

        ```java
        public long getPrize() {
            final long totalPrize = value.entrySet()
                .stream()
                .mapToLong(it -> it.getKey().getPrize(it.getValue()))
                .sum();
        
            // 전체 로또 구매수를 countMap의 NONE까지 같이 센다.
            // -> enum처럼 map도 둘다 values().stream 자동완성으로 돌 수 있다.
            final int purchasePrice = value.values().stream()
                .mapToInt(it -> it)
                .sum() * 1000;
            return totalPrize;
        }
        ```

        





3. long  / int라도 둘자 정수니 **한쪽에 (double)만 씌우면 된다.**

    ```java
    // long vs int 든, int vs int든 나누기가 들어가면 한쪽에 (double)을 씌우자.
    return totalPrize / (double) purchasePrice;
    ```

    ```java
    public double getPrize() {
        final long totalPrize = value.entrySet()
            .stream()
            .mapToLong(it -> it.getKey().getPrize(it.getValue()))
            .sum();
    
        final int purchasePrice = value.values().stream()
            .mapToInt(it -> it)
            .sum() * 1000;
    
        return totalPrize / (double) purchasePrice;
    }
    ```

    





#### 31 나눗셈(하나 double)의 Test return되는 것과 같이 구성해버리기

- `0.0`을 넣어도 되긴 하지만  당첨금(long)   `0L` / `(double)` 구입금액(int) `4000` 으로 똑같이 줘서 구성하면 된다.

```java
@Test
void name() {
    // create 테스트에 쓰던 것 그대로 활용
    final LottoResult lottoResult = new LottoResult(lottos,
                                                    new WinningLotto(
                                                        new Lotto(11, 12, 13, 14, 15, 16),
                                                        new LottoNumber(7)
                                                    ));

    final double prize = lottoResult.getPrize();

    //숫자 비교도 return되는 타입이랑 동일하게 expected주기
    // -> totalPrize / (double) purchasePrice;
    assertThat(prize).isEqualTo(0L / (double) 4000);
}
```







### 단위가 정해져 있는 것의 포장( LottoMoney )

#### (1) 단위보다 작은지 검사 -> (2) `% 단위`로 나눈 나머지 `!=0` 검사

```java
private static void validate(final int value) {
    if (value < DIVIDE_UNIT) {
        throw new LottoMoneyLessException(value);
    }
    if (value % DIVIDE_UNIT != 0) {
        throw new LottoMoneyDivideException(value);
    }
}
```





#### 32 대상의 필요성 확인후 포장 시작

- 메서드 실행시 정제된 input으로 `money`가 왔다고 가정했다. -> **근데 실제로 필요한 건 `money / 단위`로 산출되는 `횟수`**가 필요하다.

    ```java
    @Test
    void issue_lotto_by_money() {
        // new Money(3000) 예약
        final List<Lotto> lottos = LottoService.issueByMoney(3000); 
    
        // money.메세지() 보내서 횟수를 반환받아 그만큼 반복할 예정
        assertThat(lottos.size()).isEqualTo(3000 / 1000);
    }
    ```

    - count도 뽑아내야한다. count도 결국엔 포장해야할 듯.

    ```java
    public class LottoService {
    
        public static List<Lotto> issueByMoney(final int money) {
            // input으로 money -> money에게 횟수로 치면 몇번이냐 물어볼 예정임.
            final int count = money / 1000;
    
            return LottoFactory.issueLottoByCount(count);
        }
    ```

    

##### Test에서 포장 시작( 정정: Money -> LottoMoney )

1. Test에서 빨간줄로 시작하며 생성하기
    ![image-20220311231202001](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220311231202001.png)

    ```java
    public class Money {
        private final int money;
    
        public Money(final int money) {
            this.money = money;
        }
    }
    ```



##### 참고) `포장으로 파라미터 달라지는 메테(포장->자동생성->끌올-> 내용복붙후 새기준)` vs `case늘릴려고 복사해서 만드는 테메+메서드2(테매 복->case반영->메서드2복 ->메서드2수정->메서드2통과-> 메서드1대체하여 삭제)` 등..은 서로 다른 것

##### 생성한 `예비 새 기준메서드`를 기존메서드 위로 위치를 올린 뒤, 내용 복붙 -> 포장한 것으로 처리되도록 -> 기존메서드는 포장용으로 변경

2. 메서드 파라미터를 바꾸는게 아니라, `메서드2`로 생성하고 새 기준 메서드가 되도록 **포장 파라미터로 처리되도록 한다.**

    - 생성한 `예비 새 기준메서드`를 기존메서드위로 **위치를 이동**

    ![image-20220311231438444](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220311231438444.png)


    ![image-20220311231529740](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220311231529740.png)


    ![image-20220311231636794](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220311231636794.png)


​    



##### 새메서드의 내용을 포장된 것으로 바꿀 때, `기존값.getter()로 대체`해도 되지만 -> 바로  `[기존값으로 연산] -> 포장.메서드() -> 포장 내부에서 [this.값으로 연산]`하도록 처리해야한다.

3. 기존메서드의 내용을 복붙해온 뒤 -> 포장파라미터에 맞게 수정해준다. **필요시 getter**를 처리했었는데를 **연산이 바로 보이는 곳에서는 .메서드()로 처리** 해줘도 된다.
    ![image-20220311231733733](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220311231733733.png)


    ![image-20220311232026057](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220311232026057.png)


    ![image-20220311232135566](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220311232135566.png)


​    

​    

##### 기존메서드는 `내부에서 파라미터포장+ 새기준 호출만` -> 기존내용은 다 삭제 - > 회색으로 안쓰인다면 버려도 됨.

![image-20220311233535533](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220311233535533.png)

- 기존메서드가 테스트 등에서도 안쓰이니 삭제





#### 줄어드는 횟수Count VO의 포장(정리용)



#### 33 count(VO, hasNext패턴 가진 것)  포장



##### 미리 원시값도 줄어들 때 hasNext패턴을 만들어두었다.

- 내 패턴으로는 **0을 허용하면서 `더 큰 n부터 줄어들되 0이 아닐때까지, 즉, 1까지`도는 것이다.**

```java
public static List<Lotto> issueLottoByCount(int count) {
    final List<Lotto> lottos = new ArrayList<>();
    while (count != 0) {
        count -= 1;
        lottos.add(LottoFactory.issueLotto());
    }

    return lottos;
}
```





##### 메서드에서부터 파라미터로 전해지도록 Count 포장 (포장은 테매 인자에서부터~ )

- 이름은 도메인Count로 만들어야한다. **포장하는 순간, 대명사가 아니다.** 

    ![image-20220312003536372](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312003536372.png)

    

1. 포장하기

    ```java
    public class LottoCount {
        private final int value;
    
        public LottoCount(final int value) {
            this.value = value;
        }
    }
    ```

    

##### VO는 equals/hC부터 

![image-20220312003656278](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312003656278.png)





##### count VO는 0까지는 허용해주도록 검증을 만든다. (for while문에서 0까지 간 뒤 0에서 탈출시킴)



```java
public LottoCount(final int value) {
    if (value < 0) {
        throw new IllegalArgumentException("0보다 작을 수 없습니다.");
    }
    this.value = value;
}
```





##### 새기준메서드 새로 만들어, 기존메서드 위로 위치이동후, 내용복붙후, 처리



![image-20220312003800304](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312003800304.png)



![image-20220312003812864](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312003812864.png)


![image-20220312003835351](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312003835351.png)



![image-20220312003858891](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312003858891.png)





##### 값자리에는 포장.getter()를 -> 연산이 할만하면 바로  메세지를

![image-20220312003929036](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312003929036.png)

![image-20220312004010360](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312004010360.png)





![image-20220312004110748](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312004110748.png)





##### 값의 연산 -> 포장 안으로 `잘라들고가서 this.값으로 연산`

1. getter지우고 메세지보내기
    ![image-20220312004601305](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312004601305.png)
    - 만약, 지우고 빨간메서드가 남아있다면 그대로 정의해도 된다.
    - 여기선 연산`!=0`이 남아있으니, `그에 맞는 의미있는 메서드`를 지어준다. **`0`=Finish**가 **`!=` 아직 Not이냐**
        ![image-20220312004712286](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312004712286.png)
    - 남은 연산을 잘라내서 -> 메서드만들고 -> 내부에서 `this.값으로 잘라낸 연산 처리`
        ![image-20220312004815528](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312004815528.png)
        ![image-20220312004836272](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312004836272.png)
        ![image-20220312004844430](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312004844430.png)















##### 값의 재할당 -> VO에서는 `연산만 들고 들어가면서 메서드를 만들`고, `포장 = `는 그대로 둔다. ->   내부에서는`this.값으로 연산`한 뒤 `=`에 대해 `새VO로 응답`하는  [메세지]로 작성한다.

1. `-= 1`을 포장 처리하려면

    - `-1` 을 내부로 들고 들어가서 `this.값 -1`으로 연산
    - `=`은 **새 VO를 반환**

    ![image-20220312005238446](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005238446.png)


    ![image-20220312005247506](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005247506.png)





2. `=` 놓아두고, `-1`만 들고 들어가면서, `연산과 관련된 메서드명`으로 메세지를 보낸다.

    ![image-20220312005409104](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005409104.png)


    ![image-20220312005445774](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005445774.png)
    
    ![image-20220312005609930](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005609930.png)


    ![image-20220312005630589](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005630589.png)









##### 연산을 들고들어갔더라도 `바깥`에서는 `포장변수 =  `이 새 VO응답을 기다리고 있으므로 `내부연산 결과를 포장해서 응답`해준다.

![image-20220312005715018](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005715018.png)


![image-20220312005725788](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005725788.png)





- final을 풀어 재할당 가능하게 해준다.
    ![image-20220312005808639](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312005808639.png)



```java
public static List<Lotto> issueLottoByCount(LottoCount lottoCount) {
    final List<Lotto> lottos = new ArrayList<>();
    while (lottoCount.isNotFinish()) {
        lottoCount = lottoCount.decrease();
        lottos.add(LottoFactory.issueLotto());
    }

    return lottos;
}
```







##### 포장전 기존메서드가 사용중이면 찾아가서, 수정해준다.

![image-20220312010317777](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312010317777.png)



![image-20220312010330740](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312010330740.png)

- count가 사용중인데, 일단 `money.getCount()`의 반환값도 원시count으로 바꿔준다.

    ```java
    public int getCount() {
        return this.money / 1000;
    }
    ```

    ![image-20220312010436131](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312010436131.png)

    ```java
    public LottoCount getCount() {
        return new LottoCount(this.money / 1000);
    }
    ```

    ```java
    public static List<Lotto> issueByMoney(final Money money) {
        final LottoCount count = money.getCount();
    
        return LottoFactory.issueLottoByCount(count);
    }
    ```

    









##### 새 기준메서드 완성시, 포장검증을 메서드로 퉁치는 테스트(-1이하에서 에러)

![image-20220312011115940](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312011115940.png)







##### 자체 도메인Test생성후 Count VO 검사



- 0까지 허용하여  음수 생성자 에러

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



- 1개 뽑 + 감소된 것 뽑 ->  감소된 로직후 동일한지 비교

```java
@Test
void decrease() {
    final LottoCount lottoCount = new LottoCount(1);
    final LottoCount expected = new LottoCount(0); // 감소된 것 미리 뽑기

    final LottoCount actual = lottoCount.decrease();

    assertThat(actual).isEqualTo(expected);
}
```





#### 랜덤구현 -> 전략패턴으로 수동 더하기(정리용)



#### 34 [랜덤 최소단위 메서드]를 전략메서드로 바꿔 [수동]도 가능하게 하기



##### 전략부위가 만약, 메서드화 안되어있으면(ex>조건식) 메서드화 (추출 등)

- 메서드명을 `랜덤+수동다 포함하는 일반화 + 목적어 뺌(전략인페+구현전략들에 명시됨)`된 전략메서드로 바꿔주기



##### `랜덤 최소단위 메서드` 정의부 -> `호출()부` 찾기

1. 랜덤 메서드 정의부 찾기
    ![image-20220312131559611](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312131559611.png)



2. **랜덤 메서드의 호출부 찾기**
    ![image-20220312131647458](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312131647458.png)







 ##### 랜덤 메서드 호출부에서 `랜덤 메서드명을 -> 수동포함하는 일반화된 전략메서드명() + 목적어 제거`으로 변경  -> `전략인페를 상속한 new 랜덤전략구현체 객체(). 전략메서드()` 인척 빨간줄 생성해주기

1. **`일반화된 메서드명` 상태에서 -> 빨간줄로 `외부주입된 랜덤전략구현체 객체` + `전략메서드()`호출**해준다.

    - 기존상태( 메서드명을 랜덤+수동다 포함하는 **`일반화된 + 목적어빠진 전략메서드명`으로 바꿔주기**)

        ![image-20220312134713439](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312134713439.png)

        - **전략메서드에 목적어 넣지마라**

        ![image-20220312135004891](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312135004891.png)

        ```java
        public static List<Lotto> issueLottoByCount(LottoCount lottoCount) {
            final List<Lotto> lottos = new ArrayList<>();
            while (lottoCount.isNotFinish()) {
                lottoCount = lottoCount.decrease();
                lottos.add(issue());
            }
        
            return lottos;
        }
        
        public static Lotto issue() {
            Collections.shuffle(LOTTO_NUMBERS);
            return new Lotto(LOTTO_NUMBERS.stream()
                             .limit(6)
                             .sorted()
                             .collect(Collectors.toList()));
        }
        ```

        

2. **`외부주입된 new 랜덤전략객체`에서 호출한 `전략메서드()`인척 해서 빨간줄로 생성하기**

    ![image-20220312135034060](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312135034060.png)

    ```java
    public static List<Lotto> issueLottoByCount(LottoCount lottoCount) {
        final List<Lotto> lottos = new ArrayList<>();
        while (lottoCount.isNotFinish()) {
            lottoCount = lottoCount.decrease();
            lottos.add(new RandomLottoIssuer().issue());
        }
    
        return lottos;
    }
    ```

    ![image-20220312135300826](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312135300826.png)
    ![image-20220312135321651](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312135321651.png)





3. 전략메서드도 작성
    ![image-20220312135417506](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312135417506.png)

    









##### 랜덤전략객체.전략메서드()로 <- [ 기존 랜덤 메서드 ]에 있던 랜덤 로직 옮겨주기

```java
public class RandomLottoIssuer {
    static final List<LottoNumber> LOTTO_NUMBERS;

    static {
        LOTTO_NUMBERS = IntStream.rangeClosed(LottoNumber.MIN, LottoNumber.MAX)
            .mapToObj(LottoNumber::getInstance)
            .collect(Collectors.toList());
    }

    public static Lotto issue() {
        Collections.shuffle(LOTTO_NUMBERS);
        return new Lotto(LOTTO_NUMBERS.stream()
                         .limit(6)
                         .sorted()
                         .collect(Collectors.toList()));
    }
}
```







##### 외부주입 전략객체를 -> 외부파라미터로 받기 위해서 -> 실제 전략인페 정의해주기

1. 앞에서 정의한 랜덤전략객체를 보고 전략인페를 생성한다.

    - `RandomLottoIssuer` -> **`LottoIssuer`**  -> 앞으로 xxxxLottoIssuer 등 

    - **`전략인페명`으로 패키지를 만들어서 다같이 넣어주면 좋다.**
        ![image-20220312140142253](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140142253.png)

        ![image-20220312140155431](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140155431.png)
        ![image-20220312140216397](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140216397.png)

2. 전략메서드명은 다 통일이다. 목적어없이 동사만

    - `issue()`

    - **특정전략메서드의 `반환타입을 참고`해서 `추상메서드를 정의`해준다.**

        - 랜덤전략의 메서드 반환타입 보고
            ![image-20220312140307738](C:\Users\cho_desktop\AppData\Roaming\Typora\typora-user-images\image-20220312140307738.png)

        - **정의부 복붙해와서 `반환타입 + 메서드명`빼고 다 달리기**
            ![image-20220312140410033](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140410033.png)


            ![image-20220312140421064](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140421064.png)


            ![image-20220312140546308](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140546308.png)


            ```java
            public interface LottoIssuer {
                Lotto issue();
            }
            ```


​            





##### new 외부전략객체() 생성하던 곳으로 돌아가서  -> `변수/파라미터는 추상체로  외부주입 받아, 추상체.전략메서드()`하도록 한다.



1. 기존: 외부주입없이 + 구상체로 사용

    ![image-20220312140728576](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140728576.png)

    ```java
    public class LottoFactory {
    
        public static List<Lotto> issueLottoByCount(LottoCount lottoCount) {
            final List<Lotto> lottos = new ArrayList<>();
            while (lottoCount.isNotFinish()) {
                lottoCount = lottoCount.decrease();
                lottos.add(new RandomLottoIssuer().issue());
            }
    
            return lottos;
        }
    }
    
    ```





#####  호출부 중 전략객체만 선택해서 파라미터로 추출

2. **전략메서드를 제외한 전략객체만 선택해서 추출하면 된다.**
    ![image-20220312140839186](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140839186.png)
    ![image-20220312140847831](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140847831.png)

    

3. **추상체 이름과, 파라미터 Type모두 추상체로 바꿔준다.**
    ![image-20220312140923314](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140923314.png)

    ![image-20220312140931250](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312140931250.png)


    ```java
    public class LottoFactory {
    
        public static List<Lotto> issueLottoByCount(LottoCount lottoCount, final LottoIssuer lottoIssuer) {
            final List<Lotto> lottos = new ArrayList<>();
            while (lottoCount.isNotFinish()) {
                lottoCount = lottoCount.decrease();
                lottos.add(lottoIssuer.issue());
            }
            
            return lottos;
        }
    }
    ```







##### 처음에는 내부에서 특정 전략객체를 만들어썻찌만 -> 파라미터 추출하면 알아서 외부에서 주입된다

- 그 장소가 service라도 괜찮다. spring에서는 알아서 service에서 외부주입 받는다.

    ![image-20220312141339520](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312141339520.png)

    ```java
    public class LottoService {
    
        public static List<Lotto> issueByMoney(final Money money) {
            final LottoCount count = money.getCount();
    
            return LottoFactory.issueLottoByCount(count, new RandomLottoIssuer());
        }
    ```

    







##### 이제 전략인페를 impl 수동 구현만 -> TDD에서 수동메서드 완성해보자.



1. 수동 전략impl 구현체를 생성한다.
    ![image-20220312141544414](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312141544414.png)

    ![image-20220312141638620](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312141638620.png)

    

    

    

2. 관련 테스트(LottoFactoryTest)로 가서, 해당메서드를 완성해야한다.

    - test에서 `외부전략객체생성` 후 `전략메서드()`호출해도 아무도일도 안일어날 것이다.
        ![image-20220312141926117](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312141926117.png)

        ![image-20220312141931627](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312141931627.png)





3. 미리 작성된 `RandomLottoIssuer`를 띄워놓고(tabmover -> ctrl+alt+shift+D)로 참고하면서 내용을 채워나가면 된다.

    


    ![image-20220312142342869](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312142342869.png)


​    

​    

​    

​    

​    

##### 대박) 전략메서드 준비물을 [메서드() 인자]가 아닌 -> `각 전략별 [전략객체 생성자/정펙매]에서 받아 준비해두자`

- 랜덤로직을 랜덤전략객체에 옮기고 나니 **랜덤에 필요한 것들이 생성자나 변수에서 처리되고 있었다.**

    - **각 전략별 전략메서드 실행시 필요한 준비물은 `각 전략객체 생성자/정펙매에서 파라미터로 받자`**

    ![image-20220312142334323](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312142334323.png)

    ```java
    public class RandomLottoIssuer implements LottoIssuer {
        static final List<LottoNumber> LOTTO_NUMBERS;
    
        static {
            LOTTO_NUMBERS = IntStream.rangeClosed(LottoNumber.MIN, LottoNumber.MAX)
                .mapToObj(LottoNumber::getInstance)
                .collect(Collectors.toList());
        }
    
        public Lotto issue() {
            Collections.shuffle(LOTTO_NUMBERS);
            return new Lotto(LOTTO_NUMBERS.stream()
                .limit(6)
                .sorted()
                .collect(Collectors.toList()));
        }
    }
    ```

    





1. 수동로또준비물은 `수동 입력된 번호 List`일 것이다.

    ![image-20220312142852039](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312142852039.png)

    ```java
    @Test
    void manual_issue() {
        // 전략들의 준비물은 전략메서드()에서는 따로따로 못받는다. -> 전략객체 만들때 미리 준비되어있어야한다.
    
        //1. 수동로또전략의 준비물은 [1,2,3, 수동입력]이 있어야 -> issue()로 [1,2,3, 수동로또]를 발급할 수 있을 것이다.
        // -> input은 나중에 처리하고, 정제된rawInput을 생성자에서 받아주자.
    
        //final Lotto lotto = new ManualLottoIssuer().issue();
        final Lotto lotto = new ManualLottoIssuer(1, 2, 3, 4, 5, 6).issue();
    }
    ```

    - 일단 가변인자로 받도록 만들어놨으니... 저렇게 처리했음.

        

2. 전략객체 `생성시부터 준비물 -> 변수`에 받아**준비해두자**

    ```java
    public class ManualLottoIssuer implements LottoIssuer {
    
        private final Integer[] value;
    
        public ManualLottoIssuer(final Integer... value) {
            this.value = value;
        }
    
        @Override
        public Lotto issue() {
            throw new UnsupportedOperationException("ManualLottoIssuer#issue not implemented.");
        }
    }
    ```

    

3. 전략메서드에서 준비물을 사용해서 처리해주자.

    ```java
    public class ManualLottoIssuer implements LottoIssuer {
    
        private final Integer[] value;
    
        public ManualLottoIssuer(final Integer... value) {
            this.value = value;
        }
    
        @Override
        public Lotto issue() {
            //throw new UnsupportedOperationException("ManualLottoIssuer#issue not implemented.");
            //2. 전략객체가 생성시 받아둔 준비물을 가지고 전략메서드로 반환하자.
            return new Lotto(value);
        }
    }
    
    ```

    





4. 가장 기본적인 일급테스트인 size + contains만 테스트 해줬다.

    ```java
    @Test
    void manual_issue() {
        final Lotto manualLotto = new ManualLottoIssuer(1, 2, 3, 4, 5, 6).issue();
    
        assertThat(manualLotto.getValue().size()).isEqualTo(6);
    }
    ```

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

    

##### 초대박) `테스트용 람다 전략`은 전략객체 대신이 아니라 `내부 전략메서호출()될 응답`까지를 미리 주는 것 -> 테스트에서 내부랜덤결과(전략메서드 응답type)을 `외부 객체주입시 넣어줄 수 있다`



1. `전략객체를 주입받는 메서드(전략메서드 호출()부를 감싸는 메서드)`에 new 전략객체를 입력해서 

    - 내부에서 `서로다른 전략메서드()` 호출 -> `전략마다 다른 응답값`을 사용하는 것이 기본 패턴인데

    ![image-20220312151533837](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312151533837.png)

    

    ![image-20220312151549205](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220312151549205.png)





2. **람다전략은 `new전략객체` 대신 ` () -> 전략메소드의 응답(Lotto)`까지 한번에 주입한다.**

    1. 예시1: 전략객체가 들어가던 `boolean응답 전략메소드`의 전략객체 주입

        ```java
        js.move(new RandomMoveConditionStrategy());
        
        js.printPosition();
        ```

        ```java
        public void move() {
            if(isMovable()){
                this.position++;
            }
        }
        ```

        - **전략객체 대신 `람다 () -> boolean응답`까지 미리 지정해서 전략자리에 대입**

        ```java
        jk.move( () -> true);
        
        jk.printPosition();
        ```









3. 로또에 적용해보면, **2번째 인자로 전략객체를 받아 -> 그에 맞는 전략메서드()내부 호출하는 `issueLottoByCount( , )`**

    ```java
    public static List<Lotto> issueLottoByCount(LottoCount lottoCount,
                                                final LottoIssuer lottoIssuer) {
        final List<Lotto> lottos = new ArrayList<>();
        while (lottoCount.isNotFinish()) {
            lottoCount = lottoCount.decrease();
            lottos.add(lottoIssuer.issue());
        }
    
        return lottos;
    }
    ```



4. 여기에 2번째 인자에 **테스트용 람다전략으로서 `전략메서드 응답값을  포함한 람다전략`을 대입하자**

    ```java
    @Test
    void lambda() {
        final List<Lotto> lottos = LottoFactory.issueLottoByCount(
            new LottoCount(1),
            () -> new Lotto(1, 2, 3, 4, 5, 7));
        // 외부 전략객체가 들어와야하는데, 람다(내부전략메소드 호출후 응답갑까지 미리)를 대입
    
        assertDoesNotThrow(() -> lottos.get(0).contains(new LottoNumber(7)));
    }
    ```

    

##### 요약: [전략객체 ]자리에 `내부.전략메서드()호출 끝난 응답값까지 구현`하여 전달하는 람다전략







### 횟수만큼 자동 로또 생성 -> 횟수를 수동+자동 나누어서 로또 생성



#### 35 수동 로또를 input이 왔다고 치고, 나눠서 수동 + 자동 생성하기



##### 포장이 끝난 상태라면, [메서드 파라미터 추가]는  정제된 input보다는 [포장]된 것으로 추가해주자.

1. 자동로또 만들어 주는 메서드에  수동로또를 파라미터에 추가하자

    - 이 때, 정제된 input인 `List.of(1,2,3,4)`가 아니라 **포장 끝났으니 실제 포장된 `new Lotto()`를** 넣어주자.

    ```java
    @Test
    void issue_with_money_and_manual() {
        //        final List<Lotto> lottos = LottoService.issueByMoney(new Money(3000));
    
        //1. 돈만 받아서, 알아서 횟수만큼 자동로또 ->  돈 과 수동로또input을 같이 받도록 변경
        // -> 파라미터 다른 메서드를 만들고 -> 그 다음 이름변경하자. (기존 코드 돌아가야되니 복붙개념으로 가야됨)
        // -> 수동이 여러개일 수 있다 -> 만약 리스트라면, 6개숫자리스트의 리스트가 들어올 것이다.
        // -> **서비스 메서드에는 controller에서 도메인으로 이미 포장으로 변수를 받아놓고 호출되었을 것이다.
        // --> 포장작업은 이미 끝내놨으니, 파라미터를 포장으로 받아도 된다.
        // --> 서비스 메서드(메인로직)에서 이미 포장끝났다면 rawInput보다는 포장된 것을 파라미터로 던지자!
        //        LottoService.issueByMoney(new Money(3000), List.of(1,2,3,4,5,6));
        final List<Lotto> totalLotto = LottoService.issueByMoney(new Money(3000), List.of(new Lotto(1, 2, 3, 4, 5, 6)));
    
    ```

    





##### 파라미터 변경하지말고, 메서드를 새로 생성 -> 기존메서드위로 가져와서 내용복붙후 처리

```java
//2. 기존메서드 위로 올린 뒤, 내용 복붙해와서 처리해보자.
public static List<Lotto> issueByMoney(final Money money, final List<Lotto> lottos) {
    //3. 자동을 돌릴 갯수가 돈/1000이 아니라 수동로또수만큼 빼야한다.
    //final LottoCount count = money.getCount();
    final LottoCount totalCount = money.getCount();
    //4. VO 증감 메서드는 항상 응답받을 준비를 한다.
    final LottoCount autoCount = totalCount.decrease(lottos.size());

    //5. 기존에는 자동로또가 전체로또였다. 이;번엔 수동로또 + 자동로또 합쳐서 건네애햐한다.
    //        return LottoFactory.issueLottoByCount(count, new RandomLottoIssuer());

    //6. 수동로또는 파라미터로 넘어올 것이다. 자동만 갯수만큼 -> 수동은 input에 의해 controller에서 생산
    final List<Lotto> totalLottos = Stream.concat(
        lottos.stream(),
        LottoFactory.issueLottoByCount(autoCount, new RandomLottoIssuer()).stream()
    ).collect(Collectors.toList());
    return totalLottos;
}

```





##### 새 기준 메더드 완성시, 기존메서드 사용처확인해서 삭제 -> 메서드 이름 변경

```java
//포장된 로또 리스트를 받도록 수정했는데,
// 받기전 기존메서드에 빈 list를 넣어서 돌아가는지 확인되면
//-> 기존 메서드 삭제
final List<Lotto> lottos = LottoService.issueByMoney(new Money(3000), Collections.emptyList());

```

```java
//기존메서드 잘 삭제되면, 메서드 이름변경
public static List<Lotto> issueByMoneyAndManualLotto(final Money money, final List<Lotto> lottos) {
    final LottoCount totalCount = money.getCount();
    final LottoCount autoCount = totalCount.decrease(lottos.size());

    final List<Lotto> totalLottos = Stream.concat(
        lottos.stream(),
        LottoFactory.issueLottoByCount(autoCount, new RandomLottoIssuer()).stream()
    ).collect(Collectors.toList());

    return totalLottos;
}
```





##### 생성된 전체로또가 수동로또 포함하는지 테스트 하기

```java
@Test
void issue_with_money_and_manual() {
    final List<Lotto> totalLotto = LottoService.issueByMoneyAndManualLotto(new Money(3000), List.of(new Lotto(1, 2, 3, 4, 5, 6)));

    assertThat(totalLotto.size()).isEqualTo(3);

    assertThat(totalLotto).contains(new Lotto(1, 2, 3, 4, 5, 6));
}
```


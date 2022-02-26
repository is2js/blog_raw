---
toc: true
layout: post
title: TDD 3 자경(단일Dto~Service)
description: 객체->단일Dto->일급-> ... Service까지

categories: [java, 우테코, TDD, 강의]
image: "images/posts/wootech.png"
---


## Controller와 Service


### Controller는 도메인Service 의존하며, 생성자에서 같이 초기화된다.



1. **controller의 생성자에서** `inputView`와 `outputView`와 함께, 생성된 or 생성한 **`service객체`를 받아준다.**

    ```java
    public GameController(GameService gameService, InputView inputView, OutputView outputView) {
        //controller가 알고 있던 빈 service객체에
        this.gameService = gameService; // 외부에서 받음
        this.inputView = inputView;
        this.outputView = outputView; 
    }
    
    ```

2. **컨트롤러 생성시 inputView객체**를 받고

    - 받은 inputView를 생성자에서 이용하여 **`inputView.getDto() 등의 데이터`를 인자로 받아 Service를 생성**한다.
    - **한번에 input을 받을 수 있을 경우,  input에서 getDto로 Service로 다 가져온다.**
    - outputView는 controller 작동시 인자로 받아서 사용된다.

    ```java
    public GameController(InputView inputView) {
        //controller가 알고 있던 빈 service객체에
        //외부에서 받은 InputView를 이용하여 service를 생성하여 초기화
        racingGame = new RacingGame(inputView.getInitDto());
    }
    
    public void run(OutputView outputView) {
        playGame(outputView);
        endGame(outputView);
    }
    
    public static void main(String[] args) {
        GameController controller = new GameController(new InputView());
        controller.run(new OutputView());
    }
    ```

    

3. 컨트롤러 생성시 **컨트롤러 생성자에서 `service도 데이터없이 먼저 생성`**한다.

    - **service가 필요한 데이터는 `메서드의 인자`로 받아서 `service에 set`한다.** 

    ```java
    public RacingController() {
        racingService = new RacingService();
    }
    
    // 한꺼번에 input을 dto로 받는게 아니라면, 개별로 -> 메서드를 통해 input을 따로 입력받는다.
    racingController.generateRacingCars(carNameInput());
    
    
    public static void main(String[] args) {
        RacingController racingController = new  RacingController();
        racingController.generateRacingCars(carNameInput());
        int round = roundInput();
        RacingCarOutput.printRoundResultMessage();
        race(racingController, round);
        racingController.checkWinners();
    }
    ```

    

### Controller[생성]시 inputView 받 -> Service는 input데이터 받는 안받든 초기화하여 생성.  + [작동]시 결과가져 줄OuputView를 받아 전달

- inputView에서 재입력 or 나눠서 받아야하는 경우가 아니라면 `new Service( inputView.getInitDto() )` 생성시 받아 생성
- 먼저 서비스부터 생성되고 이후 순서대로 데이터를 set해야하는 경우, 먼저 서비스를 생성 `new Service()` 이후, 메서드 사용하면서 초기화





## 객체 -> Service까지 TDD



### 단일 객체 TDD

- 참고: [TDD2 자동차경주 블로](https://blog.chojaeseong.com/java/%EC%9A%B0%ED%85%8C%EC%BD%94/tdd/%EA%B0%95%EC%9D%98/2022/02/24/TDD-2-%EC%9E%90%EB%8F%99%EC%B0%A8%EA%B2%BD%EC%A3%BC_%EC%A0%95%EB%A6%AC(%EC%99%84).html)그 / [DTO 단계별 적용 + 테스트](https://blog.chojaeseong.com/java/dto/%EC%9A%B0%ED%85%8C%EC%BD%94/2022/02/22/%EC%9E%90%EB%8F%99%EC%B0%A8%EA%B2%BD%EC%A3%BC_DTO_%EB%8B%A8%EA%B3%84%EB%B3%84-%EC%A0%81%EC%9A%A9(%EC%99%84).html)

    - 인자 없는 생성자 `new Car()` + `응답하는 메서드` + `값으로 비교`부터 시작해서, 블로그 글을 보고 완성한다.
    - `when 객체` 와 `생성자로 만든 예상객체`를 비교한다.

    ```java
    @Test 		
    void car_move_1step() {
        final Car car = new Car("재성", 0);
    
        car.move(4);
    
        assertThat(car).isEqualTo(new Car("재성", 1));
    }
    
    @Test
    void car_move_2step() {
        final Car car = new Car("재성", 0);
    
        car.move(4);
        car.move(4);
    
        assertThat(car).isEqualTo(new Car("재성", 2));
    }
    
    @Test
    void car_move_stop() {
        final Car car = new Car("재성", 0);
    
        car.move(3);
    
        assertThat(car).isEqualTo(new Car("재성", 0));
    }
    ```



#### 필요시 도메인 검증 테스트



### 단일 Dto TDD

- **완성된 단일 객체 -> 꽁꽁 싸멘 일급컬렉션**으로 가기 전에, `List<단일Dto>`가 있어야 **일급컬렉션 결과조회 by `toDto()`로 가능할 것 같다.**



1. **단일객체의  `모든 인스턴스 변수(필드)`을 `원시값`으로 받는 `Dto.of( , )`로 받도록 `정팩매`부터 설계하고, `getter로 뽑는 확인`만 해주자.** 

    ```java
    final CarDto 재성 = CarDto.of("재성", 1); // Car의 모든 필드을 원시값으로 받아 만든다.
    
    assertThat(재성.getName()).isEqualTo("재성");
    ```

    ```java
    private final String name;
    private final int position;
    
    public CarDto(final String name, final int position) {
        this.name = name;
        this.position = position;
    }
    
    public static CarDto of(final String name, final int position) {
        return new CarDto(name, position);
    }
    
    public String getName() {
        return name;
    }
    
    public int getPosition() {
        return position;
    }
    
    @Override
    public boolean equals(final Object object) {
    ```

    



### 일급 TDD

- 단일객체들 생성 -> list에 넣기 -> 일급 or 서비스에 넣기 -> 단일객체 조정 -> 그대로 반영



#### 정제가상 재료로 셋업



1. input으로 들어오는 `List<String>`으로  **split 정제되어 들어온다고 가정**하고 **setUp에 세팅한다.**

    ```java
    Cars cars;
    
    @BeforeEach
    void setUp() {
        final List<String> names = Arrays.asList("재성", "재경");
        cars = new Cars(names);
    }
    ```





##### 필요시 도메인 검증 테스트

- `input에서부터 정제된 데이터(split, convert)`가 들어왔을 때, 검증하자.



#### 기능 결과 확인용 toDto() 부터 



- 일급은 쌓여져있어서 확인이 힘들다.  -> **`toDto()`로 `List<단일Dto>`로 가져와서 확인한다.**

    - 개발1: `.getCurrentCars()`로서 **`List<단일Dto>`를 가져온다.**

    ```java
    @Test
    void toDto() {
        //3-3. 일급의 기능개발 전, 확인이 될 수 있게 현재 객체들을 Dto List로 가져오는 기능
        // -> 내부에서 dto가 단일객체를 이용하도록  짤 것
        final List<CarDto> currentCars = cars.getCurrentCars();
    
        //        assertThat(currentCars.get(0)).isEqualTo(CarDto.of("재성", 0));
        // List의 검사는 contains, containsOnly로도 검사할 수있따.
        assertThat(currentCars).contains(CarDto.of("재경", 0));
    }
    ```





#### 일급.toDto()를 통한 메서드 기능 테스트



- 각종 확인 방법

    - **일급.toDto()** `contains` ( **예상객체 by Dto.of**)
    - **일급.toDto()** `containsOnly` ( **예상객체1, 2 by Dto.of**)
        - `isEqualTo` (  **Arrays.asList(예상객체1, 2 by Dto.of))**
    - **일급.toDto() .get( index)** `isEqualTo` ( **예상객체 by Dto.of **)
    - **일급.메서드() -> 객체** `isEqualTo` ( **예상객체 by Dto.of **)
    - **단일Dto.from**( 객체 1개 ) **개발** .getter()   `isEqualTo` ( 값 )

    ```java
    @Test
    void race_move_1step() {
        cars.race(5);
    
        assertThat(cars.getCurrentCars()).contains(CarDto.of("재성", 1));
    }
    
    @Test
    void race_move_0step() {
        cars.race(3);
    
        assertThat(cars.getCurrentCars()).contains(CarDto.of("재성", 0));
    }
    
    @Test
    void get_max_position_cars() {
        cars.race(4);
        final Car maxPositionCar = cars.getMaxPosition();
    
        assertThat(CarDto.from(maxPositionCar).getPosition()).isEqualTo(1);
    }
    
    ```



#### 단일Dto.from( 응답객체 ) 개발 -> (객체비교불가상황)응답 값 1개만 getter 추출 -> 값 비교 테스트

- 현재 maxPosition인 Car객체 (List)를 뽑기 위해 **`중간, Car객체 1개 추출 로직을 테스트`**해야한다.

    - `actual`에서 단일객체 1개마 뽑히는 상황 -> **단일객체1 to 단일Dto**로 바로 가도록 **`단일Dto.from()`** 정펙매를 만들어줘야 -> **값을 추출할 수 있다.**
    - **`필드 1개만 알고 있는 상황`을 확인해야하므로,  max인 값**을 가진 Car를 뽑아내기 때문에, **임의로 CarDto.of( , , )로 만들 수 없는 상황**

    ```java
    @Test
    void get_max_position_car() {
        cars.race(4);
        final Car maxPositionCar = cars.getMaxPosition(); 
        
        // 이름은 모름, position만 1인 것을 아는 상황. -> 값으로 비교해야함 -> Dto에서 뽑아내야함.
    }
    ```

    ```java
    public static CarDto from(final Car car) {
        return new CarDto(car.getName(), car.getPosition());
    }
    ```

    ```java
    @Test
    void get_max_position_car() {
        cars.race(4);
        final Car maxPositionCar = cars.getMaxPosition();
    
        //my) <일부 속성(이름제외된 숫자속성)만 변화되어, 객체==만든 예상객체>의 비교가 불가능할 경우,
        //    실제 객체를 ->  dto변형 후 값을 뽑아내 -> 값 대 값으로 확인할 수 도 있다.
        assertThat(CarDto.from(maxPositionCar).getPosition()).isEqualTo(1);
    }
    ```

    



#### 일급의 응답(`List<객체>`)는 -> 한방에 DtoList or 복수Dto로 한방에

- **일급이 사용되는 `serivce`에게는 `DtoList` or `복수 Dto`로 응답해야한다.**

    - 어차피 응답은 `객체List`로 오니 -> **`복수Dto.from( List<객체>)`로 한방에 돌리자.**
    - **내부에서 이미 개발된 `stream 단일객체` + `단일Dto.from( 단일객체)` -> list**로만 만들어주면 된다.
    - **대신 dto용 getter에 이름을... `get()`로 단순화시키자.**

    ```java
    @Test
    void get_max_position_cars() {
        cars.getWinners();
    }
    ```

    ```java
    //일급 응답이 객체리스트? -> 한방에 복수Dto.from만들기
    public List<Car> getWinners() {
        final Car maxPositionCar = getMaxPosition();
        return this.cars.stream()
            .filter(car -> car.isSamePosition(maxPositionCar))
            .collect(Collectors.toList());
    }
    ```

    ```java
    // 이미 단일Dto.from이 개발된 상황이라면, 
    // 객체리스트 -> 복수Dto 한방에 개발한다.
    return CarsDto.from(this.cars.stream()
                .filter(car -> car.isSamePosition(maxPositionCar))
                .collect(Collectors.toList()));
    ```

    ```java
    public static List<CarDto> from(final List<Car> cars) {
        // 단일Dto List로 일단 만든 뒤 -> 생성자 호출 예정
        return cars.stream()
            .map(car -> CarDto.from(car))
            .collect(Collectors.toList());
    }
    ```

    ```java
    return CarsDto.from(this.cars.stream()
                        .filter(car -> car.isSamePosition(maxPositionCar))
                        .collect(Collectors.toList()));
    ```

    ```java
    private final List<CarDto> carsDto;
    
    public CarsDto(final List<CarDto> carsDto) {
        this.carsDto = carsDto;
    }
    
    public static CarsDto from(final List<Car> cars) {
        return new CarsDto(cars.stream()
                           .map(car -> CarDto.from(car))
                           .collect(Collectors.toList()));
    }
    
    //public List<CarDto> getCarsDto() {
    public List<CarDto> get() {
        return carsDto;
    }
    ```

    - dtoList .get() -> Dto List ->  1개 뽑아서 값 비교..(현재 이름모르고 1개 값만 아는 상태)

    ```java
    @Test
    void get_max_position_cars() {
        cars.race(4);
        cars.race(4);
        cars.race(4);
        final CarsDto winnerCars = cars.getWinners();
    
        //복수Dto.get() -> List <단일Dto>
        assertThat(winnerCars.get().get(0).getPosition()).isEqualTo(3);
    }
    ```

    



### Service 객체 TDD

- inputView로부터 오는 도메인의 재료를 컨트롤러 내부에서 
    1. 생성될 때, `생성자`로 될 때 받을지
    2. 객체생성 후, `generate`로 



- **input재료**들 -> 받아서 생성자를 통한 **일급 or List단일**로의 **변환 생성**도 서비스 안에서 이루어진다.
    - 필요재료라 **인스턴스 변수로 관리**해야한다.





#### 재료없이 service객체 셋업 (원래 미리 생성 in  컨트롤러)

- 컨트롤러에서 사용한다고 생각하자.

- **일단 컨트롤러 생성자에서부터 미리 서비스를 미리 생성시킬 예정이다.**

    - 재료가 한번에 오면 initDto -> service미리 생성시킬 시 받아주면 되는데

        ```java
        @BeforeEach
        void setup() {
            racingGame = new RacingGame(InitDto.of("juri,hunch", "5"));
        }
        ```

    - 그게 아니라면, service.generate일급()처럼, 원재료를 일급/List객체로 변환하는 작업도 service가 담당한다.

        ```java
        public RacingController() {
            racingService = new RacingService();
        }
        ```

        

- 셋업에 서비스 미리 생성

    ```java
    RacingService racingService;
    
    @BeforeEach
    void setUp() {
        racingService = new RacingService();
    }
    ```





#### input재료들을 받는 service 생성자도 만들어, 셋업에서 관리변수 채워놓기.

- i**nput을 나중에 받더라도.. 미리 만들어져있어야하구나**. 그래야 **꺼내는 기능만을 확인**할 수 있다.

- service안에 관리 변수에 뭐가 들어있어야... 확인이 된**input원재료 -> 관리 변수 (`Cars cars`)를 통채로 받아주는 놈을 임시로 만들자.**

    - **검증**은 각 도메인 내에서 할 것임. **변환생성해주는 서비스에서 하지말자.**

    ```java
    @BeforeEach
    void setUp() {
        final List<String> carNames = Arrays.asList("재성", "재경", "아라");
    
        racingService = new RacingService(carNames);
    }
    ```

    ```java
    private Cars cars;
    
    public RacingService(final List<String> carNames) {
        this.cars = new Cars(carNames);
    }
    ```





#### 기능 결과 확인용 일급.toDto() 호출 함수부터

- 서비스는 내부에 **인스턴스변수로 일급 (or 객체List)를 관리**한다.

    - 일급이나 객체List를 확인하는 방법은 toDto로 미리 정의해놓았다.
    - **이것을 부르는 메서드(`getCurrent일급()`)를 먼저 TDD**한다.
        - 이름만 get일급()이지, toDto한 `List<Dto>`를 가져온다.

    ```java
    @Test
    void get_current_cars() {
        racingService.getCurrentCars();
    }
    ```

    ```java
    public class RacingService {
    
        public void getCurrentCars() {
            this.cars.getCurrentCars();
        }
    }
    ```

    ```java
    public class RacingService {
    
        private Cars cars;
    
        public List<CarDto> getCurrentCars() {
            return this.cars.getCurrentCars();
        }
    }
    ```

    ```java
    @Test
    void get_current_cars() {
        final List<CarDto> currentCars = racingService.getCurrentCars();
    
        assertThat(currentCars).contains(CarDto.of("재성", 0));
    }







#### 미리 안넣어놓고, 차후 setter역할의 service.generate()로 input재료 -> 해당도메인 객체(일급) 변환 생성 TDD

- 서비스는 해당도메인의 기능을 모아둔 것이다. **일단 해당도메인으로 변환**시킨다.

    - **서비스.해당도메인생성( input재료)** -> **내부 관리 `인스턴스 변수(일급orList)에  set`**되는 것을 확인해야한다.
    - **그것을 빼쓰라고 만든게 `일급.toDto()`있으니, `서비스에서 일급DTO내놔 메서드를 호출해서 컨트롤러에 제공`해준다.**

    ```java
    @Test
    void generate_cars() {
        //given: input에서 오는 원재료
        final List<String> names = Arrays.asList("재성", "재경");
    
        //when
        racingService.generateCars(names);
    }
    ```

    ```java
    public class RacingService {
    
        public void generateCars(final List<String> names) {
            this.cars = new Cars(names);
        }
    }
    ```

    ```java
    public class RacingService {
    
        private Cars cars;
    
        public void generateCars(final List<String> names) {
            this.cars = new Cars(names);
        }
    }
    ```

    ```java
    @Test
    void generate_cars() {
        //given: input에서 정제된 원재료
        final List<String> names = Arrays.asList("재성", "재경");
        racingService.generateCars(names);
    
        //when
        //then
        assertThat(racingService.getCurrentCars()).contains(CarDto.of("재성", 0));
    }
    ```

    

##### 필요시 전략객체 service에서 생성하여,  메서드의 파라미터로 넣어주기

#### 들어간 Service 변수(인스턴스 변수, 필요 변수)확인 테스트

##### 관리(인스턴스) 변수 중 VO는 getDto없이 asssertThat().extracting으로 내부 인변 바로 뽑아 확인하기

##### input정제 VO 원시값까지 받아주는 service 생성자 추가 생성

```java
@Test
void get_round() {
    racingService = new RacingService(carNames, 5);
}
```



##### VO 객체 개발

```java
//서비스 생성자 추가
public RacingService(final List<String> carNames, final int round) {
    this.cars = new Cars(carNames);
    this.round = new Round(round);
}


//VO 개발
public class Round {
    private final int round;

    public Round(final int round) {
        this.round = round;
    }

    @Override
        public boolean equals(final Object object) {

```





##### VO를 받아서 생성하는 service를 초기화 후, extracting으로 객체 뽑아서 1:1비교

- 기존 서비스는 노인자 -> names(stringList->일급) -> names 포함한 VO까지 받아서 처리

```java
@Test
void get_round() {
    racingService = new RacingService(carNames, 5);

    //when, then
    assertThat(racingService).extracting("round").isEqualTo(new Round(5));
}
```





#### 메인로직 TDD

##### 메인로직 실행

- 일급.메서드()를 실행시키는 단순로직

    - 응답 값이 없는 실행 메서드다?  `사용객체`와 `예상객체`로 비교
    - **랜덤값이 포함되어있으면, 그만큼 빼줘야한다... 하지만, 그냥 빼면... 일괄 적용되므로, `인터페이스로 빼고 -> 필요한 곳에서 전략메서드 호출()`하도록 뺀다.**
        - 테스트에선 `전략객체` 대신 `최종 결과`값만 넣어주면 됨. (여기선 생략)

    ```java
    @Test
    void race() {
        racingService = new RacingService(carNames, 5);
    
        racingService.race();
        racingService.race();
    
        assertThat(racingService.getCurrentCars().get(0).getPosition()).isEqualTo(2);
    }
    ```

    1. 전략 인페 + 전략메소드 정의
    2. 전략을 구현한 랜덤인페 + 메소드 정의
    3. 전략메소드 호출시점에만, 메소드 호출하여 랜덤 시행 + 그외에는 전략객체만 파라미터로 수정
    4. 테스트에선 **람다식으로 `인자 없이 랜덤한 return값을 직접 지정`**  () -> return을 준다.



####  메인로직 실행 횟수(VO)의 iterator TDD

- service가 만들어질 때나, 만들어진 이후 가지고 set되어야할 `VO`는 **service내부 관리 변수 확인 테스트에서 처음 등장했다.**

    ```java
    @Test
    void get_round() {
        racingService = new RacingService(carNames, 5);
    
        assertThat(racingService).extracting("round").isEqualTo(new Round(5));
    }
    ```

    

- **컨트롤러에서 service.메인로직()을 `service.hasNext(VO)`만큼** 돌려야한다.





##### 01 service. isContinuable() -> Vo.isNotFinish() -> true/false를 테스트한다.

- hasNext패턴의 **while()문에 들어갈 조건부**이다.

    ```java
    @Test
    void vo_is_continuable() {
        racingService = new RacingService(carNames, 5);
    
        final boolean isContinuable = racingService.isContinuable();
    
        assertThat(isContinuable).isTrue();
    }
    
    @Test
    void vo_is_not_continuable() {
        racingService = new RacingService(carNames, 0);
    
        final boolean isContinuable = racingService.isContinuable();
    
        assertThat(isContinuable).isFalse();
    }
    ```

    ```java
    //Service
    public boolean isContinuable() {
        return this.round.isNotFinish();
    }
    
    //Round
    
    public boolean isNotFinish() {
        return this.round > 0;
    }
    ```

    



##### 02 service.play메인로직() 내부에서 -> `메인객체.메인로직() 와 동시에 돌아갈 + VO감소로직 VO.getNext();` 처리

- VO감소 -> 감소된 VO반환하여 service관리 VO를 덮어쓰기

    - 이 과정이 **while문 true 내부  메인로직 돌아갈 때 같이 돌아가야한다**.

    ```java
    @Test
    void vo_can_decrease() {
        racingService = new RacingService(carNames, 4);
    
        //프로덕션코드에서는, 반복될 메인로직 실행부 메서드와 같이, race실행시 round감소를 같이시켜줘야한다.
        //racingService.play();
        racingService.playWIthRoundDecrease();
    }
    ```

    ```java
    //Service
    
    public void playWIthRoundDecrease() {
        //1. 메인(일급)객체.race()
    
        //2. decrease된 vo를 받아와야함 *값만 변화(round.decrease())하면 안됨
        round = round.getNext();
    }
    ```

    ```java
    //Round
    
    public Round getNext() {
        // 여기서는 0보다 커야한다는 검증이 있으면 0으로 갈때 에러가 나니 조심!
        // 검증은 hasNext패턴을 가지는 VO는 무조건 검증을 정팩메에 주자~
        return new Round(this.round - 1);
    }
    ```

    ```java
    @Test
    void vo_can_decrease() {
        racingService = new RacingService(carNames, 4);
    
        //프로덕션코드에서는, 반복될 메인로직 실행부 메서드와 같이, race실행시 round감소를 같이시켜줘야한다.
        //racingService.play();
        racingService.playWIthRoundDecrease();
    
        //내부의 round값이 변화되기 때문에 -> 응답 값이 아닌 사용객체 vs 예쌍객체로 비교해야한다.
        assertThat(racingService).extracting("round").isEqualTo(new Round(3));
    }
    ```

    









### 랜덤응답의 범위 TDD(단위테스트) 

- 랜덤을 생성하는 메서드를 학습하는 단위테스트 
    - 범위내로 들어오나? -> `isBetween(a, b) ` -> **`a< = <= b`의 검사**



#### 값 1개의 랜덤:  @RepeatedTest() + .isBetween



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



#### List의 랜덤: iterator의 hasNext패턴 -> 객체비교

```java
@Test
void Random_숫자범위_검사() {
    // 원래는 랜덤으로 차있는 list -> iterator
    final ListIterator<Integer> numbers = Arrays.asList(1, 2, 3, 4, 44, 45).listIterator();

    // 랜덤값들을 돌면서 검사
    while (numbers.hasNext()) {
        assertThat(numbers.next())
            .isGreaterThanOrEqualTo(1)
            .isLessThanOrEqualTo(45);
    }
}
```




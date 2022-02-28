---
toc: true
layout: post
title: TDD 4 자경(controller~view~검증)
description: controller View처리+Service에서 도메인검증

categories: [java, 우테코, TDD, 강의]
image: "images/posts/wootech.png"
---

### ControllerTest for 컨트롤러와 service 형태 결정



#### (test)Controller는 InputView를 파라미터로 받는다.

- Controller -> **ControllerTest**에서 컨트롤러 생성

```java
controller = new Controller(new InputView());
```



#### (prod)Controller 생성자 내부 new Service( inputXX ) 초기화

##### 01 모든 입력을 한번에 다 -> new Service( InputView.getInputDto() )

- 개별 try/catch는 안될 것이다.
    - 처음부터 재입력받는 로직이 필요하다면, 여기를 함수화하고 재귀 걸어주면 될듯

```java
public Controller(final InputView inputView) {
    //1-1. 서비스 생성시 inputView에서 한꺼번에 inputDto로 받는 경우
    racingService = new RacingService(inputView.getInputDto());
```



###### 01-1 inputDto.of( 개별getInput() , 개별getInput()) 형태로 return

- **개별input에서는**
    1. 출력문
    2. 기본 scanner input받아서, **공통 `NullOrEmpty` + 숫자면`포맷`** 
    3. `split(,-1)+trim` or `parseInt`의 **`정제된 형으로 변환`**
    4. 원시값 return

```java
public class InputView {

    private static Scanner SCANNER = new Scanner(System.in);

    //1-3. getINputDto내부에서 사용될 개별 getXXX는 getInput()스캐너에 의존한다.
    private String getInput() {
        return SCANNER.nextLine();
    }

    //1-2. 모든 input들을 InputDto.of에 넣어서 InputDto로 반환해주는 작업.
    // -> dto는 일단 of(모든 속성들을 원시값으로) 받아서 -> new dto( 원시값, 들로 생성)해야한다.
    public InputDto getInputDto() {
        return InputDto.of(getNames(), getRound());
    }

    //1-4. 개별 getInput들은 출력 + 공통input검증(nullOrEmpty/format)이 생성된다.
    // 개별 getInput들은 4단계가 있다고 했다 (1)출력 (2) nullOrEmypy 검증 (3) format검증후convert or split+trim 변환 (4) return
    public List<String> getNames() {
        System.out.println("이름을 입력해주세요");
        final String input = getInput();
        Validators.checkNullOrEmpty(input);
        return Arrays.stream(input.split(",", -1))
            .map(String::trim)
            .collect(Collectors.toList());
    }

    public int getRound() {
        System.out.println("몇 회 진행하겠습니까?");
        final String input = getInput();
        Validators.checkNullOrEmpty(input);
        Validators.checkValidFormat(input);
        return Integer.parseInt(input);
    }
}
```



###### 01-2 InputDto는 of 정펙매 -> 생성자 -> 원시값필드 -> getter순으로 정의

```java
public class InputDto {

    private final List<String> names;
    private final int round;

    public InputDto(final List<String> names, final int round) {
        this.names = names;
        this.round = round;
    }

    //1-5. of에서 개별input다 받았으면, 그걸로 new생성자 -> 필드 -> getter까지 다 만든다.
    public static InputDto of(final List<String> names, final int round) {
        return new InputDto(names, round);
    }

    public List<String> getNames() {
        return names;
    }

    public int getRound() {
        return round;
    }
}
```





#### 02 순차적이라면 new Service( 첫input ) -> 메서드파라미터 ( 담input )

- setter금지령으로 인해 -> 메인로직에 필요한 데이터는 생성자에서 객체 생성시 받아 ->  인변에 넣어줘야한다.
    - Controller는 service를 관리하고 있으며, 생성시service 초기화해줘야한다.
        - 그 service 초기화시 받을 input -> 좀있다가 받을 input이 따로 있는 경우

##### 02-1 첫 입력만 일단 생성자에서 개별input()으로 받는다.

```java
public Controller(final InputView inputView) {
    // 첫 입력만 일단 생성자에서 개별input()으로 받는다.
	racingService = new RacingService(inputView.getNames());
```





##### 02-2 개별input() in InputView 정의

```java
public List<String> getNames() {
    System.out.println("이름을 입력해주세요");
    final String input = getInput();
    Validators.checkNullOrEmpty(input);
    return Arrays.stream(input.split(",", -1))
        .map(String::trim)
        .collect(Collectors.toList());
}
```



##### 02-3 그에 따라 Service의 생성자 정의

```java
public RacingService(final List<String> carNames) {
    this.cars = new Cars(carNames);
}
```



- 원래 service 생성자는.. 일급 및 VO 개발시, 다 받아주는 것으로 정했었다. 여기에 추가해준다.

    ```java
    public RacingService(final List<String> carNames, final int round) {
        this.cars = new Cars(carNames);
        this.round = new Round(round);
    }
    ```

    





#### 03 inputView 콘솔입력(공통검증)부터의 테스트 -> new Controller + InputStream(null불가) 

- `new Controller( new InputView )`를 던지면

    - Controller생성자에서 inputView.콘솔input() 이 호출된다.
    - 여러개 개별input()이 포함되어있다면, 그만큼 `SequenceInputStream`을 던져야한다.

    ```java
    // 1. 콘솔의 개별input()호출 -> 공통 검증
    public Controller(final InputView inputView) {
    	racingService = new RacingService(inputView.getNames());
        
    // 2.정제된 input으로 -> 도메인생성 -> 내부에서 도메인 검증
    public RacingService(final List<String> carNames, final int round) {
        this.cars = new Cars(carNames);
    ```



- **테스트는 inpustream에 `null은 넣지못하므로 ""빈칸만` 테스트한다. **
    - **`전체 다 맞는 것 + 개별input 갯수만큼 sequence`를 만들어줘야하므로  끝나면 `가장 마지막에 테스트할 예정`으로 바꾼다.**

```java
@Test
void controller_input_test() {
    final String argument = "";
    final InputStream input = new ByteArrayInputStream((argument + "\n").getBytes());

    System.setIn(input);

    assertThatThrownBy(() -> new Controller(new InputView()))
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessageContaining("빈칸");
}
```







##### 재귀추가시 fail 뜨게 되므로 valid한 input을 다시 넣어두자.

```java
@Test
void input_null_or_empty_test() {
    final String argument1 = ""; // 틀
    final String argument2 = "재성, 재경"; // 맞아서 재귀 통과
    final InputStream input1 = new ByteArrayInputStream((argument1 + "\n").getBytes());
    final InputStream input2 = new ByteArrayInputStream((argument2 + "\n").getBytes());

    final SequenceInputStream sequenceInputStream = new SequenceInputStream(input1, input2);

    System.setIn(sequenceInputStream);

    assertThatThrownBy(() -> new Controller(new InputView())).isInstanceOf(IllegalArgumentException.class)
        .hasMessageContaining("빈칸");
}

```







#### 04 null를 직접하고 싶다면 바로 메서드 유틸Validators.checkNullorEmtpy 자체를 테스트하자.

```java
@Test
void input_null_or_empty_in_validator() {

    assertThatThrownBy(() -> Validators.checkNullOrEmpty(null))
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessageContaining("빈칸");
}
```





### ServiceTest again for 넘어온 정제된input으로 도메인 검증

- **도메인 검증**은 controller 생성자에서  **`inpuView.개별input()`에서 건너오는 `정제된 input`을 받아 `관리 도메인`으로 넣어주는 `Service 생성자`에서 한다**

    ```java
    //controller
    racingService = new RacingService(inputView.getNames());
    
    //service
    public RacingService(final List<String> carNames) {
            this.cars = new Cars(carNames);
    }
    ```

    

#### new Service() -> service.메서드()에서  받아주는 input 순서대로 도메인 검증하기

- **`new Service(  inputView.개별input() )`을 호출**하면서
    - 내부 `  this.cars = new Cars(carNames);` **도메인 생성자 호출**되므로, 
    - `inputView.개별input() ` 자리에 **원하는 인자로 테스트**한다.



#### 01 inputView.개별input() 자리에 valid 테스트 데이터 입력

- **ServiceTest에서 `inputView.개별input()을 받는 곳`을 호출하되, `값만 임의`로 넣어서 테스트한다.**

    ```java
    @Test
    void valid_domain1_input_check() {
        //new RacingService(inputView.getNames())
        //this.cars = new Cars(carNames);
    
        //given
        final List<String> names = Arrays.asList("재성", "재경");
    
        //when,then
        assertDoesNotThrow(() -> new RacingService(names));
    }
    ```

    

#### 02 invalid한 테스트 작성하면서, 예외발생토록 도메인 검증 작성

- **invalid한 경우부터는 `도메인 검증`을 만들고, 예외를 발생시켜야한다. **



- 도메인 만의 검증을 추가한다.

    - 복수/일급이라면 복수에 대한 것만 추가한다.
    - **validate( 정제input )** -> 모든 검증 나열하고 나중에, 메서드 추출하기

    ```java
    public Cars(final List<String> names) {
        // 2-5-2.  개별 도메인 검증 메서드 생성
        validate(names);
        this.cars = names.stream()
            .map(name -> new Car(name))
            .collect(Collectors.toList());
    }
    
    private void validate(final List<String> names) {
        long distinctCountOfNames = names.stream().distinct().count();
        if (distinctCountOfNames != names.size()) {
            //2-5-3. thr 이후 해당 에외클래스() 생성 -> 필요 예외 상속후 생성자만 메세지 파라미터 없이 정의 -> 내부에서 super("message")
            throw new IllegalArgumentException("중복값을 입력할 수 없습니다.");
            //throw new DuplicateNameException();
        }
    }
    ```



#### 03 커스텀 예외클래스 만들기

- thrw new 다음에 발생시킬 에러에 `예외 클래스`를 명시하고

    - 상속할 기존 예외클래스를 `extends`한 뒤
    - **부모예외클래스에서 필요했던 파라미터를 `생성자`로 받든지 or `내부에서 제공할 것으로, 파라미터 없는 생성자`를 정의해주고**
    - 부모 생성자를 그대로 빌려 `super()` 내부에서 `부모가 필요했던 상수를  생성자 내부에서 정의`해주자.

    ```java
    public class NameDuplicatedException extends IllegalArgumentException {
        public NameDuplicatedException() {
            super("이름이 중복될 수 없습니다.");
        }
    }
    ```

    



#### 04 invalid 테스트

- valid: `올바른_도메인이_들어올_경우를_확인한다`
- invalid: `도메인이_중복된_경우_예외를_발생시킨다`

```java
@Test
void invalid_domain2_input_exception_check() {
    //given
    final List<String> names = Arrays.asList("재성", "재성");

    //when,then
    assertThatThrownBy(() -> new RacingService(names))
        .isInstanceOf(NameDuplicatedException.class)
        .hasMessageContaining("중복");
}
```



#### 05 개별input~서비스객체 생성 -> 재귀메서드화 in Controller생성자

- 에러시 재입력 받게 하기 위함.

    ```java
    public Controller(final InputView inputView) {
        //racingService = new RacingService(inputView.getNames());
            getNames(inputView);
    }
    
    private void getNames(final InputView inputView) {
        try {
            racingService = new RacingService(inputView.getNames());
        } catch (Exception e) {
            System.out.println("[ERROR] " + e.getMessage());
            getNames(inputView);
        }
    }
    ```





##### 재귀달린이후 테스트: 틀->맞으로 구성된 SequenceInputStream

- 재귀 달린 순간부터 SequenceInputStream아니어서, 1번 틀리면 ControllerTest 안끝남. 

    ```java
    @Test
    void input_null_or_empty_test() {
        //        final String argument = "";
        //        final InputStream input = new ByteArrayInputStream((argument + "\n").getBytes());
        //
        //        System.setIn(input);
        final String argument1 = ""; // 틀
        final String argument2 = "재성, 재경"; // 맞아서 재귀 통과
        final InputStream input1 = new ByteArrayInputStream((argument1 + "\n").getBytes());
        final InputStream input2 = new ByteArrayInputStream((argument2 + "\n").getBytes());
    
        final SequenceInputStream sequenceInputStream = new SequenceInputStream(input1, input2);
    
        System.setIn(sequenceInputStream);
    
        assertThatThrownBy(() -> new Controller(new InputView())).isInstanceOf(IllegalArgumentException.class)
            .hasMessageContaining("빈칸");
    }
    ```

    

- **문제는.. 추가되는 재귀input -> 기존꺼 계속 Sequnece 이어져야함 -> 재귀는 가장 나중에 달던가 [TDD는 하지말자.]**





#### 06 메서드 파라미터로 들어오는 개별input과 이미 생성[되었을] service객체 

1. 이미 controller생성자 위쪽에서, 서비스 생성과 동시에 받아주는 input으로 **`service객체`를 생성한 상태이므로, `valid한 앞쪽 개별input을 미리 만들어주고, service객체를 만들어서` 테스트해줘야할 듯 싶다.** 

    - **먼저 개발된 `valid_domain1_input`으로 `service객체를 먼저 만들`면서 `메서드 + domain2_input`**을 넣어주도록 한다.

    - **그래야 메서드(`initDomain2()`) 개발이 된다.**

    ```java
    @Test
    void valid_domain2_input_check() {
        //given
        final List<String> validNames = Arrays.asList("재성", "재경");
        final int round = 4;
    
        //when,then
        assertDoesNotThrow(() -> new RacingService(validNames).initiateRound(round))
    }
    ```

    

    

2. service의 생성자에서 못받은 개별input을, service관리 인변에 넣어주자.

    ```java
    public void initiateRound(final int round) {
        this.round = new Round(round);
    }
    ```

    

#### 07 invalid case -> 도메인 검증 추가 작업 반복

```java
@Test
void invalid_domain2_input_range_exception() {
    //given
    final List<String> validNames = Arrays.asList("재성", "재경");
    final int round = -1;

    //when,then
    assertThatThrownBy(() -> new RacingService(validNames).initiateRound(round))
        .isInstanceOf(RoundRangeException.class)
        .hasMessageContaining("0회 이상");
}
```



#### 08 개별input 재귀화 -> TDD는 하지말것

```java
getRound(inputView);

private void getRound(final InputView inputView) {
    try {
        racingService.initRound(inputView.getRound());
    } catch (Exception e) {
        System.out.println("[ERROR] " + e.getMessage());
        getRound(inputView);
    }
}
```







### ControllerTest (재귀 올 개발 후 )

#### 01 재귀작성 완료후 [개별input ALL 맞->맞 ]으로 컨트롤러 생성자 테스트

- 재귀가 추가됨에 따라, controller생성시 `추가 && 맞는 것으로 맞춰줘야하는 input`이 늘어나기 때문에, **controller생성으로 인한 input테스트는 가장 나중에 해야할 것 같다.**

    - 개별input개수만큼 SequenceInputStream으로 들어가게 해준다.

    ```java
    @Test
    void controller_input_test() {
        final String argument1 = "재성, 재경"; // 틀
        final String argument2 = "5"; // 맞아서 재귀 통과
        final InputStream input1 = new ByteArrayInputStream((argument1 + "\n").getBytes());
        final InputStream input2 = new ByteArrayInputStream((argument2 + "\n").getBytes());
    
        SequenceInputStream sequenceInputStream = new SequenceInputStream(input1, input2);
    
        System.setIn(sequenceInputStream);
    
        assertDoesNotThrow(() -> new Controller(new InputView()));
    }
    ```

    

#### 02 컨트롤러 생성자withInput 전체를 셋업 올리고 -> Controller.run (new OuputView()) 개발

```java
Controller controller;

@BeforeEach
void setUp() {
    final String argument1 = "재성, 재경"; // 틀
    final String argument2 = "5"; // 맞아서 재귀 통과
    final InputStream input1 = new ByteArrayInputStream((argument1 + "\n").getBytes());
    final InputStream input2 = new ByteArrayInputStream((argument2 + "\n").getBytes());

    SequenceInputStream sequenceInputStream = new SequenceInputStream(input1, input2);

    System.setIn(sequenceInputStream);

    controller = new Controller(new InputView());
}

@Test
void controller_run() {
    controller.run(new OutputView);
}
```

```java
public class OutputView {
}
```

```java
//controller
public void run(final OutputView outputView) {
    //outputView의 기본 프린터 기능 쓰면서
    //메인로직
    
    //메인로직이후 service에게서 결과받은 것 -> ouputView가 인자로 받아서 처리
    
}
```





#### 03 controller.run()내부에 service로직 + OuputView처리

```java
public void run(final OutputView outputView) {

    outputView.printStartMessage();

    while (racingService.isContinuable()) {
        racingService.race(new RandomNumberGenerator());
        racingService.decreaseRound();
    }
    outputView.printRoundResult(racingService.getCurrentCars());

    outputView.printWinner(racingService.getWinners());
}
```



#### 04 OutputView 결과받아 출력 작성 with  OutputView Test

##### setup

```java
OutputStream output;

@BeforeEach
void setUp() {
	output = new ByteArrayOutputStream();
    System.setOut(new PrintStream(output)); // output.toString() -> actual 로서 print된 스트링을 받아올 수 있게 된다
}
```





##### output에 들어갈 결과(dto or dtoList)를 만들어서 output에 뿌려주기

```java
@Test
void name() {
    //(1)outputView.printRoundResult(racingService.getCurrentCars());
    // racingService.getCurrentCars() -> List<CarDto> 만들어주기
    
    //controller.run()에 사용되던 dto결과들을 단일dto.of() -> dto list -> dtos 까지 직접 만들어준다.
    final CarDto 재성 = CarDto.of("재성", 1);
    final CarDto 재경 = CarDto.of("재경", 2);
    final List<CarDto> cars = Arrays.asList(재성, 재경);

    //(2) outputView객체 만들어서 print뿌려주기( output에 박히게 됨 )
    new OutputView().printRoundResult(cars);

    //(3) print뿌려진 output.toString()과   예상string을 비교하기
    // -> sout는 System.lineSeparator()로 줄바꿈한다.
    // -> 내부string은 \n로 작성되면 -> \n 잡으면 된다.
    assertThat(output.toString()).isEqualTo("재성 : -\n재경 : --\n" + System.lineSeparator());
}
```



```java
@Test
void name2() {
    final CarDto 재성 = CarDto.of("재성", 1);
    final CarDto 재경 = CarDto.of("재경", 0);
    final List<CarDto> cars = Arrays.asList(재성, 재경);

    final CarsDto carsDto = new CarsDto(cars);

    new OutputView().printRoundResult(cars);

    assertThat(output.toString()).isEqualTo("재성 : -\n재경 : \n" + System.lineSeparator());
}
```



```java
@Test
void name3() {
    //outputView.printWinner(racingService.getWinners());
    // racingService.getWinners() -> CarsDto 만들어주기

    final CarDto 재성 = CarDto.of("재성", 2);
    final List<CarDto> cars = Arrays.asList(재성);

    final CarsDto carsDto = new CarsDto(cars);

    new OutputView().printWinner(carsDto);

    assertThat(output.toString()).isEqualTo("재성" + System.lineSeparator());
}

```





```java
@Test
void name4() {
    //outputView.printWinner(racingService.getWinners());
    // racingService.getWinners() -> CarsDto 만들어주기

    final CarDto 재성 = CarDto.of("재성", 2);
    final CarDto 재경 = CarDto.of("재경", 2);
    final List<CarDto> cars = Arrays.asList(재성, 재경);

    final CarsDto carsDto = new CarsDto(cars);

    new OutputView().printWinner(carsDto);

    assertThat(output.toString()).isEqualTo("재성, 재경" + System.lineSeparator());
}
```





### Application으로 controller.run(new OutputView()) 실행

```java
import controller.Controller;
import view.InputView;
import view.OutputView;

public class application {
    public static void main(final String[] args) {
        final Controller controller = new Controller(new InputView());
        controller.run(new OutputView());
    }
}
```


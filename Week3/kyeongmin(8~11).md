## 8장

### 외부 코드 사용하기

- 경계 인터페이스를 이용할 때는 클래스나 클래스 계열 밖으로 노출되지 않도록 주의한다.

```java
public class Sensors {
	private Map sensors = new HashMap();
	
	public Sensor getById(String id) {
		return (Sensor) sensors.get(id);
	}	
}
```

- Map 인스턴스를 공개 API의 인수로 넘기거나 반환 값으로 사용하지 않는다.

### 경계 살피고 익히기

**학습 테스트**

- 타사 라이브러리의 외부 코드를 짧은 시간에 익히기는 어렵다.
- 우리쪽 코드를 먼저 작성하고 외부 API는 테스트 케이스를 작성한다.

### log4j 익히기

- LogTest 클래스로 캡슐한다면 나머지 프로그램은 log4j 경계 인터페이스를 몰라도 된다.

```java
public class LogTest {
	private Logger logger;

@Before
public void initialize() {
	logger = Logger.getLogger("logger");
	logger.removeAllAppenders();
	Logger.getRootLogger().removeAllAppenders();
}

@Test
public void basicLogger() {}

@Test
public void addAppenderWithStream() {}
	
@Test
public void addAppenderWithoutStream() {}
}
```

### 학습 테스트는 공짜 이상이다

**학습 테스트**

- 필요한 지식만 확보하는 손쉬운 방법이다.
- 이해도를 높여주는 정확한 실험이다.
- 패키지가 예상대로 도는지 검증한다.
- 패키지의 새 버전이 나올 경우 학습 테스트를 통해 호환성을 확인할 수 있다.

### 아직 존재하지 않는 코드를 사용하기

- 인터페이스를 통해 통제가 가능해지고 코드 가독성이 높아지며 코드 의도도 분명해진다.

### 깨끗한 경계

- 경계에 위치하는 코드는 깔끔히 분리한다.
- 기대치를 정의하는 테스트 케이스를 작성하자.
- 통제가 불가능한 외부 패키지에 의존하는 대신 통제가 가능한 우리 코드에 의존하는 편이 좋다.
- 외부 패키지를 호출하는 코드를 가능한 줄여 경계를 관리하자.

## 9장

### TDD 법칙 세 가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

### 깨끗한 테스트 코드 유지하기

**지저분한 테스트 코드를 작성한다면**

- 지저분한 테스트 코드를 작성하는건 안 하는것보다 못하다.
- 실제 코드를 변경 시 실제 코드를 짜는 시간보다 테스트 케이스를 추가하는 시간이 더 걸린다.
- 실제 코드를 변경해 기존 테스트가 실패한다면 테스트를 점점 통과시키기 어려워진다.
- 새 버전을 출시할 때마다 팀이 테스트 케이스를 유지하고 보수하는 비용이 늘어난다.
- 변경하면 득보다 해가 더 크다 생각해 개발자는 변경을 주저한다.

### 테스트는 유연성, 유지보수성, 재사용성을 제공한다

- 코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 단위 테스트다.
- 테스트 케이스가 없다면 모든 변경이 잠정적인 버그다.
- 테스트 커버리지가 높을 수록 아키텍처와 설계를 개선할 수 있따.
- 테스트 코드가 지저분하면 코드를 변경하는 능력과 코드 구조를 개선하는 능력이 떨어진다.

### 깨끗한 테스트 코드

- 가독성이 가장 중요하다.
- 명료성, 단순성, 풍부한 표현력이 필요하다.

**BUILD-OPERATE_CHECK 패턴**

1. 첫 부분은 테스트 자료를 만든다.
2. 두 번째 부분은 테스트 자료를 조작한다.
3. 세 번째 부분은 조작한 결과가 올바른지 확인한다.

### 도메인에 특화된 테스트 언어

- API 위에다 함수와 유틸리티를 구현하여 사용한다면 테스트 코드를 짜기도 읽기도 쉬워진다.

### 이중 표준

```java
@Test
public void turnOnLoTempAlaramAtThreashold() throws Exception {
	hw.setTemp(WAY_TOO_COLD);
	controller.tic();
	assertTrue(hw.heaterState());
	assertFalse(hw.coolerState());
}
```

```java
@Test
public void turnOnCollerAndBlowerIfTooHot() throws Exception {
	tooHot();
	assertEquals("hBChl", hw.getState());
}

@Test
public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
	tooCold();
	assertEqauls("HBchl", hw.getState());
}
```

**아래의 메서드로 작성했을때**

- 메서드명이 더욱 명확해진다.
- 상태가 명시적이기 때문에 읽기 편하다.

### 테스트당 assert 하나

**TEMPLATE METHOD 패턴**

상위 클래스에서 알고리즘의 구조를 정의하고 하위 클래스에서 알고리즘의 일부 단계를 재정의하는 디자인 패턴

```java
// 음료를 만드는 추상 클래스
abstract class BeverageMaker {
    // 템플릿 메서드: 음료를 만드는 과정을 정의
    public final void makeBeverage() {
        boilWater();
        brew();
        pourInCup();
        addCondiments();
        System.out.println("Your beverage is ready!");
    }

    // 물 끓이는 메서드
    public void boilWater() {
        System.out.println("Boiling water");
    }

    // 내용물 우려내는 메서드 (하위 클래스에서 구현)
    abstract void brew();

    // 컵에 따르는 메서드
    public void pourInCup() {
        System.out.println("Pouring into cup");
    }

    // 첨가물 추가하는 메서드 (하위 클래스에서 구현)
    abstract void addCondiments();
}

// CoffeeMaker 클래스: Coffee를 만드는 방법을 구현
class CoffeeMaker extends BeverageMaker {
    @Override
    void brew() {
        System.out.println("Dripping Coffee through filter");
    }

    @Override
    void addCondiments() {
        System.out.println("Adding Sugar and Milk");
    }
}

// TeaMaker 클래스: Tea를 만드는 방법을 구현
class TeaMaker extends BeverageMaker {
    @Override
    void brew() {
        System.out.println("Steeping the tea");
    }

    @Override
    void addCondiments() {
        System.out.println("Adding Lemon");
    }
}

// 메인 클래스
public class Main {
    public static void main(String[] args) {
        System.out.println("Making coffee...");
        BeverageMaker coffeeMaker = new CoffeeMaker();
        coffeeMaker.makeBeverage();

        System.out.println("\nMaking tea...");
        BeverageMaker teaMaker = new TeaMaker();
        teaMaker.makeBeverage();
    }
}
```

- given/when 을 부모 클래스에 두고 then을 자식 클래스에 둔다.
- 독자적인 테스트 클래스를 만들어 @Before 함수에 given/when을 넣고 @Test 함수에 then을 넣는다.

### 테스트당 개념 하나

- 테스트 함수 하나는 개념 하나만 테스트하라.
- 이것저것 잡다한 개념을 연속으로 테스트하는 긴 함수는 피한다.
- 개념 당 assert 문 수를 최소로 줄여라.

### F.I.R.S.T

1. **빠르게 - First**
- 테스트는 빨라야 한다.
- 자주 돌리지 않으면 초반에 문제를 찾아내 고치지 못한다.
2. **독립적으로 - Independent**
- 각 테스트는 서로 의존하면 안된다.
- 테스트가 서로 의존하면 하나가 실패할때 나머지가 잇달아 실패하므로 원인을 찾기 어렵고 
후반 테스트가 찾아내야 할 결함이 숨겨진다.
3. **반복가능하게 - Repeatable**
- 테스트는 어떤 환경에서도 반복 가능해야 한다.
- 테스트가 돌아가지 않는 환경이 하나라도 있다면 테스트가 실패한 이유를 둘러댈 변명이 생긴다.
4. **자기검증하는 - Self-Validating**
- 테스트는 부울 값으로 결과를 내야 한다.
- 테스트가 스스로 성공과 실패를 가늠하지 않으면 판단은 주관적이 되며 지루한 수작업 평가가 필요하게 된다.
5. **적시에 - Timely**
- 테스트는 적시에 작성해야 한다.
- 실제 코드를 구현한 후 테스트 코드를 만들면 실제 코드가 테스트하기 어렵다는 사실을 발견할지도 모른다.
- 테스트가 불가능하도록 실제 코드를 설계할지도 모른다.

**결론**

- 테스트 코드는 실제 코드의 유연성, 유지보수성, 재사용성을 보존하고 강화한다.
- 테스트 코드는 지속적으로 깨끗하게 관리하고 표현력을 높이고 간결하게 정리하자.
- 테스트 API를 구현해 도메인 특화 언어를 만들자.

## 10장

### 클래스 체계

**캡슐화**

- 함수나 변수는 private 상태를 유지할 수 있도록 한다.
- 캡슐화를 풀어주는 결정은 최후의 수단이다.

### 클래스는 작아야 한다

- 클래스를 설계할 때는 함수와 마찬가지로 ‘작게’가 기본 규칙이다.
- 함수의 크기는 물리적인 행 수로 측정한다면 클래스는 맡은 책임으로 측정한다.
- 클래스명은 해당 클래스 책임을 기술해야 한다.
- 클래스 작명은 클래스의 크기를 줄이는 첫 번째 관문이다.
- 간결한 이름이 떠오르지 않거나 클래스명이 모호하다면 클래스 책임이 너무 많아서다.
- 클래스 설명은 만일(if), 그리고(and), 하며(or), 하지만(but)을 사용하지 않고 25단어 내외로 가능해야한다.

### 단일 책임 원칙

**클래스나 모듈을 변경할 이유가 단 하나여야 한다.**

- 변경할 이유를 파악하려다보면 코드를 추상화하기가 쉬워진다.
- 작은 클래스로 여럿 나뉜 시스템이라면 직접 영향이 미치는 컴포넌트만 이해해도 충분하다.
- 책임이 큰 클래스들을 변경할때 당장 알 필요가 없는 사실까지 들이밀어 독자를 방해한다.
- 큰 클래스 몇개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다.
- 클래스는 각자 맡은 책임과 변경할 이유는 하나며, 다른 클래스와 협력해 시스템에 필요한 동작을 수행한다.

### 응집도

**응집도가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미이다.**

- 클래스는 인스턴스 변수 수가 작아야 한다.
- 각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 한다.
- 메서드가 변수를 더 많이 사용할수록 메서드와 클래스는 응집도가 더 높다.
- 모든 인스턴스 변수를 메서드마다 사용하는 클래스는 응집도가 가장 높다.

**응집도를 유지하면 작은 클래스 여럿이 나온다**

- 좀 더 길고 서술적인 변수 이름을 사용한다.
- 코드에 주석을 추가하는 수단으로 함수 선언과 클래스 선언을 활용한다.
- 가독성을 높이고자 공백을 추가하고 형식을 맞춘다.

위와 같이 리팩토링된 클래스는 하나의 책임을 갖고 있으며 변경이 필요할 때는 해당 클래스만 수정하면 됩니다.

### 변경하기 쉬운 클래스

- 깨끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.
- 새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 시스템 구조가 바람직하다.
- 이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지 않는다.

```java
// 새로운 SQL문을 추가하거나 수정하려면 Sql 클래스를 수정해야한다.
public class Sql {
	public Sql(String table, Column[] columns)
	public String create()
	public String insert(Object[] fields)
	public String selectAll()
}
```

```java
abstract public class Sql {
	public Sql(String table, Column[] columns)
	abstract public String generate();
}

// Sql 클래스를 상속받는다면 새로운 SQL문을 추가할때 새로운 클래스를 작성하면 된다.
// 기존 Sql클래스를 변경할 필요가 없다.
public class CreateSql extends Sql {
	public CreateSql(String table, Column[] columns)
	@Override public String generate()
}

public class SelectSql extends Sql {
	public SelectSql(String table, Column[], columns)
	@Override public String generate()
}
```

두번째 코드로 변경한다면 SRP와 OCP 를 지킬 수 있다.

**변경으로부터 격리**

- 인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리한다.
- 상세한 구현에 의존하는 코드는 테스트가 어렵다.

```java
public interface StockExchange {
	Money currentPrice(String symbol);
}

public TokyoStockExchange implements StockExchange {
	
	@Override
	public Money currentPrice(String symbol) {}
}

// 구현 클래스 TokyoStockExchange가 아닌 StockExchange 인터페이스에 의존한다.
public Portfolio {
	private StockExchange exchange;
	public Portfolio(StockExcahnge exchange) {
		this.exchage = exchage;
	}
	
}
```

```java
public class PortfolioTest {
	private FixedStockExchangeStub exchange;
	private Portfolio portfolio;
	
	@Before
	protected void setUp() throws Exception {
		exchange = new FixedStockExchangeStub();
		exchange.fix("MSFT, 100);
		portfolio = new Portfolio(exchange);
	}
	
	@Test
	public void GivenFiveMSFTTotalShouldBe500() throws Exception {
		portfolio.add(5, "MSFT");
		Assert.assertEquals(500, portfolio.value());
	}
}
```

- 결합도가 낮다는 소리는 각 시스템 요소가 다른 요소로부터 그리고 변경으로부터 잘 격리되어 있다는 의미다.
- 위와 같은 테스트가 가능할 정도로 시스템의 결합도를 낮추면 유연성과 재사용성도 더욱 높아진다.
- 시스템 요소가 서로 잘 격리되어 있으면 각 요소를 이해하기도 더 쉬워진다.
- 결합도를 최소로 줄이면 자연스럽게 DIP를 따르는 클래스가 나온다.

## 11장

### 도시를 세운다면?

- 추상화와 모듈화로 큰 그림을 이해하지 못할지라도 개인과 개인이 관리하는 구성요소는 효율적으로 돌아간다.
- 깨끗한 코드를 구현하면 낮은 추상화 수준에서 관심사를 분리하기 쉬워진다.

### 시스템 제작과 시스템 사용을 분리하라

```java
// 초기화 지연 기법
// 필요할때까지 객체를 생성하지 않으므로 불필요한 부하가 걸리지 않는다.
// 어떤 경우에도 null 포인터를 반환하지 않는다.
public Service getService() {
	if (service == null)
		service = new MyServiceImpl();
	return service;
}
```

- getService 메서드가 MyServiceImpl과 생성자 인수에 명시적으로 의존한다.
- 런타임 로직에서 MyServiceImpl 객체를 사용하지 않더라도 의존성을 해결하지 않으면 컴파일이 안된다.
- MyServiceImpl이 무거운 객체라면 getService 메서드를 호출하기 전에 Mock으로 service 필드에 할당해야 한다.
- service가 null, null이 아닌 경우의 테스트를 진행해야한다.
- 단일 책임 원칙을 위반한다.
- MyServiceImpl이 모든 상황에 적합한 객체인지 모른다.

**Main 분리**

- 시스템 생성과 시스템 사용을 분리하는 방법
- 생성과 관련된 코드는 main이나 main이 호출하는 모듈로 옭긴다.
- 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정한다.

**팩토리**

```java
// LineItem 추상 클래스 또는 인터페이스
public abstract class LineItem {
    // LineItem의 기능 및 속성 정의
}

// LineItem을 생성하는 추상 팩토리
public interface LineItemFactory {
    LineItem createLineItem(/* 필요한 생성자 인수 */);
}

// LineItem을 구체화하는 구현체
public class LineItemFactoryImplementation implements LineItemFactory {
    @Override
    public LineItem createLineItem(/* 필요한 생성자 인수 */) {
        // LineItem 인스턴스 생성 로직
        return new ConcreteLineItem(/* 생성자 인수 전달 */);
    }
}

// Order 클래스
public class Order {
    private List<LineItem> lineItems = new ArrayList<>();

    // LineItemFactory를 통해 LineItem을 생성하여 Order에 추가
    public void addLineItem(LineItemFactory factory) {
        LineItem lineItem = factory.createLineItem(/* 필요한 생성자 인수 */);
        lineItems.add(lineItem);
    }
}

// OrderProcessing 애플리케이션의 main 메서드
public class OrderProcessing {
    public static void main(String[] args) {
        Order order = new Order();
        LineItemFactoryImplementation factory = new LineItemFactoryImplementation();
        // 필요한 경우 생성자 인수도 전달 가능
        order.addLineItem(factory);
        // Order에 다른 LineItem 추가 로직
    }
}
```

**의존성 주입**

**제어 역전기법을 의존성 관리에 적용한 메커니즘이다.**

- 새로운 객체는 넘겨받은 책임만 맡으므로 SRP를 지키게 된다.
- 의존성 관리 맥락에서 객체는 의존성 자체를 인스턴스로 만드는 책임은 지지 않는다.
- 대신에 이런 책임을 다른 전담 메커니즘에 넘겨야만 한다.

### 확장

- `처음부터 올바르게` 보다는 오늘 주어진 사용자 스토리에 맞춰 시스템을 구현한다.
- 내일은 새로운 스토리에 맞춰 시스템을 조정하고 확장한다.
- 소프트웨어 시스템은 물리적인 시스템과 다르다.
- 관심사를 적절히 분리해 관리한다면 소프트웨어 아키텍처는 점진적으로 발전할 수 있다.

**횡단 관심사**

트랜잭션, 보안, 일부 영속적인 동작은 모든 객체가 전반적으로 동일한 방식을 이용하게 해야한다.

```java

public Aspect LoggingAspect {
    before() : execution(* com.example.service.*.*(..)) {
        System.out.println("Method execution started: " + thisJoinPoint.getSignature());
    }
    
    after() : execution(* com.example.service.*.*(..)) {
        System.out.println("Method execution finished: " + thisJoinPoint.getSignature());
    }
}

public class BoardService {
    public void doSomething() {
    }
}

public class Main {
    public static void main(String[] args) {
        BoardService service = new BoardService();
        service.doSomething();
    }
}
```

- AOP는 횡단 관심사에 대처해 모듈성을 확보하는 일반적인 방법론이다.
- AOP에서 관점의 개념은 “특정 관심사를 지원하려면 시스템에서 특정 지점들이 동작하는 방식을 일관성 있게 바꿔야 한다”라고 명시한다.

### 자바 프록시

- 개별 객체나 클래스에서 메서드 호출을 감싸는 등 단순한 상황에 적합하다.

```java
interface WebPage {
    void display();
}

// 실제 웹 페이지 클래스
class RealWebPage implements WebPage {
    private String url;

    public RealWebPage(String url) {
        this.url = url;
        loadPageFromServer();
    }

    private void loadPageFromServer() {
        // 웹 페이지를 서버에서 로드하는 로직
        System.out.println("Loading web page from server: " + url);
    }

    @Override
    public void display() {
        // 웹 페이지를 표시하는 로직
        System.out.println("Displaying web page: " + url);
    }
}

// 웹 페이지 프록시 클래스
class WebPageProxy implements WebPage {
    private String url;
    private RealWebPage realWebPage;

    public WebPageProxy(String url) {
        this.url = url;
    }

    @Override
    public void display() {
        if (realWebPage == null) {
            realWebPage = new RealWebPage(url);
        }
        realWebPage.display();
    }
}

// 인터넷 브라우저 클래스
class InternetBrowser {
    private WebPage webPage;

    public InternetBrowser(WebPage webPage) {
        this.webPage = webPage;
    }

    public void browse() {
        webPage.display();
    }
}

// 메인 클래스
public class Main {
    public static void main(String[] args) {
        // 프록시를 사용하여 웹 페이지에 대한 접근 제어
        WebPageProxy proxy = new WebPageProxy("https://www.example.com");
        InternetBrowser browser = new InternetBrowser(proxy);
        browser.browse();
    }
}
```

- 코드의 양과 크기는 프록시의 두 가지 단점이다.
- 프록시를 사용하면 깨끗한 코드를 작성하기 어렵다.
- 프록시는 시스템 단위로 실행 ‘지점’을 명시하는 메커니즘도 제공하지 않는다.

### 순수 자바 AOP 프레임워크

**DECORATOR의 러시아 인형**

- DECORATOR 패턴은 객체를 확장하고 각각의 기능을 추가하여 객체의 동작을 변경할 수 있도록 합니다.
- 러시아 인형은 여러 층의 인형으로 구성되어 있으며, 각 인형은 독립적으로 존재할 수 있지만
다른 인형으로 더 장식될 수 있습니다.

```java

interface RussianDoll {
    void open();
}

// 인터페이스나 추상 클래스를 기반으로 하는 기본 객체
class BasicDoll implements RussianDoll {
    @Override
    public void open() {
        System.out.println("Basic doll is opened.");
    }
}

// 데코레이터 클래스는 다른 기능을 추가하거나 수정하여 원래 객체의 동작을 변경한다.
// 데코레이터 클래스
abstract class DollDecorator implements RussianDoll {
    protected RussianDoll doll;

    public DollDecorator(RussianDoll doll) {
        this.doll = doll;
    }

    @Override
    public void open() {
        doll.open();
    }
}

// 추가 기능을 제공하는 데코레이터 클래스
class SmallDollDecorator extends DollDecorator {
    public SmallDollDecorator(RussianDoll doll) {
        super(doll);
    }

    @Override
    public void open() {
        super.open();
        System.out.println("Small doll is opened.");
    }
}

// 또 다른 추가 기능을 제공하는 데코레이터 클래스
class ColorfulDollDecorator extends DollDecorator {
    public ColorfulDollDecorator(RussianDoll doll) {
        super(doll);
    }

    @Override
    public void open() {
        super.open();
        System.out.println("Colorful doll is opened.");
    }
}

// 메인 클래스
public class Main {
    public static void main(String[] args) {
        // 기본 인형 생성
        RussianDoll basicDoll = new BasicDoll();

        // 작은 인형으로 데코레이트
        RussianDoll smallDoll = new SmallDollDecorator(basicDoll);

        // 작은 인형에 더 많은 기능 추가
        RussianDoll colorfulSmallDoll = new ColorfulDollDecorator(smallDoll);

        // 작은 인형에서 기능 순서를 바꿔봄
        RussianDoll smallColorfulDoll = new SmallDollDecorator(new ColorfulDollDecorator(basicDoll));

        // 각 인형을 연다
        basicDoll.open();
        smallDoll.open();
        colorfulSmallDoll.open();
        smallColorfulDoll.open();
    }
}
```

여러 데코레이터를 조합하여 객체의 동작을 유연하게 조정할 수 있다.

### AspectJ 관점

AspectJ 언어

- 관심사를 관점으로 분리하는 도구
- 관점을 분리하는 강력하고 풍부한 도구 집합을 제공한다.
- 새 도구를 사용하고 새 언어 문법과 사용법을 익혀야 한다는 단점이 있다.

### 테스크 주도 시스템 아키텍처 구축

**테스트 주도 아키텍처**

**소프트웨어 시스템을 설계하고 구축할 때 TDD를 중심으로 한 아키텍처 디자인 접근 방식**

- 애플리케이션 도메인 논리를 POJO, 즉 코드 수준으로 아키텍처 관심사를 분리할 수 있다면
진정한 ”테스트 주도 아키텍처 구축”이 가능해진다.
- 프로젝트를 시작할 때는 일반적인 범위, 목표, 일정은 물론이고 결과로 내놓을 시스템의 일반적인 구조도 생각해야 한다.
- 하지만 변하는 환경에 대처해 진로를 변경할 능력도 반드시 유지해야 한다.

```java
최선의 시스템 구조는 각기 POJO (또는 다른) 객체로 구현되는 모듈화된 관심사 영역(도메인)으로 구성된다.
이렇게 서로 다른 영역은 해당 영역 코드에 최소한의 영향을 미치는 관점이나 유사한 도구를 사용해 통합한다.
이런 구조 역시 코드와 마찬가지로 테스트 주도 기법을 적용할 수 있다.
```

### 의사 결정을 최적화하라

- 때떄로 가능한 마지막 순간까지 결정을 미루는 방법이 최선일 수 있다.
- 최대한 정보를 모아 최선의 결정을 내리기 위해서다.
- 성급한 결정은 불충분한 지식으로 내린 결정이다.
- 너무 일찍 결정하면 고객 피드백을 더 모으고, 프로젝트를 더 고민하고, 구현 방안을 더 탐험할 기회가 사라진다.

```java
관심사를 모듈로 분리한 POJO 시스템은 기민함을 제공한다. 이런 기민함 덕택에 최신 정보에 기반해 최선의 시점에
최적의 결정을 내리기가 쉬워진다. 또한 결정의 복잡성도 줄어든다.
```

### 명백한 가치가 있을 때 표준을 현명하게 사용하라

- 표준(EJB2)를 사용하면 아이디어와 컴포넌트를 재사용하기 쉽다.
- 적절한 경험을 가진 사람을 구하기 쉬우며, 좋은 아이디어를 캡슐화하기 쉽고, 컴포넌트를 엮기 쉽다.
- 하지만 때로는 표준을 만드는 시간이 너무 오래 걸려 업계가 기다리지 못한다.
- 어떤 표준은 원래 표준을 제정한 목적을 잊어버리기도 한다.

### 시스템은 도메인 특화 언어가 필요하다

- 좋은 DSL은 도메인 개념과 그 개념을 구현한 코드 사이에 존재하는 ‘의사소통 간극’을 줄여준다.
- 도메인 전문가가 사용하는 언어로 도메인 논리를 구현하면 도메인을 잘못 구현할 가능성이 줄어든다.
- DSL을 효과적으로 사용한다면 추상화 수준을 코드 관용구나 디자인 패턴 이상으로 끌어올린다.

```java
도메인 특화 언어를 사용하면 고차원 정책에서 저차원 세부사항에 이르기까지 모든 추상화 수준과
모든 도메인을 POJO로 표현할 수 있다.
```

**결론**

- 깨끗하지 못한 아키텍처는 도메인 논리를 흐리며 기민성을 떨어뜨린다.
- 도메인 논리가 흐려지면 제품 품질이 떨어진다.
- 버그가 숨어들기 쉽고, 스토리를 구현하기 어려워진다.
- 기민성이 떨어지면 생산성이 낮아져 TDD가 제공하는 장점이 사라진다.
- 모든 추상화 단계에서 의도는 명확해야하기 때문에 POJO를 작성하고 관점 혹은 관점과
유사한 메커니즘을 사용해 각 구현 관심사를 분리해야 한다.
- 시스템을 설계하든 개별 모듈을 설계하든, 실제로 돌아가는 가장 단순한 수단을 사용해야한다.
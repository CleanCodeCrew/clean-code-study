# 4장

### 주석

- 잘 달린 주석은 그 어떤 정보보다 유용하다.
- 경솔하고 근거 없는 주석은 코드를 이해하기 어렵게 만든다.
- 오래되고 조잡한 주석은 거짓과 잘못된 정보를 퍼뜨려 해악을 미친다.
- 주석보다는 코드를 깔끔하게 정리하고 표현력을 강화하는 방향이 중요하다.

### 주석은 나쁜 코드를 보완하지 못한다.

- 주석으로 설명하는 시간에 코드를 깔끔하게 정리하자.

### 코드로 의도를 표현하라

- 메서드명으로도 충분히 기능의 의도를 표현할 수 있다.

## 좋은 주석

### 정보를 제공하는 주석

- 메서드가 반환할 값을 설명하는 주석으로 제공한다.

```java
// kk:mm:ss EEE, MMM, dd, yyyy 형식이다.
Pattern timeMatcher = Pattern.compile(“\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*”);
```

**시각과 날짜를 반환하는 클래스를 만들어 작성하면 더 깔끔하다.**

### 의도를 설명하는 주석

- 때때로 주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명한다.

### 의미를 명료하게 밝히는 주석

- 때때로 모호한 인수나 반환값은 그 의미를 읽기 좋게 표현하면 이해하기 쉬워진다.

### 결과를 경고하는 주석

- 때로 다른 프로그래머에게 결과를 경고할 목적으로 주석을 사용한다.

ex) SimpleDateFormat은 스레드에 안전하지 못하다.

### TODO 주석

- 때로는 앞으로 할 일을 //TODO 주석으로 남겨두면 편하다.

```java
//TODO-MdM 현재 필요하지 않다.
//체크아웃 모델을 도입하면 함수가 필요 없다.
```

- 더 이상 필요 없는 기능을 삭제하라는 알림
- 누군가에게 문제를 봐달라는 요청
- 더 좋은 이름을 떠올려달라는 부탁
- 앞으로 발생할 이벤트에 맞춰 코드를 고치라는 주의

### 중요성을 강조하는 주석

- 대수롭지 않다고 여겨질 뭔가의 중요성을 강조하기 위해서도 주석을 사용한다.

```java
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
String listItemContent = match.group(3).trim();
```

## 나쁜 주석

### 주절거리는 주석

- 특별한 이유 없이 마지못해 주석을 단다면 전적으로 시간 낭비다.

```java
try {
	String propertiesPath = propertiesLocation + “/” + PROPERTIES_FILE;
	FileInputStream propertiesStream = new FileInputStream(propertiesPath);
	
	loadProperties.load(propertiesStream);
} catch(IOException e)
{
	// 속성 파일이 없다면 기본값을 모두 메모리로 읽어 들였다는 의미다.
}
```

어느 시점에 예외를 잡아 기본값을 읽어 들인 후 예외를 던지는지 알 수 없다.

### 같은 이야기를 중복하는 주석

- 반복되는 주석은 코드만 지저분하고 기록이라는 목적에는 전혀 기여하지 못한다.

### 의무적으로 다는 주석

- 모든 함수에 Javadocs를 넣는 규칙이 아무런 가치 없는 주석이라면 잘못된 정보를 제공할 여지만 만든다.

```java
@param title CD 제목
@param author CD 저자
```

### 이력을 기록하는 주석

- 모듈을 편집할 때마다 변경 이력을 기록하고 관리하는 관례는 혼란만 가중할 뿐이다.

### 있으나 마나 한 주석

- 당연한 사실을 언급하며 새로운 정보를 제공하지 못하는 주석

```java
// 기본 생성자
protected AnnualDateRule() {}
```

### 함수나 변수로 표현할 수 있다면 주석을 달지 마라

- 주석이 필요하지 않도록 코드를 개선하는 편이 더 좋다.

### 위치를 표시하는 주석

```java
// Actions ////////////////////////////
```

위와 같은 배너 아래 특정 기능을 모아놓으면 유용할 경우도 있다.

### 닫는 괄호에 다는 주석

- 닫는 괄호에 주석을 달아야겠다는 생각이 든다면 대신에 함수를 줄이려 시도하자.

### 주석으로 처리한 코드

- 소스 코드 관리 시스템을 사용하자.

### HTML 주석

- HTML 주석은 편집기/IDE에서조차 읽기 어렵다.

### 전역정보

- 주석을 달아야 한다면 근처에 있는 코드만 기술하라.
- 코드 일부에 주석을 달면서 시스템의 전반적인 정보를 기술하지 마라.

### 너무 많은 정보

- 주석에 흥미로운 역사나 관련 없는 정보를 장황하게 늘어놓지 마라.

### 함수 헤더

- 짧고 한 가지만 수행하며 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 훨씬 좋다.

> **평소에 주석이 달린 코드를 보면 어떤 기능이 수행되는지 미리 파악할 수 있어서 비즈니스 로직을 이해하는데 도움이 되곤 했습니다.**   
> **개인적으로 특정한 기술을 사용하는 코드를 이해하기 위해 작성한 주석은 좋은 방법이라고 생각합니다.**

# 5장

### 형식을 맞추는 목적

- 처음 잡아놓은 구현 스타일과 가독성 수준은 유지보수 용이성과 확장성에 게속 영향을 미친다.
- 원래 코드는 사라질지라도 개발자의 스타일과 규율은 사라지지 않는다.

### 적절한 행 길이를 유지하라

신문 기사처럼 작성하라

- 이름은 간단하면서도 설명이 가능하게 짓는다.
- 이름만 보고도 올바른 모듈을 살펴보고 있는지 아닌지를 판단할 정도로 신경 써서 짓는다.
- 소스 파일 첫 부분은 고차원 개념과 알고리즘을 설명한다.
- 아래로 내려갈수록 의도를 세세하게 묘사한다.
- 마지막에는 가장 저차원 함수와 세부 내역이 나온다.

### 개념은 빈 행으로 분리하라

- 각 행은 수식이나 절을 나타낸다.
- 일련의 행 묶음은 완결된 생각 하나를 표현한다.
- 빈 행은 새로운 개념을 시작한다는 시각적 단서다.

### 세로 밀집도

- 세로 밀집도는 연관성을 의미한다.
- 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다.

### 수직 거리

- protected 변수를 피해야 하는 이유
    
    타당한 근거가 없다면 서로 밀접한 개념은 한 파일에 속해야 한다.
    

### 변수 선언

- 변수는 사용하는 위치에 최대한 가까이 선언한다.

### 인스턴스 변수

- 클래스 맨 처음에 선언한다.
- 변수 간에 세로로 거리를 두지 않는다.

### 종속 함수

- 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다.
- 호출하는 함수를 호출되는 함수보다 먼저 배치한다.

### 개념적 유사성

- 비슷한 동작을 수행하는 함수를 가까이 배치한다.

```java
static public void assertTrue(boolean condition) {}
static public void assertFalse(String message, boolean condition) {}
```

### 가로 형식 맞추기

- 짧은 행이 바람직하고 120자 정도로 행 길이를 제한하는것이 좋다.

### 가로 공백과 밀집도

- 왼쪽 요소와 오른쪽 요소를 분명히 나누기 위해서 공백을 넣어라.
- 함수를 호출하는 인수는 공백으로 분리해라.

### 가로 정렬

- 정렬이 필요할 정도로 목록이 길다면 문제는 목록 `길이`이다.
- 선언부가 길다면 클래스를 쪼개야 한다는 의미이다.

### 들여쓰기

- 정보는 파일 전체, 개별 클래스, 메서드, 블록 내에 적용된다.
- 계층에서 각 수준은 이름을 선언하는 범위와 실행문을 해석하는 범위를 나타낸다.
- 범위로 이뤄진 계층을 표현하기 위해 들여쓰기를 사용한다.

### 팀 규칙

- 좋은 소프트웨어 시스템은 읽기 쉬운 문서로 이뤄진다.
- 스타일은 일관적이고 매끄러워야 한다.
- 한 소스 파일에서 봤던 형식이 다른 소스 파일에도 쓰이리라는 신뢰감을 독자에게 줘야 한다.
- 온갖 스타일을 뒤섞어 소스 코드를 필요 이상으로 복잡하게 만드는 실수는 반드시 피한다.


# 6장

### 자료 추상화

- 변수를 private으로 선언하더라도 get, set함수를 제공한다면 구현을 외부로 노출하는 셈이다.
- 구현을 감추려면 추상화가 필요하다.
- 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 
진정한 의미의 클래스이다.
- 자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다.

```java
public interface Vehicle {
    double getFuelTankCapacityInGallons();
    double getGallonsOfGasoline();
}

public interface Vehicle {
    double getPercentFuelRemaining();
}
```

**2번째 방법이 더 좋은 방법이다.**

- 아무 생각 없이 조회/설정 함수를 추가하는 방법이 가장 나쁘다.

### 자료/객체 비대칭

- 복잡한 시스템을 짜다 새로운 자료 타입이 필요한 경우에는 
클래스와 객체 지향 기법이 가장 적합하다.
- 새로운 함수가 필요한 경우에는 절차적인 코드와 자료 구조가 좀 더 적합하다.

### 디미터 법칙

- 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙이다.
- 객체는 조회 함수로 내부 구조를 공개하면 안된다는 의미이다.
- 객체가 다른 객체와의 상호작용을 최소화한다.
- 대신에 직접적으로 연결된 객체만을 처리하도록 유도한다.
- 이를 통해 시스템 내의 결합도를 낮추고 응집도를 높이는 데 도움이 된다.

```jsx
public class Order {
    private Customer customer;

    public Order(Customer customer) {
        this.customer = customer;
    }

    public void processOrder() {
         System.out.println("주문이 처리되었습니다.");
    }

    public Customer getCustomer() {
        return customer;
    }
}

public class Customer {
    private String name;

    public Customer(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

public class OrderService {
    public void placeOrder(Customer customer) {
        Order order = new Order(customer);
        order.processOrder();

        System.out.println("주문한 고객: " + order.getCustomer().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer("John");
        OrderService orderService = new OrderService();
        orderService.placeOrder(customer);
    }
}
```

**OrderService 클래스는 주문과 관련된 로직에만 집중하고 고객 객체의 내부 구현에 대한 자세한 정보를 알 필요가 없다.**

### 기차 충돌

- 여러 객체가 한 줄로 이어진 기차처럼 보인다.

```java
final String outputDir = ctxt.getOptions().getScratchDir().getAbsoluterPath();
```

### 잡종 구조

- 절반은 객체, 절반은 자료 구조인 구조

```java

public class MixedFunctionality {
    public void readFile(String fileName) {
        System.out.println("파일을 읽는 기능을 수행합니다: " + fileName);
    }

    public void connectToDatabase() {
        System.out.println("데이터베이스에 연결합니다.");
    }

    public void sendEmail(String recipient, String message) {
        System.out.println("이메일을 보냅니다: " + message + " (받는 사람: " + recipient + ")");
    }

    public int performCalculation(int num1, int num2) {
        System.out.println("계산을 수행합니다: " + num1 + " + " + num2);
        return num1 + num2;
    }

    public void renderUI() {
        System.out.println("UI를 렌더링합니다.");
    }

    public static void main(String[] args) {
        MixedFunctionality mixed = new MixedFunctionality();
        mixed.readFile("example.txt");
        mixed.connectToDatabase();
        mixed.sendEmail("example@example.com", "안녕하세요!");
        
        int result = mixed.performCalculation(5, 3);
        
        System.out.println("결과: " + result);
        
        mixed.renderUI();
    }
}
```

**`MixedFunctionality`  클래스는 여러 가지의 다른 기능을 포함하고 있다.**

**이러한 혼합된 기능은 클래스의 역할을 명확하게 정의하지 않으며**

**클래스를 변경하거나 확장하기가 어렵다.**

### 자료 전달 객체

- 공개 변수만 있고 함수가 없는 클래스로 주로 자료 전달 객체(DTO)라고 한다.
- DB에 저장된 가공되지 않은 정보를 애플리케이션 코드에서 사용할 객체로 변환하는 구조체

### 활성 레코드

- 데이터베이스 테이블이나 다른 소스에서 자료를 직접 변환한 결과다.
- 비즈니스 규칙 메서드를 추가한다면 자료 구조도 아니고 객체도 아닌 잡종 구조가 나온다.

### 결론

- 객체는 동작을 공개하고 자료를 숨긴다.
- 자료 구조는 별다른 동작 없이 자료를 노출한다.

# 7장

**오류 처리 코드로 인해 프로그램 논리를 이해하기 어려워진다면 깨끗한 코드라 부르기 어렵다.**

### 오류 코드보다 예외를 사용하라

- 호출자 코드를 깔끔하게 유지하고 논리가 오류 처리 코드와 뒤섞이지 않으려면
오류가 발생했을때 예외를 던지자.

### Try-Catch-Finally 문부터 작성하라

- 예외가 발생할 코드를 try-catch-finally 문으로 시작하여 프로그램 상태를 일관성 있게 
유지해야한다.

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
	try {
		FileInputStream stream = new FileInputStream(sectionName);
		// 나머지 논리 코드 추가
		stream.close();
	} catch (FileNotFoundException e) {
		throw new StorageException("retrieval error", e);
	}
	return new ArrayList<RecordedGrip>();
}
```

- 먼저 강제로 예외를 일으키는 테스트 케이스를 작성한 후 테스트를 통과하게 코드를 작성하는 방법을 권장한다.
- 그러면 자연스럽게 try 블록의 트랜잭션 범위부터 구현하게 되므로 범위 내에서 트랜잭션 본질을 유지하기 쉬워진다.

### 예외에 의미를 제공하라

- 오류 메시지에 정보를 담아 예외와 함께 던진다.
- 실패한 연산 이름과 실패 유형도 언급한다.
- 로깅을 사용하여 catch 블록에서 오류를 기록하도록 충분한 정보를 넘겨준다.

### 호출자를 고려해 예외 클래스를 정의하라

```java
LocalPort port = new LocalPort(12);

try {
	port.open();
} catch (PortDeviceFailure e) {
	reportError(e);
	logger.log(e.getMessage(), e);
} finally {
	...
}

public class LocalPort {
	private ACMEPort innerPort;
	
	public LocalPort(int portNumber) {
		innerPort = new ACMEPort(portNumber);
	}
	
	public void open() {
		try {
			innerPort.open();
		} catch (DeviceResponseException e) {
			throw new PortDeviceFailure(e);
		} catch (ATM1212UnlockedException e) {
			throw new PortDeviceFailure(e);
		} catch (GMXError e) {
			throw new PortDeviceFailure(e);
		}
	}
}
```

**ACMEPort라는 외부 API를 감싸서 외부 라이브러리와 프로그램 사이의 의존성을 줄일 수 있다.**

### 정상 흐름을 정의하라

**특수 사례 패턴**

```java
public interface Product {
    String getName();
    void processOrder(int quantity);
}

public class NormalProduct implements Product {
    private String name;
    private int stock;

    public NormalProduct(String name, int stock) {
        this.name = name;
        this.stock = stock;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void processOrder(int quantity) {
        if (stock >= quantity) {
            stock -= quantity;
            System.out.println(quantity + "개의 " + name + " 상품을 주문하였습니다.");
        } else {
            System.out.println("주문할 수 있는 " + name + " 상품의 재고가 부족합니다.");
        }
    }
}

// OutOfStockProduct 클래스: 재고가 부족한 상품을 나타내는 클래스
public class OutOfStockProduct implements Product {
    private String name;

    public OutOfStockProduct(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void processOrder(int quantity) {
        System.out.println(name + " 상품은 재고가 부족하여 주문할 수 없습니다.");
    }
}

public class Main {
    public static void main(String[] args) {
        Product product1 = new NormalProduct("사과", 10);
        Product product2 = new OutOfStockProduct("바나나");

        product1.processOrder(5); // 재고가 있는 경우: 주문 처리됨
        product2.processOrder(3); // 재고가 부족한 경우: 특수 사례 처리됨
    }
}
```

**인터페이스를 통해 일반적인 동작을 정의하고, 구현하는 클래스에서 각각의 동작을 구현하게 한다.**

### null을 반환하지 마라

- null을 반환하는 코드는 일거리를 늘릴 뿐만 아니라 호출자에게 문제를 떠넘긴다.
- 메서드에서 null을 반환하고 싶다면 대신 예외를 던지거나 특수 사례 객체를 반환한다.
- 외부 API가 null을 반환하면 감싸기 메서드를 구현해 예외를 던지거나 특수 사례 객체를 반환한다.

### null을 전달하지 마라

- 정상적인 인수로 null을 기대하는 API가 아니라면 메서드로 null을 전달하는 코드는 피해야한다.

### 결론

- 깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.
- 오류 처리를 프로그램 논리와 분리하면 독립적인 추론이 가능해지며 코드 유지보수성도 크게 높아진다.
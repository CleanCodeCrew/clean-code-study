# 1장 깨끗한 코드

- 개발을 진행하면서 내가 작성하고 있던 코드가 좋은 코드인지 확인하게 되었고, 어떤 코드가 좋은 코드인 것인가 클린 코드를 통해 중요한 내용들을 정리하려 한다.


> 나쁜 코드로 고생한 경험이 프로그래머라면 가지고 있는 경험일 것이다. 제대로 코딩할 시간이 없다 생각해서  돌아가기만 하면 된다는 생각으로 짠 쓰레기 코드를 보며 나중에 손보겠다 생각하여 방치하며 넘어가지만 나중은 절대 오지 않는다.  모든 프로그래머가 기한을 맞추려면 나쁜 코드를 양산할 수 밖에 없다고 느낀다. 하지만 나쁜 코드를 양산하면 기한을 맞추지 못한다. 엉망진창인 상태로 인해 속도가 곧바로 늦어지고, 결국 기한을 놓친다. **기한을 맞추는 유일한 방법은 빨리 가는 유일한 방법은 언제나 코드를 최대한 깨끗하게 유지하는 습관이다.**
> 

## 깨끗한 코드란?

노련한 프로그래머들의 의견들

- 비야 스트롭스트룹
    
    > 우아하고, 효율적인 코드. 논리가 간단해야 버그가 숨어 들지 못한다. 
    **깨끗한 코드는  
    한 가지를 제대로 한다. 
    보기에 즐거운 코드다. 
    세세한 사항까지 꼼꼼하게 처리하는 코드다.**
    > 
- 그래디 부치
    
    > **깨끗한 코드는 
    단순하고 직접적이다.  
    결코 설계자의 의도를 숨기지 않는다. 
    가독성을 강조!**
    > 
- 데이브 토마스
    
    > 깨끗한 코드는 
    작성자가 아닌 사람도 읽기 쉽고 고치기 쉽다. 
    의미 있는 이름이 붙는다.
    특정 목적을 달성하는 방법은 하나만 제공한다. 
    작은 코드에 가치를 둔다.
    > 
- 론 제리스
    
    > 중복이 없다.
    클래스, 메소드, 함수 등을 최대한 줄인다.
    같은 작업을 여러 차례 반복한다면 코드가 아이디어를 제대로 표현하지 못한다는 증거
    중복과 표현력을 신경을 써야 한다.
    > 
- 워드 커닝햄
    
    > 깨끗한 코드는 코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다
    > 

## 우리는 저자다

프로그래머들은 저자이면서 독자이다. 코드를 작성할 때 저자라는 사실을 이 노력을 보고 판단을 내릴 독자가 있다는 사실을 항상 기억 해야한다.

기존 코드의 일부분을 개선 및 추가하는 경우 이러한 경험이 있을 것이다.

> 변경할 함수로 스크롤
잠시 멈추고 생각
모듈 상단으로 스크롤해 변수 초기화를 확인한다.
다시 내려와 입력
입력 / 지우기 반복
지금 바꾸려는 함수를 호출하는 함수로 스크롤 한 후 함수가 호출되는 방식을 살피기
다시 돌아와 지운 코드를 입력
반복
> 

코드를 읽는 시간 대 코드를 짜는 시간 비율이 10 대 1을 훌쩍 넘기기도 한다. 

새 코드를 짜면서 끊임없이 기존 코드를 읽는다. 기존 코드를 읽기 쉽게 짠다면 새 코드를 짜기도 쉬워진다.

### 보이스카우트 규칙

잘 짠 코드가 전부는 아니다. 시간이 지나도 언제나 깨끗하게 유지해야 한다.

> 캠프장은 처음 왔을 때보다 더 깨끗하게 해놓고 떠나라
> 

체크 아웃 할 때보다 좀 더 깨끗한 코드를 체크인 한다면 코드는 절대 나빠지지 않는다.

<aside>
💡 업무에 투입된 이후 정해진 기한에 맞추기 위하여 기능 완성에만 몰두한 나머지 지저분한 코드를 작성했었다. 업무에 조금 적응이 된 요즘 작성된 코드들을 들여다 볼 여유가 생겨 코드를 들여다 보았고, 나쁜 코드 투성인 것을 확인했다. 넘쳐나는 중복 코드, 이것저것 엉킨 로직들, 거대한 덩어리 메소드, 무엇인지 메소드에 가보지 않으면 알기 힘든 이름들,  새 코드를 짜기 위해 기존 코드를 보았지만 기존 코드를 보는 시간이 몇 배 더 걸리는 순간도 겪게 되었다.
깨끗한 코드의 정의를 이해하는데 많은 도움이 되었다. 어렴풋이 알고 있던 내용, 개발을 하면서 느꼈지만 나중에 하면 되지 라고 넘겼던 부분들. 나중은 오지 않는다는 말이 많이 와닿은 부분이라고 생각한다.

이 책을 통해 보기에 좋고, 협업할 때 쉽게 읽힐 수 있는 코드를 짤 수 있는 방법과 규칙들을 이해하고, 습관이 될 수 있게 노력하고, 공부 해야겠다고 생각이 들었다.

</aside>

# 2장 의미있는 이름

## 의도를 분명히 밝혀라

- 의도 분명한 이름이 정말로 중요하다는 사실을 강조한다.
- 변수, 함수, 클래스의 이름은 존재 이유, 수행 기능, 사용 방법이 명확하게 의도를 분명히 드러내고 있어야한다.

```java
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x: theList)
    if (x[0] == 4)
        list1.add(x);
    return list1;
}
```

- 공백과 들여쓰기도 적당하고, 변수와 상수 개수도 많지 않다.
- 그러나 코드 맥락이 코드 자체에 명시적으로 드러나지 않는다.
    - theList에 무엇이 들었는가
    - theList에서 0번째 값이 어째서 중요한가
    - 값 4는 무슨 의미인가
    - 함수가 반환하는 리스트 list1을 어떻게 사용하는가

```java
public List<Cell> getFlaggedCells() {
    List<Cell> flaggedCells = new ArrayList<Cell>();
    for (Cell cell: gameBoard)
        if (cell.isFlagged())
            flaggedCells.add(cell);
    return flaggedCells;
}
```

- 위의 코드와 비교했을 때 단순성은 변하지 않았다. 하지만 코드는 더욱 명확해졌다.
- 이름의 의도를 명확하게 작성하면 함수가 하는 일 즉, 코드의 목적을 쉽게 이해할 수 있다.

## 그릇된 정보를 피하라

코드에 그릇된 단서를 남겨서는 안된다. 

그릇된 단서는 코드의 의미를 흐린다.

여러 계정을 그룹으로 묶을 때 실제 `List`가 아니라면 accountList라 명명하지 않는다. `List`는 특수한 의미이기 때문에 계정을 담는 컨테이너가 실제 `List`가 아니라면 그릇된 정보를 제공하는 셈이다. 그러므로 `accoutGroup, Accounts`라 명명한다.

서로 비슷한 이름을 사용하지 않도록 해야한다. 한 모듈에서 `XYZControllerForEfficientHandlingOfStrings`라는 이름과 `XYZControllerForEfficientStorageOfStrings`라는 이름을 사용한다면, 아무래도 많이 헷갈릴 수 밖에 없다.

유사한 개념은 유사한 표기법을 사용한다. 요즘 환경은 자동 완성 기능을 제공하는데, 이름만 보고 객체를 선택할 때 유사한 표기법을 가진 그릇된 정보를 제공하는 변수를 사용하게 된다면 좋지 않은 결과를 초래할 수 있다.

## 의미 있게 구분하라

컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하려 한다면 문제를 일으킨다. 

컴파일러를 통과할지라도 연속된 숫자를 덧붙이거나 불용어를 추가하는 방식은 적절하지 않다. 이름이 달라야 한다면 의미도 달라져야 한다.

연속적인 숫자를 덧붙인 이름(`a1`,`a2`, ... ,`aN`)은 그릇된 정보를 제공하는 이름은 아니며, 아무런 정보를 제공하지 못하는 이름일 뿐, 저자 의도가 전혀 드러나지 않는다.

불용어를 추가한 경우 역시 아무런 정보도 제공하지 못한다. `Product`라는 클래스가 있을 때, 다른 클래스를 `ProduectInfo` 혹은 `ProductData`라 부른다면 개념을 구분하지 않은 채 이름만 달리한 경우다. 마치 a, an, the처럼 의미가 불분명한 불용어를 사용한 것과 같다. 불용어의 쓰임은 중복을 낳을 수 밖에 없다.`Name`과 `NameString`, `Customer`와 `CustomerObject` 등과 같이 차이가 전혀 와닿지 않는다.

불용어는 중복이다. 구분이 되지 않는 이름은 옳지 않다. 읽는 사람이 차이를 알도록 이름을 지어야한다.

## 발음과 검색하기 쉬운 이름을 사용하라

```java
class DtaRcrd102 {
    private Date genymdhms;
    private Date modymdhms;
    private final String pszqint = "102";
			}
```

```java
class Customer {
    private Date generationTimestamp;
    private Date modificationTimestamp;
    private final String recordId = "102";
}
```

발음하기 어려운 이름은 토론 하기도 어렵다.

아래 코드는 위 코드보다 더욱 쉽게 대화가 가능해지고 의미를 쉽게 파악할 수 있게 된다.

문자 하나를 사용하는 이름과 상수는 텍스트 코드에서 쉽게 눈에 띄지 않는다는 문제점이 있다. 예로 e라는 이름의 변수가 있고, e를 검색한다면 e가 포함되어 있는 이름과 수식이 모두 검색되어 찾고자 하는 변수를 찾기 힘들 것이다. 하지만 `WORK_DAYS_PER_WEEK` 은 보다 쉽게 찾았을 것이다. 저자는 간단한 메소드에서 로컬 변수만 한 문자를 사용한다고 말한다. 

변수나 상수의 이름 길이는 범위 크기에 비례해야 한다. 

또한 여러 곳에서 사용한다면 검색하기 쉬운 이름이 바람직하다.

## 인코딩을 피하라

유형이나 범위 정보같이 이름에 인코딩 할 정보는 아주 많다. 그렇게 되면 이름을 해독하기 더욱 어려워진다. 헝가리식 표기법이나 기타 인코딩 방식이 자바를 쓰고 있는 우리라면 오히려 방해가 될 뿐이다. 변수, 함수, 클래스 이름이나 타입을 바꾸기 어려워 지며 읽기도 어려워 진다.

### 멤버변수 접두어

멤버 변수를 선언할 때 `m_` 접두어를 붙일 필요가 없다. 전반적으로 코드에 접두어가 붙어 있으면 가독성이 떨어지고, 게다가 사람들은 접두어를 무시하고 이름을 해독하는 방식을 재빨리 익힌다. 클래스와 함수는 접두어가 필요 없을 정도로 작아야 마땅하다.

인터페이스 클래스와 구현 클래스 구분을 위해 인코딩이 필요한 경우도 있다.  `IShapeFactory`와 `ShapeFactory`처럼 인터페이스에는 옛날 코드에서 많이 사용하는 `I` 접두어를 붙이기도 하는데, 이는 주의를 흐트리고 과도한 정보를 제공할 수 있다. 이보다는 구현 클래스에 인코딩을 해서 `ShapeFactoryImp` 등으로 사용하는 편이 좋다.

## 자신의 기억력을 자랑하지 마라

독자가 코드를 읽으면서 변수 이름을 자신이 아는 이름으로 변환해야 한다면 그 변수 이름은 바람직하지 못하다. 

문자 하나만 사용하는 변수 이름은 문제가 있다. 루프에서 반복 횟수를 세는 변수 i, j, k는 괜찮다. (단 루프 범위가 아주 작고 다른 이름과 충돌하지 않을 때) 

최악은 a 와 b를 사용하므로 c를 선택한다는 논리다. 자신을 과시하는 사람들은 단순한 변수가 어떤 역할을 하는지 언제나 기억하고 있다. 똑똑한 프로그래머와 전문가 프로그래머의 차이는 전문가 프로그래머는 명료함이 최고라는 사실을 이해하고 자신의 능력을 좋은 방향으로 사용해 남들이 이해하는 코드를 내놓는다.

## 적합한 이름

클래스 혹은 객체 이름에는 명사나 명사구를 사용하는 것이 좋다. `Customer`, `WikiPage`, `Account`, `AddressParser` 등이 좋은 예시다. `Manager`, `Processor`, `Data`, `Info` 등과 같은 단어는 피하고, 동사는 사용하지 않는 것이 좋다.

메서드 이름은 동사나 동사구가 적합하다. `postPayment`, `deletePage`, `save` 등이 좋은 예입니다. 접근자, 변경자, 조건자에는 javabean 표준에 따라 값 앞에 `get`, `set`, `is`를 붙힌다.

생성자를 중복정의 할 때는 정적 패토리 메소드를 사용한다.

```java
Complex fulcrumPoint = Complex.FromRealNumber(23.0);

Complex fulcrumPoint = new Complex(23.0);
```

## 한 개념에 한 단어를 사용하라

추상적인 개념 하나에 단어 하나를 선택해 이를 고수해야 한다. 같은 메소드를 클래스마다 fetch, retrieve, get으로 혹은 controller, manager, driver 등의 경우처럼 제각각 부르면 혼란스럽다. 

더불어 한 단어를 두 가지 목적으로 사용하면 안된다. 한 개념에 한 단어를 사용하라는 규칙을 따랐더니, 예를 들어 여러 클래스에 add라는 메소드가 생겼다. 모든 add 메소드가 매개 변수와 반환값이 의미적으로 똑같다면 문제가 없다. 하지만 add라는 기존 메소드가 값 두 개를 더하거나 이어서 새로운 값을 만든다고 할 때, 집합에 값 하나를 추가하는 메소드를 add라고 부르는 것은 기존과 맥락이 달라 명명하는 것이 좋지 않다. 그러므로 insert나 append라는 이름이 적당하다.

## 의미있는 맥락을 추가하라

예를 들어,  `firstName`, `lastName`, `street`, `city`, `state`, `zipcode`라는 변수가 있다면 이들이 주소를 표현함을 금방 알아챌 수 있다. 하지만 어느 메서드가 `state` 변수 하나만 사용한다면 주소 일부라는 걸 쉽게 알아차리지 못한다. 따라서 `addr`라는 접두어를 추가해 `addrState`라 쓰거나, `Address`라는 클래스에 속하도록 수정 해주는 것이 좋다.

<aside>
💡 의도를 분명히 밝히라는 의미가 중요하게 생각이 들었다. 자기가 짠 코드는 최대한 기억할 수 있다. 하지만 코드의 양이 많아지면 잊혀지기 쉽상이다. 그러한 나비효과로 어떤 변수인지 모르고, 헤매는 불상사가 발생하는 때도 생길 수 있다. 그 때를 맞이하지 않기 위해 변수나 함수 등에 이름을 의도가 정확하게 표출되게 작성하는 것을 항상 상기 시켜야 한다.

</aside>

# 3장 함수

## 작게 만들어라!

함수를 만드는 첫번째 규칙은 **작게!** 두번째 규칙은 더 **작게!** 

**함수는 100줄을 넘어서는 안된다. 20줄도 긴 편이다. 각 함수가 명백하게 이야기 하나를 표현 하게끔 작게 작성 해야한다.** 

**if문 / else 문 / while 문 등에 들어가는 블록은 한 줄이어야 한다는 의미이다. 그렇게 되면 바깥을 감싸는 함수가 작아지고 코드를 이해하기도 쉬워진다.** 추가로 블록 안에서 호출하는 함수 이름을 적절히 짓는 것도 코드를 이해하기 쉬워질 수 있는 방법이 된다.

```java
public static String renderPageWithSetupsAndTeardowns (PageData pageData, bollean isSuite) throws Exception {
    if (isTestPage(pageData))
        includeSetupAndTeardownPages(pageData, isSuite);
    return pageData.getHtml();
}
```

## 한가지만 해라!

함수는 **한 가지를 해야 한다. 그 한가지를 해야 한다.그 한 가지만을 해야 한다.** 

문제는 그 한가지가 무엇인지 알기가 어렵다는 점이다. 

지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한가지 작업만 하는 것.

함수 내에 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈

**함수당 추상화 수준은 하나로 해야한다!** 

함수가 확실히 한가지 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일 해야한다.

한 함수 내에 추상화 수준을 섞으면 특정 표현이 근본 개념인지 아니면 세부사항인지 구분하기 어려워 헷갈린다.

내려가기 규칙이 있다. 코드는 위에서 아래로 이야기처럼 읽혀야 좋다. 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다. 즉, 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한번에 한 단계씩 낮아진다.

## Switch 문

## 서술적인 이름을 사용하라

**함수가 하는 일을 잘 표현하는 것이 좋은 이름이다.**

**길고 서술적인 이름이 짧고 어려운 이름보다 좋다. 길고 서술적인 이름이 길고 서술적인 주석보다 좋다.**

서술적인 이름을 사용하면 개발자 머릿속에서 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다.

이름을 붙일 때는 일관성이 있어야 한다. 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.


## 함수 인수

함수에서 이상적인 인수 개수는 0개다. 다음은 1개(단항), 다음은 2개(이항), 3개(삼항)는 가능한 피하는 편이 좋다. 4개 이상(다항)은 특별한 이유가 필요하다. 특별한 이유가 있어도 사용하면 안된다.

인수는 개념을 이해하기 어렵게 만든다.  인수가 3개를 넘어가면 인수마다 유효한 값으로 모든 조합을 구성해 테스트하기가 상당히 부담스러워 지기 때문에 인수는 더 어렵다.

흔이 함수에다 인수로 입력을 넘기고 반환값으로 출력을 받는다는 개념에 익숙하기 때문에 출력 인수는 입력 인수보다 이해하기 어렵다. 

### 단항 형식

함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 두 가지다. 

1. 인수에 질문을 던지는 경우
    1. `boolean fileExists(”MyFile”)` 이 좋은 예
2. 인수를 뭔가로 변환해 결과를 반환하는 경우
    1. `InputStream fileOpen(”MyFile”)` 은 String 형의 파일 이름을 InputStream으로 변환하는 예

드물게 `passwordAttemptFailedNtimes(attempts)` 처럼 이벤트 처리를 하는 함수도 있다.

이벤트 함수는 입력 인수만 있다. 출력 인수는 없다. 

프로그램은 함수 호출을 이벤트로 해석해 입력 인수로 시스템 상태를 바꾼다. 이벤트는 사실이 코드에 명확하게 드러나야 한다. 그러므로 이름과 문맥을 주의해서 선택한다.

위 경우가 아니라면 단항 함수는 가급적 피한다.

### 이항 함수

인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵다. 

`writeField(name)` 은 `writeField(outputStream, name)` 보다 이해하기 쉽다.

둘 다 의미는 명확하지만 후자는 첫 인수를 무시해야 한다는 사실을 깨닫는 시간이 필요하다. 어떤 코드는 절대로 무시하면 안된다.

가능하다면 단항 함수로 바꾸도록 노력해야한다. writeField 메소드를 outputStream 클래스 구성원으로 만들어 outputStream.writeField(name)으로 호출하게 만든다.

인수가 2-3개가 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어본다. 

```java
Circle makeCircle(double x, double y, double radius);

Circle makeCircle(Pointer pointer, double radius); // Pointer 클래스 객체로 변환
```

인수를 줄임과 x, y를 묶어 하나의 개념으로 표현하게 된다.

### 인수 목록

인수 개수가 가변적인 함수도 필요하다. 

String.format 메소드가 좋은 예이다.

String.format(”%s worked %.2f hours”, name, hours);

가변 인수 전부를 동등하게 취급하면 List형 인수 하나로 취급할 수 있다. 

함수의 의도나 인수의 순서와 의도를 제대로 표현하려면 좋은 함수 이름이 필수다.

단항 함수는 함수와 인수가 동사/명사 상을 이뤄야 하고 키워드를 추가하는 형식으로 함수 이름을 작성하면 인수 순서를 기억할 필요도 없어지게 된다.

⇒ assertExpectedEqualsActual(expected, actual)

## 부수 효과를 일으키지 마라

함수에서 한 가지를 하겠다고 약속하고선 예상치 못하게 클래스 변수를 수정하거나 함수로 넘어온 인수나 시스템 전역 변수를 수정하는 등 부수 효과를 일으키는 경우가 있다.

```java
public boolean checkPassword(String userName, String password) {
    User user = UserGateway.findByName(userName);
    if (user != User.NULL) {
        String codedPhrase = user.getPhraseEncodedByPassword();
        String phrase = crptographer.decrpt(codedPhrase, password);
        if ("Valid Password".equals(phrase)) {
            Session.initialize();
            return true;
        }
    }
    return false;
}
```

함수가 일으키는 부수 효과는 `Session.initialize()` 이다. checkPassword 메소드 이름만 봐서는 세션을 초기화 한다는 사실이 드러나지 않는다. 의도치 않게 세션 정보가 날아가는 상황이 생긴다면 이러한 부수 효과로 인해 시간적인 결함을 초래하게 된다. 

### 출력 인수

일반적으로 인수를 함수 입력으로 해석한다. 

appendFooter(s) 이 함수는 무언가에 s를 바닥글로 첨부할까? s에 바닥글을 첨부할까? s는 입력일까 출력일까? 단숨에 알기 힘들어 함수 선언부를 찾아보게 될 것이다.

public void appendFooter(StringBuffer report)

인수 s가 출력인수라는 사실은 분명하지만 함수 선언부를 찾아보고 나서야 알 수 있었다.

함수 선언부를 찾아보는 행위는 코드를 보다가 주춤하는 행위와 동급이다. 상당히 거슬리는 작업

객체 지향 언어에서는 출력 인수를 사용할 필요가 거의 없다. 출력 인수로 사용하라고 설계한 변수가 `this` 

`report.appendFooter()`

일반적으로 출력 인수는 피해야 한다. 함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택한다.

## 명령과 조회를 분리하라!

함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야한다.

public boolean set(String attribute, String value);

이 함수는 이름이 attribute인 속성을 찾아 값을 value로 설정한 후 성공하면 true, 실패하면 false를 반환한다.

if(set(”uesrname”, “unclebob”))

코드를 다른 독자 입장에서 보면 username이 unclebob으로 설정되어 있는지 확인하는 코드인지 username을 unclebob으로 설정하는 코드인지 의미를 정확하게 파악할 수 없다. set이라는 단어가 동사인지 형용사인지 분간하기가 어려운 탓.

이러한 혼란을 해결하기 위해선 명령과 조회를 분리하는 방법으로 문제를 해결할 수 있다.

if(attributeExists(”username”)) {

setAttribute(”username”, “unclebob”);

}

## 오류보다 예외를 사용하라!

오류코드를 반환하는 방식은 명령/조회 분리 규칙을 미묘하게 위반한다. 

```java
if (deletePage(page) == E_OK) {
    if (registry.deleteReference(page.name) == E_OK) {
        if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
            logger.log("page deleted");
        } else {
            logger.log("configKey not deleted");
        }
    } else {
        logger.log("deleteReference from registry failed");
    }
} else {
    logger.log("deleted failed");
    return E_ERROR;
}
```

동사/ 형용사 혼란을 일으키지 않는 대신 여러 단계로 중첩되는 코드를 야기한다. 오류 코드를 반환하면 호출자는 오류 코드를 곧바로 처리해야 한다는 문제에 부딫힌다.

```java
public void delete(Page page) {
    try {
        deletePageAndAllReferences(page);
    }
    catch (Exception e) {
        logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deletKey(page.name.makeKey());
}

private void logError(Exception e) {
    logger.log(e.getMessage());
}
```

try-catch 블록은 정상 동작과 오류 처리 동작을 뒤섞는다. 그러므로 try-catch 블록을 별도 함수로 뽑아내는 편이 좋다. 

delete 함수는 모든 오류를 처리한다. 또한 정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다.

또한 함수는 한가지 작업만 해야한다! 오류 처리도 한 가지 작업에 속한다. 그러므로 오류를 처리하는 함수는 오류만 처리해야 마땅하다.

## 반복하지 마라

중복은 소프트웨어에서 모든 악의 근원이다. 중복만 없애도 모듈 가독성이 크게 높아지는 사실이다.

객체 지향 프로그래밍은 코드를 부모 클래스로 몰아 중복을 없앤다. 

중복을 제거하는 전략으로 구조적 프로그래밍, AOP 등이 사용된다.

## 구조적 프로그래밍

데이크스트

모든 함수와 함수 내 모든 블록에 입구와 출구가 하나만 존재해야 한다.

즉, 함수는 return 문이 하나여야 한다는 말. 루프 안에서 break나 continue를 사용해선 안되며 goto는 절대로 안된다. 

구조적 프로그래밍의 목표와 규율은 공감하지만 이 규칙은 함수가 아주 클 때만 이익을 제공한다.

함수를 작게 만든다면 간혹 return, break, continue를 여러 차례 사용해도 괜찮다.

## 함수를 짜는 방법

소프트웨어를 짜는 행위는 여느 글짓기와 비슷하다. 글을 작성할 때 먼저 생각을 기록한 후 읽기 좋게 다듬는다. 초안은 대게 서투르고 어수선하므로 원하는대로 읽힐 때까지 말을 다듬고 문장을 고치고 문단을 정리한다.

함수를 짤 때도 마찬가지. 처음에는 길고 복잡하다. 들여쓰기 단계도 많고 중복된 루프도 많다. 

하지만 서투른 코드를 빠짐없이 테스트하는 단위 테스트 케이스도 만들어 코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거한다. 메소드를 줄이고 순서를 따른다. 

최종적으로는 책에서 설명한 규칙을 따르는 함수가 얻어진다. 처음부터 완벽한 코드를 짤 수는 없다.

⇒ 진짜 목표는 시스템을 구성하는 코드라는 이야기를 풀어나가기 쉽게 작성해야 한다.

<aside>
💡 이번 장에서 업무를 진행하면서 지금까지 작성하고, 또 봤던 코드들이 잘못된 메소드들이 많다는 것을 많이 느꼈다. 하나의 메소드에서 여러 작업을 진행했고, 불필요하게 반복한 부분들도 있는 것을 보았다. 
소프트웨어를 짜는 행위를 글짓기와 비슷하다고 했는데, 지금까지 이상한 외계어를 쓰고 있다는 느낌을 받았다. 1장 후기와 비슷한데 처음 코드를 짜는 방법 그대로 이어서 업무를 해왔던 것 같고, 팀의 습관이 되가고 있다는 것을 느꼈다. 테스트 코드도 작성해보지 못했고, 다양한 테스트 케이스를 만들어 테스트하여 코드를 고도화하는 작업도 하지 못하다가 최근에서야 고도화를 진행했다. 사실 그것도 최상의 방법은 아닌거 같지만 또 업무에 치여 오지 않은 나중으로 미루는 느낌을 받는다. 더욱 단순하고, 한가지만 충실히하는 함수를 만들 수 있도록 노력하고, 테스트해야겠다는 생각을 가져본다.

</aside>

# 2주차 정리

## 4장. 주석

### 주석은 나쁜 코드를 보완하지 못한다

표현력이 풍부하고 깔끔하며 주석이 거의 없는 코드가 복잡하고 어수선하며 주석이 많이 달린 코드보다 훨씬 좋다.

→ 나쁜 코드 품질을 주석으로 설명하려 애쓰지 말고, 코드 품질을 높이는 쪽에 시간을 써야 한다.

### 코드로 의도를 표현하라!

많은 경우 주석으로 달려는 설명을 함수로 만들어 표현해도 충분하다.

### 좋은 주석

일반적으로 대다수 주석은 허술한 코드를 지탱하거나, 엉성한 코드 변명하는 등 나쁜 주석에 속한다.

그래도 어떤 주석들은 필요하거나 유익할 때가 있다. 아래에 그 유형들을 소개한다.

- 법적인 이유로 넣는 주석
- 정보를 제공하는 주석
    
    : 기본적인 정보를 제공하면 편리하지만, 이름에 정보를 담거나 코드를 변경하는 쪽이 더 좋고 깔끔하다.
    
- 의도를 설명하는 주석
- 의미를 명료하게 밝히는 주석
    
    : 인수나 반환값이 표준 라이브러리나 변경하지 못하는 코드에 속한다면 주석이 유용하다.
    
- 결과를 경고하는 주석
    
    : 다른 프로그래머에게 결과를 경고할 목적으로 사용한다.
    
- TODO 주석
    
    : 프로그래머가 필요하다 여기지만 당장 구현하기 어려운 업무의 경우 TODO 주석을 사용한다.
    
    **예시**
    
    - 더이상 필요 없는 기능 삭제
    - 문제를 봐달라는 요청
    - 더 좋은 이름을 떠올려달라는 부탁
    - 앞으로 발생할 이벤트에 맞춰 코드를 변경
- 중요성을 강조하는 주석

### 나쁜 주석

- 주절거리는 주석
    
    : 이해가 안 되어 다른 모듈까지 뒤져야 하는 주석은 독자와 제대로 소통하지 못하는 주석이다.
    
- 같은 이야기를 중복하는 주석
    
    : 해당 주석은 코드보다 부정확해 독자가 함수를 대충 이해하게 넘어가게 만드는 위험을 만든다.
    
- 오해할 여지가 있는 주석
- 의무적으로 다는 주석
- 너무 당연한 사실을 언급하며 새로운 정보를 제공하지 못하는 주석
- 함수나 변수로 표현할 수 있다면 주석을 달지 마라
- 위치를 표시하는 주석
- 닫는 괄호에 다는 주석
    
    : 중첩이 심하고 장황한 함수일 경우 유용할 수 있지만, 함수를 줄이려고 시도하는 쪽이 더 좋다.
    
- 이력을 기록하는 주석, 공로를 돌리거나 저자를 표시하는 주석
    
    : 해당 내용은 소스 코드 관리 시스템에 기록되는 불필요한 내용이다.
    
- 주석으로 처리한 코드
    
    : 주석으로 처리된 코드는 다른 사람들이 지우기를 주저한다. 이유가 있어 남겨놓았으리라고 판단하게 되어 쓸모없는 코드가 계속 쌓여갈 것이다.
    
- 전역 정보
    
    : 주석은 근처에 있는 코드만 기술하고, 시스템의 전반적인 정보를 기술하면 안된다. 추후에 전역적인 정보가 변경되어도 해당 주석을 변경한다는 보장은 없다.
    
- 너무 많은 정보
- 주석과 주석이 설명하는 코드의 관계가 모호한 주석
- 함수 헤더
    
    : 짧고 한 가지만 수행하며 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 훨씬 좋다.
    

```
💡 4장 후기

나도 주석이 필요한 코드는 깔끔하게 작성되지 않아서 사족이 필요한 코드라고 생각한다. 코드를 작성할 때 주석이 없어도 이해할 수 있도록 짧게 함수를 만들고, 이해하기 쉬운 이름을 지으려고 하는 편이다. 
업무를 하며 유용하게 쓰인 유일한 주석은 TODO 주석과 FIXME 주석인 것 같다. 두 주석은 IDE에서 해당 주석을 찾아서 보여주는 기능을 제공한다. 추후에 변경해야 하는 코드이지만 시간 상의 문제로 일단 미뤄야할 때 잊지 않게 해주는 좋은 역할을 해주는 것 같다.
```

<br>

## 5장. 형식 맞추기

### 형식을 맞추는 목적

코드 형식은 너무 중요하다. 오랜 시간이 지나 원래 코드의 흔적을 더 이상 찾아보기 어려울 정도로 코드가 바뀌어도 맨 처음 잡아놓은 구현 스타일과 가독성 수준은 유지보수 용이성과 확장성에 계속 영향을 미친다.

### 적절한 행 길이를 유지하라

500줄을 넘지 않고 대부분 200줄 정도인 파일로도 커다란 시스템을 구축할 수 있다. 일반적으로 큰 파일보다 작은 파일이 이해하기 쉽다.

- 신문 기사처럼 작성하라
    
    소스 파일도 신문 기사처럼 작성한다.
    
    - 기사: 기사를 요약하는 표제 → 전체 기사 내용 요약 → 세부 사항
    - 코드: 간단하면서도 설명이 가능한 이름 → 고차원 개념과 알고리즘 → 저차원 함수와 세부 내역
- 개념은 빈 행으로 분리하라
    
    일련의 행 묶음은 완결된 생각 하나를 표현한다. 빈 행은 새로운 개념을 시작한다는 시각적 단서다. 다음의 코드를 보면 줄바꿈만으로도 코드의 가독성이 현저한 차이를 보인다.
    
    ```java
    // 빈 행으로 분리한 코드
    package fitnesse.wikitext.widgets;
    
    import java.util.regex.*;
    
    public class BoldWidget extend ParentWidget {
    									**⋮**
    	 public BoldWidget(ParentWidget parent, String text) throws Exception {
    		 super(parent);
    		 Matcher match = pattern.matcher(text);
    		 match.find();
    		 addChildWidgets(match.group(1));
    	 }
    	 
    	 public String render() throws Exception {
    		 StringBuffer html = new StringBuffer("<b>");
    		 html.append(childHtml()).append("</b>");
    		 return html.toString();
    	 }
    }
    ```
    
    ```java
    // 빈 행으로 구분하지 않은 코드
    package fitnesse.wikitext.widgets;
    import java.util.regex.*;
    public class BoldWidget extend ParentWidget {
    									**⋮**
    	 public BoldWidget(ParentWidget parent, String text) throws Exception {
    		 super(parent);
    		 Matcher match = pattern.matcher(text);
    		 match.find();
    		 addChildWidgets(match.group(1));
    	 }
    	 public String render() throws Exception {
    		 StringBuffer html = new StringBuffer("<b>");
    		 html.append(childHtml()).append("</b>");
    		 return html.toString();
    	 }
    }
    ```
    
- 세로 밀집도
    
    서로 밀접한 연관성의 코드 행은 세로로 가까이 놓이도록 한다.
    
- 수직 거리
    
    서로 밀접한 개념은 세로로 가까이 둬야 한다. 같은 파일에 속할 정도로 밀접한 두 개념은 세로 거리로 연관성을 표현한다. 연관성이 깊은 두 개념이 멀리 떨어져 있으면 코드를 읽는 사람이 소스 파일과 클래스를 여기저기 뒤지게 된다.
    
    - 변수 선언: 변수는 사용하는 위치에 최대한 가까이 선언한다.
    - 인스턴스 변수: 클래스 맨 처음에 선언한다. 잘 설계한 클래스는 많은 클래스 메서드가 인스턴스 변수를 사용하기 때문이다. (잘 알려진 위치에 인스턴스 변수를 모은다.)
    - 종속 함수: 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다. 또한 호출하는 함수를 호출되는 함수보다 먼저 배치한다.
    - 개념적 유사성: 개념적인 친화도가 높을수록 코드를 가까이 배치한다.
        - 한 함수가 다른 함수를 호출해 생기는 직접적인 종속성
        - 변수와 그 변수를 사용하는 함수
        - 명명법이 똑같고 기본 기능이 유사한 함수
- 세로 순서
    
    일반적으로 함수 호출 종속성은 아래 방향으로 유지한다. 즉, 호출되는 함수를 호출하는 함수보다 나중에 배치한다. (고차원 → 저차원)
    

### 가로 형식 맞추기

- 가로 공백과 밀집도
    
    가로로는 공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다. 공백을 넣으면 두 가지 주요 요소가 확실히 나뉜다는 사실이 분명해진다.
    
    - 함수와 인수는 서로 밀접하다. → 공백 X
    - 할당문은 양쪽 요소가 분명히 나뉜다, 함수의 인수는 서로 별개이다. → 공백 O
    
    ```java
    private void measureLine(String line) {
    	lineCount++;
    	int lineSize = line.length();
    	totalChars != lineSize;
    	lineWidthHistogram.addLine(lineSize, lineCount);
    	recordWidestLine(lineSize);
    }
    ```
    
- 들여쓰기
    
    계층에서 각 수준은 이름을 선언하는 범위이자 선언문과 실행문을 해석하는 범위다. 들여쓰는 정도는 계층에서 코드가 자리잡은 수준에 비례한다. 들여쓰기가 없다면 인간이 코드를 읽기란 거의 불가능하다.
    
    간단한 if 문, 짧은 while 문, 짧은 함수에서 들여쓰기를 무시하고픈 유혹이 생기지만, 그럼에도 들여쓰기로 범위를 제대로 표현하도록 한다.
    
- 가짜 범위
    
    빈 while 문이나 for 문을 사용해야 할 경우 새 행에 들여쓰기를 적용해서 세미콜론(;)을 덧붙인다.
    

### 팀 규칙

프로그래머라면 각자 선호하는 규칙이 있다. 하지만 팀에 속한다면 자신이 선호해야 할 규칙은 바로 **팀 규칙**이다.

소프트웨어가 일관적인 스타일을 보이려면 모든 팀원이 해당 규칙을 **반드시** 지켜야 한다.

<br>

## 6장. 객체와 자료 구조

### 자료 추상화

- 변수 사이에 함수라는 계층을 넣는다고 구현이 저절로 감춰지지는 않는다. 구현을 감추려면 추상화가 필요하다.
- 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.
- 자료를 세세하게 공개하기 보다는 추상적인 개념으로 표현하는 편이 좋다.

### 자료/객체 비대칭

두 정의는 본질적으로 상반된다. 두 개념은 사실상 정반대다. 그래서 객체와 자료 구조는 근본적으로 양분된다.

객체: 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.

자료 구조: 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.

|  | 장점 | 단점 |
| --- | --- | --- |
| 자료구조 | 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다. | 새로운 자료 구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다. |
| 객체 | 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다. | 새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다. |

<br>

**절차적인 도형 클래스**

```java
public class Square {
	public Point topLeft;
	public double side;
}

public class Rectangle {
	public Point topLeft;
	public double height;
	public double width;
}

public class Circle {
	public Point center;
	public double radius;
	public double width;
}

public class Geometry {
	public final double PI = 3.141592653585793;

	public double area(Object shape) throws NoSuchShapeException {
		if(shape instanceOf Square) {
			Square s = (Square)shape;
			return s.side * s.side;
		}
	else if(shape instanceOf Rectangle) {
			Rectangle r = (Rectangle)shape;
			return r.height * r.width;
		}
	else if(shape instanceOf Circle) {
			Circle c = (Circle)shape;
			return PI * c.radius * c.radius
		}
	}
}
```

→ 둘레 길이를 구하는 perimeter() 함수를 새롭게 추가해도 도형 클래스는 아무런 영향을 받지 않는다. 반대로 새 도형을 추가하려면 Geometry 클래스에 속한 함수를 모두 고쳐야 한다.

**객체 지향적인 도형 클래스**

```java
// 객체적인 도형 클래스
public class Square implements Shape {
	public Point topLeft;
	public double side;

	public double area() {
		return side*side;
	}
}

public class Rectangle implements Shape {
	public Point topLeft;
	public double height;
	public double width;

	public double area() {
		return height*width;
	}
}

public class Circle implements Shape {
	public Point center;
	public double radius;
	public double width;

	public double area() {
		return PI*radius*radius;
	}
}
```

→ 새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않는다. 반면 새 함수를 추가하고 싶다면 도형 클래스를 전부 고쳐야 한다.

### 디미터 법칙

> 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다. 즉, 객체는 조회 함수로 내부 구조를 공개하면 안된다.
> 

클래스 C의 메서드 f는 다음과 같은 객체의 메서드만 호출해야 한다.

- 클래스 C
- f가 생성한 객체
- f 인수로 넘어온 객체
- C 인스턴스 변수에 저장된 객체

위 객체에서 허용된 메서드가 반환하는 객체의 메서드는 호출하면 안 된다.

- 기차 충돌
    
    ```java
    final String outputDIr = ctxt.getOptions().getScratchDir().getAbsolutePath();
    ```
    
    이와 같은 코드를 기차 충돌이라 부른다. 일반적으로 조잡하다 여겨지는 방식으로 피하는 편이 좋다. 아래의 코드와 같이 나누는 편이 좋다.
    
    ```java
    Options opts = ctxt.getOptions();
    File scratchDir = opts.getScratchDir();
    String outputDir = scratchDir.getAbsolutePath();
    ```
    
    → 해당 예제가 객체라면 디미터 법칙 위반, 자료 구조라면 디미터 법칙 적용 X
    
    ```java
    final String outputDir = cxtx.opts.scratchDir.absolutePath;
    ```
    
    → 디미터 법칙을 거론할 필요없이 자료 구조는 무조건 함수 없이 공개 변수만 포함하고, 객체는 비공개 변수와 공개 함수를 포함한다.
    
- 잡종 구조
    
    때때로 절반은 객체, 절반은 자료 구조인 잡종 구조가 나올 수 있다. 이런 잡종 구조는 양쪽에서 단점만 모아놓은 구조다. 그러므로 잡종 구조는 되도록 피하는 편이 좋다.
    
    - ex) 중요한 기능을 수행하는 함수, 공개 변수, getter/setter 함수가 모두 뒤섞인 구조
- 구조체 감추기
    
    ```java
    BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
    ```
    
    디렉토리 경로를 얻는 이유는 임시 파일을 생성하기 위해서이다! 
    
    → 그렇다면, ctxt 객체에게 임시 파일 생성을 맡기면?
    
    → ctxt 객체는 내부구조를 드러내지 않으며, 모듈에서 함수는 자신이 몰라야 하는 여러 객체를 탐색할 필요가 없다.
    

### 자료 전달 객체

**DTO**

- 자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다. 이런 자료 구조체를 때로는 자료 전달 객체(Data Transfer Object)라 한다.
- DTO는 데이터베이스에 저장된 가공되지 않은 정보를 애플리케이션 코드에서 사용할 객체로 변환하는 일련의 단계에서 가장 처음으로 사용하는 구조체다.

**Bean**

- Bean 구조는 좀 더 일반적이다. 비공개 변수를 getter/setter 함수로 조작한다.

**활성 레코드** 

- 활성 레코드는 DTO의 특수한 형태다. 공개 변수가 있거나 비공개 변수에 getter/setter 설정 함수가 있는 자료 구조지만, save나 find와 같은 탐색 함수도 제공한다.
- 활성 레코드는 데이터베이스 테이블이나 다른 소스에서 자료를 직접 변환한 결과다.
- 이 활성 레코드에 비즈니스 규칙 메서드를 추가해 객체로 취급하면 잡종 구조가 나올 뿐이다. 활성 레코드는 자료 구조로 취급해야 한다.

### 결론

- 객체는 동작을 공개하고 자료를 숨긴다.
    
    → 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기 쉽다. 
    
    → 기존 객체에 새 동작을 추가하기는 어렵다.
    
- 자료 구조는 별다른 동작 없이 자료를 노출한다.
    
    → 기존 자료 구조에 새 동작을 추가하기는 쉽다. 
    
    → 기존 함수에 새 자료 구조를 추가하기는 어렵다.
    

⇒ 즉, 우수한 소프트웨어 개발자는 편견없이 직면한 문제에 최적인 해결책을 선택한다.

```
💡 6장 후기

아무래도 자바로 개발을 하다 보면 객체지향 프로그래밍에만 집착하게 되고 다른 프로그래밍 기법은 그닥 관심이 없었던 것 같다. 미래엔 함수형 프로그래밍이 대세가 될거라는 이야기도 많이 돌고 있다. 
나는 아직은 OOP에 대해 깊이를 쌓아가야 할 시기는 맞다. 하지만 이런 시대의 흐름을 보면 하나에만 매몰되어 머리가 굳어버린 개발자가 되지 않도록 항상 경계해야 할 것 같다. 
이 장단점들을 잘 파악해두어 적합한 해결책을 고를 줄 아는 유연성있는 프로그래머가 되도록 노력해야겠다.
```

<br>

## 7장. 오류 처리

뭔가 잘못될 가능성은 늘 존재한다. 오류 처리는 프로그램에 반드시 필요한 요소 중 하나다. 깨끗한 코드와 오류 처리는 확실히 연관성이 있다.

### Try-Catch-Finally 문부터 작성하라

어떤 면에서 try 블록은 트랜잭션과 비슷하다. try 블록에서 무슨 일이 생기든지 catch 블록은 프로그램 상태를 일관성있게 유지해야 한다.

→ 예외가 발생할 코드를 짤 때는 try-catch-finally 문으로 시작하는 편이 낫다.

**파일이 없으면 예외를 던지는지 알아보는 단위 테스트**

```java
@Test(expected = StorageException.class)
public void retrieveSectionShouldThrowOnInvalidFileName() {
	sectionStore.retrieveSection("invalid - file");
}
```

**단위 테스트에 맞춰 구현한 코드**

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
  try{
    FileInputStream stream = new FileInputStream(sectionName);
    stream.close();
  } catch (FileNotFoundException e) {
    throw new StorageException("retrieval error", e);
  }
  return new ArrayList<RecordedGrip>();
}
```

1. 강제로 예외를 일으키는 테스트 케이스를 작성한 후 테스트를 통과하게 코드를 작성한다.
    
    → try 블록의 트랜잭션 범위부터 구현하게 되므로 범위 내에서 트랜잭션 본질을 유지하기 쉬워진다.
    
2. try-catch 구조로 범위를 정의한 후, TDD를 사용해 필요한 나머지 논리를 추가한다.

### 미확인 unchecked 예외를 사용하라

checked exception은 OCP를 위반한다.

→ 하위 단계에서 코드를 변경하면 상위 단계 메서드 선언부를 전부 고쳐야 한다.

→ 최하위 함수를 변경해 새로운 오류를 던지면 연쇄적으로 최상위 함수까지 수정이 일어난다.

throws 경로에 위치하는 모든 함수가 최하위 함수에서 던지는 예외를 알아야 하므로 캡슐화도 깨진다.

| 구분 | Checked Exception | Unchecked Exception |
| --- | --- | --- |
| 상속 | RuntimeException을 상속하지 않음 | RuntimeException을 상속함 |
| 예외 처리 | 반드시 해야 함 (컴파일 오류 발생 O) | 개발자 선택 (컴파일 오류 발생 X) |
| 발생 원인 | 프로그램 설계 시 예상 가능한 에러 | 프로그램 실행 중 예상치 못한 에러 |
| 예시 | IOException, SQLException | NullPointerException, ArrayIndexOutOfBoundsException |

### 예외에 의미를 제공하라

오류가 발생한 원인과 위치를 찾기 쉽게 해라 

→ 오류 메시지에 정보를 담아 예외와 함께 던진다. (+ 실패한 연산 이름과 실패 유형)

### 호출자를 고려해 예외 클래스를 정의하라

오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 **오류를 잡아내는 방법**이 되어야 한다.

**오류를 형편없이 분류한 사례**

: 외부 라이브러리가 던질 예외를 전부 잡아낸다.

```java
ACMEPort port = new ACMEPort(12);

try{
	  port.open();
} catch (DeviceResponseException e) {
		reportPortError(e);
} catch (ATM1212UnlockedException e) {
		reportPortError(e);
} catch (GMXError e) {
		reportPortError(e);
} finally {
	...
}
```

**해결**

: 호출하는 라이브러리 API를 감싸서 예외 유형 하나를 반환한다.

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
```

```java
public class LocalPort {
  private ACMEPort innerPort;

  public LocalPort(int portNumber) {
    innerPort = new ACMEPort(portNumber);
  }

  public void open() {
    try{
      innerPort.open();
    } catch (DeviceResponseException e) {
      throw new PortDeviceFailure(e);
    } catch (ATM1212UnlockedException e) {
      throw new PortDeviceFailure(e);
    } catch (GMXError e) {
      throw new PortDeviceFailure(e);
    }
  }

  ...
}
```

외부 API를 사용할 때는 감싸기(wrapper) 기법이 최선이다. 

1. 외부 API 감싸면 외부 라이브러리와 프로그램 사이에서 의존성이 크게 줄어든다. 
2. wrapper 클래스에서 외부 API를 호출하는 대신 테스트 코드를 넣어주는 방법으로 프로그램을 테스트하기 쉬워진다.
3. 특정 업체가 API를 설계한 방식에 발목잡히지 않고, 프로그램이 사용하기 편리한 API를 정의할 수 있다.

### 정상 흐름을 정의하라

때로는 중단이 적합하지 않은 때도 있다. 그럴 때는 특수 사례 패턴(Spencial Case Pattern)을 적용한다.

- 특수 사례 패턴: 으로 클래스를 만들거나 객체를 조작해 특수사례를 처리한다. (클래스나 객체가 예외적인 상황을 캡슐화하여 처리)
    
    → 클라이언트 코드가 예외적인 상황을 처리할 필요가 없어진다.
    

**총계를 계산하는 코드**

식비를 비용으로 청구하지 않아 예외가 발생한 경우 일일 기본 식비를 더한다.

```java
try {
	MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
	m_total += expenses.getTotal();
} catch(MealExpensesNotFound e) {
	m_total += getMealPerDiem();
}
```

**해결**

`ExpenseReportDAO`를 고쳐 언제나 MealExpense 객체를 반환한다. 청구한 식비가 없을 땐 일일 기본 식비를 반환하는 MealExpense 객체를 반환한다.

```java
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
	m_total += expenses.getTotal();
```

```java
public class PerDiemMealExpenses implements MealExpenses {
	public int getTotal() {
		// 기본값으로 일일 기본 식비를 반환한다.
	}
}
```

### null을 반환하지 마라

null을 반환하는 습관은 나쁘다. null을 반환하는 코드는 호출자에게 문제를 떠넘겨 누구 하나라도 null 확인을 빼먹는다면 애플리케이션에 큰 문제가 발생한다.

- null 대신 예외를 던지거나 특수 사례 객체를 반환해라.
- 사용하려는 외부 API가 null을 반환한다면 wrapper 메서드를 구현해 동일하게 예외를 던지거나 특수 사례 객체를 반환하게 해라.

→ 많은 경우에 특수 사례 객체가 손쉬운 해결책이다.

**메서드가 null을 반환하는 코드**

```java
List<Employee> employees = getEmployees();
if (employees != null) {
	for(Employee e : employees) {
		totalPay += e.getPay();
	}
}
```

**해결**

null 반환 → 빈 리스트 반환 변경

```java
List<Employee> employees = getEmployees();
for(Employee e : employees) {
	totalPay += e.getPay();
}

public List<Employee> getEmployees() {
	if ( ..직원이 없다면.. ) {
		return **Collections.emptyList()**;
	}
}
```

→ 코드도 깔끔해지고, NullPointerException이 발생할 가능성도 줄어든다.

### null을 전달하지 마라

메서드에서 null을 반환하는 방식도 나쁘지만 null을 전달하는 방식은 더 나쁘다. 인수로 null을 전달하면 NullPointerException이 발생할 수 밖에 없다.

메서드로 null을 전달하는 코드는 **최대한** 피한다.

### 결론

깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.

오류 처리를 프로그램 논리와 분리하면 독립적인 추론이 가능해지고, 코드 유지보수성이 크게 높아진다.
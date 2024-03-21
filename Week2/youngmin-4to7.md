## 4장 주석

부정확한 주석은 아예 없는 주석보다 훨씬 더 나쁘다.

잘 달린 주석은 그 어떤 정보보다 유용하다. 하지만 중요한 만큼 근거없는 주석은 코드를 이해하기 어렵게 만든다. 오래되고 조잡한 주석은 거짓과 잘못된 정보를 퍼뜨려 해악을 미친다. 

프로그래밍 언어를 치밀하게 사용해 의도를 표현할 능력이 있다면 주석은 거의 필요하지 않다고 말한다. 

주석을 많이 달기 보다는 코드를 깔끔하게 정리하고 표현력을 강화하는 방향으로, 애초에 주석이 필요 없는 방향으로 에너지를 쏟고 노력하는 것이 더 좋다.

## 주석은 나쁜 코드를 보완하지 못한다.

코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문이다. 모듈을 짜고 보니 짜임새가 엉망이고 알아먹기 어렵고 지저분해 주석을 달아야한다 생각한다. 하지만 표현력이 뛰어나고 깔끔한 코드가 복잡하고 어수선하며 주석이 많이 달린 코드보다 좋다. 그 어수선한 코드를 깔끔하게 새로 만드는데 시간을 투자하자!

## 코드로 의도를 표현하라

코드만으로 의도를 설명하기 어려운 경우가 존재한다. 하지만 조금만 더 생각하면 코드로 대다수의 의도를 표현할 수 있다. 많은 경우 주석으로 달려는 설명을 함수로 만들어 표현해도 충분하다.

## 좋은 주석

### 법적인 주석

회사가 정립한 구현 표준에 맞게 법적인 이유로 특정 주석을 넣으라고 명시한다. 

저작권 정보와 소유권 정보

### 정보를 제공하는 주석

기본적인 정보를 주석으로 제공하면 편리하다.

하지만 언제나 최대한 코드로 의도 표현하는 것이 좋다.

### 의도를 설명하는 주석

주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명한다. 코드를 작성한 저자의 의도는 분명히 드러난다.

### 의도를 명료하게 밝히는 주석

모호한 인수나 반환값은 그 의미를 읽기 좋게 표현하면 이해하기 시워진다.

인수나 반환값 자체를 명확하게 만들면 좋겠지만, 인수나 반환값이 표준 라이브러리나 변경하지 못하는 코드에 속한다면 의미를 명료하게 밝히는 주석이 유용하다.

### 결과를 경고하는 주석

다른 프로그래머에게 결과를 경고 할 목적으로 주석을 사용한다.  특정 테스트 케이스를 꺼야 하는 이유를 설명하는 주석 같은 예가 있다.

요즘에는 @Ignore 어노테이션을 이용해 테스트 케이스를 꺼버린다. 

```java
public static SImpleDateFormat makeStandardHttpDateFormat()
{
	// SimpleDataFormat은 스레드에 안전하지 못하다.
	// 따라서 각 인스턴스르 독립적으로 생성해야 한다.
	
	SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
	df.setTimeZone(TimeZone.getrTimeZone("GMT"));
	return df;
}
```

하지만 더 나은 해결책이 있을 것이다.

### TODO 주석

앞으로 할일을 TODO 주석으로 남겨두면 편하다. 

```java
// TODO-MdM 현재 필요하지 않다.
// 체크아웃 모델을 도입하면 함수가 필요 없다.
protected VersionInfo makeVersion() throws Exception {
    return null;
}
```

필요하다 여기지만 당장 구현하기 어려운 업무를 기술한다. 하지만 어떤 용도로 사용하든 시스템에 나쁜 코드를 남겨 놓는 핑계가 되어서는 안된다.

### 중요성을 강조하는 주석

자칫 대수롭지 않다고 여겨질 뭔가의 중요성을 강조하기 위해서도 주석을 사용한다.

```java
String listItemContent = match.group(3).trim();
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다.
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

### 공개 API에서 Javadocs

설명이 잘 된 공개 API는 참으로 유용하여 공개 API를 구현한다면 반드시 훌륭한 Javadocs를 작성한다. 

## 나쁜 주석

대다수 주석이 이 범주에 속한다. 일반적으로 대다수 주석은 허술한 코드를 지탱하거나, 엉성한 코드를 변명하거나, 미숙한 결정을 합리화하는 등 프로그래머가 주절거리는 독백에 불과하다.

### 주절거리는 주석

특별한 이유 없이 의무감으로 혹은 프로세스에서 하라고 하니 마지못해 주석을 단다면 전적으로 시간낭비다.

주석을 달기로 결정했다면 충분한 시간을 들여 최고의 주석을 달도록 노력한다.

### 같은 이야기를 중복하는 주석

주석이 코드보다 더 많은 정보를 제공하지 못한다. 코드를 정당화하는 것도 아니고, 의도나 근거를 설명하는 주석도 아니다. 쓸모없고, 중복된 Javadocs가 매우 많다.

### 오해할 여지가 있는 주석

```java
// this.closed가 true일 때 반환되는 유틸리티 메서드다.
// 타임아웃에 도달하면 예외를 던진다.
public synchronized void waitForClose(final long timeoutMillis) throws Exception {
    if (!closed) {
        wait(timeoutMillis);
        if (!closed)
            throws new Exception("MockResponseSend could not be close");
    }
}
```

의도와 다르게 주석을 달지 못할 때가 있다. 주석에 잘못된 정보로 인해 본인이 작성한 코드가 이상하게 동작하거나 느려질 때 그것을 찾아 내는데 어려움을 겪을 수 있다.

### 의무적으로 다는 주석

모든 함수에 Javadocsfmf 달거나, 모든 변수에 주석을 달아야 한다는 규칙은 옳지 않다. 

이러한 주석들은 코드를 복잡하게 만들고, 아무 가치 없이 오히려 코드만 헷갈리게 만들어 잘못된 정보를 제공할 여지를 만들 수 있다.

### 함수나 변수로 표현할 수 있다면 주석을 달지마라

```java
// 전역 목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))
```

```java
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
```

주석에 적어두지 말고, 코드로 표현할 수 있다면 적극적으로 코드로 개선하는 것이 좋다.

### 주석으로 처리한 코드

주석으로 처리한 코드를 절대 작성하지 말자. 주석으로 처리한 코드는 다른 사람들이 지우기를 주저한다.

쓸모 없는 코드가 점점 쌓여가게 된다.

```java
InputStreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream(), formatter.getByteCount());
// InputStream resultsStream = formatter.getResultStream();
// StreamReader reader = new StreamRead(resultsStream);
// response.setContent(reader.read(formatter.getByteCount()));
```

## 5장 형식 맞추기

## 형식을 맞추는 목적

코드 형식은 너무 중요해서 무시하기 어렵다. 융통성 없이 맹목적으로 따르면 안된다. 코드 형식은 **의사소통의 일환**이다.  ‘돌아가는 코드’가 전문 개발자의 일차적인 의무라 여길지도 모르겠지만 의사소통이 전문 개발자의 일차적인 의무라고 생각이 바뀔 것이다. 

오늘 구현한 기능이 다음 버전에서 바뀔 확률이 아주 높다는 걸 고려하면 오늘 구현한 코드의 가독성은 앞으로 바뀔 코드의 품질에 지대한 영향을 미친다. 

구현 스타일과 가독성 수준은 유지보수 용이성과 확장성에 영향을 미친다.

## 적절한 행 길이를 유지하라

JUnit, FitNesse, testNG, Time and Money, JDepend, Ant, Tomcat 7개의 프로젝트의 세로 길이에 대한 세로 길이 소스코드를 통계낸 결과 평균 파일크기는 약 65줄이고, 전체 파일 중 대략 1/3이 40줄에서 100줄 조금 넘는 정도다. 가장 긴 파일은 약 400줄 즉, 세로 위치가 조금만 차이나도 실제 크기는 크게 달라진다. 500줄을 넘어가는 파일이 없고, 대다수가 200줄 미만이다. (Tomcat, Ant는 절반 이상이 200줄을 너머서고, 심지어 수춘 줄이 넘어가는 파일도 있다.) 이 정리가 말하고자 하는 것은 200줄 정도인 파일로도 커다란 시스템을 구축할 수 있다는 사실이다.

## 신문 기사처럼 작성하라

좋은 신문 기사처럼 좋은 코드는 위에서 아래로 첫 부분은 고차원 개념과 알고리즘을 설명하고, 클래스, 함수, 변수의 이름만 보고도 올바른 모듈을 살펴보고 있는지 파악할 수 있도록 아래로 내려갈수록 의도를 세세하게 묘사한다. 신문이 사실, 날짜, 이름 등을 무작위로 뒤썪은 긴 기사 하나만 싣는다면 아무도 읽지 않을 것이다.

## 개념은 빈 행으로 분리하라

거의 모든 코드는 왼쪽에서 오른쪽으로 위에서 아래로 읽힌다. 각 행은 수식이나 절을 나타내고, 일련의 행 묶음은 완련된 생각 하나를 표현한다. 생각 사이는 빈 행을 넣어 분리해야 마땅하다.

## 세로 밀집도

줄바꿈이 개념을 분리한다면 세로 밀집도는 연관성을 의미한다. 즉 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다는 뜻

## 수직 거리

함수 연관 관계와 동작 방식을 이해하려고 이 함수에서 저 함수로 오가며 소스 파일을 여기저기 찾아 헤매다 혼란이 가증된 경험이 있을 것이다. 

서로 밀접한 개념은 세로로 가까이 둬야한다. 같은 파일에 속할 정도로 밀접한 두 개념은 세로 거리로 연관성을 표현한다. 여기서 **연관성이란 한 개념을 이해하는데 다른 개념이 중요한 정도다.**

**변수**는 사용하는 위치에 최대한 가까이 선언한다. 함수는 짧게 구현해야 하므로 지역 변수는 각 함수 맨 처음에 선언한다.

**인스턴스 변수**는 클래스 맨 처음에 선언한다. 변수 간에 세로로 거리를 두지 않는다. 

자바에서는 보통 클래스 맨 처음에 인스턴스 변수를 선언한다.

**종속 함수** 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다. 

가능하다면 호출하는 함수를 호출되는 함수보다 먼저 배치한다. 

개념적 친화도가 높을 수록 가까이 배치한다. 한 함수가 다른 함수를 호출해 생기는 종속성이 한 예다. 

```java
public class Assert {
    static public void assertTime(String message, boolean condition) {
        if (!condition)
            fail(message);
    }
    
    static public void assertTrue(boolean condition) {
        assertTrue(null, condition);
    }
    
    static public void assertFalse(String message, boolean condition) {
        assertTrue(message, !condition);
    }
    
    static public void assertFalse(boolean condition) {
        assertFalse(null, condition);
    }
    
    /* ... */
}
```

## 세로 순서

일반적으로 함수 호출 종속성은 아래 방향으로 유지한다. 호출되는 함수보다 나중에 배치한다. 소스 코드 모듈이 고차원에서 저차원으로 자연스럽게 내려간다. 

신문 기사와 마찬가지로 가장 중요한 개념을 가장 먼저 표현한다. 

가장 중요한 개념을 표현할 때는 세세한 사항을 최대한 배제한다. 

세세한 사항은 가장 마지막에 표현한다.

## 가로형식

일반적으로 코드 가로 형식의 길이는 100자 ~ 120자 정도로 제한한다. 

## 가로 공백과 밀집도

가로로는 공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다.

```java
private void measureLine(String line) {
    lineCount++;
    int lineSize = line.length();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
}
```

할당 연산자를 강조하기 위해 앞 뒤에 공백을 줬다. 공백을 넣으면 두 가지 주요 요소가 확실히 나뉜다는 사실이 분명해진다. 

함수 이름과 이어지는 괄호 사이에는 공백을 넣지 않았다. 

**함수와 인수는 서로 밀접하기 때문**

함수를 호출하는 코드에서 괄호 안 인수는 공백으로 분리했다. 

**쉼표를 강조해 인수가 별개라는 사실을 보여주기 위해서**

```java
public class Quadratic {
    public static double root1(double a, double b, double c) {
        double determinant = determinant(a, b, c);
        return (-b + Math.sqrt(determinant) / (2*a));
    }
    private static double determinant(double a, double b, double c) {
        return b*b - 4*a*c;
    }
}
```

연산자 우선순위를 강조하기 위해서도 공백을 사용한다. 곱셈의 우선순위가 가장 높기 때문에 승수 사이에는 공백이 없고, 항 사이에는 공백이 들어간다.

## 들여쓰기

코드의 범위로 이뤄진 계층을 표현하기 위해 코드를 들여쓴다.

들여쓰는 정도는 계층에서 코드가 자리잡은 수준에 비례한다. 

들여쓰기 한 파일은 구조가 한눈에 들어온다. 변수, 생성자 함수, 접근자 함수, 메소드가 금방 보인다.

## 팀규칙

팀은 한 가지 규칙에 합의해야 한다. 모든 팀원은 그 규칙을 따라야 한다. 

좋은 소프트웨어 시스템은 읽기 쉬운 문서로 이뤄진다는 사실을 기억해야하고, 스타일은 일관적이고 매끄러워야 한다.

<aside>
💡 좋은 프로그램, 좋은 시스템을 만들기 위해선 프로그래머 들 간에 의사소통, 곧 코드의 형식이 올바른 규칙과 목적에 맞게 이뤄져야 한다는 것을 느낀다.
’신문 기사처럼 작성하라’ 파트에 내용처럼 작성된 코드가 어떤 역할을 나타내는지 그 코드의 개념과 알고리즘을 설명하는 것. 다른 팀원이 내가 작성한 코드를 들여다 봤을 때 한눈에 의미가 파악이 되는 세세하게 작성된 코드, 나아가 팀 전체의 규칙을 통해서 각 함수들의 개념이 한눈에 파악할 수 있는 규칙을 만든다고 한다면 지금보다 훨씬 더 빠르고, 좋은 프로그램을 만들 수 있는 기반이 될 것이라고 생각한다.

</aside>

## 6장 객체와 자료구조

변수를 비공개로 정의하는 이유가 있다. 남들이 변수에 의존하지 않게 만들고 싶어서다.

## 자료 추상화

변수를 private으로 선언하더라도 각 값마다 GET(조회), SET(설정) 함수를 제공한다면 구현을 외부로 노출하는 셈이다.

구현을 감추려면 추상화가 필요하다. 그저 형식에 치우쳐 조회 함수와 설정 함수로 변수를 다룬다고 클래스가 되지 않는다. 

그보다는 **추상 인터페이스를 제공해 사용자가 구현을 모른채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.**

자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다.

```java
public interface Vehicle{
		double getPercentFuelRemaining();
}
```

개발자는 객체가 포함하는 자료를 표현할 가장 좋은 방법을 심각하게 고민해야 한다. 

## 자료/객체 비대칭

객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.

묻지 말고 시켜라

자료 구조는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다. 

이 두가지는 본질적으로 상반된다.

```java
public class Square {
    public Point topLeft;
    public double side;
}

public class Rectangle {
    public Point topLeft;
    public double height;
    public double sidth;
}

public class Circle {
    public Point center;
    public double radius;
}

public class Geometry {
    public final double PI = 3.14;
    
    public double area(Object shape) throws NoSuchShapeException {
        if (shape instanceof Square) {
            /* ... */
        }
        else if (shape instanceof Square) {
            /* ... */
        }
        else if (shape instanceof Square) {
            /* ... */
        }
    }
}
```

만약 Geometry 클래스에 둘레 길이를 구하는 perimeter() 함수를 추가하고 싶은 경우, 도형 클래스는 아무 영향도 받지 않는다. 반대로 새 도형을 추가하고 싶다면 Geometry 클래스에 속한 함수를 모두 고쳐야 한다. 두 조건은 완전히 정반대다.

```java
public class Square implemnets Shape {
    private Point topLeft;
    private double side;
    
    public double area() {
        return side*side;
    }
}

public class Rectangle implements Shape {
    private Point topLeft;
    private double height;
    private double width;
    
    public double area() {
        return height * width;
    }
}

public class Circle implements Shape {
    private Point center;
    private double radius;
    public final double PI = 3.14;
    
    public double area() {
        return PI * radius * radius;
    }
}
```

area() 함수는 다형(Polymorphic) 메소드다. Geometry 클래스는 필요 없다. 그러므로 새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않는다. 반면 새 함수를 추가하고 싶다면 도형 클래스 전부를 고쳐야 한다.

(자료구조를 사용하는) 절차적인 코드는 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다. 반면, 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.

반대로 절차적인 코드는 새로운 자료 구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다. 객체 지향 코드는 새로운 함수를 추가하기 어렵다. 그러려면 클래스를 고쳐야 한다.

## 디미터 법칙

잘 알려진 휴리스틱으로 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙이다. 객체는 자료를 숨기고 함수를 공개한다. 즉, 객체는 조회 함수로 내부 구조를 공개하면 안 된다는 의미다. 내부 구조를 노출하는 셈이  되니깐. 

정확히 표현하자면 디미터 법칙은 “클래스 C의 메소드 f는 다음과 같은 객체의 메소드만 호출해야 한다”고 주장한다

- 클래스 C
- f가 생성한 객체
- f 인수로 넘어온 객체
- C 인스턴스 변수에 저장된 객체

하지만 위 객체에서 허용된 메소드가 반환하는 객체의 메소드는 호출하면 안된다.

```java
final String outtputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

## 기차 충돌

위와 같은 코드를 기차 충돌이라 부른다.

일반적으로 피하는 편이 좋다. 아래와 같이 나누는 편이 좋다.

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsoulutePath();
```

위 코드는 함수 하나가 아는 지식이 굉장히 많다. 위 코드를 사용하는 함수는 많은 객체를 탐색할 줄 안다는 말. 이 예제가 디미터 법칙을 위반하는지 여부는 각 클래스가 객체인지 아니면 자료구조인지에 따라 달라진다. 객체라면 내부 구조를 숨겨야 하므로 디미터 법칙을 위반하고 자료 구조라면 내부 구조를 노출하므로 디미터 법칙이 적용되지 않는다.

## 잡종 구조

때때로 절반은 객체, 절반은 자료 구조인 잡종 구조가 나온다. 잡종 구조는 중요한 기능을 수행하는 함수도 있고, 공개 변수나 공개 조회/설정 함수도 있다. 

공개 조회/설정 함수는 비공개 변수를 그대로 노출한다. 이런 구조는 새로운 함수는 물론이고 새로운 자료 구조도 추가하기 어려워 파하는 것이 좋다.

## 구조체 감추기

```java
final String outtputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

위 코드에서 각 클래스들이 객체라면, 줄줄이 사탕으로 엮으면 안된다.

```java
ctxt.getAbsolutePathOfScratchDirectoryOption();
```

- ctxt 객체에 공개해야 하는 메소드가 너무 많아진다.

```java
ctxt.getScratchDirectoryOption().getAbsolutePath();
```

getScratchDirectoryOption()이 객체가 아니라 자료 구조를 반환한다고 가정하자.

두 방법 모드 내키지 않는다. ctxt가 객체라면 무언가를 하라고 말해야지 속을 드러내라고 말하면 안된다. 

```java
String outFile = outputDir + "/" + className.replace('.','/') + ".class";
FileOutputStream fout = new FileOutputStream(outFile);
BufferedOutputStream bos = new BufferedOutputStream(fout);
```

추상화 수준을 뒤섞어 놓아 다소 불편하다. 점, 슬래시, 파일 확장자, File 객체를 부주의하게 마구 뒤섞으면 안된다. 코드를 살펴보면 임시 디렉토리의 절대 경로를 얻으려는 이유가 임시 파일을 생성하기 위한 목적이라는 것을  알 수 있다.

ctxt 객체에 임시 파일을 생성하라고 시키면 아래 코드가 적당해 보인다.

```java
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```

ctxt는 내부 구조를 드러내지 않으며, 모듈에서 해당 함수는 자신이 몰라야 하는 여러 객체를 탐색할 필요가 없다. 디미터 법칙을 위반하지 않는다.

## 자료 전달 객체

자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다. 이런 구조를 DTO라고 한다. DTO는 데이터베이스에 저장된 가공되지 않은 정보를 애플리케이션 코드에서 사용할 객체로 변환하는 일련의 단계에서 가장 처음으로 사용하는 구조체다.

<aside>
💡 새로운 자료 타입 추가 대한 유연성이 필요할 때는 `객체`, 새로운 동작을 추가하는 유연성이 필요할 때는 `자료구조`와 `절차적인 코드`가 적합하다.

상황마다 적절한 방법을 선택하여 코드를 작성하면 최적의 결과를 가지고 올 수 있을 것 같다.

특히 객체 지향 언어의 추상화와 다형성에 대한 내용을 좀 더 심화적으로 이해해야 할 필요가 있다고 생각했다.
추상클래스와 인터페이스를 사용하여 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야  하고 객체는 추상화 뒤로 자료를 숨겨 자료를 다루는 함수만을 공개하자.
getter / setter를 만들어서 구현을 외부로 노출시켜 접근 / 제어가 가능하면 의존성이 생기고 결합도가 높아져 변경이 어려워지게 된다. 
변경으로 인한 영향을 최소화할 수 있는 인터페이스 구현을 통한 활용 방법을 계속해서 고민하고, 최선의 해결책을 선택해야겠다.

</aside>

## 7장 오류 처리

## 오류 코드보다 예외를 사용하라

```java
public class Device Controller {
    public void sendShutDown() {
        DeviceHandle handle = getHandle(DEV1);
        if (handle != DeviceHandle.INVALID) {
            retrieveDeviceRecord(handle);
            if (recode.getStatus() != DEVICE_SUSPENDED) {
                pauceDevice(handle);
                clearDeviceWorkQueue(handle);
                closeDevce(handle);
            }
            else {
                logger.lod("Device suspended.");
            }
        }
        else {
            logger.log("Invalid handle for: " + DEV1.toString());
        }
    }
}
```

호출자 코드가 너무 복잡하다. 함수를 호출한 즉시 오류를 확인해야 하기 때문이다. 오류가 발생하면 예외를 던지는 편이 낫다. 그렇게되면 호출자 코드가 더 깔끔해진다. 논리가 오류 처리 코드와 뒤섞이지 않게 되기 때문에

```java
public class DeviceController {
    public void sendShutDown() {
        try {
            tryToShutDown();
        } catch (DeviceShutDownError e) {
            logger.log(e);
        }
    }
    private void tryToShutDown() throws DeviceShutDownError {
        DeviceHandle handle = getHandle(DEV1);
        DeviceRecord record = retrieveDeviceRecord(handle);
        
        pauseDevice(handle);
        clearDeviceWorkQueue(handle);
        closeDevice(handle);
    }
    
    private DeviceHandle getHandle(DeviceID id) {
        throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    }
}
```

## Try-Catch-Finally 문부터 작성하라

어떤 면에서 try 블록은 트랜잭션과 비슷하다. try 블록에서 무슨 일이 생기든지 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다. 예외가 발생할 코드를 짤 때는 try-catch-finally 문으로 시작하는 편이 낫다.

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
    try {
        FileInputStream stream = new FileInputStream(sectionName);
        stream.close();
    } catch (FileNotFoundException e) {
        throw new StorageException("retrieval error", e);
    }
    return new ArrayList<RecordedGrip>();
}
```

try-catch 구조로 범위를 정의했으므로 TDD를 사용해 필요한 나머지 논리를 추가한다.

먼저 강제로 예외를 일으키는 테스트 케이스를 작성한 후 테스트를 통과하게 코드를 작성하는 방법을 권장한다.

자연스럽게 try 블록의 트랜잭션 범위부터 구현하게 되므로 범위 내에서 트랜잭션 본질을 유지하기 쉬워진다.

## 미확인 예외를 사용하라

확인된 오류가 치르는 비용에 상응하는 이익을 제공하는지 철저하게 따져봐야 한다.

확인된 예외는 OCP(Open Close Principle)를 위반한다. 메소드에서 확인된 예외를 던졌는데 catch 블록이 세 단계 위에 있다면 그 사이 메소드 모두가 선언부에 해당 예외를 정의해야 한다.

즉, 하위 단계에서 코드를 변경하면 상위 단계 메소드 선언부를 전부 고쳐야 한다는 말이다. 

1. **확인된 예외 (Checked Exception)**:
    - 컴파일러가 체크하고 처리를 강제하는 예외.
    - **`RuntimeException`** 클래스를 상속하지 않는 예외는 모두 확인된 예외.
    - 예를 들어, 파일을 읽는 동안 발생하는 **`FileNotFoundException`**이나 IO 동작 중에 발생하는 **`IOException`** 등이 여기에 해당한다.
    - 예외처리를 강제하기 때문에 메소드를 사용하는 개발자는 반드시 이 예외를 처리하도록 코드를 작성해야 한다.
2. **미확인 예외 (Unchecked Exception)**:
    - 컴파일러가 체크하지 않고 처리를 강제하지 않는 예외.
    - **`RuntimeException`** 클래스를 상속하는 예외들이 미확인 예외에 해당한다.
    - 예를 들어, 배열 인덱스 오류를 일으키는 **`ArrayIndexOutOfBoundsException`**이나 **`NullPointerException`** 등이 여기에 해당한다.
    - 예외처리를 강제하지 않기 때문에 예외가 발생하면 호출 스택을 따라올라가며 예외를 처리하는 곳이 없다면 프로그램이 중단된다.

## 예외에 의미를 제공하라

오류가 발생한 원인과 위치를 찾기 쉽도록 전후 상황을 충분히 붙혀 예외를 던지는 것이 좋다. 오류 메세지에 정보를 담아 예외와 함께 던진다. 실패한 연산 이름과 실패 유형도 언급한다.

## 호출자를 고려해 예외 클래스를 정의하라

오류를 분류하는 방법은 수없이 많다. 오류가 발생한 위치로 분류가 가능하다. 예를 들어, 오류가 발생한 컴포넌트로 분류한다. 혹은 유형으로도 분류가 가능하다. 

예) 디바이스 실패, 네트워크 실패, 프로그래밍 오류 등으로 분류 한다.

아래 코드는 오류를 형편없이 분류한 사례로, 외부 라이브러리가 던질 예외를 모두 잡아낸다.

```java
ACMEPort port = new ACMEPort(12);

try {
    port.open();
} catch (DeviceResponseException e) {
    reportPortError(e);
    logger.log("Device response exception", e);
} catch (ATM1212UnlockedException e) {
    reportPortError(e);
    logger.log("Unlock exception", e);
} catch (GMXError e) {
    reportPortError(e);
    logger.log("Device response exception");
} finally {
		...
}
```

위 경우는 예외에 대응하는 방식이 예외 유형과 무관하게 거의 동일하다. 그래서 코드를 간결하게 고칠 수 있다.

```java
LocalPort port = new LocalPort(12);
try {
    port.open();
} catch (PortDeviceFailure e) {
    reportError(e);
    logger.log(e.getMessage(), e);
} finally {
    /* ... */
}
```

```java
public class LocalPort {
    private ACMEPort innerPort;
    
    public LocalProt(int portNumber) {
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

LocalPort 클래스처럼 외부 API를 감싸는 클래스는 매우 유용하다. 외부 API를 감싸면 외부 라이브러리와 프로그램 사이에서 의존성이 크게 줄어든다. 나중에 다른 라이브러리로 갈아타더라도 비용이 적다.

## 정상 흐름을 정의하라

특수 사례 패턴이라고 부르는 방식으로, 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식을 사용하여 클라이언트 코드가 예외적인 상황을 처리할 필요가 없어지게 만든다. 클래스나 객체가 예외적인 상황을 캡슐화해서 처리하므로.

```java
try{
	MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
	m_total += expenses.getTotal();
}catch(MealExpensesNotFound e){
	m_total += getMealPerDiem();
}
```

```java
public class PerDiemMealExpenses implements MealExpenses{
	public int getTotal(){
		// 기본값으로 일일 기본 식비를 반환한다.
	}
}
```

## null을 반환하지 마라

null을 반환하는 코드는 일거리를 늘릴 뿐만 아니라 호출자에게 문제를 떠넘긴다.

누구 하나라도 null 확인을 빼먹는다면 애플리케이션이 통제 불능에 빠질지도 모른다.

메소드에서 null을 반환한다면 메소드를 구현해 예외를 던지거나 특수 사례 객체를 반환하는 방식을 고려한다.

```java
public List<Employee> getEmployees() {
    if (/* 직원이 없다면 */) {
        return Collections.emptyList();
    }
}
```

위처럼 null을 반환하는 대신 빈 컬렉션을 반환한다면 null 체크할 필요없이 코드가 훨씬 깔끔 질 뿐더러 NullPointerException이 발생할 가능성도 줄어든다.

## null을 전달하지 마라

정상적인 인수로 null을 기대하는 API가 아니라면 메소드로 null을 전달하는 코드는 최대한 피한다.

<aside>
💡 프로그램을 작성할 때 발생할 오류들에 대한 처리가 중요하고, 오류 코드보다는 예외 처리를 더욱 활용을 하자!
예외가 발생할 코드(파일, SQL)를 짤 때는 try-catch-fianlly를 먼저 시작하고, 예외들을 작성할 때도 예외 클래스를 정의하여 의존성을 낮추는 방식을 채택하여 예외를 처리해야겠다.
또한 확인된 예외를 사용할 때 OCP 규칙을 위반할 수 있기에 조금 더 신중하고, 미확인 예외를 조금 더 활용할 수 있는 그런 코드를 작성하는 방법을 사용해 구현하는 것이 일반적으로 좀 더 이익이 되는 코드가 되지 않을까 생각한다.

</aside>

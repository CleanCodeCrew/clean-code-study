## 15장 JUnit 들여다보기

JUnit은 자바 프레임워크 중에서 가장 유명하다. 프레임워크가 그렇듯 개념은 단순하며 정의는 정밀하고 구현은 우아하다.

## JUnit 프레임워크

자바 단위 테스트를 위한 테스팅 프레임워크

살펴볼 모듈은 ComparisonCompactor라는 모듈로 문자열 비교 오류를 파악할 때 유용한 코드

→ 두 문자열을 받아 차이를 반환 

ex) ABCDE와 ABXDE를 받아 <…B[X]D…>를 반환

assertTrue : `condition`이 `true`이면 테스트에 성공한다.

assertEquals :  `expected` 와 `actual` 의 값이 같으면 테스트에 성공한다

작성된 테스트 케이스가 코드 커버리지 분석을 수행 후 100%가 나온다면 모든 행, 모든 if문, 모든 for문을 실행한다는 의미

```java
package junit.tests.framework;
import junit.framework.ComparisonCompactor;
import junit.framework.TestCase;
public class ComparisonCompactorTest extends TestCase {
  public void testMessage() {
    String failure= new ComparisonCompactor(0, "b", "c").compact("a");
    assertTrue("a expected:<[b]> but was:<[c]>".equals(failure));
  }
  public void testStartSame() {
    String failure= new ComparisonCompactor(1, "ba", "bc").compact(null);
    assertEquals("expected:<b[a]> but was:<b[c]>", failure);
  }
  public void testEndSame() {
    String failure= new ComparisonCompactor(1, "ab", "cb").compact(null);
    assertEquals("expected:<[a]b> but was:<[c]b>", failure);
  }
  public void testSame() {
    String failure= new ComparisonCompactor(1, "ab", "ab").compact(null);
    assertEquals("expected:<ab> but was:<ab>", failure);
...
```

## ComparisonCompactor.java

```java
package junit.framework;
public class ComparisonCompactor {
  private static final String ELLIPSIS = "...";
  private static final String DELTA_END = "]";
  private static final String DELTA_START = "[";
  private int fContextLength;
  private String fExpected;
  private String fActual;
  private int fPrefix;
  private int fSuffix;
  public ComparisonCompactor(int contextLength,
                             String expected,
                             String actual) {
    fContextLength = contextLength;
    fExpected = expected;
    fActual = actual;
  }
  public String compact(String message) {
    if (fExpected == null || fActual == null || areStringsEqual())
      return Assert.format(message, fExpected, fActual);
    findCommonPrefix();
    findCommonSuffix();
    String expected = compactString(fExpected);
    String actual = compactString(fActual);
    return Assert.format(message, expected, actual);
  }
  private String compactString(String source) {
    String result = DELTA_START +
        source.substring(fPrefix, source.length() -
            fSuffix + 1) + DELTA_END;
    if (fPrefix > 0)
      result = computeCommonPrefix() + result;
    if (fSuffix > 0)
      result = result + computeCommonSuffix();
    return result;
  }
  private void findCommonPrefix() {
    fPrefix = 0;
    int end = Math.min(fExpected.length(), fActual.length());
    for (; fPrefix < end; fPrefix++) {
      if (fExpected.charAt(fPrefix) != fActual.charAt(fPrefix))
        break;
    }
  }
  private void findCommonSuffix() {
    int expectedSuffix = fExpected.length() - 1;
    int actualSuffix = fActual.length() - 1;
    for (;
         actualSuffix >= fPrefix && expectedSuffix >= fPrefix;
         actualSuffix--, expectedSuffix--) {
      if (fExpected.charAt(expectedSuffix) != fActual.charAt(actualSuffix))
        break;
    }
    fSuffix = fExpected.length() - expectedSuffix;
  }
  private String computeCommonPrefix() {
    return (fPrefix > fContextLength ? ELLIPSIS : "") +
        fExpected.substring(Math.max(0, fPrefix - fContextLength),
            fPrefix);
  }
  private String computeCommonSuffix() {
    int end = Math.min(fExpected.length() - fSuffix + 1 + fContextLength,
        fExpected.length());
    return fExpected.substring(fExpected.length() - fSuffix + 1, end) +
        (fExpected.length() - fSuffix + 1 < fExpected.length() -
            fContextLength ? ELLIPSIS : "");
  }
  private boolean areStringsEqual() {
    return fExpected.equals(fActual);
  }
}
```

- 코드가 잘 분리되어 있다.
- 표현력이 적절하다.
- 구조가 단순하다.
- 하지만 긴 표현식 몇개와 이상한 +1 등 눈에 띄는 부분이 있다.

보이스카우트 규칙에 따라 더 깨끗하게 해놓아야 한다. 

### 1. 멤버 변수 앞에 접두어 f 제거

변수 이름에 범위를 명시할 필요가 없다. 

접두어 f는 중복되는 정보다. 따라서 접두어 f를 모두 제거한다.

```java
  private int contextLength;
  private String expected;
  private String actual;
  private int prefix;
  private int suffix;

```

### 2. compact 함수 시작 부 - 캡슐화되지 않은 조건문의 캡슐화

의도를 명확히 표현하려면 조건문을 캡술화해야 한다. 즉, 조건문을 메소드로 뽑아내 적절한 이름을 붙인다.

```java
public String compact (String message) {
    if (shouldNotCompact())
        return Assert.format(message, expected, actual);
		....
	  ....
}
private boolean shouldNotCompact() {
    return expected == null || actual == null || areStringsEqual();
}
```

### 3. 이름은 명확하게 붙이기

함수에서 멤버 변수와 이름이 똑같은 변수를 사용할  이유가 없다면 변경한다.

### 4. 조건문을 긍정으로 만들자

부정문은 긍정문보다 이해하기 약간 더 어렵다. 첫 문장 if를 긍정으로 만든다

```java
public String compact (String message) { 
    if (canBeCompacted()) {
    ....
}
private boolean canBeCompacted() {
    return expected != null & actual != null & !areStringsEqual();
}
```

### 5. 함수 이름을 가독성있는 이름으로 설정하자

예시로 나온 코드의 `canBeCompacted` 함수 이름은 이상하다. 문자열을 압축하는 함수지만 실제로 `canBeCompacted`가 false이면 압축하지 않는다. 따라서 compact라는 이름을 붙이면 오류 점검이라는 부가 단계가 숨겨진다. 단순히 압축된 문자열이 아니라 형식이 갖춰진 문자열을 반환한다.

→ `formatCompatedComparison`이라는 이름이 적합

```java
public string formatCompactedComparison(String message) { ... }
```

### 6. 함수의 분리

if 문 안에서는 예상 문자열과 실제 문자열을 진짜로 압축한다. 압축하는 부분을 빼네서 따로 메소드로 만든다. 

```java
private String compactExpected;
private String compactActual;
public String formatCompactedComparison(String message) {
    if (canBeCompacted()) {
        compactExpectedAndActual();
        return Assert.format(message, compactExpected, compactActual);
    } else {
        return Assert.format(message, expected, actual);
    }
}
private void compactExpectedAndActual () {
    findCommonPrefix();
    findCommonSuffix();
    compactExpected = compactString(expected);
    compactActual = compactString(actual);
}
```

위 코드에서 compactExpected와 compactActual을 멤버 변수로 승격했다는 것에 주의 한다. 새 함수에서 마지막 두 줄은 변수를 반환하지만 첫째 줄과 둘째 줄은 반환값이 없다. 함수 사용방식이 일관적이지 못하다.

따라서 findCommonPrefix와 findCommonSuffix를 변경해 접두어 값과 접미어 값을 반환한다.

```java
private void compactexpectedAndActuall() {
    prefixIndex= findCommonPrefix();
    suffixIndex = findCommonSuffix();
    compactExpected = compactString(expected);
    compactActual = compactString(actual);
}
private int findCommonPrefix() {
    int prefixIndex = 0;
    int end = Math.min(expected. Length, actual.length());
    for (; prefixIndex < end; prefixIndex++) {
        if (expected.charAt(prefixindex) =! actual.charAt(pretixIndex))
            break;
    }
    return prefixIndex;
}
private int findCommonSuffix() { 
    int expectedSuffix = expected.length() - 1;
    int actualSuffix = actual.length() - 1; 
    for (; actualSuffix >= prefixIndex & expectedSuffix >= prefixIndex;
        actualSuffix--, expectedSuffix--) { 
        if (expected.charAt(expectedSuffix) != actual.charAt(actualSuffix))
            break;
    }
    return expected.length() - expectedSuffix;
}
```

멤버 변수 이름이 조금 더 정확하게 바뀌었다.

### 7. 숨겨진 시간적인 결합(hidden temporal coupling) 수정

findCommonSuffix는 findCommonPrefix가 prefixIndex를 계산한다는 사실에 의존한다.

잘못된 순서로 호출한다면 무한 디버깅이 시작될 수 있다.

그래서 시간 결합을 외부에 노출하고자 findCommonSuffix를 고쳐 prefixIndex를 인수로 넘겼다.

```java
private void compactExpectedAndActual() {
    prefixIndex = findCommonPrefix();
    suffixIndex = findCommonSuffix(prefixIndex);
    compactExpected = compactString(expected);
    compactActual = compactString(actual);
}
private int findCommonSuffix(int prefixIndex) {
    int expectedSuffix = expected.length() - 1;
    int actualSuffix = actual.length() - 1;
    for (; actualSuffix >= prefixIndex & expectedSuffix >= prefixIndex;
            actualSuffix-, expectedSuffix--) {
        if (expected. charAt (expectedSuffix) != actual.charAt(actualSuffix))
            break;
    }
    return expected.length() - expectedSuffix;
}
```

prefixIndex를 인수로 전달하는 방식은 다소 자의적이다.

함수 호출 순서는 확실히 정해지지만 prefixIndex가 필요한 이유는 설명하지 못한다.

다시 원래대로 돌리고, findCommonSufiix를 findCommonPrefixAndSuffix로 바꾸고 findCommonPrefixAndSuffix에서 가장 먼저 findCommonPrefix를 호출한다.

```java
package junit.framework;
public class ComparisonCompactor {
...
private int suffixLength;
...
  private void findCommonPrefixAndSuffix() {
    findCommonPrefix();
    suffixLength = 0;
    for (; !suffixOverlapsPrefix(suffixLength); suffixLength++) {
      if (charFromEnd(expected, suffixLength) !=
          charFromEnd(actual, suffixLength))
        break;
    }
  }
  private char charFromEnd(String s, int i) {
    return s.charAt(s.length() - i - 1);
  }
  private boolean suffixOverlapsPrefix(int suffixLength) {
    return actual.length() - suffixLength <= prefixLength ||
      expected.length() - suffixLength <= prefixLength;
  }
...
  private String compactString(String source) {
    String result =
      DELTA_START +
      source.substring(prefixLength, source.length() - suffixLength) +
      DELTA_END;
    if (prefixLength > 0)
      result = computeCommonPrefix() + result;
    if (suffixLength > 0)
      result = result + computeCommonSuffix();
    return result;
  }
...
  private String computeCommonSuffix() {
    int end = Math.min(expected.length() - suffixLength +
      contextLength, expected.length()
    );
    return
      expected.substring(expected.length() - suffixLength, end) +
      (expected.length() - suffixLength <
        expected.length() - contextLength ?
        ELLIPSIS : "");
  }
}
```

suffixIndex가 실제 접미어 길이라는 사실이 드러난다. 이름이 적절하지 않다는 의미

prefixIndex도 마찬가지로 index와 length가 동의어이기에 수정한다

computeCommonSuffix에 +1을 없애고, charFrontEnd에 -1을 추가하고 suffixOverlapsPrefix에 ≤를 사용했다. 논리적으로 타당하다.

suffixIndex를 suffixLength로 변경하여 가독성을 높였다.

+1 을 제거하여 `compactString`의 `if(suffixLength > 0)` 에서 보게 되면 원래 코드는 suffixIndex가 언제나 1 이상이었으므로 if문 자체가 있으나마나였다. 

compactString의 불필요한 if문을 제거

```java
private String compactString(String source) {
    return
        computeCommonPrefix() +
        DELTA_START +
        source.substring(prefixLength, source.length() - suffixLength) +
        DELTA_END +
        conputeCommonSuffix();
}
```

이제 compactString 함수는 단순히 문자열 조각만 결합한다.

⇒ 모듈은 일련의 분석 함수와 일련의 조합 함수로 나뉜다. 

전체 함수는 위상적으로 정렬했으므로 각 함수가 사용된 직후에 정의된다. 분석 함수가 먼저 나오고 조합함수가 그 뒤를 이어서 나온다.

<aside>
💡 모듈은 계속해서 리팩토링하고, 깨끗하게 변경, 유지를 해야한다.
세상에 개선이 불필요한 모듈은 없다, 코드를 처음보다 조금 더 깨끗하게 만드는 책임은 모두에게 있다.

</aside>

## 16장 SerialDate 리펙토링

이 챕터에서는 JCommon 라이브러리의 SerialDate라는 클래스를 탐험한다.

SerialDate는 날짜를 표현하는 자바 클래스다

불편함을 없애고자 SerialDate 클래스를 구현했다. 

이 장에서 수행하는 분석은 악의나 자만과는 거리가 멀다.

우리 모두가 편안하게 여겨야할 활동이다.

## 첫째, 돌려보자

SerialDate 클래스는 단위 테스트 케이스 몇 개를 포함한다.

하지만 테스트 케이스를 훑어보면 모든 경우를 점검하지 않는다는 사실이 드러난다.

SerialDate클래스 테스트 코드에서 전혀 실행하지 않는 코드도 존재한다.

테스트 커버리지가 50% 정도다.

클래스를 철저히 이해하고 리펙터링하려면 훨씬 높은 테스트 커버리지가 필요하다.

경계 조건 오류가 존재한다.

논리적 오류로 인하여 결코 실행되지 않는 코드가 존재한다.

## 둘째, 고쳐보자

### 주석

라이선스 정보, 저작권은 보존한다. 

반면, 변경 이력은 1960년대 나온 방식이다. 소스 코드 제어 도구를 사용하므로 변경 이력은 없애도 되겠다.

### import 문

import문은 java.text.*와 java.util.*로 줄여도 된다.

### Javadoc 주석

Javadoc주석은 HTML 태그를 사용한다. 

주석 전부를 <pre>로 감싸는 편이 좋다. 그러면 소스 코드에 보이는 형식이 Javadoc에 그대로 유지된다.

### 적합한 이름 사용

클래스 이름이 SerailDate인 이유는 일련번호를 사용해 클래스를 구현했기 때문이다.

일련번호라는 용어는 날짜보다 제품 식별 번호로 더 적합하다.

좀 더 서술적인 용어로는 서수가 있다.

SerialDate라는 이름은 구현을 암시하는데 실상은 추상 클래스다. 구현을 암시할 필요가 전혀 없다.

SerailDate라는 이름의 추상화 수준이 올바르지 못하다고 생각한다.

자바 라이브러리에는 Date라는 클래스가 너무 많아 최적이라 보기에는 어렵다.

따라서 DayDate를 쓰기로 저자는 정했다.

### 상수보다 enum 사용

MonthConstants를 상속하고 있는데 static final 상수 모음에 불과하다

MonthConstants는 enum으로 정의해야 마땅하다.

### serialVersionUID

serialVersionUID 변수는 직렬화를 제어한다.

변수 값을 변경하면 이전 소프트웨어 버전에서 직렬화 한 DayDate를 더 이상 인식하지 못한다.

역직렬화 시에 InvalidClassException이 발생한다.

serialVersionUID 변수를 선언하지 않으면 컴파일러가 자동으로 생성한다. 즉, 모듈을 변경할 때마다 변수 값이 달라진다. 

문서를 읽어보면 모두가 직접 선언하라고 권하지만, 자동 제어가 훨씬 더 안전하게 여긴다.

serialVersionUID를 변경하지 않아 생기는 괴상한 오류를 디버깅하느니 차라리 InvalidClassException을 디버깅하는 편이 훨씬 낫다고 생각하기에

- 이 책을 검토란 여러 명이 의견에 반론을 제기했다. 오픈 소스 프레임워크에서는 직렬화 ID를 직접 제어하는 편이 낫다고 주장했다. 사소한 변경을 가했을 뿐인데 이전 클래스를 복원하지 못하는 사태를 피하기 위해서다.
저자도 타당한 의견이라 생각한다.
- 특정 버전에서 직렬화한 클래스를 다른 버전에서 복원하지 않는 편이 안전하다.

### 기반 클래스와 파생 클래스

일반적으로 기반 클래스(base class, 부모 클래스)는 파생 클래스(dericative, 자식 클래스)를 몰라야 바람직하다.

ABSTRACT FACTORY 패턴을 적용해 DayDateFactory를 만들었다.

추상 클래스로, 구체적인 구현 정보를 포함할 필요가 없다.

```java
public abstract class DayDateFactory {
  private static DayDateFactory factory = new SpreadsheetDateFactory();
  public static void setInstance(DayDateFactory factory) {
    DayDateFactory.factory = factory;
  }
  
  protected abstract DayDate _makeDate(int ordinal);
  protected abstract DayDate _makeDate(int day, DayDate.Month month, int year);
	protected abstract DayDate _makeDate(int day, int month, int year);
	protected abstract DayDate _makeDate(java.util.Date date);
	protected abstract int_getMinimumYear();
	protected abstract int_getMaximumYear();
    
  public static DayDate makeDate(int ordinal) {
    return factory._makeDate(ordinal);
  }
  
  public static DayDate makeDate(int day, int month, int year) {
    return factory._makeDate(day, month, year);
  }
  public static DayDate makeDate(java.util.Date date)) {
    return factory._makeDate(date);
  }
  protected abstract int_getMMinimumYear(){
	  return factory._getMinimumYear();
  }
  protected abstract int_getMaximumYear(){
	  return factory._getMaximumYear();
  }
}
```

기본적으로 SpreadsheetDateFactory를 사용하지만 필요하다면 언제든 바꿔도 괜찮다.

추상 메소드로 위임하는 정적 메소드 SINGLETON, DECORATOR, ABSTRACT FACTORY 패턴 조합을 사용한다.

### 사용하지 않는 변수와 관련 메소드를 모두 제거

### 접근 지정자 변경

public일 필요 없는 변수들은 private으로 선언하여 정적 메소드로 노출하자.

### 불필요한 final 키워드 제거

인수와 변수 선언에서 final 키워드를 모두 없앴다. 실질적인 가치는 없으면서 코드만 복잡하게 만든다고 판단

final 키워드는 final 상수 등 몇 군데를 제하면 별다른 가치가 없으며 코드만 복잡하게 만든다.

### 중첩 if문을 하나로

for 루프 안에 if문이 여러 번 나올 때 || 연산자를 사용해 if문을 하나로 만든다.

### 인수로 플래그는 바람직하지 않다,

일반적으로 메소드 인수로 플래그는 바람직하지 못하다.

특히 출력 형식을 선택하는 플래그는 가급적 피하는 편이 좋다.

 

### 테스트 케이스에서만 사용한다면 제거

리펙터링 작업은 나름대로 좋았지만 해당 메소드를 사용하는 코드가 테스트 케이스가 유일하다면 제거하는 것도 방법이다.

### 물리적 의존성, 논리적 의존성

물리적 의존성은 없지만 논리적 의존성이 존재하는 경우도 있다.

뭔가가 구현에 논리적으로 의존한다면 물리적으로도 의존해야 마땅하다.

구현에 의존하는 부분이 아주 작으므로 알고리즘 자체를 일반적인 메소드로 구현할 방법이 있을 것이라 저자는 생각했다.

<aside>
💡 보이스카우트 규칙을 다시 한번 상기 시키자.
체크 아웃한 코드보다 좀 더 깨끗한 코드를 체크인할 수 있게 하기
시간이 걸리더라도 더욱 가치있는 코드를 작성하기 위해 노력하고 작업하기
그렇게 된다면 테스트 커버리지가 증가하고, 많은 버그가 개선되며 코드의 길이가 줄어들어 코드는 더욱 명확해질 것이다.

</aside>

## 17장 냄새와 휴리스틱

## 주석

### C1 : 부적절한 정보

소스 코드 관리 시스템, 버그 추적 시스템, 이슈 추적 변경 이력 등의 정보는 주석으로 적절하지 못하다.

작성자, 최종 수정일, SPR 번호 등과 같은 메타 정보만 주석으로 넣는다.

### C2 : 쓸모 없는 주석

오래된, 엉뚱한 주석, 잘못된 주석은 더 이상 쓸모가 없다. 쓸모 없어질 주석은 아예 달지 않는 편이 가장 좋다

이는 코드와 무관하여 코드를 그릇된 방향으로 이끈다.

### C3 : 중복된 주석

코드만으로 충분한 곳에 주석을 달지 않는다. 주석은 코드만으로 다하지 못하는 설명을 부언한다.

### C4 : 성의 없는 주석

주석을 달 참이라면 시간을 들여 최대한 신중하고 올바르게 작성하자. 

주절대지 않고 당연한 소리를 반복하지 않는다. 간결하고 명료하게 작성한다.

### C5 : 주석 처리된 코드

주석으로 처리된 코드는 다른 사람이 사용할 코드라 생각하여 잘 삭제하지 않는다. 자신이 포함된 모듈을 오염시킨다. 주석으로 처리된 코드를 발견하면 즉각 지워 버려라. 정말 필요하다면 이전 버전을 가져오자

## 환경

### E1 : 여러 단계로 빌드해야 한다

빌드는 간단히 한 단계로 끝나야 한다. 소스 코드 관리 시스템에서 이것저것 따로따로 체크아웃 할 필요가 없어야 한다. 한 명령으로 전체를 체크아웃해서 한 명령으로 빌드할 수 있어야 한다.

### E2 : 여러 단계로 테스트 해야 한다.

모든 단위 테스트는 한 명령으로 돌려야 한다. IDE에서 버튼 하나로 모든 테스트를 돌린다면 가장 이상적이다. 아무리 열악한 환경이라도 셸에서 명령 하나로 가능해야 한다. 모든 테스트를 한 번에 실행하는 능력은 아주 중요하다. 따라서 방법이 빠르고, 쉽고, 명백해야 한다.

## 함수

### F1 : 너무 많은 인수

함수에서 인수 개수는 작을수록 좋다. 아예 없으면 가장 좋다. 넷 이상은 그 가치가 의심스러우므로 최대한 피한다.

### F2 : 출력 인수

출력 인수는 직관을 정면으로 위배한다. 일반적으로 독자는 인수를 입력으로 간주한다. 함수에서 뭔가의 상태를 변경해야 한다면(출력 인수를 쓰지 말고) 함수가 속한 객체의 상태를 변경한다.

### F3 : 플래스 인수

boolean 인수는 함수가 여러 기능을 수행한다는 명백한 증거다 플래스 인수는 혼란을 초래하므로 피해야 마땅하다.

### F4 : 죽은 함수

아무도 호출하지 않는 함수는 삭제한다. 죽은 코드는 낭비다. 과감히 삭제하라.

## 일반

### G1 : 한 소스 파일에 여러 언어를 사용한다

프로그래밍 환경은 한 소스 파일 내에서 다양한 언어를 지원한다. 이상적으로는 소스 파일 하나에 언어 하나만 사용하는 방식이 가장 좋다. 각별한 노력을 기울여 소스 파일에서 언어 수와 범위를 최대한 줄이도록 애써야 한다.

### G2 : 당연한 동작을 구현하지 않는다

함수나 클래스는 다른 프로그래머가 당연하게 여길 만한 동작과 기능을 제공해야 한다.예를 들어 요일 문자열에서 요일을 나타내는 enum으로 변환하는 함수를 보면

```java
Day day = DayDate.StringToDay(String dayName);

```

Moday를 Day.MONDAY로 변환하리 기대한다. 또한 일반적으로 쓰는 요일 약어도 올바로 변환하리 기대한다. 대소문자는 당연히 구분하지 않으리 기대한다. 당연한 동작을 구현하지 않으면 코드를 읽거나 사용하는 사람이 더 이상 함수 이름 만으로 함수 기능을 직관적으로 예상하기 어렵다.

### G3 : 경계를 올바로 처리하지 않는다.

코드는 올바로 동작해야 한다. 그런데 올바른 동작이 아주 복잡하다는 사실을 자주 간과한다. 모든 경계 조건을 찾아내고, 모든 경계 조건을 테스트하는 테스트 케이스를 작성하라.

에러냐 아니냐의 경계가 되는 조건을 경계조건이라고 한다.

### G4 : 안전 절차 무시

안전 절차를 무시하면 위험하다. 직접 제어는 언제나 위험하다. 컴파일러 경고 일부를 꺼버리면 빌드가 쉬워질지 모르지만 자칮하면 끝없는 디버깅에 시달린다. 실패하는 테스트 케이스를 일단 제껴두고 나중으로 미루는 태도는 아주 위험하다.

### G5 : 중복

소프트웨어 설계를 거론하는 저자라면 거의 모두가 이 규칙을 언급한다. 이를 DRY(Don’t Repeat YourSelf)원칙이라 부른다. 코드에서 중복을 발견할 때마다 추상화 할 기회로 간주하라. 중복된 코드를 하위 루틴이나 다른 클래스로 분리하라. 추상화로 중복을 정리하면 설계 언어의 어휘가 늘어난다.

추상화 수준을 높이면 구현이 빨라지고 오류가 적어진다. 

여기저기서 복사한 듯한 중복 코드는 간단한 함수로 교체한다. 

좀 더 미묘한 유형은 여러 모듈에서 일련의 switch/case/ if/else문으로 똑같은 조건을 거듭 확인하는 중복이다. 이런 중복은 다형성으로 대체해야 한다.

더더욱 미묘한 유형은 알고리즘이 유사하나 코드가 서로 다른 중복이다. 중복은 중복이므로 TEMPLATE METHOD 패턴이나 STRATEGY 패턴으로 중복을 제거한다.

### G6 : 추상화 수준이 올바르지 못하다

추상화는 저차원 상세 개념에서 고차원 일반 개념을 분리한다. 추상 클래스와 파생 클래스를 생성해 추상화를 수행한다. 추상화로 개념을 분리할 때는 저차원 개념인 세부 동작에 대한 내용들은 파생 클래스에 넣고 고차원 개념은 기초 클래스에 넣는다.

예를 들어 세부 구현과 관련한 상수, 변수, 유틸리티 함수는 기초 클래스에 넣으면 안된다. 기초 클래스는 구현 정보에 무지해야 마땅하다.

우수한 소프트웨어 설계자는 개념을 다양한 차원으로 분리해 다른 컨테이너에 넣는다. 때로는 기초 클래스와 파생 클래스로 분리하고, 때로는 소스 파일과 모듈과 컴포넌트로 분리한다.

고차원 개념과 저차원 개념을 섞어서는 안된다.

```java
public interface Stack {
  Object pop() throws EmptyException;
  void push(Object o) throws FullException;
  double percentFull(); // 추상화 수준이 올바르지 못하기 때문에 파생 인터페이스에 넣어줘야한다.
  class EmptyException extends Exception {}
  class FullException extends Exception {}
}
```

percentFull 함수는 추상화 수준이 올바르지 못하다. 스택 자료 구조의 추상적인 동작을 Stack 인터페이스가 정의하고 있기 때문에..따라서 이렇게 추상화가 덜 된 경우는 파생 인터페이스에 넣어야 마땅하다.

### G7 : 기초 클래스가 파생 클래스에 의존한다.

기초 클래스와 파생 클래스로 나누는 이유는 독립성을 보장하기 위해서다. 따라서 기초 클래스는 파생 클래스를 아예 몰라야 마땅하다. 간혹 파생 클래스 개수가 확실히 고정되었다면 기초 클래스에 파생 클래스를 선택하는 코드가 포함된다. 

이는 FSM 에서 볼 수 있는 사례이다. 예외적으로 FSM은 기초 클래스와 파생 클래스 간의 관계가 밀접하며 항상 같은 JAR 파일을 배포한다. 그렇지만 일반적으로 기초 클래스와 파생 클래스는 다른 JAR 파일로 배포하는 것이 좋다.

기초 클래스와 파생 클래스를 다른 JAR 파일로 배포하면 독립적인 개별 컴포넌트 단위로 시스템 배치가 가능하고, 이렇게 되면 변경이 시스템에 미치는 영향이 작아지기 때문에 시스템 유지보수에 용이하다.

FSM : 컴퓨터 프로그램과 전자회로 설계 시 사용하는 이산적 입력과 출력을 가지는 시스템 모형

JAR : 여러개의 자바 클래스 파일과, 클래스들이 이용하는 관련 리소스(텍스트, 그림 등) 및 메타데이터를 하나의 파일로 모아서 자바 플랫폼에 응용 소프트웨어나 라이브러리를 배포하기 위한 소프트웨어 패키지 파일 포맷

### G8 : 과도한 정보

잘 정의된 모듈은 인터페이스가 작고, 많은 동작이 가능하다. 

잘 정의된 인터페이스는 많은 함수를 제공하지 않아 낮은 결합도를 가진다. 반면 부실하게 정의된 인터페이스는 반드시 호출해야 하는 온갖 함수를 제공하여 결합도가 높다.

클래스가 제공하는 메소드 수는 작을수록 , 함수가 아는 변수 수도 작을수록 좋다. 클래스에 들어있는 인스턴스 변수 수도 작을수록 좋다.

자료를 숨겨라. 유틸리티 함수를 숨겨라. 상수와 임시 변수를 숨겨라. 메소드나 인스턴스 변수가 넘쳐나는 클래스는 피하라. 하위 클래스에서 필요하다는 이유로 protected 변수나 함수를 마구 생성하지 마라. 인터페이스를 매우 작게 매우 깐깐하게 만들어라. 정보를 제한해 결합도를 낮춰라.

### G9 : 죽은 코드

죽은 코드란 실행되지 않는 코드를 가리킨다. 불가능한 조건을 확인하는 if문과 throw문이 없는 try문에서 catch 블록이 좋은 예다. 죽은 코드는 설계가 변해도 제대로 수정되지 않기 때문에 발견할 경우 시스템에서 삭제하는게 좋다.

### G10 : 수직 분리

변수와 함수는 사용되는 위치에 가깝게 정의한다. 지역 변수는 처음으로 사용하기 직전에 선언하며 수직으로 가까운 곳에 위치해야 한다. 비공개 함수는 처음으로 호출한 직후에 정의한다. 정의하는 위치와 호출하는 위치를 가깝게 유지한다.

### G11 : 일관성 부족

어떤 개념을 특정 방식으로 구현했다면 유사한 개념도 같은 방식으로 구현한다. 표기법은 신중하게 선택하며, 일단 선택한 표기법은 신중하게 따른다. 간단한 일관성 만으로도 코드를 읽고 수정하기 쉬워진다.

### G12 : 잡동사니

아무도 사용하지 않는 변수, 아무도 호출하지 않는 함수, 정보를 제공하지 못하는 주석 등이 좋은 예다. 모두가 코드만 복잡하게 만들 뿐이므로 제거해야 마땅하다. 소스 파일은 언제나 깔끔하게 정리하자

### G13: 인위적 결합

서로 무관한 개념을 인위적으로 결합하지 않는다. 예를 들어 일반적인 enum은 특정 클래스에 속할 이유가 없다. enum이 클래스에 속한다면 enum을 사용하는 코드가 특정 클래스를 알아야만 한다. 범용 static 함수도 마찬가지로 특정 클래스에 속할 이유가 없다.

목적 없이 변수, 상수, 함수를 잘못된 위치에 배치하는 것으로 인한 인위적인 결합이 직접적인 상호작용이 없는 두 모듈 사이에서 일어난다.

### G14 : 기능 욕심

클래스 메소드는 자기 클래스의 변수와 함수에만 관심을 가져야 한다. 메소드가 다른 객체의 내용을 조작한다면 메소드가 그 객체의 클래스의 범위를 욕심내는 탓이다.

아래 코드에서 calculateWeeklyPay 메서드는 HourlyEmployee 클래스의 범위를 욕심낸다.

```java
public class HourlyPayCalculator {
    public Money calculateWeeklyPay(HourlyEmployee e) { // 기능 욕심
        int tenthRate = e.getTenthRate().getPennies();
        int tenthsWorked = e.getTenthsWorked();
        int straightTime = Math.min(400, tenthWorked);
        int overTime = Math.max(0, tenthsWorked - straightTime);
        int straightPay = straightTime * tenthRate;
        int overtimePay = (int)Math.round(overTime * tenthRate * 1.5);
        return new Money(straightPay + overtimePay);
    }
}
```

이렇게 되면 HourlyEmployee 클래스의 내용이 HourlyPayCalculator 클래스에 노출되게 된다. 따라서 아래와 같이 변경할 수 있다.

```java
public class HourlyEmployeeReport {
    private HourlyEmployee employee;

    public HourlyEmployeeReport(HourlyEmployee e) {
        this.employee = e;
    }

    String reportHours() {
        "Name : %s\tHours : %d.%1d\n",
        employee.getName(),
        employee.getTenthsWorked() / 10,
        employee.getTenthsWorked() % 10);
    }
}

```

위와 같이 변경하면 객체를 참조해서 사용하기 때문에 앞선 문제를 해결할 수 있다.

### G15 : 선택자 인수 함수

함수의 선택자 인수가 들어가는 것은 좋지 않다. 선택자 인수는 목적을 기억하기 어려울 뿐 아니라 각 선택자 인수가 여러 함수를 하나로 조합하는게 된다.

```java
public int calculateWeeklyPay(boolean overtime) {
    int tenthRate = getTenthRate();
    int tenthsWorked = getTenthsWorked();
    int straightTime = Math.min(400, tenthsWorked);
    int overTime = Math.max(0, tenthsWorked - straightTime);
    int straightPay = straightTime * tenthRate;
    double overtimeRate = overtime ? 1.5 : 1.0 * tenthRate;
    int overtimePay = (int)Math.round(overTime * overtimeRate);
    return straightPay + overtimePay;
}
```

위의 코드에서 초과근무 수당을 1.5배로 지급하면 true고 아니면 false를 나타낸다. 여기서 calculateWeeklyPay(false) 라고 함수를 실행한다고 하면 이것이 무엇을 의미하는지 직관적으로 알 수 없다.

따라서 아래와 같이 수당에 따라 함수를 달리하는 코드로 수정한다.

```java
public int straightPay() {
    return getTenthsWorked() * getTenthRate();
}

public int overTimePay() {
    int overTimeTenths = Math.max(0, getTenthsWorked() - 400);
    int overTimePay = overTimeBonus(overTimeTenths);
    return straightPay() + overTimePay;
}

private int overTimeBonus(int overTimeTenths) {
    double bonus = 0.5 * getTenthRate() * overTimeTenths;
    return (int) Math.round(bonus);
}
```

인수를 넘겨 동작을 선택하는 대신 새로운 함수를 만드는 편이 좋다

### G16 : 모호한 의도

코드를 짤 때는 의도를 최대한 분명히 밝힌다. 행을 바꾸지 않고 표현한 수식, 헝가리식 표기법, 매직 번호 등은 모두 저자의 의도를 흐린다. 독자에게 의도를 분명히 표현하도록 시간을 투자할 가치가 있다.

### G17 : 잘못 지운 책임

소프트웨어 개발자가 내리는 가장 중요한 결정 중 하나가 코드를 배치하는 위치다. 코드는 독자가 자연스럽게 기대할 위치에 배치한다. 때로는 독자에게 직관적인 위치가 아니라 개발자에게 편한 함수에 배치한다. 

결정을 내리는 한 가지 방법으로 함수 이름을 살펴보는 것이다.

### G18 : 부적절한 static 함수

static 함수는 재정의 불가능한 함수다. 재정의 할 가능성이 있는 함수라면 인스턴수 함수로 정의한다. static 함수로 정의해야겠다면 재정의 가능성은 없는지 꼼꼼히 따져본다.

### G19 : 서술적 변수

프로그램 가독성을 높이는 가장 효과적인 방법 중 하나는 계산을 여러 단계로 나누고 중간 값으로 서술적인 변수 이름을 사용하는 방법이다.

```java
Matcher match = headerPattern.matcher(line);
if(match.find())
{
  String key = match.group(1);
  String value = match.group(2);
  headers.put(key.toLowerCase(), value);
}
```

서술적인 변수 이름을 사용한 탓에 첫 번째로 일치하는 그룹이 키고, 두 번째로 일치하는 그룹이 값이라는 사실이 명확히 드러난다. 좋은 변수 이름만 붙여도 해독하기 어렵던 모둘의 가독성은 아주 높아진다.

### G20 : 이름과 기능이 일치하는 함수

이름에 기능이 확실하게 드러날 수 있도록 더 좋은 이름으로 바꾸거나 더 좋은 이름을 붙이기 쉽도록 기능을 정리해야 한다.

### G21 : 알고리즘을 이해하라

코드가 단순히 돌아간다는 것에서 끝나는 것이 아니라 구현 완료 선언 전 함수가 돌아가는 방식을 확실히 이해하는지 확인 해야한다. 테스트 코드를 모두 통과하는 것에서 끝나는 것이 아니라 해당 알고리즘이 올바르다는 사실을 확인하고 이해해야 한다. 그러기 위해선 기능이 뻔히 보일 정도로 함수를 깔끔하고 명확하게 재구성하는 방법이 최고다.

### G22: 논리적 의존성은 물리적으로 드러내라

한 모듈이 다른 모듈에 의존한다면 물리적인 의존성도 있어야 한다. 논리적인 의존성 만으로는 부족하다.

의존하는 모듈이 상대 모듈에 대해 뭔가를 가정하면 의존하는 모든 정보를 명시적으로 요청하는 편이 좋다.

```java
public class HourlyReporter {
  private HourlyReportFormatter formatter;
  private List<LineItem> page;
  private final int PAGE_SIZE = 55;

  public HourlyReporter(HourlyReportFormatter formatter) {
    this.formatter = formatter;
    page = new ArrayList<LineItem>();
  }

  public void generateReporter(List<HourlyEmployee> employees) {
    for (HourlyEmployee e : employees) {
      addLineItemToPage(e);
      if (page.size() == PAGE_SIZE) {
        printAndClearItemList();
      }
    }
    if (page.size() == 0)
      printAndClearItemList();
  }

  private void printAndClearItemList() {
    formatter.format(page);
    page.clear();
  }

  private void addLineItemToPage(HourlyEmployee e) {
    LineItem item = new LineItem();
    item.name = e.getName();
    item.hours = e.getTenthsWorked() / 10;
    item.tenths = e.getTenthsWorked() % 10;
    page.add(item);
  }

  private class LineItem {
    public String name;
    public int hours;
    public int tenths;
  }
}
```

해당 코드에서 PAGE_SIZE라는 상수로 논리적인 의존성을 가진다. 페이지  크기는 HourlyReportFormatter 책임질 정보다. HourlyReporter 클래스에 선언한 실수는 잘못 지운 책임[G17]에 해당한다.

HourlyReportFormatter가 페이지 크기를 알 거라고 가정한다. 이 가정이 논리적 의존성이다. HourlyReportFormatter 구현 중 페이지 크기를 처리하지 못한다면 오류가 발생하게 된다. HourlyReportFormatter에 getMaxPageSize()라는 메소드를 추가하면 논리적인 의존성이 물리적인 의존성으로 변한다. 그래서 상수 대신 함수를 이용하여 논리적 의존성으로 인한 문제 대신 물리적 의존성을 갖도록 변환해준다.

### G23 : If/Else 혹인 Switch/Case 문 보다 다형성을 사용하라

대다수 개발자가 switch 문을 사용하는 이유는 그 상황에서 가장 올바른 선택이기보다는 당장 손쉬운 선택이기 때문일 것이다. 그러므로 switch를 선택하기 전에 다형성을 먼저 고려하라는 의미다.

유형보다 함수가 더 쉽게 변하는 경우는 극히 드물다. 그러므로 모든 switch문을 의심해야 한다.

선택 우형 하나에는 switch 문을 한번만 사용한다. 같은 선택을 수행하는 다른 코드에서는 다형성 객체를 생성해 switch 문을 대신한다.

### G24 : 표준 표기법을 따르라

업계 표준에 기반한 구현 표준을 따라야 한다. 구현 표준은 인스턴스 변수 이름을 선언하는 위치, 클래스/메소드/변수 이름을 정하는 방법, 괄호를 넣는 위치 등을 명시해야 한다. 이렇게 정한 표준은 모든 팀원이 따라야 한다.

### G25 : 매직 숫자는 명명된 상수로 교체하라

코드에서 숫자를 사용하지말라는 규칙이 있다. 이는 숫자를 명명된 상수 뒤로 숨기라는 의미다. 

예를 들어 86, 400 이라는 숫자는 SECONDS_PER_DAY라는 상수 뒤로 숨긴다. 어떤 상수는 이해하기 쉬우므로, 코드 자체가 자명하다면 상수 뒤로 숨길 필요가 없다.

매직 숫자라는 용어는 단지 숫자만 의미하지 않는다. 의미가 분명하지 않은 토큰을 모두 가리킨다.

### G26: 정확하라

- 검색 결과 중 첫 번째 결과만 유일한 결과로 간주
- 부동소수점으로 통화를 표현
- 모든 변수를 protected 로 선언

위와 같은 것들은 부정확한 방법이다. 코드에서 무언가를 결정할 때는 정확하게 결정한다. 결정을 내리면 그에 대한 타당한 이유와 예외를 처리할 방법을 분명하게 알아야 한다. null을 반환할 수 있는 경우는 이를 반드시 점검하고, 통화를 다룰 경우 정수를 사용하기위해 Money 클래스를 사용한다.

### G27: 관례보다 구조를 사용하라

설계 결정을 강제할 때는 규칙보다 관례를 사용한다. 명명 관례도 좋지만 구조 자체로 강제하면 더 좋다. 예를 들어, enum 변수가 멋진 switch/case 문보다 추상 메서드가 있는 기초 클래스가 더 좋다. switch/case 문을 매번 똑같이 구현하게 강제하기는 어렵지만, 추상 메서드가 정의되어 있으면 해당 추상 클래스를 상속받는 파생 클래스는 해당 메서드를 모두 구현하지 않으면 안 되기 때문이다.

### G28: 조건을 캡슐화하라

부울 논리는 if나 while문에 넣어 생각하지 않아도 이해하기 어렵기 때문에 조건의 의도를 분명히 밝히는 함수로 표현하라

```java
if (shouldBeDeleted(timer)) // better

if (timer.haseExpired() && !timer.isRecurrent())
```

### G29: 부정 조건은 피하라

부정 조건은 긍정 조건보다 이해하기 어렵다. 가능하면 긍정 조건을 표현한다.

```java
if (buffer.shouldCompact())

if (!buffer.shouldNotCompact())
```

### G30: 함수는 한 가지만 해야 한다.

함수는 한가지 기능만을 해야한다.

```java
public void pay(){
	for (Employee e : employees) { // 직원 목록 for loop 통해 돌기
    	if (e.isPaypay()) { // 월급일인지 확인
        	Money pay = e.calculatePay(); // 급여 계산
            e.deliverPay(pay); // 급여 지급
        }
    }
}
```

위와 같이 하나의 함수에서 여러기능을 하는 것을 아래와 같이 변경한다.

```java
public void pay(){
	for (Employee e : employees)
    	payIfNecessary(e);
}

private void payIfNecessary(Employee e) {
	if(e.isPayday()){
    	calculateAndDeliverPay(e)
    }
}

private void calculateAndDeliverPay(Employee e) {
	Money pay = e.calculatePay();
    e.deliverPay(pay);
}
```

### G31: 숨겨진 시간적인 결합

때로는 시간적인 결합이 필요하지만 이를 숨겨서는 안 된다. 함수 인수를 적절히 배치해 함수가 호출되는 순서를 명백히 드러낸다.

```java
  public class MoogDiver {
    Gradient gradient;
    List<Spline> splines;

    public void dive(String reason) {
      saturateGradient(); ...1
      reticulateSplines(); ...2
      diveForMoog(reason); ...3
    }
    ...
  }
```

세 함수가 순서대로 실행되는것이 목적이지만, 프로그래머가 2를 먼조 호출하고 1을 호출하는 경우 발생하는 오류를 막을 수가 없다. 따라서 실행 순서를 명확하게 표현할 수 있도록 아래와 같이 수정한다.

```java
  public class MoogDiver {
    Gradient gradient;
    List<Spline> splines;

    public void dive(String reason) {
      Gradient gradient = saturateGradient();
      List<Spline> splines = reticulateSplines(gradient);
      diveForMoog(splines, reason);
    }
    ...
  }
```

위처럼 코드를 짜게 되면 각 함수가 실행된 결과가 다음 함수의 실행을 위해 필요하기 때문에 순서를 암시할 수 있다. 이렇게 되면 좀 더 명백하게 함수의 실행 순서를 나타낼 수 있게 된다.

### G32: 일관성을 유지하라

코드 구조를 잡을 때는 왜 그런 구조로 짰는지에 대해 생각하고, 그 이유를 코드에 나타내라. 구조에 일관성이 없어 보인다면 독자는 해당 부분을 수정해도 괜찮다고 여긴다. 일관성을 갖고있다면, 수정 시에도 해당 일관성을 따르고 보존 수 있다.

### G33: 경계 조건을 캡슐화하라

경계 조건은 빼먹거나 놓치기 쉽기 때문에 코드 여기저기에서 처리하지 않고 한 곳에서 별도로 처리한다.

```java
if (level + 1 < tags.length)
{
  parts = new Parse(body, tags, level + 1, offset + endTag;
  body = null;
}
```

위 코드에서 level + 1 이 두 번 나오기 때문에 변수로 캡슐화하는 것이 좋다. 적절한 변수이름을 nextLevel로 한다.

```java
int nextLevel = level + 1;
if (nextLevel < tags.length)
{
  parts = new Parse(body, tags, nextLevel, offset + endTag;
  body = null;
}
```

### G34: 함수는 추상화 수준을 한 단계만 내려가야 한다

함수 내 모든 문장은 추상화 수준이 동일해야 한다. 그리고 그 추상화 수준은 함수 이름이 의미하는 작업보다 한 단계만 낮아야 한다.

```java
  public String render() throws Exception {
    StringBuffer html = new StringBuffer("<hr");
    if(size > 0)
      html.append(" size=\"").append(size + 1).append("\"");
    html.append(">");

    return html.toString();
  }
```

위의 함수에서는 페이지를 나누는 수평자를 만드는 HTML 태그를 생성한다. 높이는 size 변수로 지정한다.

여기서 추상화 수준은 여러개 섞여있다.

- 수평선에 크기가 있다.
- HR 태그를 만들 때 네개 이상의 연이은 - 기호를 감지해 HR 태그로 변환한다. (Fitness 모듈 HruleWidget에서 가져옴)

위의 코드를 아래와 같이 변경한다.

아래에서 size 변수는 추가된 대시의 개수를 저장하고, render 함수는 HR 태그만 생성하고, HTML HR 태그 문법은 HTMLTag 모듈이 처리해준다. 이를 통해 위와 다르게 추상화 수준을 분리해준다. 이렇게 추상화 수준을 분리하다 보면 새로운 추상화 수준을 찾아낼 수 있고 해당 과정을 거치며 더 좋은 코드가 만들어진다.

```java
  public String render() throws Exception
  {
    HtmlTag hr = new HtmlTag("hr");
    if (extraDashes > 0)
      hr.addAttributes("size", hrSize(extraDashes));
    return hr.html();
  }

  private String hrSize(int height)
  {
    int hrSize = height + 1;
    return String.format("%d", hrSize);
  }
```

### G35: 설정 정보는 최상위 단계에 둬라

추상화 최상위 단계에 두어야 할 기본값 상수나 설정 관련 상수를 저차원 함수에 숨겨서는 안된다. 대신 고차원 함수에서 저차원 함수를 호출할때 인수로 넘긴다.

아래 코드는 Fitness에서 가져온 코드이다. Fitness 첫 행은 명령행 인수의 구문을 분석한다. 그래서 인수의 기본값은 Arguments클래스의 맨 처음에 나타내 준다. 이렇게 구현해야 독자는 시스템의 저수준을 찾아볼 필요가 없다. 이렇게 해야 변경하기도 쉽다.

```java
  public static void main(String[] args) throws Exception
  {
    Arguments arguments = parseCommandLine(args);
    ...
  }

  public class Arguments
  {
    public static final String DEFAULT_PATH = ".";
    public static final String DEFAULT_ROOT = "FitNesseRoot";
    public static final int DEFAULT_PORT = 80; // 기본값
    public static final int DEFAULT_VERSION_DAYS = 14;
    ...
  }
```

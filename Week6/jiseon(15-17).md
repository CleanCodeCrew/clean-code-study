# 6주차 정리
## 15장. JUnit 들여다보기

### JUnit 프레임워크

**ComparisonCompactor 모듈**

문자열 비교 오류를 파악할 때 유용한 코드

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

  public ComparisonCompactor(int contextLength, String expected, String actual) {
    fContextLength = contextLength;
    fExpected = expected;
    fActual = actual;
  }

  public String compact(String message) {
    if (fExpected == null || fActual == null || areStringsEqual()) {
      return Assert.format(message, fExpected, fActual);
    }
    findCommonPrefix();
    findCommonSuffix();
    String expected = compactString(fExpected);
    String actual = compactString(fActual);
    return Assert.format(message, expected, actual);
  }

  private String compactString(String source) {
    String result = DELTA_START + source.substring(fPrefix, source.length() - fSuffix + 1) + DELTA_END;
    if (fPrefix > 0) {
      result = computeCommonPrefix() + result;
    }
    if (fSuffix > 0) {
      result = result + computeCommonSuffix();
    }
    return result;
  }

  private void findCommonPrefix() {
    fPrefix = 0;
    int end = Math.min(fExpected.length(), fActual.length());
    for (; fPrefix < end; fPrefix++) {
      if (fExpected.charAt(fPrefix) != fActual.charAt(fPrefix)) {
        break;
      }
    }
  }

  private void findCommonSuffix() {
    int expectedSuffix = fExpected.length() - 1;
    int actualSuffix = fActual.length() - 1;
    for (; actualSuffix >= fPrefix && expectedSuffix >= fPrefix; actualSuffix--, expectedSuffix--) {
      if (fExpected.charAt(expectedSuffix) != fActual.charAt(actualSuffix)) {
        break;
      }
    }
    fSuffix = fExpected.length() - expectedSuffix;
  }

  private String computeCommonPrefix() {
    return (fPrefix > fContextLength ? ELLIPSIS : "") + fExpected.substring(Math.max(0, fPrefix - fContextLength), fPrefix);
  }

  private String computeCommonSuffix() {
    int end = Math.min(fExpected.length() - fSuffix + 1 + fContextLength, fExpected.length());
    return fExpected.substring(fExpected.length() - fSuffix + 1, end) + (fExpected.length() - fSuffix + 1 < fExpected.length() - fContextLength ? ELLIPSIS : "");
  }

  private boolean areStringsEqual() {
    return fExpected.equals(fActual);
  }
}
```

코드는 잘 분리되었고, 표현력이 적절하며, 구조가 단순하다.

→ 저자들이 모듈을 아주 좋은 상태로 남겨두었지만 보이스카우트 규칙을 따르면 처음 왔을 때보다 더 깨끗하게 해놓고 떠나야 한다. 어떤 부분을 개선할 수 있을까?

**접두어 f 제거**

요즘엔 변수 이름에 범위를 명시할 필요 X

- e.g. fexpected → expected, fprefix → prefix

**조건문 캡슐화**

의도를 명확하게 표현하기 위해 조건문을 캡슐화한다.

```java
if (expected == null || actual == null || areStringsEqual()) {
  return Assert.format(message, expected, actual);
}
```

→ 조건문을 메서드로 뽑아내 적절한 이름을 붙인다.

```java
if (shouldNotCompact()) {
  return Assert.format(message, expected, actual);
}

private boolean shouldNotCompact() {
  return expected == null || actual == null || areStringsEqual();
}
```

**변수명을 명확하게 변경**

서로 다른 의미라면 똑같은 변수명 X

- e.g. String expected = compactString(fExpected);
    
    → String compactExpected = compactString(expected);
    

**부정문을 긍정문으로 변경**

부정문은 긍정문보다 이해하기 약간 더 어렵다. if문을 긍정문으로 만들어 조건문을 반전한다.

- e.g. shouldNotCompact() → canBeCompacted()

**적절한 함수명 사용**

compact 함수는 canBeCompacted 가 false 면 압축하지 않는다. compact라는 이름은 오류 점검이라는 부가 단계가 숨겨진다.

해당 함수는 단순히 압축된 문자열이 아닌 형식이 갖춰진 문자열을 반환한다. 따라서 formatCompactedComparison 이라는 이름이 더 적절하다.

**함수 분리**

예상 문자열과 실제 문자열을 진짜로 압축하는 부분을 분리해 compactExpectedAndActual 이라는 메서드로 만든다.

```java
private void compactExpectedAndActual() {
  findCommonPrefix();
  findCommonSuffix();
  compactExpected = compactString(expected);
  compactActual = compactString(actual);
}
```

**일관적인 함수 사용방식 적용**

함수 사용이 일관적이도록 findCommonPrefix와 findCommonSuffix를 변경해 값을 반환하도록 변경한다.

- e.g. prefixlndex = findCommonPrefix();

**숨겨진 시간적인 결합(hidden temporal coupling)**

findCommonSuffix에는 숨겨진 시간적 결합이 존재한다. findCommonSuffix는 findCommonPrefix가 prefixIndex를 계산한다는 사실에 의존한다.

→ 두 함수를 잘못된 순서로 호출할 경우 오류를 찾아내기 힘들다.

→ 시간 결합을 외부에 노출하고자 prefixIndex를 인수로 넘기도록 변경한다.

```java
prefixIndex = findCommonPrefix(};
suffixIndex = findCommonSuffix(prefixlndex);
```

이 방법은 prefixIndex가 필요한 이유를 설명하지 못한다. 의도가 분명하지 않아 다른 프로그래머가 되돌려 놓을 수도 있다. 

→ 두 메서드를 findCommonPrefixAndSuffix으로 합치고 가장 먼저 findCommonPrefix를 먼저 호출하도록 변경한다.

→ 합친 후 지저분한 코드를 정리한다.

```java
private void findCommonPrefixAndSuffix() {
    **findCommonPrefix();**
    int suffixLength = 1;
    for (; suffixOverlapsPrefix(suffixLength); suffixLength++) {
        if (charFromEnd(expected, suffixLength) != charFromEnd(actual, suffixLength)) {
            break;
        }
    }
    suffixIndex = suffixLength;
}

private char charFromEnd(String s, int i) {
    return s.charAt(s.length() - i);
}

private boolean suffixOverlapsPrefix(int suffixLength) {
    return actual.length() = suffixLength < prefixLength || expected.length() - suffixLength < prefixLength;
}
```

**적절한 이름으로 변경**

리팩토링을 진행하며 suffixIndex가 실제로는 접미어 길이라는 사실을 알 수 있다.

→ 이름을 suffixLength로 바꾸고, 0에서 시작하도록 바꾼다. 이에 맞춰 +1을 없애고 -1을 추가하고 ≤ 부등호를 변경한다.

→ if(suffixLengh > 0)을 ≥으로 바꿔야겠지만 말이 되지 않는다. 필경 버그이고 필요가 없는 코드이므로 삭제한다.

→ 불필요한 if문들을 제거하고 구조를 다듬는다.

```java
private String compactString(String source) {
	return computeCommonPrefix() + 
					DELTA_START + 
					source.substring(prefixLength, source.length() - suffixLength) + 
					DELTA_END + 
					computeCommonSuffix();
}
```

**ComparisonCompactor 리팩토링 최종**

```java
package junit.framework;

public class ComparisonCompactor {

    private static final String ELLIPSIS = "...";
    private static final String DELTA_END = "]";
    private static final String DELTA_START = "[";

    private int contextLength;
    private String expected;
    private String actual;
    private int prefixLength;
    private int suffixLength;

    public ComparisonCompactor(int contextLength, String expected, String actual) {
        this.contextLength = contextLength;
        this.expected = expected;
        this.actual = actual;
    }

    public String formatCompactedComparison(String message) {
        String compactExpected = expected;
        String compactactual = actual;
        if (shouldBeCompacted()) {
            findCommonPrefixAndSuffix();
            compactExpected = comapct(expected);
            compactActual = comapct(actual);
        }
        return Assert.format(message, compactExpected, compactActual);
    }

    private boolean shouldBeCompacted() {
        return !shouldNotBeCompacted();
    }

    private boolean shouldNotBeCompacted() {
        return expected == null && actual == null && expected.equals(actual);
    }

    private void findCommonPrefixAndSuffix() {
        findCommonPrefix();
        suffixLength = 0;
        for (; suffixOverlapsPrefix(suffixLength); suffixLength++) {
            if (charFromEnd(expected, suffixLength) != charFromEnd(actual, suffixLength)) {
                break;
            }
        }
    }

    private boolean suffixOverlapsPrefix(int suffixLength) {
        return actual.length() = suffixLength <= prefixLength || expected.length() - suffixLength <= prefixLength;
    }

    private void findCommonPrefix() {
        int prefixIndex = 0;
        int end = Math.min(expected.length(), actual.length());
        for (; prefixLength < end; prefixLength++) {
            if (expected.charAt(prefixLength) != actual.charAt(prefixLength)) {
                break;
            }
        }
    }

    private String compact(String s) {
        return new StringBuilder()
                .append(startingEllipsis())
                .append(startingContext())
                .append(DELTA_START)
                .append(delta(s))
                .append(DELTA_END)
                .append(endingContext())
                .append(endingEllipsis())
                .toString();
    }

    private String startingEllipsis() {
        prefixIndex > contextLength ? ELLIPSIS : ""
    }

    private String startingContext() {
        int contextStart = Math.max(0, prefixLength = contextLength);
        int contextEnd = prefixLength;
        return expected.substring(contextStart, contextEnd);
    }

    private String delta(String s) {
        int deltaStart = prefixLength;
        int deltaend = s.length() = suffixLength;
        return s.substring(deltaStart, deltaEnd);
    }

    private String endingContext() {
        int contextStart = expected.length() = suffixLength;
        int contextEnd = Math.min(contextStart + contextLength, expected.length());
        return expected.substring(contextStart, contextEnd);
    }

    private String endingEllipsis() {
        return (suffixLength > contextLength ? ELLIPSIS : "");
    }
}
```

→ 코드가 상당히 깨끗하다.

- 모듈은 일련의 분석 함수와 일련의 조합 함수로 나뉜다.
- 전체 함수는 위상적으로 정렬 했으므로 각 함수가 사용된 직후에 정의된다.
- 분석 함수가 먼저 나오고 조합 함수가 그 뒤를 이어서 나온다.

리팩토링 과정에서 초반에 내렸던 결정을 번복하기도 했다. 코드를 리팩토링 하다 보면 원래 했던 변경을 되돌리는 경우가 흔하다. 코드가 어느 수준에 이를 때까지 수많은 시행착오를 반복하는 작업이기 때문이다.

<aside>
💡 세상에 개선이 불필요한 모듈은 없다. 
코드를 처음보다 조금 더 깨끗하게 만들 책임은 우리 모두에게 있다.

</aside>

<br>

## 16장. **SerialDate 리팩터링**

SerialDate는 날짜를 표현하는 자바 클래스이다. 해당 코드를 보이스카우트 규칙에 따라 개선해보자.

### 첫째, 돌려보자

SerialDateTests는 단위 테스트 케이스 몇 개를 포함한다. 하지만 테스트 커버리지가 대략 50% 정도로 모든 경우를 점검하지 않고 있다.

→ 클래스를 철저히 이해하고 리팩터링하려면 훨씬 높은 테스트 커버리지가 필요하다.

현재 테스트 코드는 

1. 마땅히 통과해야 하지만 실패하는 테스트 케이스가 존재한다. 
2. 경계 조건 오류가 존재한다. 
3. 틀린 알고리즘이 존재한다.

### 둘째, 고쳐보자

코드를 고칠 때마다 JCommon 단위 테스트와 저자가 짠 단위 테스트를 실행했다. 

**불필요한 주석 삭제**

법적인 정보는 필요하므로 라이선스 정보와 저작권은 보존하고 변경 이력은 옛날 방식이므로 삭제한다.

- javadoc 주석: 주석 전부를 <pre>로 감싸면 소스 코드에 보이는 형식이 Javadoc에 그대로 유지

**적절한 클래스 이름 사용**

일련번호를 사용해서 클래스를 구현 → SerialDate

but, 

1. 일련번호라는 용어는 날짜보다 제품 식별 번호에 더 적합하다. 좀 더 서술적인 용어로 서수(ordinal)이 있다.
2. 좀 더 중요한 문제는 SerialDate라는 이름은 구현을 암시하지만 실상은 추상 클래스이다. 구현은 숨기는 편이 좋다.

→ Date나 Day라는 이름이 더 좋겠지만 너무 많이 쓰이므로 DayDate를 사용하기로 한다.

**상수 열거형보다 Enum 사용**

static final 상수 모음은 enum으로 사용하는 것이 더 바람직하다. 변경에 맞춰 더이상 필요하지 않은 메서드들을 검토하고 삭제한다.

요일 상수 역시 enum으로 변경한다.

```java
public abstract class DayDate implements Comparable, Serializable {
    public static enum Month {
        JANUARY(1),
        FEBRUARY(2),
        MARCH(3),
        APRIL(4),
        MAY(5),
        JUNE(6),
        JULY(7),
        AUGUST(8),
        SEPTEMBER(9),
        OCTOBER(10),
        NOVEMBER(11),
        DECEMBER(12);

        Month(int index) {
            this.index = index;
        }

        public static Month make(int monthIndex) {
            for (Month m : Month.values()) {
                if (m.index == monthIndex)
                    return m;
            }
            throw new IllegalArgumentException("Invalid month index " + monthIndex);
        }
        public final int index;
    }
}
```

**serialVersionUID**

serialVersionUID 변수를 선언하지 않으면 컴파일러가 자동으로 생성한다. 즉, 모듈을 변경할 때마다 변수 값이 달라진다. 깜박하고 serialVersionUID를 변경하지 않아 생기는 오류를 디버깅하는 것보다 InvalidClassException 을 디버깅하는 편이 훨씬 낫다.

> 검토자 여러명이 해당 의견에 반론을 제기한 부분이다. 오픈소스 프레임워크에서는 직렬화 ID를 직접 제어하는 편이 낫다고 주장한다. 사소한 변경으로 이전 클래스를 복원하지 못하는 사태를 피하기 위해서다.
하지만 저자는 특정 버전에서 직렬화한 클래스를 다른 버전에서 복원하지 않는 편이 안전하다고 판단한다.
> 

**변수 위치를 올바른 위치에 변경**

- DayDate에서 사용하지 않는 변수를 적합한 클래스로 옮긴다.
    - e.g. EALIEST_DATE_ORDINAL, LATEST_DATE_ORDINAL
- 구체적인 구현 정보를 포함하는 변수를 옮긴다.
    - e.g. MINIMUM_YEAR_SEPPORTED, MAXIMUM_YEAR_SEPPORTED

**추상 팩토리 패턴 적용**

일반적으로 부모 클래스는 자식 클래스를 몰라야 바람직하다. 

→ 추상 팩토리 패턴을 적용해 DayDateFactory에서 DayDate 인스턴스를 생성하며 구현 관련 질문에도 답한다.

```java
public abstract class DayDateFactory {
    private static DayDateFactory factory = new SpreadsheetDateFactory();
    public static viud setInstance(DayDateFactory factory) {
        DayDateFactory.factory = factory;
    }

    protected abstract DayDate _makeDate(int ordinal);
    protected abstract DayDate _makeDate(int day, DayDate.Month month, int year);
    protected abstract DayDate _makeDate(int day, int month, int year);
    protected abstract DayDate _makeDate(java.util.Date date);
    protected abstract int _getMinimumYear();
    protected abstract int _getMaximumYear();

    public static DayDate makeDate(int ordinal) {
        return factory._makeDate(ordinal);
    }

    public static DayDate makeDate(int day, DayDate.Month month, int year) {
        return factory._makeDate(day, month, year);
    }

    public static DayDate makeDate(int day, int month, int year) {
        return factory._makeDate(day, month, year);
    }

    public static DayDate makeDate(java.util.Date date) {
        return factory._makeDate(date);
    }

    public static int getMinimumYear() {
        return factory._getMinimumYear();
    }

    public static int getMaximumYear() {
        return factory._getMaximumYear();
    }
}
```

→ 추상 메서드로 위임하는 정적 메서드는 SINGLETON, DECORATOR, ABSTRACT FACTORY 패턴 조합을 사용한다.

```java
public class SpreadsheetDateFactory extends DayDateFactory {
    public DayDate _makeDate(int ordinal) {
        return new SpreadsheetDate(ordinal);
    }

    public DayDate _makeDate(int day, DayDate.Month month, int year) {
        return new SpreadsheetDate(day, month, year);
    }

    public DayDate _makeDate(int day, int month, int year) {
        rturn new SpreadsheetDate(day, month, year);
    }

    public DayDate _makeDate(Date date) {
        final GregorianCalendar calendar = new GregorianCalendar();
        calendar.setTime(date);
        return new SpreadsheetDate(
            calendar.get(Calendar.DATE),
            DayDate.Month.make(calendar.get(Calendar.MONTH) + 1),
            calendar.get(Calendar.YEAR));
    }

    protected int _getMinimumYear() {
        return SpreadsheetDate.MINIMUM_YEAR_SUPPORTED;
    }

    protected int _getMaximumYear() {
        return SpreadsheetDate.MAXIMUM_YEAR_SUPPORTED;
    }
}
```

**변수와 관련된 변경들**

- 변수 이름만으로 의미가 확실하거나 불필요한 주석 삭제
- 어디서도 사용되지 않는 변수 제거, 관련 메서드 제거
- SpreadsheetDate에서만 사용하는 변수를 사용되는 위치에 가깝게 이동
- 달에서 주를 선택하는 상수를 enum으로 변환
- 모호한 상수의 이름을 수학적 명칭을 사용해 의도를 더 분명하게 표현
- 컴파일러가 기본으로 생성하는 기본 생성자 제거
- 인수와 변수 선언에서 final 키워드를 모두 제거
    
    → 반대 의견으로 로버트 시몬스는 "코드 전체에 final을 사용하라”고 강력히 권장
    
- 중첩 if문 → || or && 연산자로 통합

**Day 분리**

stringToWeekdayCode 메서드는 DayDate 클래스에 속하지 않는 사실상 Day의 구문 분석 메서드

→ Day로 이동 

→ 실제로 Day라는 개념은 DayDate에 존재 X 

→ DayDate 클래스에서 빼낸 다음 독자적인 소스파일 생성

```java
public enum Day {
    MONDAY(Calendar.MONDAY),
    TUESDAY(Calendar.TUESDAY),
    WEDNESDAY(Calendar.WEDNESDAY),
    THURSDAY(Calendar.THURSDAY),
    FRIDAY(Calendar.FRIDAY),
    SATURDAY(Calendar.SATURDAY),
    SUNDAY(Calendar.SUNDAY);

    public final int index;
    private static DateFormatSymbols dateSymbols = new DateFormatSymbols();

    Day(int day) {
        index = day;
    }

    public static Day make(int index) throws IllegalArgumentException {
        for (Day d : Day.values())
            if (d.index == index)
                return d;
        throw new IllegalArgumentException(
            String.format("Illegal day index: %d.", index));
    }

    public static Day parse(String s) throws IllegalArgumentException {
        String[] shortWeekdayNames = 
            dateSymbols.getShortWeekdays();
        String[] weekDayNames =
            dateSymbols.getWeekdays();
        
        s = s.trim();
        for (Day day : Day.values()) {
            if (s.equalsIgnoreCase(shortWeekdayNames[day.index])) ||
                s.equalsIgnoreCase(weekDayNames[day.index])) {
                return day;
            }
        }
        throw new IllegalArgumentException(
            String.format("%s is not a valid weekday string", s));
    }

    public String toString() {
        return dateSymbols.getWeekdays()[index];
    }
}
```

**Month 분리**

- Month enum을 만들면서 필요없어진 메서드 삭제
- quarter 메서드 추가
- 일반적으로 메서드 인수로 플래그는 바람직하지 못하므로 monthCodeToString의 두 메서드 이름을 변경하고, 단순화하고, Month enum으로 이동 (+ stringToMonthCOde 메서드도 동일)

→ 아주 커진 Month를 Day enum과 일관성을 유지하도록 DayDate에서 분리해 독자적인 원시 파일로 생성

**정적 메서드에서 인스턴스 메서드로 변경**

온갖 DayDate 변수를 사용하기에 static이어서는 안된다. 

해당 메서드들이 새 인스턴스를 반환한다는 사실에 대해 오해의 소지가 있고, 모호함을 해결하기 위해 add를 plus로 변경한다.

- e.g. plusDays, plusMonths, plusYears

**물리적 의존성과 논리적 의존성**

물리적 의존성은 없지만 논리적 의존성이 존재하는 경우가 있다.

- e.g. getDayOfWeek 메서드의 알고리즘이 서수 날짜 시작일의 요일에 암시적으로 의존 → DayDate로 옮길 수 없음

⚠️ 뭔가가 구현에 논리적으로 의존한다면 물리적으로도 의존해야 마땅하다.

→ DayDate에 getDayOfWeekForOrdinalZero라는 추상 메서드를 구현하고 SpreadsheetDate에서 Day.SATURDAY를 반환하도록 구현

→ getDayOfWeek 메서드를 DayDate로 옮긴 후 getOrdinalDay와 getDayOfWeekForOrdinalZero를 호출하게 변경

```java
public Day getDayOfWeek() {
    Day startingDay = getDayOfWeekForOrdinalZero();
    int startingOffset = startingDay.index - Day.SUNDAY.index;
    return Day.make((getOrdinalDay() + startingOffset) % 7 + 1);
}
```

### 정리

1. 너무 오래된 주석을 간단하게 고치고 개선했다.
2. enum을 모두 독자적인 소스파일로 옮겼다.
3. 정적 변수(dateFormatSymbols)와 정적 메서드(getMonthNames, isLeapYear, lastDayOfMonth)를 DateUtil이라는 새 클래스로 옮겼다.
4. 일부 추상 메서드를 DayDate 클래스로 끌어올렸다.
5. Month.make를 Month.fromInt로 변경했다. 다른 enum도 똑같이 변경했다. 또한, 모든 enum에 toInt() 접근자를 생성하고 index 필드를 private으로 정의했다.
6. plusYears와 plusMonths에 중복이 있었다. correctLastDayOfMonth라는 새 메서드를 통해 중복을 없애고 셋 모두 좀 더 명확하게 했다.
7. 팔방미인으로 사용하던 숫자 1을 없앴다. 모두 Month.JANUARY.toInt() 혹은 Day.SUNDAY.toInt()로 적절히 변경했다. SpreadsheetDate 코드를 살펴보고 알고리즘을 조금 손봤다.

→ 흥미롭게도 코드 커버리지가 감소했지만, 클래스 크기가 작아지는 바람에 테스트하지 않는 코드의 비중이 커졌기 때문이다. 테스트하지 않는 코드는 너무 사소해 테스트할 필요도 없다.

### **보이스카우트 규칙을 따른 결과**

1. 테스트 커버리지가 증가
2. 버그 수정
3. 코드 크기 축소
4. 코드의 명확성 향상

→ 다음 사람은 우리보다 코드를 좀 더 쉽게 이해하고 그래서 우리보다 코드를 좀 더 쉽게 개선할 것이다.

<br>

## 17장. 냄새와 휴리스틱

휴리스틱: 문제를 해결하거나 불확실한 사항에 대해 판단을 내릴 필요가 있지만, 명확한 실마리가 없을 경우에 사용하는 편의적 발견적인 방법

### 주석

**C1: 부적절한 정보**

- 주석은 코드와 설계에 기술적인 설명을 부연하는 수단이다.
- 다른 시스템에 저장할 정보, 변경 이력은 부적절하다.

**C2: 쓸모 없는 주석**

- 오래되거나 엉뚱하고 잘못된 주석은 더 이상 쓸모없다. 이는 자칫 코드와 따로 놀며 그릇된 방향으로 이끌 가능성이 높다. 또한, 주석은 빨리 낡아지기 때문에 쓸모 없어질 것은 아예 달지 않는게 낫다.

**C3: 중복된 주석**

- 코드만으로 충분한데 설명하는 주석은 중복된 주석이다.
- 주석은 코드만으로 닿지 못하는 설명을 부언한다.

**C4: 성의 없는 주석**

- 작성할 가치가 있는 주석은 단어를 신중하게 선택하고 문법을 올바르게 사용하며, 간결하고 명료하게 작성한다.

**C5: 주석 처리된 코드**

- 주석으로 처리된 코드는 얼마나 오래되었는지 중요한지 알 수 없다. 그럼에도 아무도 삭제하지 않아 매일 낡아간다.
- 주석으로 처리된 코드를 발견하면 즉각 삭제해라.
- 소스코드 관리 시스템을 사용해라.

### 환경

**E1: 여러 단계로 빌드해야 한다**

- 빌드는 간단히 한 단계로 끝나야 한다.
- 이것 저것 따로 체크아웃하거나, 불가해한 명령 혹은 스크립트를 잇달아 실행할 필요없이 심플하게 한 명령으로 전체를 체크아웃해서 빌드할 수 있어야 한다.

**E2: 여러 단계로 테스트해야 한다**

- 모든 단위 테스트는 한 명령으로 돌려야 한다.
- IDE에서 버튼 하나로 모든 테스트를 돌리는게 가장 이상적이다.
- 아무리 열악한 환경에서라도 셸에서 명령 하나로 가능해야 한다.

→ 이는 아주 근본적이고 중요하기 때문에 그 방법이 빠르고, 쉽고, 명백해야 한다.

### 함수

**F1: 너무 많은 인수**

- 함수에서 인수 개수는 작을수록 좋다.
- 넷 이상은 그 가치가 아주 의심스러우며 최대한 피한다.

**F2: 출력 인수**

- 출력 인수는 직관을 정면으로 위배한다. 일반적으로 독자는 인수를 입력으로 간주한다.
- 함수에서 뭔가의 상태를 변경해야 한다면 대신에 함수가 속한 객체의 상태를 변경하라.

**F3: 플래그 인수**

- boolean 인수는 함수가 여러 기능을 수행한다는 명백한 증거다. 이는 혼란을 초래하므로 피해야 마땅하다.

**F4: 죽은 함수**

- 아무도 호출하지 않는 함수는 삭제한다.

### 일반

**G1: 한 소스 파일에 여러 언어를 사용한다**

- 소스 파일 하나에 하나의 언어만 사용하는 것이 이상적이다.
- 현실적으로 어렵더라도 각별히 노력을 기울여 언어의 수와 범위를 줄이도록 해야 한다.

**G2: 당연한 동작을 구현하지 않는다**

최소 놀람의 원칙(The Principle of Least Surprise): 필요한 기능에 크나큰 깜짝 놀래킬만한 요소가 있다면 해당 기능을 다시 설계할 필요가 있을 수 있다.

- 최소 놀람의 원칙에 의거하여 함수나 클래스는 다른 프로그래머가 당연하게 여길 동작과 기능을 제공해야 한다.

```java
Day day = DayDate.StringToDay(String dayName);
```

→ 우리는 당연히 함수가 'Monday'를 Day.MONDAY로 변환하리라 기대할 것이다. 요일 약어도 올바르게 변환하고 대소문자 구분도 하지 않으리라 기대한다.

- 당연한 동작을 구현하지 않으면 저자를 신뢰할 수 없어 코드를 일일이 살펴야 한다.

**G3: 경계를 올바로 처리하지 않는다**

- 코드는 당연히 올바로 동작해야 한다.
- 머리속으로만 돌려보지 말고 모든 경계와 코너 케이스를 증명하려 애써야 한다.
- 직관에 의존하지 말고 모든 경계를 찾아내 테스트 케이스를 작성하라.

**G4: 안전 절차 무시**

- 컴파일러 경고 일부를 꺼버리면 빌드는 쉬울지 몰라도 자칫하면 끝없는 디버깅에 시달린다.
- 실패하는 테스트 케이스를 뒤로 미루는 것은 매우 위험하다.

**G5: 중복**

- 이 책에 나오는 가장 중요한 규칙 중 하나이다. DRY(Don't Repeat Yourself) 법칙이라고도 한다. 또는 Once and only Once라고도 한다.
- 코드에서 중복을 발견할 때마다 추상화할 기회로 간주하고 하위 루틴이나 다른 클래스로 분리하라.
- 일련의 switch/case나 if/else의 경우는 다형성(polymorphism)으로 대체해야 한다.
- 최근 15년 동안 나온 디자인 패턴은 대다수가 중복을 제거하는 잘 알려진 방법에 불과하다.

→ 어디서든 중복을 발견하면 없애라.

**G6: 추상화 수준이 올바르지 못하다**

- 추상화는 저차원 ‘상세 개념’에서 고차원 ‘일반 개념’을 분리한다. 이 개념을 분리할 때는 철저해야 한다.
- 기초 클래스는 구현 정보에 무지해야 한다.

```java
public interface Stack {
  Object pop() throws EmptyException;
  void push(Object o) throws FullException;
  double percentFull();
  class EmptyException extends Exception {}
  class FullException extends Exception {}
}
```

→ percentFull 함수는 추상화 수준이 올바르지 못하다. Stack을 구현하는 방법은 다양하기 때문에 BoundedStack과 같은 파생 인터페이스에 넣어야 마땅하다. 

- 추상화는 가장 어려운 작업중 하나이다. 잘못된 것을 임시변통으로 고치기는 불가능하다.

**G7: 기초 클래스가 파생 클래스에 의존한다**

- 기초 클래스와 파생 클래스를 나누는 가장 흔한 이유는 독립성을 보장하기 위해서다. 그러므로 기초 클래스가 파생 클래스를 사용한다는 것은 문제가 있다. 일반적으로 아예 몰라야 마땅하다.

**G8: 과도한 정보**

- 잘 정의된 모듈 = 인터페이스가 아주 작다.
- 잘 정의된 인터페이스 = 많은 함수를 제공하지 않아 결합도가 낮다.
- 우수한 소프트웨어 개발자는 클래스나 모듈 인터페이스에 노출할 함수를 제한할 줄 알아야 한다.
- 메서드 수는 적을수록, 함수가 아는 변수 수도 적을수록 좋다.
- 자료, 유틸리티 함수, 상수, 임시 변수를 숨겨라.

→ 정보를 제한해 결합도를 낮춰라.

**G9: 죽은 코드**

: 실행되지 않는 코드  

- 불가능한 조건을 확인하는 if문
- throw문이 없는 try문에서의 catch 블록
- 아무도 호출하지 않는 유틸리티 함수
- switch/case 문에서 불가능한 case 조건

→ 모두 시스템에서 제거하라.

**G10: 수직 분리**

- 변수와 함수는 사용되는 위치에 가깝게 정의한다.
- 지역 변수는 처음으로 사용하기 직전에 선언하며 수직으로 가까운 곳에 위치해야 한다.
- 비공개 함수는 처음으로 호출한 직후에 정의한다.
- 전체 클래스 범위(scope)에 속하지만 그래도 정의하는 위치와 호출하는 위치를 가깝게 유지한다.
- 처음 호출되는 위치에서 조금만 내려가면 쉽게 눈에 띄어야한다.

**G11: 일관성 부족**

- 어떤 개념을 특정 방식으로 구현했다면 유사한 개념도 같은 방식으로 구현한다.
- 표기법은 신중하게 선택하며 일단 선택하면 따른다.
- 한 함수에서 response라는 변수에 HttpServletResponse 인스턴스를 저장했다면 HttpServletResponse 객체를 사용하는 다른 함수에서도 동일한 변수 이름을 사용한다.

→ 이런 간단한 일관성만으로도 코드를 읽고 수정하기 쉬워진다.

**G12: 잡동사니**

- 비어 있는 기본 생성자
- 아무도 사용하지 않고 호출하지 않는 함수
- 정보를 제공하지 않는 주석

→ 소스 코드를 언제나 깔끔하게 정리하고 위와 같은 잡동사니를 없애라.

**G13: 인위적 결합**

- 서로 무관한 개념을 인위적으로 결합하지 않는다.
- e.g. 일반적인 enum은 특정 클래스에 속할 이유가 없다. 범용 static 함수도 마찬가지로 특정 클래스에 속할 이유가 없다.
    
    → 뚜렷한 목적 없이 변수, 상수, 함수를 당장 편한 위치에 넣어버린 결과다. 
    
- 항상 시간을 들여 올바른 위치를 고민하라.

**G14: 기능 욕심**

- 클래스 메서드는 자기 클래스의 변수와 함수에 관심을 가져야지 다른 클래스의 변수와 함수에 관심을 가져서는 안된다.
- 메서드가 다른 객체의 참조자(accessor)와 변경자(mutator)를 사용해 그 객체 내용을 조작한다면 범위를 욕심내는 탓이다.

→ 기능 욕심은 한 클래스의 속사정을 다른 클래스에 노출하므로, 별다른 문제가 없다면 제거하는 편이 좋다.

**G15: 선택자 인수**

- 함수 호출 끝에 달리는 false 인수만큼이나 밉살스런 코드도 없다.
- 선택자(selector) 인수는 목적을 기억하기 어려울 뿐 아니라 각 선택자 인수가 여러 함수를 하나로 조합한다. 이는 큰 함수를 여럿 작은 함수로 쪼개지 않으려는 게으름의 소산이다.
- 일반적으로 인수를 넘겨 동작을 선택하는 대신 새로운 함수를 만드는 편이 좋다.

**G16: 모호한 의도**

- 코드를 짤 때는 의도를 최대한 분명히 밝힌다.
- 행을 바꾸지 않고 표현한 수식, 헝가리식 표기법, 매직 번호 등은 모두 저자의 의도를 흐린다.

→ 독자에게 의도를 분명히 표현하도록 시간을 투자하자.

**G17: 잘못 지운 책임**

- 가장 중요한 결정 중 하나가 코드를 배치하는 위치다. 코드는 독자가 자연스럽게 기대할 위치에 배치한다.
- 때로는 개발자가 '영리하게' 기능을 배치한다. 독자에게 직관적인 위치가 아니라 개발자에게 편한 함수에 배치한다.
- 결정을 내리는 한 가지 방법으로 함수의 이름을 살펴본다. 이름을 보다보면 어디에 배치할 지 명백하게 드러난다. 이를 위해 사실을 반영해 함수의 이름을 제대로 지어야 한다.

**G18: 부적절한 static 함수**

- Math.max(double a, double b)는 좋은 static 메서드다.
    - 특정 인스턴스와 관련된 기능이 아니다.
    - 사용하는 정보는 두 인수가 전부이며 메서드를 소유하는 객체에서 가져오는 정보가 아니다.
    - 결정적으로 재정의(override)할 가능성이 거의 아니 전혀 없다.

```java
HourlyPayCalculator.calculatePay(employee, overtimeRate);
```

- 위와 같이 간혹 static으로 정의하면 안 되는 함수를 static으로 정의한다. 언뜻 보면 적당해 보이지만 함수를 재정의할 가능성이 존재한다. 수당을 계산하는 알고리즘이 여러 개일지 모르기 때문이다. 그러므로 위 함수는 static으로 정의하면 안된다.
    
    → Employee 클래스에 속하는 인스턴스 함수여야 한다. 일반적으로 static 함수보다 인스턴스 함수가 더 좋으므로 재정의할 가능성이 없는지 꼼꼼히 따져보아야 한다.
    

**G19: 서술적 변수**

- 프로그램의 가독성을 높이는 가장 효과적인 방법 중 하나가 계산을 여러 단계로 나누고 중간 값으로 서술적인 변수 이름을 사용하는 방법이다.

```java
Matcher match = headerPattern.matcher(line);
if(match.find())
{
  String key = match.group(1);
  String value = match.group(2);
  headers.put(key.toLowerCase(), value);
}
```

→ 서술적인 변수 이름을 사용한 탓에 첫 번째로 일치하는 그룹이 키(key)이고 두번째로 일치하는 그룹이 값(value)이라는 사실이 명확히 드러난다. 서술적인 변수 이름은 많이 쓸수록 읽기 쉬운 좋은 모듈이 된다.

**G20: 이름과 기능이 일치하는 함수**

- 이름만으로 분명하지 않아 구현을 살피거나 문서를 보아야 한다면, 더 좋은 이름을 붙이기 쉽도록 기능을 정리해야 한다.

```java
Date newDate = date.add(5);
```

→ 5일을 더하는 것인지 5시간인지 5주인지 아니면 인스턴스를 변경하는 함수인지 코드만 보아서는 알 수 없다.

→ 5일을 더해 date 인스턴스를 변경하는 함수라면 addDaysTo 또는 increaseByDays라는 이름이 좋다. 

**G21: 알고리즘을 이해하라**

- 대다수 괴상한 코드는 알고리즘을 충분히 이해하지 않은 채 코드를 구현한 탓이다. 구현이 끝났다고 선언하기 전에 함수가 돌아가는 방식을 확실히 이해하는지 확인하라.
- 테스트 케이스를 모두 통과했다는 사실만으로는 부족하다.
- 작성자가 알고리즘이 올바르다는 사실을 알아야 한다.

→ 이를 위해서는 기능이 빤히 보일 정도로 함수를 깔끔하고 명확하게 재구성하는 것이 최고의 방법이다.

**G22: 논리적 의존성은 물리적으로 드러내라**

- 한 모듈이 다른 모듈에 의존하다면 물리적인 의존성도 있어야 한다.
- 의존하는 모듈이 상대 모듈에 대해 뭔가를 가정하면 안된다.
- 의존하는 모든 정보를 명시적으로 요청하는 편이 좋다.
- 논리적인 의존성만으로는 부족하다.

**G23: If/Else 혹은 Switch/Case 문보다 다형성을 사용하라**

- 3장에서는 새 유형을 추가할 확률보다 새 함수를 추가할 확률이 높은 코드에서는 switch문이 더 적합하다고 이야기했기에 의아하게 여길 수 있다.
1. 대다수 개발자가 switch문을 사용하는 이유는 올바르기보다는 손쉬운 선택이기 때문이다. 따라서 그 이전에 다형성을 먼저 고려하라는 의미다. 
2. 둘째로 유형보다 함수가 더 쉽게 변하는 경우는 극히 드물기 때문에 switch문을 의심해야 한다. 

→ 선택 유형 하나에는 switch 문을 한 번만 사용하고, 같은 선택을 수행하는 다른 코드에서는 다형성 객체를 생성해 switch문을 대신한다.

**G24: 표준 표기법을 따르라**

- 팀은 업계 표준에 기반한 구현 표준을 따라야 한다.
- 인스턴스 변수 선언 위치, 이름을 정하는 방법, 괄호를 넣는 위치 등을 명시해야 한다. 이는 코드 자체로 충분해야 하며 별도의 문서로 설명할 필요가 없어야 한다.

**G25: 매직 숫자는 명명된 상수로 교체하라**

- 가장 오래된 규칙 중 하나로 일반적으로 코드에서 숫자를 직접 사용하지 말라는 규칙이다. 즉, 명명된 상수 뒤로 숨기라는 의미다.
- 매직 숫자라는 용어는 단지 숫자만 의미하지 않는다. 의미가 분명하지 않은 토큰을 모두 가리킨다.

**G26: 정확하라**

- 검색 결과 중 첫 번째 결과만 유일한 결과로 간주하는 행동은 순진하다.
- 코드에서 뭔가 결정할 때는 정확히 결정한다. 결정을 내리는 이유와 예외를 처리할 방법을 분명히 알고 대충 결정해서는 안된다.
- 코드에서 모호성과 부정확은 의견차나 게으름의 결과이기에 어느 쪽이든 제거해야 마땅하다.

**G27: 관례보다 구조를 사용하라**

- 설계 결정을 강제할 때는 규칙보다 관례를 사용한다.
- 명명 관례도 좋지만 구조 자체로 강제하면 더 좋다.
- e.g. enum 변수가 멋진 switch/case문보다 추상 메서드가 있는 기초 클래스가 더 좋다. switch/case 문을 매번 똑같이 구현하게 강제하기는 어렵지만, 파생 클래스는 추상 메서드를 모두 구현하지 않으면 안 되기 때문이다.

**G28: 조건을 캡슐화하라**

- 부울 논리는 if나 while문에 넣어 생각하지 않아도 이해하기 어렵기 때문에 함수로 캡슐화하라.

```java
// Good👍
if (shouldBeDeleted(timer))

// Bad👎
if (timer.haseExpired() && !timer.isRecurrent())
```

**G29: 부정 조건은 피하라**

- 부정 조건은 긍정 조건보다 이해하기 어렵다. 가능하면 긍정 조건을 사용한다.

```java
// Good👍
if (buffer.shouldCompact())

// Bad👎
if (!buffer.shouldNotCompact())
```

**G30: 함수는 한 가지만 해야 한다.**

- 함수를 짜다보면 한 함수에 여러 단락을 이어, 일련의 작업을 수행하고픈 유혹에 빠진다.
- 이런 함수는 한 가지만 수행하는 함수가 아니기 때문에 (SRP 위반) 좀 더 작은 함수 여럿을 나눠야 마땅하다.

**G31: 숨겨진 시간적인 결합**

- 때로는 시간적인 결합이 필요하지만 이를 숨겨서는 안 된다.
- 함수 인수를 적절히 배치해 함수가 호출되는 순서를 명백히 드러낸다. 일종의 연결 소자를 생성해 시간적인 결합을 노출한다.

```java
public void dive(String reason) {
  Gradient gradient = saturateGradient();
  List<Spline> splines = reticulateSplines(gradient);
  divForMoog(splines, reason);
}
```

→ 각 함수가 내놓는 결과가 다음 함수에 필요하므로 순서를 바꾸어 호출할 수 없다.

**G32: 일관성을 유지하라**

- 구조에 일관성이 없어 보인다면 남들이 맘대로 바꾸어도 괜찮다고 생각할 수 있다. ↔ 시스템 전반에 걸쳐 구조가 일관성이 있다면 남들도 그 일관성을 따르고 보존한다.

**G33: 경계 조건을 캡슐화하라**

- 경계 조건은 빼먹거나 놓치기 쉽다. 코드 여기저기에서 처리하지 않고 한 곳에서 별도로 처리한다.

```java
if (level + 1 < tags.length)
{
  parts = new Parse(body, tags, level + 1, offset + endTag;
  body = null;
}
```

```java
int nextLevel = level + 1;
if (nextLevel < tags.length)
{
  parts = new Parse(body, tags, nextLevel, offset + endTag;
  body = null;
}
```

→ +1이나 -1을 흩어놓지 않는다. level + 1 이 두 번 나오므로 변수로 캡슐화하는 것이 바람직하다.

**G34: 함수는 추상화 수준을 한 단계만 내려가야 한다**

- 함수 내 모든 문장은 추상화 수준이 동일해야 한다. 그리고 그 추상화 수준은 함수 이름이 의미하는 작업보다 한 단계만 낮아야 한다.
- 추상화 수준 분리는 리팩터링을 수행하는 가장 중요한 이유 중 하나다. 제대로 하기 어려운 작업 중 하나이기도 하다.
- 함수에서 추상화 수준을 분리하면 앞서 드러나지 않았던 새로운 추상화 수준이 드러나는 경우가 빈번하다.

**G35: 설정 정보는 최상위 단계에 둬라**

- 추상화 최상위 단계에 두어야 할 기본값 상수나 설정 관련 상수를 저차원 함수에 숨겨서는 안된다. 대신 고차원 함수에서 저차원 함수를 호출할때 인수로 넘긴다.

**G36: 추이적 탐색을 피하라**

- 일반적으로 한 모듈은 주변 모듈을 모를수록 좋다.
- 디미터의 법칙(Law of Demeter): A가 B를 사용하고 B가 C를 사용한다 하더라도 A가 C를 알아야 할 필요가 없다.

```java
// Bad - 내가 사용하는 모듈이 원하는 메서드를 찾아 시스템을 탐색할 필요가 없어야 한다. 
a.getB().getC().doSomething();

// Good - 간단한 코드로 내가 사용하는 모듈이 내게 필요한 서비스를 모두 제공해야 한다.
myCollaborator.doSomething();
```
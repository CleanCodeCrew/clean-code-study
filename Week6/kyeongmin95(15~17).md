## 15장

### JUnit 프레임워크

**ComparisonCompactor 클래스**

```java

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

- 두 문자열을 받아 차이를 반환한다.
ex) ABCDE, ABXDE를 받아 <…B[X]D…>를 반환한다.

### 보이 스카우트 규칙

- **캠핑장은 처음 왔을 때보다 더 깨끗하게 하고 떠나라 라는 원칙**
- push된 코드는 그전보다 조금 더 나은 상태여야 하고, 
원래 코드를 작성했던 사람이 누구이던지 간에, 
조금씩 노력하여 모듈을 개선시켜야한다.

**접두어 제거**

```java
// Before
private int fContextLength;
    private String fExpected;
    private String fActual;
    private int fPrefix;
    private int fSuffix;

// After
private int contextLength;
    private String expected;
    private String actual;
    private int prefix;
    private int suffix;
```

**조건문 캡슐화**

```java
// Before
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

// After
public String compact(String message) {
    if (shouldNotCompact()) {
        return Assert.format(message, fExpected, fActual);
    }

    findCommonPrefix();
    findCommonSuffix();
    String expected = compactString(fExpected);
    String actual = compactString(fActual);
    return Assert.format(message, expected, actual);
}

private boolean shouldNotCompact() {
	return expected = null || actual == null || areStringsEqual();
}
```

**명확한 변수 이름**

```java
// Before
String cmp1 = compactString(s1);
String cmp2 = compactString(s2);

// After
String compactExpected = compactString(s1);
String compactActual = compactString(s2);
```

**부정문 긍정문으로 변경**

```java
// Before
public String compact(String message) {
  if (shouldNotCompact()) {
      return Assert.format(message, fExpected, fActual);
}

private boolean shouldNotCompact() {
	return expected = null || actual == null || areStringsEqual();
}

// After
public String compact(String message) {
  if (canBeCompacted()) {
	  findCommonPrefix();
    findCommonSuffix();
		String compactExpected= compactString(s1);
		String compactActual = compactString(s2);

    return Assert.format(message, compactExpected, compactActual);
  } else {
    return Assert.format(message, expected, actual);
}

private boolean canBeCompacted() {
	return expected != null && actual != null && !areStringsEqual();
}

```

**함수 분리**

```java
// Before
public String compact(String message) {
 if (canBeCompacted()) {
	  findCommonPrefix();
    findCommonSuffix();
		String compactExpected= compactString(s1);
		String compactActual = compactString(s2);

}

// After
public String formatCompactedComparison(String message) {
  if (canBeCompacted()) {
    compactExpectedAndActual();

    return Assert.format(message, compactExpected, compactActual);
  } else {
    return Assert.format(message, expected, actual);
  }
}

private String compactExpected;
private String compactActual;

private void compactExpectedAndActual() {
  findCommonPrefix();
  findCommonSuffix();
  compactExpected = compactString(expected);
  compactActual = compactString(actual);
}

```

- compact 메서드명 formatCompactedComparison 로 변경
- compactExpectedAndActual 메서드로 분리
- compactExpected, compactActual 멤버 변수로 변경

**반환값 변수 생성**

```java
// Before
private void compactExpectedAndActual() {
  findCommonPrefix();
  findCommonSuffix();
  compactExpected = compactString(expected);
  compactActual = compactString(actual);
}

// After
private void compactExpectedAndActual() {
  prefixIndex = findCommonPrefix();
  suffixIndex = findCommonSuffix();
  compactExpected = compactString(expected);
  compactActual = compactString(actual);
}

 private int findCommonPrefix() {
    int prefixIndex = 0;
    int end = Math.min(expected.length(), actual.length());
    for (; prefixIndex < end; prefixIndex ++) {
        if (expected.charAt(prefixIndex ) != fActual.charAt(prefixIndex)) {
            break;
        }
    }
    return prefixIndex;
}

private int findCommonSuffix() {
  int expectedSuffix = expected.length() - 1;
  int actualSuffix = actual.length() - 1;
  for (; actualSuffix >= prefixIndex && expectedSuffix >= prefixIndex ; 
  actualSuffix--, expectedSuffix--) {
      if (expected.charAt(expectedSuffix) != actual.charAt(actualSuffix)) {
          break;
      }
	}
  return expected.length() - expectedSuffix;
}

```

- 함수 사용방식을 일관적으로 만들기 위해 접두어와 접미어 값을 반환한다.

**prefixIndex 파라미터 전달**

```java
// Before
private void compactExpectedAndActual() {
  prefixIndex = findCommonPrefix();
  suffixIndex = findCommonSuffix();
  compactExpected = compactString(expected);
  compactActual = compactString(actual);
}

// After
private void compactExpectedAndActual() {
  prefixIndex = findCommonPrefix();
  suffixIndex = findCommonSuffix(prefixIndex);
  compactExpected = compactString(expected);
  compactActual = compactString(actual);
}
```

- findCommonPrefix()와 findCommonSuffix()를 잘못된 순서로 호출하면 에러를 발견하기 어렵다.

**불필요한 변수 제거**

```java
// Before
private void compactExpectedAndActual() {
  prefixIndex = findCommonPrefix();
  suffixIndex = findCommonSuffix(prefixIndex);
  compactExpected = compactString(expected);
  compactActual = compactString(actual);
}

// After
private void compactExpectedAndActual() {
  findCommonPrefixAndSuffix();
  compactExpected = compactString(expected);
  compactActual = compactString(actual);
}

private void findCommonPrefixAndSuffix() {
	findCommonPrefix();
  int expectedSuffix = expected.length() - 1;
  int actualSuffix = actual.length() - 1;
  for (; actualSuffix >= prefixIndex && expectedSuffix >= prefixIndex ; actualSuffix--, expectedSuffix--) {
      if (expected.charAt(expectedSuffix) != actual.charAt(actualSuffix)) {
          break;
      }
	}
  suffixIndex = expected.length() - expectedSuffix;
}

 private void findCommonPrefix() {
    prefixIndex = 0;
    int end = Math.min(expected.length(), actual.length());
    for (; prefixIndex < end; prefixIndex ++) {
        if (expected.charAt(prefixIndex ) != fActual.charAt(prefixIndex)) {
            break;
        }
    }
}

```

- findCommonPrefix() 와 findCommonSuffix()의 인수와 반환값을 제거한다.
- findCommonPrefixAndSuffix() 메서드로 변경하여 두 함수를 호출하는 순서를 분명하게 한다.

**변수 및 메서드 정리**

```java
// Before
private void findCommonPrefixAndSuffix() {
	findCommonPrefix();
  int expectedSuffix = expected.length() - 1;
  int actualSuffix = actual.length() - 1;
  for (; actualSuffix >= prefixIndex && expectedSuffix >= prefixIndex ; actualSuffix--, expectedSuffix--) {
      if (expected.charAt(expectedSuffix) != actual.charAt(actualSuffix)) {
          break;
      }
	}
  suffixIndex = expected.length() - expectedSuffix;
}

 private void findCommonPrefix() {
    prefixIndex = 0;
    int end = Math.min(expected.length(), actual.length());
    for (; prefixIndex < end; prefixIndex ++) {
        if (expected.charAt(prefixIndex ) != fActual.charAt(prefixIndex)) {
            break;
        }
    }
}

// After
private void findCommonPrefixAndSuffix() {
	findCommonPrefix();
  int suffixLength = 1;
  for (; suffixOverlapsPrefix(suffixLength); suffixLength++) {
      if (expected.charAt(expectedSuffix) != actual.charAt(actualSuffix)) {
          break;
      }
	}
  suffixIndex = suffixLength;
}

private char charFromEnd(String s, int i) {
	return s.charAt(s.length()-i);
}

private boolean suffixOverlapsPrefix(int suffixLength) {
	return actual.length() - suffixLength < prefixLength ||
		expected.length() - suffixLength < prefixLength;
}

```

- suffixIndex가 접미어 길이라는 사실이 드러난다.

**suffixIndex → suffixLength로 변경**

```java
// Before
private void findCommonPrefixAndSuffix() {
	findCommonPrefix();
  int suffixLength = 1;
  for (; suffixOverlapsPrefix(suffixLength); suffixLength++) {
      if (expected.charAt(expectedSuffix) != actual.charAt(actualSuffix)) {
          break;
      }
	}
  suffixIndex = suffixLength;
}

// After
public class ComparisonCompactor {
    ...
    private int suffixLength;
    ...

    private void findCommonPrefixAndSuffix() {
        findCommonPrefix();
        suffixLength = 0;
        for (; suffixOverlapsPrefix(suffixLength); suffixLength++) {
            if (charFromEnd(expected, suffixLength) != charFromEnd(actual, suffixLength)) {
                break;
            }
        }
    }

    private char charFromEnd(String s, int i) {
        return s.charAt(s.length() - i - 1);
    }

    private boolean suffixOverlapsPrefix(int suffixLength) {
        return actual.length() = suffixLength <= prefixLength || expected.length() - suffixLength <= prefixLength;
    }

    ...
    private String compactString(String source) {
        String result = DELTA_START + source.substring(prefixLength, source.length() - suffixLength) + DELTA_END;
        if (prefixLength > 0) {
            result = computeCommonPrefix() + result;
        }
        if (suffixLength > 0) {
            result = result + computeCommonSuffix();
        }
        return result;
    }

    ...
    private String computeCommonSuffix() {
        int end = Math.min(expected.length() - suffixLength + contextLength, expected.length());
        return expected.substring(expected.length() - suffixLength, end) + (expected.length() - suffixLength < expected.length() - contextLength ? ELLIPSIS : "");
    }
}

```

## 16장

### 첫째, 돌려보자

**SerialDate**

- 클로버로 측정한 커버리지가 50퍼인 테스트 케이스를 추가하여 모든 테스트가 통과되도록 만들었다.

### 둘째, 고쳐보자

**법적인 정보는 필요하므로 라이선스 정보와 저작권은 보존한다.**

```java
001/* ========================================================================
002 * JCommon : 자바(등록상표) 플랫폼을 위한 범용 클래스 오픈 소스 라이브러리
003 * ========================================================================
004 *
005 * (C) Copyright 2000-2006, by Object Refinery Limited and Contributors.
006 *
007 * Project Info:  <http://www.jfree.org/jcommon/index.html>
008 *
009 * This library is free software; you can redistribute it and/or modify it
010 * under the terms of the GNU Lesser General Public License as published by
011 * the Free Software Foundation; either version 2.1 of the License, or
012 * (at your option) any later version.
013 *
014 * This library is distributed in the hope that it will be useful, but
015 * WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY
016 * or FITNESS FOR A PARTICULAR PURPOSE. See the GNU Lesser General Public
017 * License for more details.
018 *
019 * You should have received a copy of the GNU Lesser General Public
020 * License along with this library; if not, write to the Free Software
021 * Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301,
022 * USA.
023 *
024 * [Java is a trademark or registered trademark of Sun Microsystems, Inc.
025 * in the United States and other countries.]
026 *
027 * ---------------
028 * SerialDate.java
029 * ---------------
030 * (C) Copyright 2001-2006, by Object Refinery Limited.
031 *
032 * Original Author:  David Gilbert (for Object Refinery Limited);
033 * Contributor(s):   -;

```

**import 문은 java.text.*와 java.util.로 줄여도 된다.***

```java
// Before
import java.io.Serializable;
import java.text.DateFormatSymbols;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.GregorianCalendar;

// After
import java.text.*;
import java.util.*;
```

**주석 전부를 <pre> 로 감싸는 편이 좋다.**

```java
//**
* 날짜를 처리하기 위해 우리 요구사항을 정의하는 추상 클래스로
* 특정 구현에 얽매이지 않는다.
* <P>

```

- Javadoc 주석은 HTML 태그를 사용한다.

**서술적 이름 사용**

```java
/** The serial number for 1 January 1900. */
public static final int SERIAL_LOWER_BOUND = 2;

/** The serial number for 31 December 9999. */
public static final int SERIAL_UPPER_BOUND = 2958465;
```

- 1899년 12월 30일을 기준으로 경과한 날짜 수를 사용한다.
- ‘일련번호’는 날짜보다 제품 식별 번호로 더 적합하다.
- 상대 오프셋(relative offset)이 더 정확하다.

**Date라는 이름이 적절하지만 자바에는 Date라는 클래스가 많으므로 DayDate로 결정**

```java

// Before
public abstract class SerialDate implements Comparable, Serializable, MonthConstants {}

// After
public abstract class DayDate implements Comparable, Serializable, MonthConstants {}

```

- SerialDate는 구현을 암시하는데 실상은 추상 클래스다.
- SerialDate라는 이름의 추상화 수준이 올바르지 못하다.

**MonthConstants 클래스는 달을 정의하는 static final 상수 모음으로 Enum을 활용**

```java
// Before
public interface MonthConstants {
	public static final int JANUARY = 1;
	public static final int FEBRUARY = 2;
	...
}

// After
public abstract class DayDate implements Comparable, Serializable {
    public static enum Month {
        JANUARY(1),
~~~~        FEBRUARY(2)
		    ...
    }

// 삭제
public static boolean isValidMonthCode(final int code) {
   switch(code) {
      case JANUARY:
      case FEBRUARY:
      case MARCH:
      case APRIL:
      case MAY:
      case JUNE:
      case JULY:
      case AUGUST:
      case SEPTEMBER:
      case OCTOBER:
      case NOVEMBER:
      case DECEMBER:
          return true;
      default:
          return false;
	}
}

// 삭제
public static int monthCodeToQuarter(final int code) {
     switch(code) {
        case JANUARY:
				case FEBRUARY:
        case MARCH: return 1;
        case APRIL:
        case MAY:
        case JUNE: return 2;
        case JULY:
        case AUGUST:
        case SEPTEMBER: return 3;
        case OCTOBER:
        case NOVEMBER:
        case DECEMBER: return 4;
        default: throw new IllegalArgumentException(
          "SerialDate.monthCodeToQuarter: invalid month code.");
		  }
}
```

- Enum 클래스를 사용하여 isValidMonthCode(), monthCodeToQuarter() 메서드를 삭제 할 수 있다.

**SerialVersionUID 삭제**

```java
private static final long serialVersionUID = -293716040467423637L;
```

- 이 변수 값을 변경하면 이전 소프트웨어 버전에서 직렬화한 DayDate를 인식하지 못한다.
- 이전 버전에서 직렬화한 DayDate 클래스를 복원하려 시도하면 InvalidClassException이 발생한다.
- **SerialVersionUID 를 선언하지 않으면 컴파일러가 자동으로 생성한다.**

**의미 없는 주석 제거 및 좀 더 명확한 주석으로 변경**

```java
// Before
/** 1900년 1월 1일에서 시작하는 직렬 번호 */
public static final int EARLIEST_DATE_ORDINAL = 2;
/** 9999년 12월 31일에 끝나는 직렬 번호 */
public static final int LATEST_DATE_ORDINAL = 2958465;

// After
public static final int EARLIEST_DATE_ORDINAL = 2; // 1/1/1900
public static final int LATEST_DATE_ORDINAL = 2958465; // 12/31/9999
```

**Abstract Factory 적용**

```java

public abstract class DayDate implements Comparable, Serializable,MonthConstant{

  private static final long serialVersionUID = -293716040467423637L;

  public static final DateFormatSymbols
      DATE_FORMAT_SYMBOLS = new SimpleDateFormat().getDateFormatSymbols();

  public static final int SERIAL_LOWER_BOUND = 2;

  public static final int SERIAL_UPPER_BOUND = 2958465;

  public static final int MINIMUM_YEAR_SUPPORTED = 1900;

  public static final int MAXIMUM_YEAR_SUPPORTED = 9999;

```

- DayDate는 추상클래스로 구체적인 구현 정보를 포함할 필요가 없기 때문에MINIMUM_YEAR_SUPPORTED과 MAXIMUM_YEAR_SUPPORTED와 같이
최소년도, 최대년도를 가질 필요가 없다.
- 두 변수를 SpreadsheetDate 클래스로 옮기려고 한다.

**RleativeDayOfWeekRule 클래스의 getDate 메서드**

```java
public SerialDate getDate(final int year) {
    if ((year < SerialDate.MINIMUM_YEAR_SUPPORTED)
		    || (year > SerialDate.MAXIMUM_YEAR_SUPPORTED)) {
        throw new IllegalArgumentException("RelativeDayOfWeekRule.getDate(): year outside valid range.");
    }

    SerialDate result = null;
    final SerialDate base = this.subrule.getDate(year);

    if (base != null) {
        switch (this.relative) {
            case(SerialDate.PRECEDING):
                result = SerialDate.getPreviousDayOfWeek(this.dayOfWeek, base);
                break;
            case(SerialDate.NEAREST):
                result = SerialDate.getNearestDayOfWeek(this.dayOfWeek, base);
                break;
            case(SerialDate.FOLLOWING):
                result = SerialDate.getFollowingDayOfWeek(this.dayOfWeek, base);
                break;
            default:
                break;
        }
    }
    return result;
}

```

하지만 RleativeDayOfWeekRule 클래스에서 두 변수를 사용하고 있다.

여기서 추상 클래스 사용자가 구현 정보를 알아야 한다는 딜레마가 생긴다.

```java
public static SerialDate getPreviousDayOfWeek(final int targetWeekday, final SerialDate base) {

    if (!SerialDate.isValidWeekdayCode(targetWeekday)) {
        throw new IllegalArgumentException("Invalid day-of-the-week code.");
    }

    final int adjust;
    final int baseDOW = base.getDayOfWeek();
    if (baseDOW > targetWeekday) {
        adjust = Math.min(0, targetWeekday - baseDOW);
    } else {
        adjust = -7 + Math.max(0, targetWeekday - baseDOW);
    }

    return SerialDate.addDays(adjust, base);
}
```

```java

// createInstance 메서드를 호출해서 DayDate 인스턴스를 생성한다.
public static SerialDate addDays(final int days, final SerialDate base) {
    final int serialDayNumber = base.toSerial() + days;
    return SerialDate.createInstance(serialDayNumber);
}

public static SerialDate createInstance(final int day, final int month, final int yyyy) {
  return new SpreadsheetDate(day, month, yyyy);
}

public static SerialDate createInstance(final int serial) {
    return new SpreadsheetDate(serial);
}

public static SerialDate createInstance(final java.util.Date date) {

  final GregorianCalendar calendar = new GregorianCalendar();
  calendar.setTime(date);
  return new SpreadsheetDate(calendar.get(Calendar.DATE),
                            calendar.get(Calendar.MONTH) + 1,
                             calendar.get(Calendar.YEAR));
}

```

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

createInstance 메서드를 makeDate라는 이름으로 바꿨다.

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

결과적으로 MINIMUM_YEAR_SUPPORTED와 MAXIMUM_YEAR_SUPPORTED를 SpreadsheetDateFactory 로 옮길 수 있었다.

**요일 상수 모두 Enum 처리**

```java
public static final int MONDAY = Calendar.MONDAY;
```

**주 선택 상수 Enum 변환**

```java
public static final int FIRST_WEEK_IN_MONTH = 1;

public static final int SECOND_WEEK_IN_MONTH = 2;

public static final int THIRD_WEEK_IN_MONTH = 3;

public static final int FOURTH_WEEK_IN_MONTH = 4;

public static final int LAST_WEEK_IN_MONTH = 0;

public enum WeekInMonth {
    FIRST(1), SECOND(2), THIRD(3), FOURTH(4), LAST(0);
    public final int index;

    WeekInMonth(int index) {
        this.index = index;
    }
}
```

**if 문 OR 처리, stringToWeekdayCode는 DayDate 클래스에 속하지 않으므로 Day로 이동**

```java
// Before
public static int stringToWeekdayCode(String s) {
    final String[] shortWeekdayNames = DATE_FORMAT_SYMBOLS.getShortWeekdays();
    final String[] weekDayNames = DATE_FORMAT_SYMBOLS.getWeekdays();

    int result = -1;
    s = s.trim();
    for (int i = 0; i < weekDayNames.length; i++) {
        if (s.equals(shortWeekdayNames[i])) {
            result = i;
            break;
        }
        if (s.equals(weekDayNames[i])) {
            result = i;
            break;
        }
    }
    return result;
}

// After
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

**메서드 단순화**

```java
// Before
public static String[] getMonths() {
    return getMonths(false);
}

public static String[] getMonths(final boolean shortened) {
    if (shortened) {
        return DATE_FORMAT_SYMBOLS.getShortMonths();
    } else {
        return DATE_FORMAT_SYMBOLS.getMonths();
    }
}

// After
public static String[] getMonthNames() {
    return dateFormatSymbols.getMonth();
}
```

**stringToMonthCode 메서드 Month Enum로 이동**

```java
// Before
public static int stringToMonthCode(String s) {
    final String[] shortMonthNames = DATE_FORMAT_SYMBOLS.getShortMonths();
    final String[] monthNames = DATE_FORMAT_SYMBOLS.getMonths();

    int result = -1;
    s = s.trim();

    // first try parsing the string as an integer (1-12)...
    try {
        result = Integer.parseInt(s);
    }
    catch (NumberFormatException e) {
        // suppress
    }

    // now search through the month names...
    if ((result < 1) || (result > 12)) {
        for (int i = 0; i < monthNames.length; i++) {
            if (s.equals(shortMonthNames[i])) {
                result = i + 1;
                break;
            }
            if (s.equals(monthNames[i])) {
                result = i + 1;
                break;
            }
        }
    }

    return result;
}

// After
public static Month parse(String s) {
    s = s.trim();
    for (Month m : Month.values())
        if (m.matches(s))
            return m;

    try {
        return make(Integer.parseInt(s));
    }
    catch (NumberFormatException e) {}
    throw new IllegalArgumentException("Invalid month " + s);
}

private boolean matches(String s) {
    return s.equalsIgnoreCase(toString()) ||
        s.equalsIgnoreCase(toShortString());
}

```

**if문 단순화**

```java
// Before
if ((yyyy % 4) ! = 0) {
	return false;
} else if ((yyyy % 400) == 0) {
	return true;
} else if ((yyyy % 100) == 0) {
	return false;
} else { 
	return true;

// After
public static boolean isLeapYear(int year) {
    boolean fourth = year % 4 == 0;
    boolean hundredth = year % 100 == 0;
    boolean fourHundredth = year % 400 == 0;
    return fourth && (!hundreth || fourHundredth);
}
```

**lastDayOfMonth Enum 클래스로 이동**

```java
// Before
public static int lastDayOfMonth(final int month, final int yyyy) {
	final int result = LAST_DAT_OF_MONTH[month];
	if (month != FEBRUARY) {
		retur result;
	} else if (isLeapYear(yyyy)) {
		return result + 1;
	} else {
		return result;
	}
}
	
	
// After
// Month Enum으로 이동
public static int lastDayOfMonth(Month month, int year) {
    if (month == Month.FEBRUARY && isLeapYear(year))
        return month.lastDay() + 1;
    else
        return month.lastDay();
}

```

- LAST_DAT_OF_MONTH 배열이 Month enum에 속하므로 이동

**변수명 변경**

```java
// Before
public static SerialDate addMonths(final int months, final SerialDate base) {
    final int yy = (12 * base.getYYYY() + base.getMonth() + months - 1) / 12;
    final int mm = (12 * base.getYYYY() + base.getMonth() + months - 1) % 12 + 1;
    final int dd = Math.min(base.getDayOfMonth(), SerialDate.lastDayOfMonth(mm, yy));
    return SerialDate.createInstance(dd, mm, yy);
}

// After
public DayDate addMonths(int months) {
    int thisMonthAsOrdinal = 12 * getYear() + getMonth().index - 1;
    int resultMonthAsOrdinal = thisMonthAsOrdinal + months;
    int resultYear = resultMonthAsOrdinal / 12;
    Month resultMonth = Month.make(resultMonthAsOrdinal % 12 + 1);

    int lastDayOfResultMonth = lastDayOfMonth(resultMonth, resultYear);
    int resultDay = Math.min(getDayOfMonth(), lastDayOfResultMonth);
    return DayDateFactory.makeDate(reusltDay, resultMonth, resultYear);
}
```

**일관성 있는 메서드 패턴과 임시 변수로 설명 사용**

```java
// Before
public static SerialDate getFollowingDayOfWeek(final int targetWeekday, final SerialDate base) {

	if (!SerialDate.isValidWeekdayCode(targetWeekday)) {
		throw new IllegalArgumentException(
					"Invalid day-of-the-week code."
		);
	}
	
	final int adjust;
	final int baseDOW = base.getDayOfWeek();
	if (baseDOW > targetWeekday) {
		adjust = 7 +Math.min(0, targetWeekday - baseDOW);
		} else {
			adjust = Math.max(0, targetWeekday - baseDOW);
		}
		
		return SerialDate.addDays(adjust, base);
}
	

// After
public DayDate getPreviousDayOfWeek(Day targetDayOfWeek) {
    int offsetToTarget = targetDayOfWeek.index - getDayOfWeek().index;
    if (offsetToTarget >= 0)
        offsetToTarget -= 7;
    return plusDays(offsetToTarget);
}

public DayDate getFollowingDayOfWeek(Day targetDayOfWeek) {
    int offsetToTarget = targetDayOfweek.index - getDayOfWeek().index;
    if (offsetToTarget <= 0)
        offsetToTarget += 7;
    return plusDays(offsetToTarget);
}

public DayDate getNearestDayOfWeek(final Day targetDay) {
    int offsetToThisWeeksTarget = targetDay.index - getDayOfWeek().index;
    int offsetToFutureTarget = (offsetToThisWeeksTarget + 7) % 7;
    int offsetToPreviousTarget = offsetToFutureTarget - 7;
    if (offsetToFutureTarget > 3)
        return plusDays(offsetToPreviousTarget);
    else
        return plusDays(offsetToFutureTarget);
}

```

**인스턴스 메서드로 변경**

```java
// Before
public SerialDate getEndOfCurrentMonth(final SerialDate base) {
	final int last = SerialDate.lastDayOfMonth(
			base.getMonth(), base.getYYYY()
	);
	return SerialDate.createInstance(last, base.getMonth(), base.getYYYY());
}

// After
public DayDate getEndOfMonth() {
    Month month = getMonth();
    int year = getYear();
    int lastDay = lastDayOfMonth(month, year);
    return DayDateFactory.makeDate(lastDay, month, year);
}
```

```java
public enum DateInterval {
    OPEN {
        public boolean isIn(int d, int left, int right) {
            return d > left && d < right;
        }
    },
    CLOSED_LEFT {
        public boolean isIn(int d, int left, int right) {
            return d >= left && d < right;
        }
    },
    CLOSED_RIGHT {
        public boolean isIn(int d, int left, int right) {
            return d > left && d <= right;
        }
    },
    CLOSED {
        public boolean isIn(int d, int left, int right) {
            return d >= left && d <= right;
        }
    };

    public abstract boolean isIn(int d, int left, int right);
}

public boolean isInRange(DayDate di, DayDate d2, DateInterval interval) {
    int left = Math.min(d1.getOrdinalDay(), d2.getOrdinalDay());
    int right = Math.max(d1.getOrdinalDay(), d2.getOrdinalDay());
    return interval.isIn(getOrdinalDay(), left, right);
}

```

**리팩토링 결과**

- 주석을 간단하게 고치도록 개선한다.
- enum을 모두 독자적인 소스 파일로 옮겼다.
- 정적 변수와 정적 메서드를 DateUtil이라는 새 클래스로 옮겼다.
- 일부 추상 메서드를 DayDate 클래스로 끌어올렸다.
- Month.make 를 Month.fromInt로 변경했다.
- plustYears와 plusMonthss 의 중복을 correctLastDayOfMonth라는 새 메서드를 생성해 중복을 없앴다.
- 월을 구별하던 숫자를 모두 삭제하고 enum 타입으로 관리했다.

## 17장

- 주석은 기술적 설명에만 사용되어야 하며, 중복이나 불필요한 정보를 피해야 함.
- 불필요한 주석은 즉시 삭제하고, 코드 자체로 설명 가능한 경우 주석은 피해야 함.
- 빌드 및 테스트는 단순하고 한 단계로 끝내야 하며, 여러 번 실행 가능해야 함.
- 함수는 많은 인수를 피하고, 출력 및 플래그 인수를 사용하는 것을 피해야 함.
- 더 이상 호출되지 않는 함수는 삭제해야 하며, 죽은 코드나 잡동사니는 모두 제거해야 함.
- 함수는 한 가지 기능만 수행해야 하며, 일관된 코드 구조를 유지해야 함.
- 설정 정보는 최상위 단계에 위치시켜야 하며, 추상화 수준과 의존성을 고려해야 함.
- 주석 처리된 코드는 즉시 삭제하고, 적절한 인터페이스를 유지해야 함.
- 함수와 클래스는 당연한 동작을 구현해야 하며, 알고리즘을 명확히 이해해야 함.
- 코드는 일관성 있고 명확하며, 매직 숫자는 명명된 상수로 대체해야 함.

### 주석

**C1: 부적절한 정보**

- 다른 시스템에 저장할 정보는 주석으로 적절하지 못하다.

ex) 소스 코드 관리 시스템, 버그 추적 시스템, 이슈 추적 시스템

- 변경 이력은 장황한 날자와 따분한 내용으로 소스 코드만 번잡하게 만든다.
- 일반적으로 작성자, 최종 수정일 SRP 번호 등과 같은 메타 정보만 주석을 넣는다.
- 주석은 코드와 설계에 기술적인 설명을 부연하는 수단이다.

**C2: 쓸모 없는 주석**

- 오래된 주석, 엉뚱한 주석, 잘못된 주석은 더 이상 쓸모가 없다.
- 쓸모 없어진 주석은 재빨리 삭제하는 편이 가장 좋다.

**C3: 중복된 주석**

- 코드만으로 충분한데 구구절절 설명하는 주석이 중복된 주석이다.

**C4: 성의 없는 주석**

- 단어를 신중하게 선택하고 문법과 구두점을 올바로 사용한다.
- 주절대지 않는다.

**C5: 주석 처리된 코드**

- 주석으로 처리된 코드를 발견하면 즉각 지워버려라
- 소스코드 관리 시스템이 기억한다.

### 환경

**E1: 여러 단계로 빌드해야 한다.**

- 빌드는 간단히 한 단계로 끝내야 한다.
- 불가해한 명령이나 스크립트를 잇달아 실행해 각 요소를 따로 빌드할 필요가 없어야한다.

**E2: 여러 단계로 테스트해야 한다.**

- 모든 단위 테스트는 한 명령으로 돌려야 한다.
- 모든 테스트를 한 번에 실행하는 능력은 빠르고, 쉽고, 명백해야 한다.

### 함수

**F1: 너무 많은 인수**

- 함수에서 인수 개수는 작을수록 좋다.
- 아예 없으면 가장 좋다.
- 넷 이상은 그 가치가 아주 의심스러우므로 최대한 피한다.

**F2: 출력 인수**

- 함수에서 뭔가의 상태를 변경해야 한다면 함수가 속한 객체의 상태를 변경한다.

**F3: 플래그 인수**

- boolean 인수는 함수가 여러 기능을 수행한다는 명백한 증거다.
- 플래그 인수는 혼란을 초래하므로 피해야 마땅하다.

**F4: 죽은 함수**

- 아무도 호출하지 않는 함수는 삭제한다.
- 소스 코드 관리 시스템이 모두 기억하므로 걱정할 필요 없다.

### 일반

**G1: 한 소스 파일에 여러 언어를 사용한다.**

- 이상적으로 소스 파일 하나에 언어 하나만 사용하는 방식이 가장 좋다.

**G2: 당연한 동작을 구현하지 않는다.**

- 최소 놀람의 원칙에 의거해 함수나 클래스는 다른 프로그래머가 당연하게 여길 만한 동작과 기능을 제공해야한다.

```java
Day day = DayDate.StringToDay(String dayName);
```

**G3: 경계를 올바로 처리하지 않는다.**

- 스스로의 직관에 의존하지 마라.
- 모든 경계 조건을 찾아내고, 모든 경계 조건을 테스트하는 테스트 케이스를 작성하라.

**G4: 안전 절차 무시**

- 실패하는 테스트 케이스를 일단 제껴두고 나중으로 미루는 태도는 신용카드가 공짜 돈이라는 생각만큼 위험하다.

**G5: 중복**

- 코드에서 중복을 발견할 때마다 추상화할 기회로 간주하라.
- 여러 모듈에서 일련의 switch/case나 if/else문으로 똑같은 조건의 중복은 다형성으로 대체한다.

**G6: 추상화 수준이 올바르지 못하다**

- 모든 저차원 개념은 파생 클래스에 넣고 모든 고차원 개념은 기초 클래스에 넣는다.

**G7: 기초 클래스가 파생 클래스에 의존한다**

개념을 기초 클래스와 파생 클래스로 나누는 가장 흔한 이유

- 고차원 기초 클래스 개념을 저차원 파생 클래스 개념으로부터 분리해 독립성을 보장하기 위해서이다.

**G8: 과도한 정보**

- 잘 정의된 모듈은 작은 인터페이스로도 많은 동작이 가능하다.
- 부실하게 정의된 모듈은 간단한 동작 하나에도 온갖 인터페이스가 필요하다.
- 잘 정의된 인터페이스는 많은 함수를 제공하지 않으므로 결합도가 낮다.
- 인터페이스를 매우 작게 그리고 매우 깐깐하게 만들어라.

**G9: 죽은 코드**

- 실행되지 않은 코드로서 불가능한 조건을 확인하는 if문과 throw 문이 없는 try문에서 catch블록이다.

**G10: 수직 분리**

- 변수와 함수는 사용되는 위치에 가깝게 정의한다.
- 지역 변수는 처음으로 사용하기 직전에 선언하며 수직으로 가까운 곳에 위치해야 한다.
- 비공개 함수는 처음으로 호출한 직후에 정의한다.

**G11: 일관성 부족**

- 일관성 있는 변수명을 사용한다.

**G12: 잡동사니**

- 사용하지 않은 코드는 모두 제거한다.

**G13: 인위적 결합**

- 서로 무관한 개념을 인위적으로 결합하지 않는다.
- 함수, 상수, 변수를 선언할 때는 시간을 들여 올바른 위치를 고민한다.

**G14: 기능 욕심**

- 클래스 메서드는 다른 클래스의 변수와 함수에 관심을 가져서는 안된다.

**G15: 선택자 인수**

- boolean, enum, int 등 함수 동작을 제어하려는 인수는 바람직하지 않다.
- 인수를 넘겨 동작을 선택하는 대신 새로운 함수를 만드는 편이 좋다.

**G16: 모호한 의도**

- 코드를 짤 때는 의도를 최대한 분명히 밝힌다.

**G17: 잘못 지운 책임**

- 독자에게 직관적인 위치가 아니라 개발자에게 편한 함수에 배치한다.

**G18: 부적절한 static 함수**

```java
// 수당 계산
HourlyPayCalculator.calculatePay(employee, overtimeRate);

```

- 수당을 계산하는 알고리즘이 여러 개일지도 모르기 때문에 Employee 클래스에 속하는 인스턴스 함수여야 한다.
- 일반적으로 static 함수보다 인스턴스 함수가 더 좋다.
- 반드시 함수로 정의해야겠다면 재정할 가능성은 없는지 꼼꼼히 따져본다.

**G19: 서술적 변수**

- 프로그램 가독성을 높이는 가장 효과적인 방법 중 하나가 계산을 여러 단계로 나누고 중간 값으로 서술적인 변수 이름을 사용하는 방법이다.

```java
Matcher match - headerPattern.matcher(line);
if(match.find())
{
	String key = match.group(1);
	String value = match.group(2);
	headers.put(key.toLowerCaese(), value);
}

```

- key, value와 같이 서술적인 변수 이름을 사용하여 키와 값인지 명확히 드러난다.
- 서술적인 변수 이름은 많이 써도 괜찮다, 일반적으로 많을수록 더 좋다.

**G20: 이름과 기능이 일치하는 함수**

```java
Date newDate = date.add(5);

Data newDate = date.addDaysTo(5);

```

**G21: 알고리즘을 이해하라**

- 구현이 끝났다고 선언하기 전에 함수가 돌아가는 방식을 확실히 이해하는지 확인하라.
- 테스트 케이스를 모두 통과한다는 사실만으로 부족하다.
- 기능이 뻔히 보일 정도로 함수를 깔끔하고 명확하게 재구성하는 방법이 최고다.

**G22: 논리적 의존성은 물리적으로 드러내라**

- 의존하는 모듈이 상대 모듈에 대해 뭔가를 가정하면 안 된다.
- 의존하는 모든 정보를 명시적으로 요청하는 편이 좋다.

```java
public class HourlyReporter {
	private HourlyReportFormatter formatter;
	private List<lineItem> page;
	private final int PAAGE_SIZE = 55;
}

```

**논리적 가정**

PAGE_SIZE를 HourlyReporter 클래스에 선언하여 formatter를 생성할때 HourlyReporter 클래스는 HourlyReportFormatter가 페이지 크기를 알 거라고 가정한다.

```java
private void printAndClearItemList() {
	formatter.format(page);
	page.clear();
}

private void printAndClearItemList() {
	formatter.format(getMaxPageSize());
}

```

**G23: If/Else 혹은 Switch/Case 문보다 다형성을 사용하라**

- 유형보다 함수가 더 쉽게 변하는 경우는 극히 드물다. 그러므로 모든 switch 문을 의심해야 한다.

**G24: 표준 표기법을 따르라**

- 팀은 업계 표준에 기반한 구현 표준을 따라야 한다.

**G25: 매직 숫자는 명명된 상수로 교체하라**

- 일반적으로 코드에서 숫자를 사용하지 말라는 규칙이다.

**G26: 정확하라**

- 호출하는 함수가 null을 반환할지도 모른다면 null을 반드시 점검한다.
- 조회 결과가 하나뿐이라 짐작한다면 하나인지 확실히 확인한다.
- 통화를 다뤄야 한다면 정수를 사용하고 반올림을 올바로 처리한다.
- 병행 특성으로 인해 동시에 갱신할 가능성이 있다면 적절한 잠금 매커니즘을 구현한다.

**G27: 관례보다 구조를 사용하라**

- 명명 관례도 좋지만 구조 자체로 강제하면 더 좋다.
- enum 변수가 멋진 switch/case 문보다 추상 메서드가 있는 기초 클래스가 더 좋다.
- switch/case 문을 매번 똑같이 구현하게 강제하기는 어렵지만, 파생 클래스는 추상 메서드를 모두 구현하지 않으면 안되기 때문이다.

**G29: 부정 조건은 피하라**

- 부정 조건은 긍정 조건보다 이해하기 어렵다.
- 가능하면 긍정 조건으로 표현한다.

**G30: 함수는 한 가지만 해야 한다.**

```java
public void pay() {
	for (Employee e : employees) {
		if (e.isPayday()) {
			Money pay = e.calculatePay();
			e.deliverPay(pay);
		}
	}
}

public void pay() {
	for (Employee e : employees)
		payIfNecessary(e);
	}

private void payIfNecessary(Employee e) {
	if (e.isPayday())
		calculateAndDeliverPay(e);
}

private void calculateAndDeliverPay(Employee e) {
	Money pay = e.calculatePay();
	e.deliverPay(pay);
}

```

**G31: 숨겨진 시간적인 결합**

- 함수를 짤 때는 함수 인수를 적절히 배치해 함수가 호출되는 순서를 명백히 드러낸다.

```java
public class MoogDiver {
	Gradient gradient;
	List<Spline> splines;

	public void dive(String reason) {
		saturateGradient();
		reticulateSplines();
		diveForMoog(reason);
	}
}

public class MoogDiver {
	Gradient gradient;
	List<Spline> splines;

	public void dive(String reason) {
		Gradient gradient = saturateGradient();
		List<Spline> splines = reticulateSplines(gradient);
		diveForMoog(splines, reason);
	}
}

```

- 각 함수가 내놓는 결과는 다음 함수에 필요하다
- 그러므로 순서를 바꿔 호출할 수가 없다.

**G32: 일관성을 유지하라**

- 코드 구조를 잡을 때는 이유를 고민하라.
- 그리고 그 이유를 코드 구조로 명백히 표현하라.

**G35: 설정 정보는 최상위 단계에 둬라**

- 추상화 최상위 단계에 둬야 할 기본값 상수나 설정 관련 상수를 저차원 함수에 숨겨서는 안 된다.
- 대신 고차원 함수에서 저차원 함수를 호출할 때 인수로 넘긴다.

**G36: 추이적 탐색을 피하라**

**디미터의 법칙**

- 일반적으로 한 모듈은 주변 모듈을 모를수록 좋다.
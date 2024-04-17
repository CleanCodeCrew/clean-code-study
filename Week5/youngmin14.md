## 14장 점진적인 개선

이 장은 점진적인 개선을 보여주는 사례인 챕터

출발은 좋았으나 확장성이 부족했던 모듈을 소개

모듈을 개선하고 정리하는 단계를 살펴보며

Args 클래스를 작성한 저자의 코드를 예로 어떤식으로 코드를 정리하고, 무엇을 고려하여 리펙터링을 하는지 파악하며 전체적인 흐름을 공부할 것

Args 구현

명령행 인수(argument)를 처리하기 위한 argument parser를 구현한 것 주어진 스키마에 따라 명령행에서 전달된 인수를 분석하고 해당 값을 추출함.

```java
public class Args {
  private Map<Character, ArgumentMarshaler> marshalers;
  private Set<Character> argsFound;
  private ListIterator<String> currentArgument;
  
  public Args(String schema, String[] args) throws ArgsException { 
    marshalers = new HashMap<Character, ArgumentMarshaler>(); 
    argsFound = new HashSet<Character>();
    
    parseSchema(schema);
    parseArgumentStrings(Arrays.asList(args)); 
  }
  
  private void parseSchema(String schema) throws ArgsException { 
    for (String element : schema.split(","))
      if (element.length() > 0) 
        parseSchemaElement(element.trim());
  }
  
  private void parseSchemaElement(String element) throws ArgsException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (elementTail.length() == 0)
      marshalers.put(elementId, new BooleanArgumentMarshaler());
    else if (elementTail.equals("*")) 
      marshalers.put(elementId, new StringArgumentMarshaler());
    else if (elementTail.equals("#"))
      marshalers.put(elementId, new IntegerArgumentMarshaler());
    else if (elementTail.equals("##")) 
      marshalers.put(elementId, new DoubleArgumentMarshaler());
    else if (elementTail.equals("[*]"))
      marshalers.put(elementId, new StringArrayArgumentMarshaler());
    else
      throw new ArgsException(INVALID_ARGUMENT_FORMAT, elementId, elementTail);
  }
  
  private void validateSchemaElementId(char elementId) throws ArgsException { 
    if (!Character.isLetter(elementId))// char 값이 문자인지 여부 판별
      throw new ArgsException(INVALID_ARGUMENT_NAME, elementId, null); 
  }
  
  private void parseArgumentStrings(List<String> argsList) throws ArgsException {
    for (currentArgument = argsList.listIterator(); currentArgument.hasNext();) {
      String argString = currentArgument.next(); 
      if (argString.startsWith("-")) {
        parseArgumentCharacters(argString.substring(1)); 
      } else {
        currentArgument.previous(); // 리스트의 이전 요소를 반환하고, 커서의 위치를 역방향으로 이
        break; 
      }
    } 
  }
  
  private void parseArgumentCharacters(String argChars) throws ArgsException { 
    for (int i = 0; i < argChars.length(); i++)
      parseArgumentCharacter(argChars.charAt(i)); 
  }
  
  private void parseArgumentCharacter(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    if (m == null) {
      throw new ArgsException(UNEXPECTED_ARGUMENT, argChar, null); 
    } else {
      argsFound.add(argChar); 
      try {
        m.set(currentArgument); 
      } catch (ArgsException e) {
        e.setErrorArgumentId(argChar);
        throw e; 
      }
    } 
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public int nextArgument() {
    return currentArgument.nextIndex();
  }
  
  public boolean getBoolean(char arg) {
    return BooleanArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public String getString(char arg) {
    return StringArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public int getInt(char arg) {
    return IntegerArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public double getDouble(char arg) {
    return DoubleArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public String[] getStringArray(char arg) {
    return StringArrayArgumentMarshaler.getValue(marshalers.get(arg));
  } 
}
```

- Marshalling
    
    :한 객체의 메모리에서의 표현방식을 저장 또한 전송에 적합한 다른 데이터 형식으로 변환하는 과정
    

### ArgumentMarshaler

```java
public interface ArgumentMarshaler {
  void set(Iterator<String> currentArgument) throws ArgsException;
}
```

### DoubleArgumentMarshaler

```java
import java.util.Iterator;

import static com.objectmentor.utilities.args.ArgsException.ErrorCode.INVALID_DOUBLE;
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.MISSING_DOUBLE;

public class DoubleArgumentMarshaler implements ArgumentMarshaler {

    private double doubleValue = 0;

    public void set(Iterator<String> currentArgument) throws ArgsException {
        String parameter = null;
        try {
            parameter = currentArgument.next();
            doubleValue = Double.parseDouble(parameter);
        } catch (NoSuchElementException e) {
            throw new ArgsException(MISSING_DOUBLE);
        } catch (NumberFormatException e) {
            throw new ArgsException(INVALID_DOUBLE, parameter);
        }
    }
    public Object get(){
	    return doubleValue;
    }

    public static double getValue(ArgumentMarshaler am) {
        if (am != null && am instanceof DoubleArgumentMarshaler) {
            return ((DoubleArgumentMarshaler) am).doubleValue;
        } else {
            return 0;
        }
    }
}
```

### StringArrayArgumentMarshaler

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

import static com.objectmentor.utilities.args.ArgsException.ErrorCode.INVALID_STRING_ARRAY;
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.MISSING_STRING_ARRAY;

public class StringArrayArgumentMarshaler implements ArgumentMarshaler {

    private List<String> stringArrayValue = new ArrayList<>();

    public void set(Iterator<String> currentArgument) throws ArgsException {
        try {
            String nextArg = currentArgument.next();
            if (!nextArg.startsWith("[") || !nextArg.endsWith("]")) {
                throw new ArgsException(INVALID_STRING_ARRAY, nextArg);
            }
            String[] arrayArgs = nextArg.substring(1, nextArg.length() - 1).split(",");
            for (String arg : arrayArgs) {
                stringArrayValue.add(arg.trim());
            }
        } catch (NoSuchElementException e) {
            throw new ArgsException(MISSING_STRING_ARRAY);
        }
    }
    
    public Object get(){
	    return stringArrayValue;
    }

    public static String[] getValue(ArgumentMarshaler am) {
        if (am != null && am instanceof StringArrayArgumentMarshaler) {
            return ((StringArrayArgumentMarshaler) am).stringArrayValue.toArray(new String[0]);
        } else {
            return new String[0];
        }
    }
}
```

### ArgsException

```java
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class ArgsException extends Exception { 
  private char errorArgumentId = '\0'; 
  private String errorParameter = null; 
  private ErrorCode errorCode = OK;
  
  public ArgsException() {}
  
  public ArgsException(String message) {super(message);}
  
  public ArgsException(ErrorCode errorCode) { 
    this.errorCode = errorCode;
  }
  
  public ArgsException(ErrorCode errorCode, String errorParameter) { 
    this.errorCode = errorCode;
    this.errorParameter = errorParameter;
  }
  
  public ArgsException(ErrorCode errorCode, char errorArgumentId, String errorParameter) {
    this.errorCode = errorCode; 
    this.errorParameter = errorParameter; 
    this.errorArgumentId = errorArgumentId;
  }
  
  public char getErrorArgumentId() { 
    return errorArgumentId;
  }
  
  public void setErrorArgumentId(char errorArgumentId) { 
    this.errorArgumentId = errorArgumentId;
  }
  
  public String getErrorParameter() { 
    return errorParameter;
  }
  
  public void setErrorParameter(String errorParameter) { 
    this.errorParameter = errorParameter;
  }
  
  public ErrorCode getErrorCode() { 
    return errorCode;
  }
  
  public void setErrorCode(ErrorCode errorCode) { 
    this.errorCode = errorCode;
  }
  
  public String errorMessage() { 
    switch (errorCode) {
      case OK:
        return "TILT: Should not get here.";
      case UNEXPECTED_ARGUMENT:
        return String.format("Argument -%c unexpected.", errorArgumentId);
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
      case INVALID_DOUBLE:
        return String.format("Argument -%c expects a double but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_DOUBLE:
        return String.format("Could not find double parameter for -%c.", errorArgumentId); 
      case INVALID_ARGUMENT_NAME:
        return String.format("'%c' is not a valid argument name.", errorArgumentId);
      case INVALID_ARGUMENT_FORMAT:
        return String.format("'%s' is not a valid argument format.", errorParameter);
    }
    return ""; 
  }
  
  public enum ErrorCode {
    OK, INVALID_ARGUMENT_FORMAT, UNEXPECTED_ARGUMENT, INVALID_ARGUMENT_NAME, 
    MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, MISSING_DOUBLE, INVALID_DOUBLE
  }
}
```

코드가 위에서 아래로 읽힘. 자바는 정적 타입 언어라 타입 시스템을 만족하려면 많은 단어가 필요하다. 단순한 개념을 구현하는데도 코드가 많이 필요함.

따라서 이름을 붙이는 방법, 함수 크기, 코드 형식에 각별히 주목해야 한다.

간단하게 ArgumentMarshaler에서 새 클래스를 파생해 getter 함수를 추가한 후 parseSchemaElement함수에 새 case문만 추가하면 끝. 필요하다면 ArgsException.ErrorCode를 만들고 새 오류 메세지를 추가하면 된다.

### 어떻게 짜야하나

깨끗한 코드를 짜려면 먼저 지저분한 코드를 짠 뒤에 정리해야 한다.

주니어 개발자는 무조건 돌아가는 프로그램을 목표로 잡는다. 프로그램이 돌아가면 다음 업무로 넘어간다. 그 상태가 어떻든 넘어간다. 이것은 **자살 행위**

### 초안

1차적인 코드 작성은 미완성적이고, 지저분하게 보인다. 단순히 기능 구현에 초점을 맞추기에 그럴 수 밖에 없을 것

저자도 1차 초안인 Args 클래스를 예시로 보여주었고, TILT와 같은 문자열, HashSets와 TreeSets, try-catch-catch 블록 등 지저분한 코드에 기여하는 요인이 많은  것을 보여줬다.

나름 괜찮아 보이는 코드지만 비교 대상이 늘어나게 되면 점차 지저분해진 이유가 분명히 드러나게 된다.

그렇게 짜여진 코드는 점차 통제에서 벗어나기 시작하게 되고, 엉망이 되어 버린다.

## 지저분한 코드에서 깨끗한 코드로 가기 위한 여정

### 그래서 멈췄다

추가할 기능이 있음에도 코드 구조가 어수선하고, 지저분하다면 훨씬 더 나빠지고, 나아가서는 기능 저하가 발생할 것이다. 밀어붙이면 프로그램은 완성하겠지만 그랬다가는 너무 커져 어려운 골칫거리가 생겨날 것이다. 따라서 기능을 더 이상 추가하지 않고 리펙터링을 시작한다. 

### 점진적으로 개선하다

프로그램을 망치는 가장 좋은 방법 중 하나는 개선이라는 이름 아래 구조를 크게 뒤집는 행위다. 

그저 그런 개선을 하게 되면 이 전과 똑같이 프로그램을 돌리기 아주 어렵기 때문이다.

이 때 TDD(Test-Driven Developemt) 기법을 사용한다. TDD는 언제 어느 때라도 시스템이 돌아가야 한다는 원칙을 따른다. TDD는 시스템을 망가뜨리는 변경을 허용하지 않는다. 변경을 가한 후에도 시스템이 변경 전과 똑같이 돌아가야 한다는 말.

변경 전후에 시스템이 똑같이 돌아간다는 사실을 확인하려면 언제든 실행이 가능한 자동화 된 테스트 슈트가 필요하다.

ArgumentMashaler 클래스 

1. HashMap을 선택하기 위한 스키마 요소의 구문 해석(parse)
2. 명령행 인수에서 인수 유형을 분석해 진짜 유형으로 변환(set)
3. get 메소드를 구현해 호출자에게 진짜 유형 반환(get)

### 시도

각 인수 유형 별로 처리가 아닌 모두 ArgumentMarshaler 클래스에 넣고 ArgumentMarshaler 파생 클래스를 만들어 기능을 분리할 계획

1. ArgumentMarshaler 클래스에 추상 메소드 get / set을 만듦.
2. 초안에 작성되어 있던 인수 유형마다 따로 만든 맵 3개를 없애고 ArgumentMarshaler로 맵을 만들어 관련 메소드를 변경
    1. P.281 - 287

```java
~~private Map<Character,~~ ArgumentMarshaler~~> booleanArgs = new HashMap<Character,~~ ArgumentMarshaler~~>();  
private Map<Character,~~ ArgumentMarshaler~~> stringArgs = new HashMap<Character,~~ ArgumentMarshaler~~>();   
private Map<Character,~~ ArgumentMarshaler~~> intArgs = new HashMap<Character,~~ ArgumentMarshaler~~>();~~

private Map<Character, ArgumentMarshalerr> marshalers = new HashMap<Character, ArgumentMarshaler>();
```

1차 리팩토링 

```java
import java.text.ParseException; 
import java.util.*;

public class Args {
  private String schema;
  private String[] args;
  private boolean valid = true;
  private Set<Character> unexpectedArguments = new TreeSet<Character>(); 
  private Map<Character, Marshaler> marshalers = new HashMap<Character, Marshaler>();
  private Set<Character> argsFound = new HashSet<Character>();
  private int currentArgument;
  private char errorArgumentId = '\0';
  private String errorParameter = "TILT";
  private ErrorCode errorCode = ErrorCode.OK;
  
  private enum ErrorCode {
    OK, MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, UNEXPECTED_ARGUMENT}
    
  public Args(String schema, String[] args) throws ParseException { 
    this.schema = schema;
    this.args = args;
    valid = parse();
  }
  
  private boolean parse() throws ParseException { 
    if (schema.length() == 0 && args.length == 0)
      return true; 
    parseSchema(); 
    try {
      parseArguments();
    } catch (ArgsException e) {
    }
    return valid;
  }
  
  private boolean parseSchema() throws ParseException { 
    for (String element : schema.split(",")) {
      if (element.length() > 0) {
        String trimmedElement = element.trim(); 
        parseSchemaElement(trimmedElement);
      } 
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) throws ParseException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (isBooleanSchemaElement(elementTail)) 
        marshalers.put(elementId, new BooleanArgumentMarshaler());
    else if (isStringSchemaElement(elementTail)) 
        marshalers.put(elementId, new StringArgumentMarshaler());
    else if (isIntegerSchemaElement(elementTail)) 
        marshalers.put(elementId, new IntegerArgumentMarshaler());
    else
        throw new ParseException(String.format("Argument: %c has invalid format: %s.", elementId, elementTail), 0);
    } 
  }
    
  private void validateSchemaElementId(char elementId) throws ParseException { 
    if (!Character.isLetter(elementId)) {
      throw new ParseException("Bad character:" + elementId + "in Args format: " + schema, 0);
    }
  }
  
  private boolean isStringSchemaElement(String elementTail) { 
    return elementTail.equals("*");
  }
  
  private boolean isBooleanSchemaElement(String elementTail) { 
    return elementTail.length() == 0;
  }
  
  private boolean isIntegerSchemaElement(String elementTail) { 
    return elementTail.equals("#");
  }
  
  private boolean parseArguments() throws ArgsException {
    for (currentArgument = 0; currentArgument < args.length; currentArgument++) {
      String arg = args[currentArgument];
      parseArgument(arg); 
    }
    return true; 
  }
  
  private void parseArgument(String arg) throws ArgsException { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg) throws ArgsException { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar) throws ArgsException { 
    if (setArgument(argChar))
      argsFound.add(argChar); 
    else 
      unexpectedArguments.add(argChar); 
      errorCode = ErrorCode.UNEXPECTED_ARGUMENT; 
      valid = false;
  }
  
  private boolean setArgument(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    try {
        if (m instanceof BooleanArgumentMarshaler)
            setBooleanArg(argChar);
        else if (m instanceof StringArgumentMarshaler)
            setStringArg(argChar);
        else if (m instanceof IntegerArgumentMarshaler)
            setIntArg(argChar);
        else
            return false;
    } catch (ArgsException e) {
        valid = false;
        errorArgumentId = argChar;
        throw e;
    }
    return true; 
  }
  
  private void setIntArg(ArgumentMarshaler m) throws ArgsException { 
    currentArgument++;
    String parameter = null;
    try {
      parameter = args[currentArgument];
      m.set(parameter); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_INTEGER;
      throw new ArgsException();
    } catch (ArgsException e) {
      errorParameter = parameter;
      errorCode = ErrorCode.INVALID_INTEGER; 
      throw e;
    } 
  }
  
  private void setStringArg(ArgumentMarshaler m) throws ArgsException { 
    currentArgument++;
    try {
      m.set(args[currentArgument]); 
    } catch (ArrayIndexOutOfBoundsException e) {
      errorCode = ErrorCode.MISSING_STRING; 
      throw new ArgsException();
    } 
  }
  
  private void setBooleanArg(ArgumentMarshaler m) { 
    try {
        m.set("true");
    } catch (ArgsException e) {
    }
  }
  
  public int cardinality() { 
    return argsFound.size();
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public String errorMessage() throws Exception { 
    switch (errorCode) {
      case OK:
        throw new Exception("TILT: Should not get here.");
      case UNEXPECTED_ARGUMENT:
        return unexpectedArgumentMessage();
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
    }
    return ""; 
  }
  
  private String unexpectedArgumentMessage() {
    StringBuffer message = new StringBuffer("Argument(s) -"); 
    for (char c : unexpectedArguments) {
      message.append(c); 
    }
    message.append(" unexpected.");
    
    return message.toString(); 
  }
  
  public boolean getBoolean(char arg) { 
    Args.ArgumentMarshaler am = marshalers.get(arg); 
    boolean b = false;
    try {
      b = am != null && (Boolean) am.get(); 
    } catch (ClassCastException e) {
      b = false; 
    }
    return b; 
  }

  public String getString(char arg) { 
    Args.ArgumentMarshaler am = marshalers.get(arg); 
    try {
       return am == null ? "" : (String)am.get();
      } catch (ClassCastException e) {
        return "";
      }
  }
  
  public int getInt(char arg) {
    Args.ArgumentMarshaler am = marshalers.get(arg); 
    try {
        return am == null ? 0 : (Integer)am.get();
      } catch (ClassCastException e) {
        return 0;
      }
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public boolean isValid() { 
    return valid;
  }
  
  private class ArgsException extends Exception {
  } 
}

private abstract class ArgumentMarshaler { 
    public abstract void set(String s);
    public abstract Object get();
}

private class BooleanArgumentMarshaler extends ArgumentMarshaler {
    private boolean booleanValue = false;

    public void set(String s){
        booleanValue = true;
    }

    public Object get() {
        return booleanValue;
    }
}

private class StringArgumentMarshaler extends ArgumentMarshaler {
    private String stringValue = "";

    public void set(String s){
        stringValue = s;
    }

    public Object get() {
        return stringValue;
    }
}

private class IntegerArgumentMarshaler extends ArgumentMarshaler {
    private int intValue = 0;
    
    public void set(String s) throws ArgsException {
        try {
            intValue = Integer.parseInt(s);
        } catch (NumberFormatException e) {
            throw new ArgsException();
        }
    }

    public Object get() {
        return intValue;
    }
}
```

: 구조가 나아짐. 하지만 첫머리에 나오는 변수가 그대로 남아있고, setArgument에는 유형을 일일이 확인하는 보기 싫은 코드도 그대로 남아있다.

→ setArgument 함수에서 유형을 일일이 확인하는 대신 ArgumentMarshaler.set만 호출하면 되게 수정

setIntArg, setStringArg, setBooleanArg을 ArgumentMarshaler 파생 클래스로 내려야 한다는 뜻

1. args 배열을 list로 변환 후 Iterator를 set 함수로 전달

```java
  private Iterator<String> currentArgument;
  private List<String> argsList;
```

ArgumentMarshaler에 set 함수가 있다면 직접 호출할 수 있다. 

```java
public abstract void set(Iterator<String> currentArgument) throws ArgsException;
```

각 파생 클래스에도 set 메소드 추가

```java
private boolean setArgument(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    if (m == null){
        return false;
    }
    try {
        ~~if (m instanceof BooleanArgumentMarshaler)
            m.set(currentArgument);
        else if (m instanceof StringArgumentMarshaler)
            m.set(currentArgument);
        else if (m instanceof IntegerArgumentMarshaler)
            m.set(currentArgument);~~
        m.set(currentArgument);
    } catch (ArgsException e) {
        valid = false;
        errorArgumentId = argChar;
        throw e;
    }
    return true; 
  }
```

모든 예외 하나로 모아 ArgsException 클래스를 만든 후 독자 모듈로 옮긴다.

Args 클래스가 던지는 예외는 ArgsException뿐이 되고, ArgsException을 독자적인 모듈로 만들면 Args 모듈에서 잡다한 오류 지원 코드를 옮겨올 수 있다.

Args 모듈이 깨끗해져 확장도 쉬워진다.

ArgumentMarshaler를 인터페이스로 변환

```java
private interface ArgumentMarshaler{
	void set(Iterator<String> currentArgument) throws ArgsException;
	Object get();
}
```

→ 새로운 인수 유형을 추가하기 쉬워진다. 변경할 코드는 아주 적으며, 나머지 시스템에 영향을 미치지 않는다.

```java
public class Args {
  private String schema;
  private Map<Character, ArgumentMarshaler> marshalers = new HashMap<Character, ArgumentMarshaler>();
  private Set<Character> argsFound = new HashSet<Character>(); 
  private Iterator<String> currentArgument;
  private List<String> argsList;
  
  public Args(String schema, String[] args) throws ArgsException { 
    this.schema = schema;
    argsList = Arrays.asList(args);
    parse();
  }
  
  private void parse() throws ArgsException { 
    parseSchema();
    parseArguments();
  }
  
  private boolean parseSchema() throws ArgsException {
    for (String element : schema.split(",")) { 
      if (element.length() > 0) {
        parseSchemaElement(element.trim()); 
      }
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) throws ArgsException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (elementTail.length() == 0)
      marshalers.put(elementId, new BooleanArgumentMarshaler());
    else if (elementTail.equals("*")) 
      marshalers.put(elementId, new StringArgumentMarshaler());
    else if (elementTail.equals("#"))
      marshalers.put(elementId, new IntegerArgumentMarshaler());
    else if (elementTail.equals("##")) 
      marshalers.put(elementId, new DoubleArgumentMarshaler());
    else
      throw new ArgsException(ArgsException.ErrorCode.INVALID_FORMAT, elementId, elementTail);
      
  private void validateSchemaElementId(char elementId) throws ArgsException { 
    if (!Character.isLetter(elementId)) {
      throw new ArgsException(ArgsException.ErrorCode.INVALID_ARGUMENT_NAME, elementId, null);
    } 
  }
  
  private void parseArguments() throws ArgsException {
    for (currentArgument = argsList.iterator(); currentArgument.hasNext();) {
      String arg = currentArgument.next();
      parseArgument(arg); 
    }
  }
  
  private void parseArgument(String arg) throws ArgsException { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg) throws ArgsException { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar) throws ArgsException { 
    if (setArgument(argChar))
      argsFound.add(argChar); 
    else 
      throw new ArgsException(ArgsException.ErrorCode.UNEXPECTED_ARGUMENT, argChar, null);
  } 
  
  private boolean setArgument(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    if (m == null)
      return false; 
    try {
      m.set(currentArgument);
      return true;
    } catch (ArgsException e) {
      e.setErrorArgumentId(argChar);
      throw e; 
    }
  }
  
  public int cardinality() { 
    return argsFound.size();
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public boolean getBoolean(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    boolean b = false;
    try {
      b = am != null && (Boolean) am.get(); 
    } catch (ClassCastException e) {
      b = false; 
    }
    return b; 
  }
  
  public String getString(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? "" : (String) am.get(); 
    } catch (ClassCastException e) {
      return ""; 
    }
  }
  
  public int getInt(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? 0 : (Integer) am.get(); 
    } catch (Exception e) {
      return 0; 
    }
  }
  
  public double getDouble(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? 0 : (Double) am.get(); 
    } catch (Exception e) {
      return 0.0; 
    }
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  } 
}
```

소프트웨어 설계는 분할만 잘해도 품질이 크게 높아진다. 
적절한 장소를 만들어 코드만 분리해도 설계가 좋아진다. 

관심사를 분리하면 코드를 이해하고 보수하기 훨씬 더 좋아진다.

<aside>
💡 그저 돌아가는 코드만으로는 부족하다. 돌아가는 코드가 심하게 망가지는 사례는 흔하다.
나쁜 코드보다 더 오랫동안 더 심각하게 개발 프로젝트에 악역향을 미치는 요인도 없다.
처음부터 코드를 깨끗하게 유지하기란 상대적으로 쉽다.
코드는 언제나 최대한 깔끔하고 단순하게 정리하자!

</aside>

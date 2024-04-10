## 14장

### Args 구현

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
  
  // 문자열 스키마 파싱
  private void parseSchema(String schema) throws ArgsException { 
    for (String element : schema.split(","))
      if (element.length() > 0) 
        parseSchemaElement(element.trim());
  }
  
  // 스키마 요소를 파싱하여 marshalers 매개변수에 put
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
  
  // 스키마의 ID가 유효한지
  private void validateSchemaElementId(char elementId) throws ArgsException { 
    if (!Character.isLetter(elementId))
      throw new ArgsException(INVALID_ARGUMENT_NAME, elementId, null); 
  }
  
  // 문자열 파싱하며 다음 인수가 있는지 확인
  private void parseArgumentStrings(List<String> argsList) throws ArgsException {
    for (currentArgument = argsList.listIterator(); currentArgument.hasNext();) {
      String argString = currentArgument.next(); 
      if (argString.startsWith("-")) {
        parseArgumentCharacters(argString.substring(1)); 
      } else {
        currentArgument.previous();
        break; 
      }
    } 
  }
  
  private void parseArgumentCharacters(String argChars) throws ArgsException { 
    for (int i = 0; i < argChars.length(); i++)
      parseArgumentCharacter(argChars.charAt(i)); 
  }
  
  // ArgumentMarshaler에 값을 할당
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
```

**메서드의 호출 메서드는 바로 아래에 작성되어 있어 코드가 순차적으로 읽힌다.**

```java
public interface ArgumentMarshaler {
  void set(Iterator<String> currentArgument) throws ArgsException;
}
```

```java
// Boolean 타입의 값을 확인하고 가져옴
public class BooleanArgumentMarshaler implements ArgumentMarshaler { 
  private boolean booleanValue = false;
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    booleanValue = true;
  }
  
  public static boolean getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof BooleanArgumentMarshaler)
      return ((BooleanArgumentMarshaler) am).booleanValue; 
    else
      return false; 
  }
}
```

```java
// String 타입의 값을 확인하고 가져옴
public class StringArgumentMarshaler implements ArgumentMarshaler { 
  private String stringValue = "";
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    try {
      stringValue = currentArgument.next(); 
    } catch (NoSuchElementException e) {
      throw new ArgsException(MISSING_STRING); 
    }
  }
  
  public static String getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof StringArgumentMarshaler)
      return ((StringArgumentMarshaler) am).stringValue; 
    else
      return ""; 
  }
}
```

```java
// Integer 타입의 값을 확인하고 가져옴
public class IntegerArgumentMarshaler implements ArgumentMarshaler { 
  private int intValue = 0;
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    String parameter = null;
    try {
      parameter = currentArgument.next();
      intValue = Integer.parseInt(parameter);
    } catch (NoSuchElementException e) {
      throw new ArgsException(MISSING_INTEGER);
    } catch (NumberFormatException e) {
      throw new ArgsException(INVALID_INTEGER, parameter); 
    }
  }
  
  public static int getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof IntegerArgumentMarshaler)
      return ((IntegerArgumentMarshaler) am).intValue; 
    else
    return 0; 
  }
}
```

**오류 처리 클래스**

```java
public class ArgsException extends Exception { 
  private char errorArgumentId = '\0'; 
  private String errorParameter = null; 
  private ErrorCode errorCode = OK;
    
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
    OK, INVALID_ARGUMENT_FORMAT, UNEXPECTED_ARGUMENT, 
    INVALID_ARGUMENT_NAME, 
    MISSING_STRING, 
    MISSING_INTEGER, INVALID_INTEGER, 
    MISSING_DOUBLE, INVALID_DOUBLE
  }
}
```

- 날짜, 복소수 인수 등 새로운 인수 유형을 추가하는 방법이 명백하고 고칠 코드도 별로 없다.
- ArgumentMarshaler에서 새 클래스를 파생해 getXXX 함수를 추가한 후 parseSchemaElement 함수에 새로운 case 문만 추가하면 끝이다.

 **어떻게 짰느냐고?**

- 깨끗한 코드를 짜려면 먼저 지저분한 코드를 짠 뒤에 정리해야 한다.
- 깔끔한 작품을 내놓으려면 단계적으로 개선해야 한다.

### Args: 1차 초안

```java
public class Args {
	private String schema;
	private String[] args;
	private boolean valid = true;
	private Set<Character> unexpectedArguments = new TreeSet<Character>();
	private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
	private Map<Character, String> stringArgs = new HashMap<Character, String>();
	private Map<Character, Integer> intArgs = new HashMap<Character, Integer>();
	private Set<Character> argsFound= new HashSet<Character>();
	private int currentArgument;
	private char errorArgumentId = '\0';
	private String errorParameter = "TILT";
	private ErrorCode errorCode = ErrorCode.OK;
```

- “TILT”와 errorArgumentId와 같이 목적이 불명확한 필드들이 포함되어 있다.
- Args 클래스에서 예외/오류 처리 코드를 담당하고 있다.

**그래서 멈췄다**

1. 인수 유형에 해당하는 HashMap을 선택하기 위해 스키마 요소의 구문을 분석한다.
2. 명령행 인수에서 인수 유형을 분석해 진짜 유형으로 변환한다.
3. getXXX 메서드를 구현해 호출자에게 진짜 유형을 반환한다.

**점진적으로 개선하다**

- 프로그램을 망치는 가장 좋은 방법 중 하나는 개선이라는 이름 아래 구조를 크게 뒤집는 행위이다.

```java
private abstract class ArgumentMarshaler {
	public abstract void set(Iterator<String> currentArguemnt) throws ArgsExcetpion;
	public abstract void set(String s) throws ArgsException;
	public abstract Object get();
}

private class IntegerArgumentMarshaler extends ArgumentMarshaler {
	private int intValue = 0
	
	public void set(Iterator<String> currentArguemnt) throws ArgsExcetpion {
		String parameter = null;
		try {
			parameter = currentArgument.next();
			intValue = Integer.parseInt(parameter);
		} catch (NosuchElementException e) {
			errorCode = ErrorCode.MISSING_INTEGER;
			throw new ArgsExcetpion();
		} catch (NumberFormatException e) {
			errorParameter = parameter;
			errorCode = ErrorCode.INVALID_INTEGER;
			throw new ArgsException();
		}
	}			

private interface ArgumentMarshaler {
	void set(Iterator<String> currentArguemnt) throws ArgsExcetpion;
	Object get();
}
```

- 추상 클래스를 상속할때는 해당 클래스와 높은 결합도를 가질 수 있다.
- 인터페이스를 사용하여 느슨한 결합도를 가질 수 있다.

```java
public class ArgsExcetpion extends Exception {
	private char errorArgumentId = "\0";
	private String errorParameter = "TILT";
	private ErrorCode errorCdoe = ErrorCode.OK;
	
	public ArgsExcetpion() {}
	
	public ArgsException(String message) {super(message);}
	
	public enum ErrorCode {
		OK, MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER,
		UNEXPECTED_ARGUMENT, MISSING_DOUBLE, INVALID_DOUBLE}
}

```

- ArgsException와 같이 독자적인 모듈로 만들면 Args 모듈에서 잡다한 오류 지원 코드를 옮겨 올 수 있다.
- Args 모듈의 차후 확장도 쉬워진다.
- 소프트웨어 설계는 분할만 잘해도 품질이 크게 높아진다.
- 적절한 장소를 만들어 코드만 분리해도 설계가 좋아진다.
- 관심사를 분리하면 코드를 이해하고 보수하기 훨씬 더 쉬워진다.

```java
public class Args {
	private String schema;
	private String[] args;
	private boolean valid = true;
	private Set<Character> unexpectedArguments = new TreeSet<Character>();
	private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
	private Map<Character, String> stringArgs = new HashMap<Character, String>();
	private Map<Character, Integer> intArgs = new HashMap<Character, Integer>();
	private Set<Character> argsFound= new HashSet<Character>();
	private int currentArgument;
	private char errorArgumentId = '\0';
	private String errorParameter = "TILT";
	private ErrorCode errorCode = ErrorCode.OK;
```

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
				parseSchemaElement(element,trim());
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
		else if (elementTail.equals("*");
			marshalers.put(elementId, new StringArgumentMarshaler());
		else if (elementTail.equals("#");
			marshalers.put(elementId, new IntegerArgumentMarshaler());
		else if (elementTail.equals("##");
			marshalers.put(elementId, new DoubleArgumentMarshaler());
		else 
			throw new ArgsException(ArgsException.ErrorCode.INVALID_FORMAT,
															elementId, elementTail);
	}
	
	private void validateSchemaElementId(char elementId) throws ArgsException {
		if (!Character.isLetter(elementId)) {
			throw new ArgsException(ArgsException.ErrorCode.INVALID_ARGUMENT_NAME<
															elementId, null);
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
			parseElements(args);
	}
	
	private void parseElements(String arg) throws ArgsException {
		for (int i = 1; i < arg.length(); i++)
			parseElement(arg.charAt(i));
	}
	
	private void parseElement(char argChar) throws ArgsException {
		if (setArgument(argCahr))
			argsFound.add(argChar);
		else {
			throw new ArgsException(ArgsException.ErrorCode.UNEXPECTED_ARGUMENT,
															argChar, null);
		}
	}
	
	private boolean setArgument(cahr argChar) throws ArgsException {
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
				return am = null ? "" : (String) am.get();
			} catch (ClassCastException e) {
				return "";
			}
		}
		
		public int getInt(char arg) {
			ArgumentMarshaler am = marshalers.get(arg);
			try {
				return am = null ? 0 : (Integer) am.get();
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

**클래스의 역할과 책임을 분리시켜 Args 클래스의 필드가 명확해졌다.**
	

### 결론

- 나쁜 코드보다 더 오랫동안 더 심각하게 개발 프로젝트에 악영향을 미치는 요인도 없다.
- 코드는 언제나 최대한 깔끔하고 단순하게 정리하자.
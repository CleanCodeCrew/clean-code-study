### 자바

**J1: 긴 import 목록을 피하고 와일드카드를 사용하라**

- 패키지에서 클래스를 둘 이상 사용한다면 와일드카드를 사용해 패키지 전체를 가져오라.

**J2: 상수는 상속하지 않는다**

- 상수를 인터페이스에 넣은 다음 그 인터페이스를 상속해 해당 상수를 사용하는 경우는 static import를 사용하라.

**J3: 상수 대 Enum**

- public static final int 보다는 Enum 클래스를 사용하자.

### 이름

**N1: 서술적인 이름을 사용하라**

- 소프트웨어 가독성의 90%는 이름이 결정한다.
- 시간을 들여 현명한 이름을 선택하고 유효한 상태로 유지한다.

**N2: 적절한 추상화 수준에서 이름을 선택하라**

- 구현을 드러내는 이름은 피하라
- 작업 대상 클래스나 함수가 위치하는 추상화 수준을 반영하는 이름을 선택하라

**N3: 가능하다면 표준 명명법을 사용하라**

- 기존 명명법을 사용하는 이름은 이해하기 더 쉽다.
- 프로젝트에 유효한 의미가 담긴 이름을 많이 사용할수록 독자가 코드를 이해하기 쉬워진다.

**N4: 명확한 이름**

- 함수나 변수의 목적을 명확히 밝히는 이름을 선택한다.

**N5: 긴 범위는 긴 이름을 사용하라**

- 범위가 작으면 아주 짧은 이름을 사용해도 괜찮다.

**N6: 인코딩을 피하라**

- 이름에 유형 정보나 범위 정보를 넣어서는 안 된다.

**N7: 이름으로 부수 효과를 설명하라**

- 함수, 변수, 클래스가 하는 일을 모두 기술하는 이름을 사용한다.
- 이름에 부수 효과를 숨기지 않는다.

### 테스트

**T1: 불충분한 테스트**

- 테스트 케이스는 잠재적으로 깨질 만한 부분을 모두 테스트해야 한다.

**T2: 커버리지 도구를 사용하라**

- 테스트가 불충분한 모듈, 클래스, 함수를 찾기가 쉬워진다.

**T4: 무시한 테스트는 모호함을 뜻한다**

- 불분명한 요구사항은 테스트 케이스를 주석으로 처리하거나 테스트 케이스에 @Ignore을 붙여 표현한다.

**T6: 버그 주변은 철저히 테스트하라**

- 버그는 서로 모이는 경항이 있다, 한 함수에서 버그를 발견했다면 그 함수를 철저히 테스트하는편이 좋다.

**T7: 실패 패턴을 살펴라**

- 합리적인 순서로 정렬된 꼼꼼한 테스트 케이스는 실패 패턴을 드러낸다.

**T8: 테스트 커버리지 패턴을 살펴라**

- 통과하는 테스트가 실행하거나 실행하지 않는 코드를 살펴보면 실패하는 테스트 케이스의 실패 원인이 드러난다.

**T9: 테스트는 빨라야 한다.**

- 테스트 케이스가 빨리 돌아가게 최대한 노력한다.

### 동시성

**I/O** 

- 소켓 사용, 데이터베이스 연결, 가상 메모리 스와핑 기다리기 등에 시간을 보낸다.

**프로세서**

- 수치 게산, 정규 표현식 처리, 가비지 컬렉션 등에 시간을 보낸다.

**프로그램이 주로 프로세서 연산에 시간을 보낸다면, 새로운 하드웨어를 추가해 성능을 높여 테스트를 통과하는 방식이 적합하다.**

**프로그램이 주로 I/O 연산에 시간을 보낸다면 동시성이 성능을 높여주기도 한다.**

**서버 살펴보기**

1. 소켓 연결 관리
2. 클라이언트 처리
3. 스레드 정책
4. 서버 종료 정책

**문제**

- 단일 책임 원칙 위반

**결론**

- 동시성은 그 자체가 복잡한 문제이므로 다중 스레드 프로그램에서는 SRP가 중요하다.

### 가능한 실행 경로

**경로 수**

루프나 분기가 없는 명령 N개를 스레드 T개가 차례로 실행한다면 가능한 경우의 수

- (NT)! / N!^T
- snchronized 키워드를 사용할때 스레드가 N개라면 가능한 경로 수는 N!이다.

### 심층 분석

원자적 연산

- 중단이 불가능한 연산

프레임

- 반환 주소, 메서드로 넘어온 매개변수, 메서드가 정의하는 지역 변수를 포함한다.

지역 변수

- 메서드 범위 내에 정의되는 모든 변수

피연산자 스택

- JVM이 지원하는 명령을 저장하는 장소
- LIFO 자료구조

### 라이브러리를 이해하라

**Executor 프레임워크**

- 스레드 풀링으로 정교한 실행을 지원한다.
- 애플리케이션에서 스레드는 생성하나 스레드 풀을 사용하지 않는다면 사용을 고려하자.
- 직접 생성한 스레드 풀을 사용할때 사용을 고려하자.
- Executor 클래스를 사용하면 코드가 깔끔해지고, 이해하기 쉬워지고, 크기가 작아진다.
- 스레드 풀을 관리하고 풀 크기를 자동으로 조정하며 필요하다면 스레드를 재사용한다.
- 다중 스레드 프로그래밍에서 많이 사용하는 Future을 지원한다.
- Runnable 인터페이스를 구현한 클래스는 물론 Callable 인터페이스를 구현한 클래스도 지원한다.

**스레드를 차단하지 않는 방법**

```java
public class ObjectWithValue {
	private int value;
		
	public void synchronized incrementValue() { ++value; }
	public int getValue() { return value; }
}
```

- synchronized 키워드를 사용하여 동기화를 수행한다 - 스레드를 차단한다.

**AtomicInteger**

```java
public class ObjectWithValue {
	private AtomicInteger value = new AtomicInteger(0);
	
	public void incrementValue() {
		value.incrementAndGet();
	}
	
	public int getValue() {
		return value.get();
	}
}
```

- synchronized 키워드보다 거의 항상 더 빠르다. - synchronized 키워드는 언제나 락을 걸기 때문이다.
- CAS 연산을 지원한다. - 낙관적 잠금와 유사하다.

**다중 스레드 환경에서 안전하지 않은 클래스**

- SimpleDateFormat
- 데이터베이스 연결
- java.util 컨테이너 클래스
- 서블릿

**다중 스레드 환경에서 안전한 클래스**

- java.util.concurrent

**메서드 사이에 존재하는 의존성을 조심하라**

**IntegerIterator**

```java
public class IntegerIterator implements Iterator<Integer> {
	private Integer nextValue = 0;
	
	public synchronized boolean hasNext() {
		return nextValue < 100000;
	}
	
	public synchronized Integer next() {
		if (nextValue == 100000)
			throw nex IteratorPastEndException();
		return nextValue++;
	}
	
	public synchronized Integer getNextValue() {
		return nextValue;
	}
}

IntegerIterator iterator = new IntegerIterator();
while(iterator.hasNext()) {
	int nextValue = iterator.next();
}
	
```

스레드 두 개가 IntegerIterator 인스턴스 하나를 공유할때 맨 끝에 두 스레드가 서로를 간섭해 한 스레드가 끝을 지나치는 바람에 예외가 발생할 가능성이 작게나마 존재한다.

**해결 방안**

- 실패를 용인한다.
- 클라이언트를 바꿔 문제를 해결한다. 즉, 클라이언트-기반 잠금 메커니즘을 구현한다.
- 서버를 바꿔 문제를 해결한다. 서버에 맞춰 클라이언트도 바꾼다. 즉, 서버-기반 잠금 메커니즘을 구현한다.

**실패를 용인한다**

- 때로는 실패해도 괜찮도록 프로그램을 조정할 수 있다.

**클라이언트-기반 잠금**

```java

IntegerIterator iterator = new IntegerIterator();
while(true) {
	int nextValue;
	synchronized (iterator) {
		if (!iterator.hasNext())
			break;
		nextValue = iterator.next();
		}
		doSometingWith(nextValue);
}
```

synchronized 키워드를 이용해 IntegerIterator 객체에 락을 건다.

**서버-기반 잠금**

```java
// 서버
public class IntegerIteratorServerLocked {
	private Integer nextValue = 0;
	public synchronized Integer getNextOrNull() {
	if (nextValue < 100000)
		return nextValue++;
	else
		return null;
	}
}

// 클라이언트
while (true) {
	Integer nextValue = iterator.getNextOrNull();
	if (next == null)
		break;
	}
```

일반적으로 서버-기반 잠금이 더 바람직하다.

- 코드 중복이 줄어든다.
- 성능이 좋아진다.
- 오류가 발생할 가능성이 줄어든다.
- 스레드 정책이 하나다.
- 공유 변수 범위가 줄어든다.

### 데드락

**데드락의 발생 조건**

상호 배제

- 여러 스레드가 한 자원을 공유하나 그 자원은 여러 스레드가 동시에 사용하지 못하며 개수가 제한적일때

잠금 & 대기

- 스레드가 자원을 점유하면 필요한 나머지 자원까지 모두 점유해 작업을 마칠 때까지 이미 점유한 자원을 내놓지 않는다.

선점 불가

- 한 스레드가 다른 스레드로부터 자원을 빼앗지 못한다.

순환 대기

- 여러 개의 스레드가 각 스레드가 필요한 리소스를 점유하고 있다.

**상호 배제 조건 깨기**

- 동시에 사용해도 괜찮은 자원을 사용한다.
- 스레드 수 이상으로 자원 수를 늘인다.
- 자원을 점유하기 전에 필요한 자원이 모두 있는지 확인한다.

**잠금 && 대기 조건 깨기**

기아

- 한 스레드가 계속해서 필요한 자원을 점유하지 못한다.

라이브락

- 여러 스레드가 한꺼번에 잠금 단계로 진입하는 바람에 계속해서 자원을 점유했다 내놨다를 반복한다.

**선점 불가 조건 깨기**

- 다른 스레드로부터 자원을 뺏어온다.

**순환 대기 조건 깨기**

- 모든 스레드가 일정 순서에 동의하고 그 순서로만 자원을 할당한다.

### 다중 스레드 코드 테스트

**몬테 카를로 테스트**

- 조율이 가능하게 유연한 테스트를 만든다.
- 그런 다음 임의로 값을 조율하면서 반복해 돌린다.
- 테스트가 실패하면 버그가 있다는 증거다.

### 스레드 코드 테스트를 도와주는 도구

**IBM의 ConTest**

1. 실제 코드와 테스트 코드를 작성한다.
2. ConTest로 실제 코드와 테스트 코드에 보조 코드를 추가한다.
3. 테스트를 실행한다.
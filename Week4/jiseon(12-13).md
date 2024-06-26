# 4주차 정리

## 12장. 창발성

### 창발적 설계 규칙 (중요도순)

- 모든 테스트를 실행한다.
- 중복을 없앤다.
- 프로그래머 의도를 표현한다.
- 클래스와 메서드 수를 최소로 줄인다.

### 단순한 설계 규칙 1: 모든 테스트를 실행하라

테스트를 철저히 거쳐 모든 테스트 케이스를 항상 통과하는 시스템 = 테스트가 가능한 시스템

⇒ 테스트가 불가능한 시스템은 검증도 불가능하다. 검증이 불가능한 시스템은 절대 출시하면 안된다.

- 철저한 테스트가 가능한 시스템 → SRP를 준수하는 클래스, 낮은 결합도 → 더 나은 설계

### 단순한 설계 규칙 2~4: 리팩터링

앞서 테스트 케이스를 작성했기 때문에 코드를 정리하면서 시스템이 깨질 걱정할 필요가 없다.

- 이 단계에서 다양한 리팩토링 기법 동원
    - 응집도 높이기
    - 결합도 낮추기
    - 관심사 분리
    - 시스템 관심사를 모듈로 분리
    - 함수와 클래스 크기 줄이기
    - 더 나은 이름 선택

### 중복을 없애라

> 중복 = 추가 작업, 추가 위험, 불필요한 복잡도
> 

**일부 코드가 중복되는 예**

```java
public void scaleToOneDimension(float desiredDimension, float imageDimension) {
  if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
    return;
  float scalingFactor = desiredDimension / imageDimension;
  scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);

  RenderedOpnewImage = ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor);
  image.dispose();
  System.gc();
  image = newImage;
}

public synchronized void rotate(int degrees) {
  RenderedOpnewImage = ImageUtilities.getRotatedImage(image, degrees);
  image.dispose();
  System.gc();
  image = newImage;
}
```

scaleToOneDimension 메서드와 rotate 메서드의 중복되는 코드를 replaceImage 메서드로 분리

```java
public void scaleToOneDimension(float desiredDimension, float imageDimension) {
  if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
    return;
  float scalingFactor = desiredDimension / imageDimension;
  scalingFactor = (float) Math.floor(scalingFactor * 10) * 0.01f);
  replaceImage(ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor));
}

public synchronized void rotate(int degrees) {
  replaceImage(ImageUtilities.getRotatedImage(image, degrees));
}

private void replaceImage(RenderedOpnewImage) {
  image.dispose();
  System.gc();
  image = newImage;
}
```

→ 소규모 재사용을 제대로 익히면 시스템 복잡도를 극적으로 줄일 수 있다. 소규모 재사용을 제대로 익혀야 대규모 재사용이 가능하다.

**템플릿 메서드 패턴으로 중복을 제거하는 예**

<img src="https://github.com/CleanCodeCrew/clean-code-study/assets/63100425/92c25869-6ae3-4bea-8a2d-5ca689bdeaab" width="600" height="600"/>

ref. refactoring.guru

해당 패턴은 고차원 중복을 제거하는 목적으로 자주 사용(알고리즘의 특정 단계들만 확장하고, 전체 알고리즘이나 구조는 확장하지 못하도록 고정시킬 때)

```java
public class VacationPolicy {
  public void accrueUSDDivisionVacation() {
    // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
    // ...
    // 휴가 일수가 미국 최소 법정 일수를 만족하는지 확인하는 코드
    // ...
    // 휴가 일수를 급여 대장에 적용하는 코드
    // ...
  }

  public void accrueEUDivisionVacation() {
    // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
    // ...
    // 휴가 일수가 유럽연합 최소 법정 일수를 만족하는지 확인하는 코드
    // ...
    // 휴가 일수를 급여 대장에 적용하는 코드
    // ...
  }
}
```

템플릿 메서드 패턴을 적용하여 눈에 들어오는 중복을 제거하고, 하위 클래스는 중복되지 않는 정보만 제공해 accrueVacation 알고리즘에서 빠진 구멍을 메워준다.

```java
abstract public class VacationPolicy {
  public void accrueVacation() {
    caculateBseVacationHours();
    alterForLegalMinimums();
    applyToPayroll();
  }

  private void calculateBaseVacationHours() { /* ... */ };
  abstract protected void alterForLegalMinimums();
  private void applyToPayroll() { /* ... */ };
}

public class USVacationPolicy extends VacationPolicy {
  @Override protected void alterForLegalMinimums() {
    // 미국 최소 법정 일수를 사용한다.
  }
}

public class EUVacationPolicy extends VacationPolicy {
  @Override protected void alterForLegalMinimums() {
    // 유럽연합 최소 법정 일수를 사용한다.
  }
}
```

### 표현하라

소프트웨어 프로젝트 비용 중 대다수는 장기적인 유지보수에 들어간다. 시스템이 점차 복잡해지면서 유지보수 개발자가 시스템을 이해하느라 보내는 시간은 점점 늘어나고 동시에 코드를 오해할 가능성도 점점 커진다.

**개발자의 의도를 분명히 표현하고 코드를 명백히 짜는 법**

- 좋은 이름을 선택한다.
- 함수와 클래스 크기를 가능한 줄인다.
- 표준 명칭을 사용한다. 예를 들어, 디자인 패턴은 의사소통과 표현력 강화가 주요 목적이다. 클래스 이름에 패턴 이름을 넣어주면 설계 의도를 이해하기 쉬워진다.
- 단위 테스트 케이스를 꼼꼼히 작성한다.

> 표현력을 높이는 가장 중요한 방법은 노력이다. 코드만 돌린 후 다음 문제로 직행하는 사례가 너무 흔하다. 나중에 읽을 사람을 고려해 조금이라도 읽기 쉽게 만드려는 충분한 고민을 해야 함을 명심하자.
더 나은 이름을 선택하고, 큰 함수를 작은 함수 여럿으로 나누고, 자신의 작품에 조금만 더 주의를 기울이자.
> 

### 클래스와 메서드 수를 최소로 줄여라

중복을 제거하고, 의도를 표현하고, SRP를 준수한다는 기본적인 개념도 극단으로 치달으면 득보다 실이 많아진다. 

→ 클래스와 메서드 크기를 줄이면서, 그 수는 가능한 줄여야 한다. 무의미하고 독단적인 정책을 멀리하고 실용적인 방식을 택해라

목표는 함수와 클래스 크기를 작게 유지하면서 동시에 시스템 크기도 작게 유지하는 데 있다.

<br>

## 13장. 동시성

### 동시성이 필요한 이유?

동시성은 결합을 없애는 전략이다. 즉, **무엇(what)** 과 **언제(when)** 를 분리하는 전략이다. 

1. 구조적 개선
    
    **무엇**과 **언제**를 분리하면 애플리케이션 구조와 효율이 극적으로 나아진다. 구조적인 관점에서 프로그램은 거대한 루프 하나가 아니라 작은 협력 프로그램 여럿으로 보이게 되고, 이는 곧 시스템을 이해하기 쉽고 문제를 분리하기도 쉬워진다.
    
2. 응답 시간과 작업 처리량 개선

**동시성에 관련한 미신과 오해**

- 동시성은 항상 성능을 높여준다.
    
    : 대기 시간이 아주 길어 여러 스레드가 프로세서를 공유할 수 있거나 여러 프로세서가 동시에 처리할 독립적인 계산이 충분히 많은 경우에만 성능이 높아진다.
    
- 동시성을 구현해도 설계는 변하지 않는다.
    
    : 단일 스레드 시스템과 다중 스레드 시스템은 설계가 판이하게 다르다.
    
- 웹 또는 EJB 컨테이너를 사용하면 동시성을 이해할 필요가 없다.
    
    : 실제로는 컨테이너가 어떻게 동작하는지, 어떻게 동시 수정, 데드락 등과 같은 문제를 피할 수 있는지를 알아야만 한다.
    

**동시성과 관련된 타당한 생각 몇 가지**

- 동시성은 다소 부하를 유발한다. 성능 측면에서 부하가 걸리며 코드도 더 짜야한다.
- 동시성은 복잡하다. 간단한 문제라도 동시성은 복잡하다.
- 일반적으로 동시성 버그는 재현하기 어렵다. 그래서 진짜 결함으로 간주되지 않고 일회성 문제로 여겨 무시하기 쉽다.
- 동시성을 구현하려면 흔히 근본적인 설계 전략을 재고해야 한다.

**동시성을 구현하기 어려운 이유에 대한 예제**

```java
public class X {
  private int lastIdUsed;

  public int getNextId() {
    return ++lastIdUsed;
  }
}
```

lastIdUsed 필드를 42로 설정한 다음, 두 스레드가 해당 인스턴스를 공유한다. 결과는 아래 셋 중 하나이다.

- 한 스레드는 43을 받고 다른 스레드는 44를 받는다. → lastIdUsed = 44
- 한 스레드는 44를 받고 다른 스레드는 43을 받는다. → lastIdUsed = 44
- 두 스레드 모두 43을 받는다. → lastIdUsed = 43

⇒ 대다수는 올바른 결과를 내지만, 문제는 잘못된 결과를 내놓는 **일부** 경로다.

### 동시성 방어 원칙

- **단일 책임 원칙 (Single Responsibility Principle, SRP)**

    : SRP는 주어진 메서드/클래스/컴포넌트를 변경할 이유가 하나여야 한다는 원칙이다.

    → 동시성 관련 코드는 다른 코드와 분리하라.

- **자료 범위를 제한하라**

    : 공유 객체를 사용하는 임계 영역은 synchronized 키워드로 보호하고, 이런 임계 영역의 수를 줄여야 한다.

    → 자료를 캡슐화하라. 공유 자료를 최대한 줄여라.

- **자료 사본을 사용하라**

    : 공유 자료를 줄이려면 처음부터 공유하지 않는 방법이 제일 좋다. 객체를 복사해 사용하는 것의 비용이 공유 자원을 사용하는 비용을 상쇄할 가능성이 크다.

- **스레드는 가능한 독립적으로 구현하라**

    : 독자적인 스레드로, 가능하면 다른 프로세서에서, 돌려도 괜찮도록 자료를 독립적인 단위로 분할하라.

    → 독자적인 스레드는 클라이언트 요청 하나를 처리한다. 모든 정보는 비공유 출처에서 가져오며 로컬 변수에 저장한다.

### 실행 모델을 이해하라

**기본 개념**

- 한정된 자원 (Bound Resource)
    - 다중 스레드 환경에서 사용하는 한정된 자원으로 크기나 숫자가 제한적
    - e.g. 데이터베이스 연결, 길이가 일정한 읽기/쓰기 버퍼
- 상호 배제 (Mutual Exclusion)
    - 한 번에 한 스레드만 공유 자원을 사용할 수 있는 경우
- 기아 (Starvation)
    - 한 스레드가 굉장히 오랫동안 혹은 영원히 자원을 기다리는 경우
    - e.g. 항상 제일 짧은 스레드에게 우선순위를 줄 때, 긴 스레드가 기아 상태에 빠질 수 있음
- 데드락 (Deadlock)
    - 여러 스레드가 서로가 끝나기를 기다리는 경우
    - e.g. 모든 스레드가 각기 필요한 자원을 다른 스레드가 점유한 상태
- 라이브락 (Livelock)
    - 락을 거는 단계에서 각 스레드가 서로를 방해하는 경우

**실행 모델**

일상에서 접하는 대다수 다중 스레드 문제는 아래 세 범주 중 하나에 속한다. 각 알고리즘을 공부하고 해법을 구현해보는 연습을 해라.

1. 생산자-소비자 문제 (Producer-Consumer problem)
    
    : 여러 개의 프로세스를 어떻게 동기화할 것인가에 관한 고전적인 문제 (한정 버퍼 문제)
    
    유한한 개수의 물건(데이터)을 임시로 보관하는 보관함(버퍼)에 여러 명의 생산자들과 소비자들이 접근한다.
    
    - 생산자는 물건이 하나 만들어지면 그 공간에 저장한다. 이때 저장할 공간이 없는 문제가 발생할 수 있다. → 빈 공간이 생길 때까지 대기
    - 소비자는 물건이 필요할 때 보관함에서 물건을 하나 가져온다. 이 때는 소비할 물건이 없는 문제가 발생할 수 있다. → 정보가 채워질 때까지 대기
    - 서로에게 시그널을 보내는데, 둘 다 진행 가능함에도 불구하고 동시에 서로에게 시그널을 기다리는 상태가 될 가능성이 있다.
2. 읽기-쓰기 문제 (Readers-Writers problem)

   : 여러 명의 독자와 저자들이 하나의 저장 공간(버퍼)을 공유하며 이를 접근할 때 발생하는 문제  

      - 독자는 공유 공간에서 데이터를 읽어온다. 여러 명의 독자가 동시에 데이터를 읽어오는 것이 가능하다.
      - 저자는 공유 공간에 데이터를 쓴다. 한 저자가 공유 공간에 데이터를 쓰고 있는 동안에는 그 저자만 접근이 가능하며, 다른 독자들과 저자들은 접근할 수 없다.
      - 각자의 요구를 적절히 만족시켜 처리율도 적당히 높이고 기아를 방지하는 해법이 필요하다.

3. 식사하는 철학자들 문제 (Dining Philosophers problem)

    : 동시성과 교착 상태를 설명하는 예시로, 여러 프로세스가 동시에 돌아갈 때 교착 상태가 나타나는 원인을 직관적으로 알 수 있다.
   <img src="https://github.com/CleanCodeCrew/clean-code-study/assets/63100425/278e5f42-4401-447c-9bf0-c4f98fb1f226" width="300" height="300"/>
   
   - 다섯 명의 철학자가 원탁에 앉아 있고, 각자의 앞에는 스파게티가 있고 양옆에 포크가 하나씩 있다. 그리고 각각의 철학자는 다른 철학자에게 말을 할 수 없다. 이때 철학자가 스파게티를 먹기 위해서는 양 옆의 포크를 동시에 들어야 한다.
   - 이때 각각의 철학자가 왼쪽의 포크를 들고 그 다음 오른쪽의 포크를 들어서 스파게티를 먹는 알고리즘을 가지고 있으면, 다섯 철학자는 동시에 왼쪽의 포크를 들 수 있으나 오른쪽의 포크는 이미 가져가진 상태이기 때문에 다섯 명 모두가 무한정 서로를 기다리는 **교착 상태**에 빠지게 될 수 있다.
   - 어떤 경우에는 동시에 양쪽 포크를 집을 수 없어 식사를 하지 못하는 **기아 상태**가 발생할 수도 있고, 몇몇 철학자가 다른 철학자보다 식사를 적게 하는 경우가 발생하기도 한다.

### 동기화하는 메서드 사이에 존재하는 의존성을 이해하라

동기화하는 메서드 사이에 의존성이 존재하면 동시성 코드에 찾아내기 어려운 버그가 생긴다. 

→ 공유 객체 하나에는 메서드 하나만 사용하라

**공유객체 하나에 여러 메서드가 필요한 경우**

- 클라이언트에서 잠금: 클라이언트에서 첫 번째 메서드를 호출하기 전에 서버를 잠근다. 마지막 메서드를 호출할 때까지 잠금을 유지한다.
- 서버에서 잠금: 서버에 “서버를 잠그고 모든 메서드를 호출한 후 잠금을 해제”하는 메서드를 구현한다. 클라이언트는 해당 메서드를 호출한다.
- 연결 서버: 잠금을 수행하는 중간 단계를 생성한다. ‘서버에서 잠금’ 방식과 유사하지만 원래 서버는 변경하지 않는다.

### 동기화하는 부분을 작게 만들어라

자바에서 synchronized 키워드를 사용하면 락을 설정할 수 있다. 같은 락으로 감싼 모든 코드 영역은 한 번에 한 스레드만 실행이 가능하다. 

but, 락은 스레드를 지연시키고 부하를 가중시킨다. 

→ 임계영역은 반드시 보호하고, 임계영역의 수를 최대한 줄여야 한다.

→ 수를 줄이고자 임계영역 크기를 키우면 스레드 간에 경쟁이 늘어나고 프로그램 성능이 떨어진다.

### 올바른 종료 코드는 구현하기 어렵다

깔끔하게 종료하는 다중 스레드 코드는 올바로 구현하기 어렵다. 개발 초기부터 충분히 시간을 투자해야 하고, 기존의 알고리즘을 검토해라

### 스레드 코드 테스트하기

테스트가 정확성을 보장하지는 않지만 충분한 테스트는 위험을 낮출 수 있다.

다중 스레드 테스트는 고려할 사항이 아주 많다. 아래의 구체적인 지침을 참고해라

- 말이 안 되는 실패는 잠정적인 스레드 문제로 취급하라
    
    : 다중 스레드 코드는 때때로 말이 안 되는 오류를 일으킨다. 많은 개발자가 하드웨어 문제, 단순한 ‘일회성’ 문제로 치부하고 무시한다. 일회성 문제란 존재하지 않는다고 가정하는 편이 안전하다. 일회성 문제를 계속 무시한다면 잘못된 코드 위에 코드가 계속 쌓인다.
    
    → 시스템 실패를 ‘일회성’이라 치부하지 마라.
    
- 다중 스레드를 고려하지 않은 순차 코드부터 제대로 돌게 만들자
    
    : 스레드 환경 밖에서 코드가 제대로 도는지 반드시 확인한다. 
    
    일반적인 방법으로, 스레드가 호출하는 POJO를 만든다. POJO는 스레드를 모른다. 따라서 스레드 환경 밖에서 테스트가 가능하다. POJO에 넣는 코드는 많을수록 더 좋다.
    
    → 스레드 환경 밖에서 생기는 버그와 스레드 환경에서 생기는 버그를 동시에 디버깅하지 마라. 먼저 스레드 환경 밖에서 코드를 올바로 돌려라.
    
- 다중 스레드를 쓰는 코드 부분을 다양한 환경에 쉽게 끼워 넣을 수 있게 스레드 코드를 구현하라
    
    : 다중 스레드를 쓰는 코드를 다양한 설정으로 실행하기 쉽게 구현하라.
    
    → 다양한 설정에서 실행할 목적으로 다른 환경에 쉽게 끼워 넣을 수 있게 코드를 구현하라.
    
- 다중 스레드를 쓰는 코드 부분을 상황에 맞게 조율할 수 있게 작성하라
    
    : 처음부터 다양한 설정으로 프로그램의 성능 측정 방법을 강구한다.
    
- 프로세서 수보다 많은 스레드를 돌려보라
    
    : 시스템이 스레드를 스와핑할 때도 문제가 발생한다. 스와핑을 일으키려면 프로세서 수보다 많은 스레드를 돌린다. 스와핑이 잦을수록 임계영역을 빼먹은 코드나 데드락을 일으키는 코드를 찾기 쉬워진다.
    
- 다른 플랫폼에서 돌려보라
    
    : 다중 스레드 코드는 플랫폼에 따라 다르게 돌아간다. 
    
    → 처음부터 그리고 자주 모든 목표 플랫폼에서 코드를 돌려라.
    
- 코드에 보조 코드를 넣어 돌려라. 강제로 실패를 일으키게 해보라.
    
    : 스레드 버그가 산발적이고 우발적이고 재현이 어려운 이유는 코드가 실행되는 수천 가지 경로 중에 아주 소수만 실패하기 때문이다.
    
    → 보조코드를 추가해 코드가 실행되는 순서를 바꿔주어 오류를 좀 더 자주 일으킬수 있도록 할 수 있다.
    
    - e.g. 스레드 실행 순서에 영향을 미치는 메서드 : Object.wait(), Object.sleep(), Object.yield(), Object.priority()
    - 코드에 보조 코드를 추가하는 방법
        - 직접 구현하기
        - 자동화 : AOP, CGLib, ASM 등

# 스프링 AOP

## AOP란?

- Aspect Oriented Programming



### 관점 지향...?

- 어떤 로직을 <u>핵심적인 관점</u>과 <u>부가적인 관점</u>으로 나누어서 바라보고 그 관점을 기준으로 각각 <u>모듈화</u>하겠다!

  > <u>핵심적인 관점</u>이란 핵심 비즈니스 로직을 의미하며
  >
  > <u>부가적인 관점</u>이란 핵심 로직을 실행하기 위해 행해지는 로직(DB 연결, 로깅, 파일 입출력 등)을 말한다.

  > <u>모듈화</u>란 공통된 로직이나 기능을 하나의 단위로 묶어 재사용하는 것을 의미한다.

- 즉, AOP는 흩어져있는 중복 코드들을 모아 모듈화하고 핵심적인 비즈니스 로직에서 분리하여 재사용하는 방식



### AOP 구현 방법

- 컴파일 과정에서 코드를 추가하는 방법

  > A.java => 컴파일 (+ 코드 추가!) => A.class

- 바이트 코드 조작하는 방법

  > A.java => A.class => 클래스로더 (+ 코드 추가!) => 메모리

- 프록시 패턴 (스프링 AOP에서 사용)

 

## 프록시 패턴

- 실제 기능을 수행하는 객체 대신 가상의 객체(Proxy)를 사용하여 로직의 흐름을 제어한다.



### 특징

- 원래 기능을 수행하며 그 외의 부가적인 작업을 수행할 수 있다.
- 클라이언트(사용자)는 인터페이스를 이용하므로 실체 구현체가 무엇인지 신경 쓸 필요가 없고 코드 변경이 적다.



### 간단한 구현 예시

- 기존 코드

  ```java
  public interface Payment {
  	void pay(int amount);
  }
  ```

  ```java
  public class Cash implements Payment {
  	@Override
    void pay(int amount) {
      System.out.println(amount + "현금 결제");
    }
  }
  ```

  ```java
  public class Store {
  	Payment payment;
    
    public Store(Payment payment) {
      this.payment = payment;
    }
    
    public void buySomething(int amount) {
      payment.pay(amount);
    }
  }
  ```

- 프록시 패턴이 적용된 코드

  ```java
  public class CashPerf implements Payment {
    Payment cash = new Cash();
    
    @Override
    void pay(int amount) {
      // 부가적으로 수행할 기능 1
      // ...
      
      // 원래 수행하려던 기능
      cash.pay(amount);
  
      // 부가적으로 수행할 기능 2
      // ... 
    }
  }
  ```

- 클라이언트에서 사용하는 코드

  ```java
  //Payment payment = new Cash();		// 프록시 객체 사용 이전
  Payment payment = new CashPerf(); // 주입할 객체를 프록시 객체로 바꿔치기만 하면 된다!
  Store store = new Store(payment);
  store.buySomething();
  ```

  => 중요한 것은 새로운 부가 기능이 추가되었지만 기존 코드는 거의 변경되지 않고 수행할 수 있다는 것이다!



## 스프링 AOP 활용

- 프로그래머가 비즈니스 로직에 집중할 수 있도록 프록시 패턴을 이용한 AOP를 자동으로 구현해준다.



### 활용된 예시

- `@Transactional`
  - SQL문을 수행하기 이전에 AutoCommit 옵션을 false로 변경하는 작업과 수행한 이후에는 커밋을 할지, 롤백을 할지 결정하는 작업들이 프록시 패턴으로 구현되어 있다.

 

### AOP 구현해보기

- AOP를 적용할 위치를 표시하는 어노테이션 정의

  ```java
  @Target(ElementType.METHOD)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface LogExceptionTime {
  }
  ```

- 수행 시간을 로그로 남기는 Aspect 정의

  ```java
  @Component
  @Aspect
  public class LogAspect {
    Logger logger = LoggerFactory.getLogger(LogAspect.class);
    
    @Around("@annotation(LogExceptionTime)")
    public Object logExceptionTime(ProceedJoinPoint joinPoint) throws Throwable {
      StopWatch stopWatch = new StopWatch();
      stopWatch.start();
      
      Object proceed = joinPoint.proceed();
      
      stopWatch.stop();
      logger.info(stopWatch.prettyPrint());
      
      return proceed;
    }
  }
  ```

- 클라이언트에서 사용하는 코드

  ```java
  public class OwnerController {
    // 생략
    @Getmapping("/owners/new")
    @LogExceptionTime
    public String initCreationForm(Map<String, Object> model) {
      //생략
    }
  }
  ```

- 프록시 패턴 예시와 역할 비교해보기

  <img src="https://user-images.githubusercontent.com/33659848/87701616-42257780-c7d3-11ea-9a7e-77486efcd12a.png" alt="aop_proxy" style="zoom: 40%;" />

  - `Cash`와 `OwnerController` : 원래 수행해야할 기능이 정의된 클래스

  -  `LogAspect`와 `CashPerf` : 부가적으로 수행할 기능이 정의된 클래스

  - 단, 프록시 객체를 만드는 작업은 스프링에서 자동으로 처리한다.

    > 동일한 인터페이스 상속, `OwnerController` 대신 빈으로 등록될 프록시 객체를 생성 및 주입하기 등

  

## 기타 키워드

- AspectJ



## 참고

[[Spring] 스프링 AOP (Spring AOP) 총정리 : 개념, 프록시 기반 AOP, @AOP](https://engkimbs.tistory.com/746)

[Refactoring Guru - Proxy](https://refactoring.guru/design-patterns/proxy)


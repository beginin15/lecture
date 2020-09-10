# 스프링 AOP 2부

## 스프링 AOP

### 특징

- **프록시 기반**의 AOP 구현체

- **스프링 빈**에만 AOP를 적용할 수 있다.

- 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 흔히 발생하는 문제에 대한 솔루션을 제공하는 것이 목적이다.

  </br>

### 프록시 패턴

- 사용 이유

  - <u>기존 코드를 변경하지 않고</u> 접근 제어 또는 부가 기능을 추가할 수 있다.

  - 클라이언트 코드가 변경되지 않는 것은 **인터페이스를 이용하기 때문이다.**

    </br>

    <img src="https://user-images.githubusercontent.com/33659848/92730677-14ae1200-f3af-11ea-9edb-144c2948922c.png" alt="proxy_pattern" style="zoom:50%;" />

- 원리

  - Target 객체(Real Subject)와 동일한 타입의 Proxy 객체를 생성하여 내부적으로 Target을 참조한다.
  
  - Client에서는 인터페이스 타입으로 Target 객체를 감싼 Proxy 객체를 사용하게 된다.
  
    </br>

## 등장 배경

> Crosscutting concern 처리하는 과정을 떠올리자.

> [기존 코드 - 프록시 패턴 적용 이전](https://github.com/beginin15/spring-framework-core/commit/3a58bbe2924e669f3dfdbfda018a77e0966a5557)

</br>

### 1차 - 프록시 패턴 사용하기

- 클라이언트의 코드 변경 없이 성능 측정을 위한 부가 기능을 수행할 수 있도록 프록시 패턴을 사용했다.

- 문제점

  - 중복 코드는 여전히 발생한다.
  - Target 마다 프록시 클래스를 정의해야 한다.
  - 해당 기능을 다른 클래스에서 사용하고 싶다면 더욱 많은 중복 코드가 발생한다.

  > [구현 코드 - 프록시 패턴 적용](https://github.com/beginin15/spring-framework-core/commit/72bf3a8fd38366077fab500daadee2e3127b4fad)

### 2차 - 동적으로 프록시 객체를 만든다.

- 애플리케이션이 동작할 때 동적으로 특정 빈의 프록시 객체를 만들 수 있다면 1차에서 발생한 문제를 해결할 수 있다.

  - 스프링 IoC 컨테이너가 제공하는 기능과 Dynamic 프록시를 함께 사용하면 가능하다. => 이것이 바로 스프링 AOP가 제공하는 기능!

    </br>

## 원리

### 스프링 IoC + Dynamic 프록시

- [`AbstractAutoProxyCreator`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/aop/framework/autoproxy/AbstractAutoProxyCreator.html)가 빈으로 등록된 특정 클래스(Target)를 감싸는 프록시 객체를 생성하여 해당 클래스 대신 빈으로 등록한다.
  - `BeanPostProcessor`의 구현체
  - 빈 인스턴스를 생성한 직후 조작할 수 있는 라이프 사이클 인터페이스
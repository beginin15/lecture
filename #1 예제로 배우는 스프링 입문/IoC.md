# 스프링 IoC

## IoC란?

- Inversion of Control
- (의존성에 대한) 제어권이 역전되었다.



### 일반적인 의존성

- 일반적으로 내가 사용할 의존성은 내가 만든다.

  ```java
  class OwnerController {
  	private OwnerRepository repository = new OwnerRepository();
  }
  ```

  

### 의존성에 대한 제어권 역전

- 내가 사용할 의존성을 내가 아닌 누군가가 만들어 준다.

  = 의존성에 대한 제어권이 내가 아닌 외부에 있다.

  = 제어권이 역전되었다.

  ```java
  class OwnerController {
  	private OwnerRepository repository;
    
    public OwnerController(OwnerRepository repository) { // 생성자를 통해 외부에서 의존성이 주입된다.
      this.repository = repository;
    }
  }
  ```

  

### DI는 IoC의 일종

- DI (Dependency Injection)은 의존성이 외부로부터 주입되는 것을 뜻한다.
- 즉, DI도 IoC라고 볼 수 있다.



## IoC 컨테이너

### 역할

- Bean 생성 및 제공
- Bean 간의 의존성 관리



### 종류

- `BeanFactory`
  - IoC 컨테이너

- `ApplicationContext`

  - `BeanFactory`를 상속
  - 그 밖에 여러가지 인터페이스를 상속받아 다양한 기능을 제공한다.

  

### 의존성 주입

- 의존성 주입은 Bean 사이에서만 가능하다.

  - 즉, 기본적으로 IoC 컨테이너가 가지고 있는 Bean 끼리만 의존성 주입이 가능하다.

    > IoC 컨테이너 외부의 객체에 의존성 주입하는 것이 불가능한 것은 아니다.



## Bean   

- IoC 컨테이너가 관리하는 객체
- 일반적인 객체와 차이점
  - 프로그래머가 직접 객체를 생성하지 않는다.

### 특징

- 객체의 유효 범위(scope)는 **Singleton**
  - 하나의 애플리케이션 내에서 최초 한번만 생성하고 해당 객체를 재사용한다.



### 등록 방법



## 질문

- `ApplicationContext`도 Bean으로 취급되는가?
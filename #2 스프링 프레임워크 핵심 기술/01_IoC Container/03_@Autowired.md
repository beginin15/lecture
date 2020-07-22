# IoC 컨테이너 1부

## `@Autowired`

- 애플리케이션 구동 시점에 의존성을 자동으로 주입해준다.



### `repuired` 옵션

- 해당 어노테이션이 붙은 객체가 필수적으로 빈으로 등록되어야 하는지 결정할 수 있다.
- default는 `true`, 즉 해당 객체가 빈으로 등록되어 있지 않으면 애플리케이션 구동에 실패한다!



### 사용할 수 있는 위치

- 생성자

  > 스프링 4.5 부터 생략 가능

- 세터

- 필드



### 위치에 따른 차이점

- 생성자

  - 의존 객체가 빈으로 등록되어 있지 않으면 생성하고자 하는 빈의 인스턴스를 생성하지 못하도록 **강제할 수 있다.**
  - `required=false` 옵션을 사용해도 인스턴스를 생성하지 못하며 <u>정상적으로 애플리케이션이 구동되지 않는다.</u> 

  ```
  ***************************
  APPLICATION FAILED TO START
  ***************************
  
  Description:
  
  Parameter 0 of constructor in com.course.spring_framework.core.BookService required a bean of type 'com.course.spring_framework.core.BookRepository' that could not be found.
  ```

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/d0178e979756063fa848c24348facec143e832af)

- 세터, 필드

  - `required=false` 옵션을 사용하면 의존 객체가 빈으로 등록되어 있지 않더라도 생성하고자 하는 빈의 인스턴스를 생성할 수 있다.

  - 세터와 필드를 이용한 의존성 주입도 빈의 인스턴스를 생성되는 시점에 수행되기 때문에 `required=true` 라면 인스턴스가 생성되지 못하도록 관여할 수 있다.

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/811c61522346859095c0e059b927bb76475a40fd)

  

## 의존 객체가 여러 개인 경우

- 의존성 주입될 타입에 해당되는 구현체가 여러 개인 경우, 주입할 타입을 지정해줘야 한다.
- `@Primary`, `@Qualifier`, 리스트로 모두 받기, 필드 이름 이용하기 등 여러 방법이 존재한다.



### 언제 필요할까?

- 의존 객체가 여러 개인 경우

  1. 의존 객체가 super 타입 (`BookRepository`)으로 선언되어 있고, 해당 타입의 자식 또는 구현 클래스가 2개 이상이다.

  2. 모두 빈으로 등록되어 있다.



<img src="https://user-images.githubusercontent.com/33659848/88136826-1cf69600-cc25-11ea-8af3-a9ab1a65459c.png" alt="autowired_several_dependencies" style="zoom:33%;" />

- 위 경우에 모두 해당되면 스프링은 어떤 클래스를 주입해야 할 지 알 수가 없어 에러를 발생한다.

  ```
  Description:
  
  Parameter 0 of constructor in com.course.spring_framework.core.BookService required a single bean, but 2 were found:
  	- myBookRepository: defined in file [.../spring-framework-core/build/classes/java/main/com/course/spring_framework/core/MyBookRepository.class]
  	- yourBookRepository: defined in file [.../spring-framework-core/build/classes/java/main/com/course/spring_framework/core/YourBookRepository.class]
  ```



### `@Primary`

- 항상 동일한 타입을 주입하는 경우, 해당 클래스에 붙인다.

  ```java
  @Repository @Primary
  public class MyBookRepository implements BookRepository {
    // ...
  }
  ```

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/50b929bbb507623bb5ad79b4fa4887110b268383)



### `@Qualifier`

- 상황에 맞춰 타입을 주입하는 경우, `@Autowired` 옆에 붙인다.

  ```java
  @Service
  public class BookService {
  
      @Autowired @Qualifier(value = "yourBookRepository")
      private BookRepository bookRepository;
      
      // ...
  }
  ```

  - 빈의 이름(`value`)은 클래스 이름을 작성하되 소문자로 시작하며, Camel case로 작성한다.

- 세터와 필드를 통해 의존성을 주입하는 경우에만 가능하다.

  ```java
      @Autowired @Qualifier(value = "yourBookRepository") // 생성자에 적용하려고 하면 컴파일 에러 발생
      public BookService(BookRepository bookRepository) {
          this.bookRepository = bookRepository;
      } 
  ```

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/531bd339b10ddaa682b746233c06f23d7bf36fae)



### 리스트로 모두 받기

- 타입이 동일한 의존 객체를 리스트에 모두 받는 것도 가능하다.

  ```java
      @Autowired
      private List<BookRepository> bookRepositories;
  ```

  - 리스트에 `MyBookRepository`와 `YourBookRepository`가 모두 담겨있다.

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/38a7d0fe2e89c74cb2598397e3b7ee69827d77ed)



### 필드 이름 이용하기

- 의존성을 주입할 때, 주입하고자 하는 타입뿐만 아니라 필드의 이름도 확인한다.

- 필드의 이름을 주입하고자 하는 빈 이름과 동일하게 작성하면 에러 없이 해당 타입을 주입해준다.

  > Type safe하지 않기 때문에 좋은 방법은 아니다!

  ```java
      @Autowired
      private BookRepository myBookRepository; // MoBookRepository를 주입해준다. 
  ```

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/0e2e97960c022c9dbb915d56bf94c3892e736eca)



## 스프링이 의존성을 주입하는 방법

> 의존 객체가 여러 개일 때 처리하는 다양한 방법을 살펴보았다. 그렇다면 스프링은 이러한 작업들을 어떻게 처리하는걸까?



### 라이프사이클 콜백

- 빈 인스턴스가 생성된 이후에 초기화 (Initialization) 라이프사이클이 존재한다.

- 빈의 초기화 이전 (`BeforeInitialization`) 또는 그 이후 (`AfterInitialization`)에 추가적인 작업을 처리할 수 있는 라이프사이클 콜백이 존재한다.

- IoC 컨테이너는 빈의 라이프사이클에 관여할 수 있다.

- 즉, 빈 인스턴스를 생성한 이후, 초기화 이전 (`BeforeInitialization`) 라이프사이클 콜백을 이용하여 의존성 주입을 처리한다.

  ![bean_lifecycle_initialization](https://user-images.githubusercontent.com/33659848/88140608-018f8900-cc2d-11ea-99ef-8b3b7080f421.png)



### `BeanPostProcessor`

- 빈 초기화 라이프사이클 콜백을 제공하는 인터페이스

- `@Autowired`는 해당 인터페이스를 구현한 `AutowiredAnnotaionBeanPostProcessor`가 초기화 이전에 타입에 맞는 빈을 주입하므로써 동작한다.

  > `BeanFactory`가 자신에게 등록되어 있는 `BeanPostProcessor` 타입을 찾고 그 중에 `AutowiredAnnotaionBeanPostProcessor`을 이용하여 `@Autowired` 어노테이션을 처리하는 로직을 일반적인 빈들에게 수행한다. 여기서 `AutowiredAnnotaionBeanPostProcessor`도 빈이라는 것을 알 수 있다.



## 초기화 메소드

### `@PostContruct`

- 초기화 메소드를 제공한다.
- **Before-Initialization 이후!**
- 즉, 빈에 필요한 의존성이 주입된 이후 딱 한번 호출되는 메소드

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/33f87f0e02c7048d7dab7d5dc9fc563eef7bbb76)



### `InitializingBean`

- 초기화 메소드를 제공하는 인터페이스
- `@PostContruct`와 실행되는 시점이 동일하다.

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/40badcf9f5b4f42d918f7b9e70767f1f1f269f75)


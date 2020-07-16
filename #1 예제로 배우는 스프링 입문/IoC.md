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



### 특징

- 객체의 유효 범위(scope)는 **Singleton**
  - 하나의 애플리케이션 내에서 최초 한번만 생성하고 해당 객체를 재사용한다.



### 등록 방법

- Component Scanning

  - `@Component`가 붙은 클래스는 IoC 컨테이너가 생성되는 시점에 자동으로 Bean으로 등록된다.

    > `@Repository`, `@Service`, `@Controller`, `@Configuration` 등

    > 스프링의 Lifecycle callbacks 중에는 `@Component`가 붙은 모든 클래스들의 인스턴스를 생성하여 빈으로 등록하는 작업을 수행하는 Annotaion Processor가 존재한다. (`@ComponentScan` 참고)

- 직접 Bean으로 등록하기

  - xml 파일

  - 자바 설정 파일

    ```java
    @Configuration
    public class SampleConfig {
      
      @Bean
      public SampleController sampleController() {
        return new SampleController();
      }
    }
    ```

    

### 사용 방법

- 필드 주입

  - 필드에 `@Autowired` 붙이는 방법

    ```java
    @Autowired
    private OwnerRepository owners;
    ```

    

- 생성자 주입

  - 4.3 버전 이상부터 빈의 생성자가 1개 뿐이고, 생성자로 주입받는 객체들이 모두 빈으로 등록되어 있으면 `@Autowired` 없어도 자동으로 의존성을 주입시켜준다.

  - 생성자를 통한 의존성 주입을 권장한다.

    > 필수적으로 사용해야하는 레퍼런스 없이는 인스턴스를 만들 수 없도록 강제할 수 있기 때문에

    > 생성자 주입으로 Bean 간의 순환 참조를 방지할 수 있다. 생성자로 서로 주입하려고 하기 때문에 애초에 생성하지 못한다.
    >
    > 반면, 필드 주입과 세터 주입은 인스턴스를 생성한 이후에 의존성을 주입할 수 있기 때문에 만일 순환 참조가 불가피한 경우에 사용한다.

    

- 세터 주입

  - 세터 메소드에 `@Autowired` 붙이는 방법

    ```
    @Autowired
    public void setOwners(OwnerRepository owners) {
      this.owners = owners;
    }
    ```

    

- 직접 `ApplicationContext`에서 빈 가져오기

  ```java
  this.owners = applicationContext.getBean(OwnerRepository.class);
  ```

  

## 질문

- `ApplicationContext`도 Bean으로 취급되는가?

- Lifecycle callback이란 무엇인가?
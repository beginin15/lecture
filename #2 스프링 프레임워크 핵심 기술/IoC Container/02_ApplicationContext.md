# IoC 컨테이너 2부

## `ApplicationContext`와 다양한 빈 설정 방법

- xml을 이용한 방법부터 스프링 부트에서 제공하는 어노테이션 기반 방법까지 만들어진 순서대로 경험해보자

</br>

## xml

- xml 파일을 이용하여 빈을 등록하고 의존성 주입에 관여한다.
- `ClassPathXmlApplicationContext` 구현체를 통해 빈을 사용할 수 있다.
- [xml 파일 명세](#xml 파일 명세)
- [구현 코드](https://github.com/beginin15/spring-framework-core/commit/69375bfbce29b71fcadb9c29cc2c50c27bdb9c35)

</br>

## xml + Component scan

- xml 파일과 어노테이션을 기반으로 빈을 관리한다.

- `ClassPathXmlApplicationContext` 구현체를 통해 빈을 사용할 수 있다.

- 방법

  - xml 파일에 Component scan할 베이스 패키지를 명시한다.

    ```xml
    <context:component-scan base-package="com.course.spring-framework.core"/>
    ```

    > `base-package` 하위에 포함되는 클래스 중에 빈을 스캔하여 등록한다.

  - [구현 코드](https://github.com/beginin15/spring-framework-core/commit/94d5768f0ebf4d69ce61f9d256e8b7918dfe1a0a)

- 기타 특징

  - `@Component`와 이를 확장한 어노테이션을 기반으로 빈을 인지한다.

  - 의존성 주입은 처리하지 않는다.

    > `@Autowired`나 `@Inject`를 이용하여 의존성을 주입해줘야 한다. 

</br>

## 자바 설정 파일 

- 자바 파일이 xml 역할을 대신한다.
- `AnnotaionConfigApplicationContext` 구현체를 통해 빈을 사용할 수 있다.
- 방법
  - `@Configuration` 어노테이션을 붙여 설정 파일이라는 것을 알려준다.
  - 해당 파일에서 빈으로 등록할 객체를 반환하는 메소드를 정의하고, 해당 메소드에 `@Bean` 어노테이션을 붙여 반환 데이터가 빈이라는 것을 알려준다.
  - [구현 코드](https://github.com/beginin15/spring-framework-core/commit/8441de6bfe1f9552b847842a10ba48200d7c17e6)

- 기타 특징

  - 빈으로 등록된 객체는 파라미터로 받아서 의존성 주입이 가능하다. [참고](https://github.com/beginin15/spring-framework-core/commit/3418e3974c2166d2f13b38d3db277c8dd31e3bbc)

</br>

## 자바 설정 파일 + Component Scan

- 자바 설정 파일에 `@ComponentScan` 어노테이션을 붙이면 빈을 직접 정의하지 않아도 된다.

  ```java
  @Configuration
  @ComponentScan(basePackageClasses = SpringFrameworkCoreApplication.class)
  public class ApplicationConfig {
  	// 빈을 정의할 필요가 없다!
  }
  ```

  > `basePackageClasses` 옵션은 해당 클래스를 포함한 패키지로부터 컴포넌트 스캔을 수행한다. 즉, 스캔을 시작할 패키지 위치를 지정한다.

- `AnnotaionConfigApplicationContext` 구현체를 통해 빈을 사용할 수 있다.

- [구현 코드](https://github.com/beginin15/spring-framework-core/commit/9151a6b81f8fbabdc5dcefa8dd537e8ecb5dcbdd)

</br>

## `@SpringBootApplication`

- 스프링 부트에서 제공하는 어노테이션으로, `@ComponentScan`을 포함한다.
- 즉, <u>자바 설정 파일 없이</u> 어노테이션을 기반으로 빈을 등록할 수 있다.

- [구현 코드](https://github.com/beginin15/spring-framework-core/commit/4f1b9e504ee8fa60bcf42927158c6713a8cc6011)

</br>

## 부연 설명

### xml 파일 명세

- `<bean>` 태그
  - `id`
    - 빈의 이름
    - `ApplicationContext`에서 빈을 꺼내올 때 사용하는 이름
  - `class`
    - 빈으로 등록할 클래스명
    - 패키지를 포함한 이름
  - `scope`
    - `prototype` / `request` / `session` / `singleton` [참고](#빈 스코프 종류)
    - default는 `singleton`

- `<propety>` 태그

  - 생성할 빈의 setter 메소드를 의미한다.

  - 빈에 필요한 의존성을 setter 메소드를 통해 주입할 때 사용한다.

  - `name`

    - 프로퍼티의 세터 이름을 나타낸다.

      > setXXX의 XXX

  - `ref`

    - setter를 통해 주입할 빈의 `id`를 명시한다.

    - 주입할 객체도 빈으로 정의되어 있어야 한다.

</br>

### 빈 스코프 종류

- prototype
  - 매번 새로운 객체를 생성한다.
- request
  - 요청마다 객체를 생성한다.
- session
  - Http 세션마다 객체를 생성한다.
- singleton
  - 애플리케이션 실행시 객체를 단 한번 생성한다.
  - 하나의 어플리케이션에서 동일한 객체를 사용한다.


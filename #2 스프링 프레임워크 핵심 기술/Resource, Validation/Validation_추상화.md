# Resource / Validation 2부

> 스프링의 대표적인 추상화 레퍼런스를 살펴보자

</br>

## Validation의 추상화

애플리케이션에서 사용하는 객체 검증용 인터페이스 (`Validator`)를 제공한다.

</br>

### 특징

- 계층에 상관없이 사용할 수 있다.

  > 웹, 서비스, 데이터 등 모든 계층

- 어노테이션 기반을 객체를 검증할 수 있다.

- Bean Validation(1.0, 1.1)을 지원하는 구현체가 존재한다.

  > Bean Validation은 Java EE 표준 스펙이다.

</br>

## `Validator` 활용하기

### `Validator`의 필수 구현 메소드

- `support()`

  - 검증하고자 하는 클래스를 결정한다.
  - 인자로 전달된 객체가 해당 Validator가 검증할 수 있는 클래스인지 검사한다.

- `validate()`

  - 실질적인 객체 검증을 수행한다.

  - `ValidationUtils`를 사용하면 편리하다.

    > `ValidationUtils`를 이용하여 특정 필드에 대한 검증을 수행한다면 해당 필드에 대한 getter가 정의되어야 한다. (없으면 Runtime 에러가 발생한다.)

</br>

### `Errors`

- 에러 코드를 제공한다.
  - 내가 직접 지정할 수도 있고, 검증하고자하는 필드 이름과 타입을 조합하여 기본적으로 제공한다.
- 구현체 중 하나인 `BeanPropertyBindingResult`는 스프링 MVC가 자동으로 생성하여 파라미터로 넘겨준다.
  - 즉, 스프링 MVC 프로젝트라면 해당 구현체는 직접 생성하여 사용할 일은 거의 없다.

> [구현 코드 - ValidationUtils 사용](https://github.com/beginin15/spring-framework-core/commit/77bc169ad75a4222904f31c1067f0ec73c2dc841)
>
> [구현 코드 - 직접 검증 구현](https://github.com/beginin15/spring-framework-core/commit/e6185b2b24fd145c20a564f66ef8c37d491da680)

</br>

## 어노테이션 활용하기

스프링 부트 2.0.5 이상 버전을 사용한다면, `LocalValidatorFactoryBean`이 빈으로 자동 등록된다.

</br>

### `LocalValidatorFactoryBean`

- 빈에 붙은 Validation 관련 어노테이션을 기반으로 객체를 검증해준다.
- 간단한 검증이라면 `LocalValidatorFactoryBean`과 제공되는 어노테이션으로 수행할 수 있기 때문에 직접 Validator를 구현하지 않아도 된다.

- Bean Validation 2.0.1 구현체를 사용하기 때문에 hibernate-validator가 필요하다.

  ```java
      @Autowired
      Validator validator; // 기본적으로 LocalValidatorFactoryBean가 주입된다.
  ```

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/674c6a2af6c4b7a4d2e2b996b98913155966fe5a)


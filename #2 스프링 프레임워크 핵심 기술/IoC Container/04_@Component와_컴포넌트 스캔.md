# IoC 컨테이너 4부

## `@ComponentScan`

### `basePackages`

- 컴포넌트 스캔 범위를 지정한다.

- 지정한 패키지를 기준으로 모든 하위 패키지 내 컴포넌트들을 스캔하여 빈으로 등록한다.

  ```java
  @ComponentScan(basePackages={"com.firstpackage","com.secondpackage"})
  ```

- `basePackageClasses` 를 사용하면 type-safe하게 범위를 지정할 수 있다. (추천!)

  ```java
  @ComponentScan(basePackageClasses = SpringFrameworkCoreApplication.class)
  ```

  > `basePackageClasses` 에 지정한 클래스를 포함하는 패키지 기준으로 컴포넌트 스캔을 수행한다.

- default는 `@ComponentScan`을 붙이고 있는 Configuration 클래스부터 컴포넌트 스캔을 수행한다.

</br>

### `excludeFilters`

-   컴포넌트 중에서 빈에서 제외할 대상들을 필터링하는 기능을 제공한다.

</br>

### 컴포넌트 스캔의 동작

- `BeanFactoryPostProcessor` 를 구현하고 있는 `ConfigurationClassPostProcessor` 에 의해 스캐닝이 처리된다.

- `BeanFactoryPostProcessor` 은 다른 모든 빈들을 생성하기 이전에 동작하는 라이프사이클 콜백을 제공한다.

  > [`BeanFactoryPostProcessor`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanFactoryPostProcessor.html) used for bootstrapping processing of [`@Configuration`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html) classes.

  > `BeanPostProcessor` 과 다른 라이프사이클!

</br>

### 컴포넌트 스캔의 단점?

- 싱글톤 스코프인 빈들은 애플리케이션이 구동되는 초기에 모두 생성하기 때문에 구동 시간이 오래 걸릴 수 있다.

</br>

## Functional을 사용한 빈 등록 방법

### 사용 이유

- 프록시, 리플렉션과 같은 기법들을 사용하지 않기 때문에 초기 구동 시 성능상 이점을 얻을 수 있다.
- `@Bean`을 이용하여 빈을 등록하는 방법을 대체할 수 있다.
  - But, 어노테이션 기반으로 동작하지 않으므로 설정 파일의 크기가 커지고 일일이 빈을 등록해야 하기 때문에 컴포넌트 스캔을 대체하는 방법이라고 보긴 어렵다.

</br>

### 특징

- 컴포넌트 스캔과 달리 패키지 범위에 영향받지 않고 빈으로 등록할 수 있다.

</br>

### 사용 방법

- 애플리케이션 인스턴스를 직접 생성하여, `addInializers()` 를 통해 필요한 `ApplicationContext`을 직접 초기화한다.

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/20bf6bee10959745991969eb79d25b190e0eb320)

  > [람다식 대체](https://github.com/beginin15/spring-framework-core/commit/cbe796243d6a7c98c51d4603ece0b90da82bdf5a)

</br>

## 기타

### 컴포넌트 종류

- `@Repository`
- `@Service`
- `@Controller`
- `@Configuration`
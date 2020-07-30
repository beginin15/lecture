# IoC 컨테이너 1부

## 프로퍼티

- key - value 형태로 정의할 수 있는 설정값
- 계층형으로 접근한다. 즉, 우선 순위가 존재한다.
- `Environment`는 프로퍼티 소스 설정, 프로퍼티 값을 가져오는 기능을 제공한다.

</br>

### 프로퍼티 가져오기

- `Environment` 이용하기

  ```java
  Environment environment = context.getEnvironment();
  System.out.println(environment.getProperty("app.name"));
  ```

- 스프링 부트의 `@Value`

  ```java
  @Value("${app.name}")
  String appName
  ```

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/7174a12c54f15c93ee5bfdab3da5ca6b91f5095b)

</br>

### 프로퍼티 소스

- .propeties 파일에 프로퍼티들을 정의한 것

- `@PropertySource`

  - `Environment`를 통해 프로퍼티 추가하는 방법

  ```java
  @PropertySource("classpath:/app.properties")
  public class SpringFrameworkCoreApplication {
  	// ...
  }
  ```

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/59af5537a01cfe0e234ec4e914917f36050876d2)

- 스프링 부트는 기본 프로퍼티 소스를 지원한다.

  > application.properties

</br> 

### 프로퍼티 우선 순위 *(자세히 알아보기)*

`StandardServletEnvironment` 우선 순위

- `ServletConfig` 매개변수

- `ServletContext` 매개변수

- JNDI

- JVM 시스템 프로퍼티

  > `-Dapp.name=spring`

- JVM 시스템 환경 변수

  > 운영체제 환경 변수
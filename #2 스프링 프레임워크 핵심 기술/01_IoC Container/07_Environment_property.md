# IoC 컨테이너 1부

## 프로퍼티

- key - value 형태로 정의할 수 있는 설정값
- `Environment`는 프로퍼티 소스 설정, 프로퍼티 값을 가져오는 기능을 제공한다.



### 프로퍼티 가져오기

```java
Environment environment = context.getEnvironment();
System.out.println(environment.getProperty("app.name"));
```



### 프로퍼티 소스

- .propeties 파일에 프로퍼티들을 정의할 수 있다.

- `@PropertySource`으로 사용하고자 하는 파일을 명시하면 `Environment`에 저장된다.

  ```java
  @PropertySource("classpath:/app.properties")
  public class SpringFrameworkCoreApplication {
  	// ...
  }
  ```

- 스프링 부트는 기본 프로퍼티 소스를 지원한다.

  > application.properties

 
# Resource / Validation 1부

> 스프링의 대표적인 추상화 레퍼런스를 살펴보자

</br>

## Resource의 추상화

### 등장 이유

- 자바의 표준 `java.net.URL` 클래스와 URL prefix를 처리하는 표준 핸들러는 로우 레벨 자원에 접근하기에 불편하다.
  - classpath 기준으로 리소스를 읽어오는 기능이 없다.
  - `ServletContext`를 기준으로 상대 경로를 읽어오는 기능이 없다.
  - 특별한 URL 접미사를 처리하는 새로운 핸들러를 등록하여 사용할 수 있지만 구현이 복잡하고 편의성 메소드가 부족하다. (파일이 존재하는지 검사하는 메소드 등)

- <u>`java.net.URL`을 **추상화**하여</u> 다양한 URL prefix를 가진 리소스에 접근하는 방법을 통일시켰다. 

</br>

### 활용 예시

스프링 설정 파일을 찾을 때 자주 사용된다.

```java
ApplicationContext context = new ClassPathXmlApplicationContext("blah.xml");
// ApplicationContext context = new FileSystemXmlApplicationContext("blah.xml"); 
```

- 인자로 전달된 문자열이 내부에서 `Resource`로 변환되어 처리된다.
- URL prefix 중 하나인 classpath를 기준으로 설정 파일을 찾는다. (주석은 `file:///` 기준으로 설정 파일을 찾는다?)

 </br>

### 구현체

- `UrlResource`
  - `java.net.URL` 의 구현체
  - 기본적으로 http, https, ftp, file, jar 프로토콜을 지원한다.
- `ClassPathResource`
- `FileSystemResource`
- `ServletContextResource`
  - 웹 애플리케이션 루트에서 상대 경로로 리소스를 찾는다.
  - 스프링 부트는 기본적으로 서블릿 기반 `WebApplicationContext`를 하며`WebApplicationContext`의 구현체는`ServletContextResource`를 사용하기 때문에 사실상 가장 많이 사용된다고 볼 수 있다. [📌](#WebApplicationContext)

</br>

## 리소스 읽어오기

`Resource` 의 타입은 **`ApplicationContext`의 타입**과 location 문자열에 따라 결정된다.

<br>

### ApplicationContex의 타입

사용하는 `ApplicationContext`의 구체적인 타입에 따라, 인자로 전달된 리소스 문자열이 변환될 Resource 구현체 타입이 결정된다.

> 예시 1

```java
var ctx = new FileSystemXmlApplicationContext("blah.xml");
```

- `ApplicationContext`의 구체적인 타입이 `FileSystemXmlApplicationContext`이기 때문에 인자로 전달된 리소스는 `FileSystemResource`가 된다.

</br>

> 예시 2

```java
    @Autowired
    ResourceLoader resourceLoader;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(resourceLoader.getClass());
        Resource resource = resourceLoader.getResource("test.txt");
      	System.out.println(resource.getClass());
    }
```

- 스프링 MVC는 기본적으로 `WebApplicationContext`의 구현체가 주입될 것이다.
- 리소스 문자열의 prefix가 명시되지 않았으므로, `ApplicationContext` 타입에 따라 서블릿 기반 리소스 타입 (`ServletContextResource`)을 반환한다.

> 예시 2 결과

```
class org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext
class org.springframework.web.context.support.ServletContextResource
```

- 참고로 스프링 MVC 프로젝트가 아닌 일반 스프링 부트 프로젝트라면 기본으로 `AnnotationConfigApplicationContext` 가 주입된다.

- 이 때, prefix를 명시하지 않은 리소스의 타입은 `ClassPathResource`로 반환한다.

  ```
  class org.springframework.context.annotation.AnnotationConfigServletWebServerApplicationContext
  class org.springframework.core.io.ClassPathResource
  ```

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/82aa3086a367dc91105c94df437f1ff941a80272)

</br>

### Location의 Prefix

`ApplicationContext`의 타입과 상관없이 리소스의 타입을 강제하려면 Prefix를 명시한다.

- Prefix 종류  

  `java.net.URL`이 지원하는 접두어 + `classpath:`

> 예시

```java
    @Autowired
    ResourceLoader resourceLoader;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(resourceLoader.getClass());
        Resource resource = resourceLoader.getResource("classpath:test.txt");
      	System.out.println(resource.getClass());
    }
```

- 기본으로 `WebApplicationContext` 계열의 `ApplicationContext`가 주입되지만, Location의 prefix (`classpath:`)를 명시해주었기 때문에 서블릿 기반 리소스가 아닌 `ClassPathResource` 를 반환한다.

> 예시 결과

```
class org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext
class org.springframework.core.io.ClassPathResource
```

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/dc2b6041367722af7a08142eca28c8f57462b20c)

</br>

## 기타

### `WebApplicationContext`

- 웹 애플리케이션에 대한 설정을 제공한다.
- 스프링 부트가 생성하는 `ApplicationContext`가 기본적으로 서블릿 기반 `WebApplicationContext`이다.

</br>

### URL prefix

- 파일의 위치를 표현하기 위해 사용된 프로토콜

  > `classpath:`, `http:`, `file:///`, etc

</br>

### 클래스 패스 ✏️

### 웹 애플리케이션 루트 ✏️

</br>

## 참고

[생활코딩 - 클래스 패스](https://opentutorials.org/course/1223/5527)
# SpEL

> Spring Expression Language

### SpEL 이란?

- 동적으로 객체 그래프를 조회하거나 조작할 때 사용한다.
- JSP에서 사용하는 [Unified Expression Language](https://docs.oracle.com/javaee/5/tutorial/doc/bnahq.html)과 비슷한 역할
  - SpEL은 메소드 호출을 지원하며, 문자열 템플릿 기능을 추가적으로 제공한다.

</br>

### 문법

- `#{"표현식"}`

  - 중괄호 안을 표현식으로 인식하여 실행한다.

- `${"프로퍼티"}`

  - 표현식은 프로퍼티를 가질 수 있지만 그 반대는 성립하지 않는다.

    ```java
    @Value("#{${my.value} eq 100}") // 가능
    ```

> [구현 코드 - #{}](https://github.com/beginin15/spring-framework-core/commit/87f7e9759f5bffb0ebf068c0f85dbd421995682d)
>
> [구현 코드 - ${}](https://github.com/beginin15/spring-framework-core/commit/9a83cd5ccc7281856cb4abf26a3a7a7d4381c0e8)

</br>

### 활용 예시

- `@Value` 어노테이션

- `@ConditionalOnExpression`
  - 선택적으로 빈을 등록하거나 빈 설정 파일을 읽을 때 사용한다.

- [스프링 시큐리티](https://docs.spring.io/spring-security/site/docs/3.0.x/reference/el-access.html)

  -  `EvaluationContext`을 이용하면 빈이 제공하는 프로퍼티, 필드, 메소드를 사용할 수 있다.

    ```java
      StandardEvaluationContext context = new StandardEvaluationContext(bean) // 빈을 인자로 넘긴다.
    ```

  - 내부적으로 `EvaluationContext` 를 이용하여 시큐리티 관련 메소드를 호출한다.

    ```java
      @PreAuthorized("hasRole('ROLE_UER')")
      public void create(Contact contact);
    
      @PreAuthorize("hasRole('ROLE_USER')")
      @PostFilter("hasPermission(filterObject, 'read') or hasPermission(filterObject, 'admin')")
      public List<Contact> getAll();
    ```

- [스프링 데이터]()

  - 대표적으로 `@Query` 어노테이션

    ```java
    @Query("select u from User u where u.age = ?#{[0]}")
    List<User> findUsersByAge(int age);
    
    @Query("select u from User u where u.firstname = :#{#customer.firstname}") // 인자의 필드값을 참조할 때
    List<User> findUsersByCustomersFirstname(@Param("customer") Customer customer);
    ```

- Thymeleaf

</br>

## SpEL 인터페이스

> 여러 가지 인터페이스를 제공하지만 가장 간단한 내용만 정리했다.

</br>

### `ExpressionParser`

- 문자열로된 표현식을 코드 자체로 파싱하는 기능을 제공한다.

  > 예시

  ```java
  ExpressionParser parser = new SpelExpressionParser();
  Expression exp = parser.parseExpression("'Hello World'.concat('!')"); 
  String message = (String) exp.getValue();
  ```

  > 결과

  ```
  Hello World!
  ```

- SpEL도 내부적으로`ConversionService` 를 사용하여 변환한다.

  ```java
  ExpressionParser parser = new SpelExpressionParser();
  Expression expression = parser.parseExpression("2 + 100");
  System.out.println(expression.getValue(Integer.class)); // getValue()에서 타입 변환 발생
  ```

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/ad26c181f84afedbf35498c662feb96d8ef83efc)

### ``EvaluationContext`` ✏️

</br>

## 참고

[Spring 공식 - SpEL](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#expressions)


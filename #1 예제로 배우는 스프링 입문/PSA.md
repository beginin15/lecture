# 스프링 PSA

## PSA란?

- Portable Service Abstraction



### Service Abstraction

- 추상화 계층을 사용하여 개발자에게 편의성을 제공하는 것

- 예시
  - `@Controller` 어노테이션이 붙은 클래스를 이용하여 요청을 맵핑한다.

    > 내부적으로는 서블릿 기반으로 코드가 작동하지만 서블릿 기술은 추상화 계층에 의해 숨겨져 있기 때문에 가능한 것



### Portable + Service Abstraction

- 추상화 계층을 기반으로 다른 기술 스택으로 교체가 용이하도록(Portable) 확장성을 제공하는 것



## 스프링이 제공하는 PSA

### Spring Web MVC

- `HttpServlet`을 직접 사용하지 않아도 된다.
- 어노테이션을 이용하여 간편하게 요청을 맵핑할 수 있다.
- 코드의 (큰) 변경없이 톰캣, 제티, 네티, 언더토우 등 웹 애플리케이션 서버 스펙을 변경할 수 있다. (Portable!)



### Spring Transaction

- DB의 트랜잭션 처리를 어노테이션으로 간편하게 처리할 수 있다.

  > 직접 트랜잭션을 처리하는 경우

  ```java
  try {
  	dbConnection = getDBConnection();
  	dbConnection.setAutoCommit(false);
  
  	// SQL 실행 ...
    
  	dbConnection.commit();
    
  } catch(SQLException e) {
    dbConnection.rollback();
  }
  ```

-  코드의 변경없이 트랙잭션을 관리하는 구현체를 사용하는 기술에 따라 교체가 가능하다. (Portable!)

  > Jpa TransactionManager, Hibernate TransactionManager ...



### Spring Cache

- 캐시 구현체에 상관없이 어노테이션을 이용하여 캐시를 처리할 수 있다.



## 기타

- 서블릿을 직접 사용해보고 SA의 필요성을 느껴보면 좋을 것 같다.

- WebFlux
- Cache
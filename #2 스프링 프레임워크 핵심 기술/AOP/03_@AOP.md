# 스프링 AOP 3부

## `@AOP`

> 어노테이션 기반으로 스프링 AOP 사용하는 방법에 대해 알아보자.

</br>

## Aspect 정의하기

```java
@Component
@Aspect
public class PerfAspect {

    @Around("execution(* com.course..*.EventService.*(..))")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable { // proceed하면서 에러가 발생할 수 있으므로 throws
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }
}
```

### `@Aspect` 

- 빈으로 등록해야하므로 `@Component`를 함께 사용한다.

- Advice와 Point cut 대한 정보가 필요하다.

  </br>

### `@Around`

- Point cut를 정의할 때 사용한다.

- 메서드를 감싸는 형태로 Aspect를 적용하는 Point cut

  > 메소드 호출을 감싸고 있기 때문에 호출 이전, 호출 이후, 해당 메소드에서 발생한 에러를 처리할 때 사용할 수 있다.

  </br>

### Point cut 표현식

- execution

  - 특정 클래스(Target)의 모든 메소드에 적용할 수 있다.

    ```java
    @Around("execution(* com.course..*.EventService.*(..))") // EventService의 모든 메소드에 Aspect를 적용한다.
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
      // ...
    }
    ```

  - 표현식을 조합하여 어노테이션처럼 특정 메소드에만 적용 가능하다.  (나중에 찾아보자)

- 어노테이션

  - 어노테이션이 붙은 특정 메소드에만 적용할 수 있다.
  - 여러 클래스에서 재사용하기 쉽다.
  - Aspect가 적용된다는 것을 명시하는 효과가 있기 때문에 어노테이션 기반으로 Aspect를 적용하는 방법을 추천한다.

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/0c53ffe0769b51a54b65ddc2658a84bddcbb0d08)

- bean

  - execution과 마찬가지로 특정 빈의 모든 메소드에 적용할 때 사용한다.

    ```java
    @Around("bean(simpleEventService)")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
    	// ...
    }
    ```

  </br>

## 기타

### 어노테이션의 `RetentionPolicy`  📌

- 어노테이션 정보를 **어느 기간 동안 유지할 것인가**를 결정한다.

- 종류

  **CLASS**

  - 클래스 파일까지 유지하기

  **SOURCE**

  - 소스 코드까지 유지하기

  - 컴파일 전까지 유지한다. 즉 컴파일 된 이후에는 적용되지 않는다.
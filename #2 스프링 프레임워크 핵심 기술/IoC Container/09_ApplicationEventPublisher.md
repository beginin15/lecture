# IoC 컨테이너 9부

## `ApplicationEventPublisher`

- **이벤트** 프로그래밍에 필요한 인터페이스를 제공한다.
- **옵저버** 패턴의 구현체
- `ApplicationContext`가 상속받고 있는 인터페이스 중 하나
  - 즉, `ApplicationContext`를 이용하여 이벤트 처리가 가능하다.

</br>

### 4.2 버전 이전 이벤트 처리

- `ApplicationEvent` 클래스

  - 이벤트 객체가 상속받는다

  - 생성자로 이벤트를 발생시킨 주체의 객체를 받는다.

    ```java
    public class MyEvent extends ApplicationEvent {
    
        private int data;
    
        public MyEvent(Object source, int data) { // source - 이벤트 발생 주체
            super(source);
            this.data = data;
        }
        // ...
    }
    ```

- `ApplicationListener<T>` 인터페이스

  - 이벤트 핸들러가 구현한다.

  - 이벤트를 처리하는 메소드를 오버라이딩 한다.

    ```java
    @Component
    public class MyEventHandler implements ApplicationListener<MyEvent> {
        @Override
        public void onApplicationEvent(MyEvent event) {
    			// ...
        }
    }
    ```

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/8a3b8ab9b88d5e5945ae16531e0bd1dda86d00d4)

</br>

### 4.2 버전 이후 이벤트 처리

- 이벤트 객체는 POJO 클래스로 정의한다.

  - 스프링에서 제공하는 클래스나 인터페이스를 상속 및 구현할 필요가 없다.

- `@EventListener`를 이용하여 빈의 메소드에 사용할 수 있다. (이벤트 핸들러가 처리할 메소드)

  - 즉, `ApplicationListener`를 구현할 필요없이 어노테이션만 붙이면 된다.

    ```java
    @Component
    public class MyEventHandler {
        @EventListener
        public void handle(MyEvent event) {
            // ...
        }
    }
    ```

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/5f5620e7628eed15d997f9a222fda683ec07ef93)

</br>

### 이벤트 발생

- `ApplicationEventPublisher` 의 `publishEvent()` 메소드를 이용하여 이벤트를 발생시킬 수 있다.

  ```java
  eventPublisher.publishEvent(new MyEvent(this, 100)); // 인자로 발생시킬 이벤트 객체를 전달한다.
  ```

</br>

## 주의할 것

- 이벤트 객체는 빈이 아니다!
- 이벤트 핸들러는 빈이다.
- 동일한 이벤트를 처리하는 핸들러가 존재한다면, 기본적으로 순차적으로 실행한다.
  - 동시에 서로 다른 스레드가 실행하지 않는다.
  - 어떤 이벤트가 먼저 처리될지 알 수 없다. 

</br>

## 이벤트 우선순위

### `@Order`

- 동일한 이벤트를 처리하는 핸들러가 2개 이상인 경우, `@Order`를 이용하여 대략적인 순서를 지정할 수 있다.
  - 대략적이라고 표현한 이유는 디테일하게 지정할 수 없기 때문에? *(자세히 알아보기)*

  ```java
  @EventListener
  @Order(Ordered.HIGHEST_PRECEDENCE) // 일빠로 처리한다.
  public void handle(MyEvent event) {
    // ...
  }
  ```

  ```java
  @EventListener
  @Order(Ordered.HIGHEST_PRECEDENCE + 2) // 상수값으로 대략적인 순서를 표현한다.
  public void handle(MyEvent event) {
    // ...
  }
  ```

  > 아래가 위보다 나중에 처리된다.

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/be4778a4d0b15e87a8faa0308dcecc3ed2ee0e74)

</br>

### 비동기적으로 이벤트 처리

- 기본적으로 이벤트는 synchronized 하게 처리한다.

- `@Async`를 이용하여 서로 다른 스레드가 이벤트를 동시적으로 처리할 수 있다.

  - `@Configuration` 이 붙은 클래스에 `@EnableAsync` 함께 사용해야 한다.

    ```java
    @SpringBootApplication
    @EnableAsync
    public class SpringFrameworkCoreApplication {
    	// ...
    }
    ```

- 서로 다른 스레드가 이벤트를 처리, 우선 순위(`@Ordered`)는 무의미하다.

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/69309144cec9f402e5f020a79f38ae7a790c9cbd)

  > `@Async` 사용 전

  ```
  Thread[restartedMain,5,main]
  또 다른 핸들러, 100
  Thread[restartedMain,5,main]
  이벤트 받았다! 100
  ```

  > `@Aysnc` 사용 후

  ```
  Thread[task-2,5,main] // 스레드가 다르다.
  Thread[task-1,5,main]
  이벤트 받았다! 100
  또 다른 핸들러, 100
  ```

</br>

## 스프링이 제공하는 기본 이벤트

### `ContextRefreshedEvent`

- `ApplicationContext`를 초기화 했거나 리프레시 했을 때 발생하는 이벤트

###  `ContextClosedEvent`

- `ApplicationContext`를 `close()`하여 싱글톤 빈이 소멸되는 시점에 발생하는 이벤트

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/6ba7d8b81f39800da9d938ee3172b1d6d0168fae)

  > 이벤트 호출 시점 살펴보기

  ```
  2020-07-31 14:11:27.778  INFO 2084 --- [  restartedMain] c.c.s.c.SpringFrameworkCoreApplication   : Starting SpringFrameworkCoreApplication on useruiMcBookPro with PID 2084 (/Users/user/IdeaProjects/spring-framework-core/build/classes/java/main started by user in /Users/user/IdeaProjects/spring-framework-core)
  2020-07-31 14:11:27.780  INFO 2084 --- [  restartedMain] c.c.s.c.SpringFrameworkCoreApplication   : No active profile set, falling back to default profiles: default
  2020-07-31 14:11:27.808  INFO 2084 --- [  restartedMain] .e.DevToolsPropertyDefaultsPostProcessor : Devtools property defaults active! Set 'spring.devtools.add-properties' to 'false' to disable
  2020-07-31 14:11:28.163  INFO 2084 --- [  restartedMain] o.s.b.d.a.OptionalLiveReloadServer       : LiveReload server is running on port 35729
  Thread[restartedMain,5,main]
  ContextRefreshedEvent // ApplicationContext 초기화
  2020-07-31 14:11:28.186  INFO 2084 --- [  restartedMain] c.c.s.c.SpringFrameworkCoreApplication   : Started SpringFrameworkCoreApplication in 0.589 seconds (JVM running for 1.079)
  2020-07-31 14:11:28.197  INFO 2084 --- [  restartedMain] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
  Thread[task-2,5,main]
  이벤트 받았다! 100
  Thread[task-1,5,main]
  또 다른 핸들러, 100
  Thread[SpringContextShutdownHook,5,main]
  ContextClosedEvent // 프로그램 종료하여 싱글톤 빈들이 소멸되는 시점
  2020-07-31 14:11:31.664  INFO 2084 --- [extShutdownHook] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'
  ```

  

</br>

## 기타

### 비침투성

- 스프링의 철학 중 하나
- POJO 클래스에서 스프링 패키지를 Import하지 않는다.
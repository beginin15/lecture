# 데이터 바인딩 1부

> 스프링의 대표적인 추상화 레퍼런스를 살펴보자

</br>

## 데이터 바인딩이란?

- 프로퍼티 값을 타겟 객체에 바인딩한다.

- 사용자가 입력한 값을 애플리케이션 도매인 객체에 동적으로 할당하는 것

  - 사용자가 입력한 값은 주로 문자열로 넘어오기 때문에, 이러한 값들을 객체가 가진 다양한 타입의 필드에 맵핑하기 위해서 바인딩 작업이 필요하다.

  - 스프링 웹 MVC에서 기본적인 타입에 대한 바인딩은 지원한다.

    ```java
    @GetMapping("/event/{id}")
    public String getEvent(@PathVariable Integer id) { // 문자열을 Integer로 변환
      // ...
    }
    ```

- 스프링에서 제공하는 데이터 바인딩은 웹 MVC에만 특화된 것이 아니라 다양한 곳에서 사용된다.
  - 웹 MVC 요청을 처리
  - xml 설정 파일에 작성된 문자열을 빈이 가지고 있는 타입으로 변환
  - SqEL

</br>

## `PropertyEditor` 활용하기

> 요구 사항

```java
    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event) {
        System.out.println(event);
        return event.getId().toString();
    }
```

- `/event/1` 로 요청이 왔을 때 해당 값을 가지고 있는 `Event` 객체를 반환한다.

</br>

### `PropertyEditorSupport`

- `PropertyEditor` 인터페이스를 직접 구현하는 방법이 있지만, 구현해야할 메소드가 너무 많아서 실용성이 떨어진다.

  <img src="https://user-images.githubusercontent.com/33659848/90764471-4fc6a380-e323-11ea-8542-92cbf3dd2d36.png" alt="PropertyEditor_methods" style="zoom: 33%;" />

</br>

### `PropertyEditorSupport`

- `PropertyEditor`의 구현체로, 필요한 메소드만 구현해서 사용할 수 있다.
- `PropertyEditor`는 내부적으로 바인딩 대상이 되는 객체를 값으로 가지고 있다.

- 주요 구현 메소드

  - `setAsText(String text)`

    인자로 전달된 문자열을 맵핑하고자 하는 타입으로 변환하여 객체를 생성해주면 된다.

    ```java
        @Override
        public void setAsText(String text) throws IllegalArgumentException {
            setValue(new Event(Integer.parseInt(text)));
        }
    ```

  - `getAsText()`

    객체의 특정 필드를 문자열로 반환한다.

    ```java
        @Override
        public String getAsText() {
            Event event = (Event)getValue();
            return event.getId().toString();
        }
    ```

  - `setValue()`

    바인딩 객체를 저장한다.

  - `getValue()`

    바인딩 객체를 가져온다.

</br>

### 중요 특징

- `PropertyEditor`는 stateful하다. 즉, **thread-safe 하지 않다!**
  - `PropertyEditor`가 가지고 있는 값(객체)는 모든 스레드가 공유한다.
  - `PropertyEditor`의 구현체는 **빈, 컴포넌트로 등록해서 사용하면 안된다.** (스레드 스코프 빈으로 사용해야 한다.)
  - 컨트롤러에서 `@InitBinder` 를 이용하여 별도로 등록해서 사용해야 한다.
- `Obejct`와 `String` 간의 변환만 가능하다.

</br>

### `@InitBinder`

- 특정 컨트롤러에서 사용할 바인더를 등록한다.

- 클래스 내의 모든 메소드에 대해 파라미터를 바인딩하기 전에 자동으로 호출된다.

  - 즉 모든 요청마다 새로 바인더를 생성한다.

  ```java
  @RestController
  public class EventController {
  
      @InitBinder
      public void initBinder(WebDataBinder webDataBinder) {
          webDataBinder.registerCustomEditor(Event.class, new EventEditor());
      }
      
      // ...
  }
  ```

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/32f7ef9c1f31838b0f2e0cd7d50bead0b7c38311)
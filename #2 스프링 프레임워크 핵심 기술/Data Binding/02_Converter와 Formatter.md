# 데이터 바인딩 2부

> 스프링의 대표적인 추상화 레퍼런스를 살펴보자

</br>

## `Converter`

### 특징

- `PropertyEditor`의 단점을 보완했다.

  - `Object` - `String` 뿐만 아니라 객체 - 객체간의 변환을 제공한다.

  - stateful하지 않기 때문에 thread-safe하며, 빈으로 등록 가능하다.

    > 빈으로 등록된다는 것은 의존성 주입으로 다른 빈을 가져다 사용할 수 있다는 의미

- 컨버터를 클래스 단위가 아니라 전역으로 설정할 수 있다.

  - `ConvertRegistry` 에 등록해서 사용해야 한다.

</br>

### `ConvertRegistry` 에 등록하기

- 스프링 부트가 아닌 스프링 웹 MVC를 사용한다면 `WebMvcCongifurer`을 이용하여 별도로 컨버터를 등록해야 한다.

  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
  
      @Override
      public void addFormatters(FormatterRegistry registry) {
          registry.addConverter(new EventConverter.StringToEventConverter());
      }
  }
  ```

  - `WebMvcConfigurer`는 스프링 웹 설정을 제공하는 인터페이스

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/e4c2ec3a1e45d660c1719850d01e4a974252c01e)

</br>

## `Formatter`

### 특징

- 웹에 특화된 바인딩 기능 제공
  - `MessageSource` 사용 가능 (메세지 다국화)

> [구현 코드 - 빈으로 등록하지 않는 방법](https://github.com/beginin15/spring-framework-core/commit/f229a3058e1b165cbf697f7e38d86cac20e782e9)
>
> [구현 코드 - 빈으로 등록, MessageSource 이용](https://github.com/beginin15/spring-framework-core/commit/2741154f32fcc34be93e6b9fcdd09d74cdfab79d)

</br>

## `ConversionService`

### 역할

- `Converter`와 `Formatter`를 이용한 데이터 바인딩을 처리하는 인터페이스
  - `PropertyEditor`를 사용한 데이터 바인딩 작업은 `DataBinder` 인터페이스가 처리한다.

- 스프링 MVC, xml 설정 파일, SpEL에서 문자열을 객체로 바인딩할 때 사용된다.
- 여러가지 기본 Converter와 Formatter가 등록되어 있다.

</br>

### `DefaultFormattingConversionService`

- 스프링에서 제공하는 `ConversionService` 구현체

- `FormatterRegistry`, `ConversionService`를 구현했다.

  - `FormatterRegistry`는 `ConverterRegistry`를 상속받았기 때문에 `FormatterRegistry`를 통해서 `addConverter()`도 가능하다.

  ![DefaultFormattingConversionService_hierarchy](https://user-images.githubusercontent.com/33659848/90773473-ee0d3600-e330-11ea-94d8-069e0e92e0ad.png)

</br>

### `WebConversionService`

- 스프링 부트에서 제공하는 `DefaultFormattingConversionService` 을 확장한 클래스
  - `DefaultFormattingConversionService`보다 더 다양한 웹 관련 기능을 제공한다. (날짜, 통화, 시간 formatter 등)
- 컴포넌트로 등록된 Formatter와 Converter를 자동으로 `ConversionService`에 등록해준다.
  - 컴포넌트로 등록했다면 `WebMvcConfigurer` 를 이용하여 직접 등록할 필요가 없다.

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/b859260a303fa014c160df90dd42a6e17ca146e7)

</br>

## 기타

### 슬라이스 테스트

- 레이어별로 구분하여 테스트하는 것을 말한다.

- 모든 빈을 등록하지 않고 특정 레이어에서 필요한 빈만 등록할 수 있다. (테스트의 경량화)

- `@WebMvcTest`는 기본적으로 웹 레이어와 관련된 빈만 등록하여 테스트한다.

  - 커스텀하게 구현한 Formatter와 Converter를 빈으로 등록하지 않는다.

- `@WebMvcTest`로 빈 등록하는 방법

  ```java
  @WebMvcTest({
          EventController.class,
          EventFormatter.class})
  ```

  - 컴포넌트 스캔이 가능한 빈만 지원한다.

> [구현 코드](https://github.com/beginin15/spring-framework-core/commit/9f923992e413e1036e5c74f9b81e728719383bf3)

</br>

### JPA의 컨버터

- `@Entity` 가 적용된 클래스에 대한 Converter를 기본으로 제공한다.


java.net URL을 추상화 한 것

- 클래스 패스를 기준으로 리소스를 가져오는 기능이 없었기 때문에

classpath?

- 지원하는 접두어가 classpath



ApplicationContext의 타입에 따라 결정된다.

Xml, Classpath, File...emd



구현체에 따라 인자로 전달되는 문자열이 리소스 타입으로 연결된다. 

구현체에 상관없이 리소스 타입을 강제하려면 prefix!! 권고! 명시하는것이 좋음

---

Validation 추상화

- 웹 애플리케이션용이 아님. 여러 계층에서 사용 가능 (쉡, 서비스, 데이터)
- 2가지 메소드를 구현해야 한다.
  - support 해당 인터페이스가 지원하는 클래스인지 검사
  - validate 실제 객체 검증을 위한 로직 



- BeanProperyBindingResult는 테스트라서 직접 생성하는 것. 스프링 MVC가 자동으로 생성해서 파라미터로 넘기준다.

- 에러 코드를 제공한다.
  - 내가 직접 지정할 수도 있고, 검증하고자하는 데이터의 필드, 타입, 에러코드 이름 등을 제공한다.

스프링부트

- LocalValidatorFactoryBean
- Bean Validation 어노테이션을 지원
- 그냥 Validator를 주입받으면 거기에 Loca..이 주입됨
  - 검증하고자 하는 빈에 존재하는 어노테이션을 기반으로 객체 검증을 해줌 



---

데이터 바인딩 추상화

- 프로퍼티를 타겟 객체에 바인딩한다.
- 입력한 값을 애플리케이션 도매인 객체에 할당한다.
- 입력은 주로 문자열, 객체가 가진 프로퍼티 타입에 매칭되도록, 변환해주는 기능 자체를 바인딩이라고 한다.



- DataBinder
  - MVC 웹 요청 처리시
  - xml 파일에 작성되는 이름을 적절한 빈 타입으로 변환할 때도 사용되었다.
  - Express?
  - 여러 곳에서 사용된다.

 PropertyEditor

- 구현해야 하는 메소드가 너무 많음

PropertyEditorSupport

- getAsText
  - 객체를 텍스트로 바인딩하여 반환
- setAsText
  - 텍스트를 객체로 바인딩

-  stateful 하다
  - 가지고 있는 value가 스레드 간에 공유한다.
    - editor가 가지고 있는 값, 상태를 가진다.
  - thread-safe하지 않다
  - 즉, 컴포넌트로 등록하지 말아야한다. (싱글톤 스코프 안돼!)
  - 혹은 스레드 스코프로 지정한다.
    - 한 스레드 내에서만 유효하다.



- @InitBinder
  - 특정 컨트롤러에서 사용할 바인더를 등록한다.
  - 클래스 내의 모든 메소드에 대해 파라미터를 바인딩하기 전에 자동으로 호출된다.





- 여기까지 스프링 3.0이전 바인딩. 이런 저런 설정이 불편하니까
  - 설정이 은근 많다.
  - thread-safe하지 않다.

---

문자와 객체간의 변환이 아닌, 객체와 객체간의 변환도 제공

상태를 가지고 있는 thread-safe 보완

- 빈으로 등록 가능!
- 컨버터를 전역으로 설정 가능 (클래스 단위가 아닌)



ConverterRegistry에 등록해야 한다.

- 부트가 아닌 웹 MVC를 사용하는 경우
  - webConfig로 컨버터 등록 
  - WebMvcCongifurer
    - 스프링 웹 설정을 제공하는 인터페이스





Formatter

- 웹 특화 바인딩 기능 제공
- 웹은 보통 문자열로 데이터를 표현하기 때문에
- thread-safe
- 빈으로 등록 가능
- 의존성 주입도 가능
- MessageSource 이용도 가능



빈으로 등록하지 않고 쓰는 방법

- WebConfig addFormatter로 그냥 등록





PropertyEditor를 사용하는 것은 DataBinder가 처리

Converter, Formatter는 ConversionService가 실제 변환을 처리



스프링 부트는 WebConversionService를 제공한다.

- DefaultFormattingConversionService를 상속해서 구현되어있다.
- 직접 convert, canConvert으로 변환을 구현할 수 있다. (거의 안씀)



Formatter, Converter를 사용하려고 빈으로 등록할 필요가 없다. 자동으로 등록해준다.

@Component



부트의 테스트

슬라이싱?

계층형 테스트

- 기본적으로 웹과 관련된 빈들만 등록한다. (컨트롤러)



테스트에 웹 빈이 아닌 Converter, Formatter를 빈으로 등록하라고 명시해주자

- 컴포넌트 스캔이 가능한 경우에만 지원해준다.



등록된 컨버터 보는 방법

conversionService 출력하면 다 나옴





---

SpEL

- 스프링 Expression Language
- 객체 그래프?
- jsp에서 자주 사용하는 표현식과 비슷
- 왜 개발?
  - 메소드를 호출하는 기능, 문자열 템플릿 기능을 제공해야해서?
- 어디서 써??
- #{ ... }
  - 표현식을 참고
  - 괄호안을 표현식으로 인식한다.
  - 표현식을 실행한다.
  - 표현식 안에서 프로퍼티 참고 가능
- ${}
  - 프로퍼티를 참고 
  - 프로퍼티 안에서는 표현식 사용 못함



- 산술 연산
- sprint.io.spEl 참고해보자



- 빈 참고하기
  - 그냥 빈 객체를 참조해서 값을 가져오는 것과 sqel로 하는 차이점
  - 데이터 변경 방지? 특정 value만 가져올 수 있음?





- 어디서 쓴다고?
- @ConditionalOnExpression
  - sqel지원
  - 선택적으로 빈을 등록할 수 있다. 빈 설정 파일을 읽어들일 때 선별적으로 빈 등록
  - 마찬가지로 표현식으로 지정 가능
- 스프링 시큐리티
  - xml 설정 파일에서
  - 어노테이션에서 사용하기도 함
  - EvaluationContext
    - 빈을 등록하면 해당 빈이 제공하는 메소드를 표현식으로 호출할 수 있다.
- Spring Data의 @Query 어노테이션
- @Quryㅇ에서 :#_{} 이런거!
- Thymeleaf



ExpressionParser

- 표현식 문자열을 코딩으로 value로 파싱하는 기능 제공

- expression.getValue(Integer.class)
  - 여기서도 ConversionService 인터페이스를 사용하는 것
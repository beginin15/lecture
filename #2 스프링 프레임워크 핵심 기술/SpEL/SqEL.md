SpEL

- 스프링 Expression Language
- 객체 그래프?
- jsp에서 자주 사용하는 표현식과 비슷
- 왜 개발?
  - 메소드를 호출하는 기능, 문자열 템플릿 기능을 제공해야해서?
- 어디서 써??
- \#{ ... }
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
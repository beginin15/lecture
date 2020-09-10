# TDD, 리팩토링이란?

## 용어 정리

![main_test.PNG](https://firebasestorage.googleapis.com/v0/b/nextstep-real.appspot.com/o/lesson-attachments%2F-KtjhuyrC6mMND3m57bq%2Fmain_test.PNG?alt=media&token=c25514e2-6e59-49c0-bd92-de58dbb8c1c3)

- 위 그림을 통해 확인할 수 있듯이 **프로덕션 코드(Production Code)** 프로그램 구현을 담당하는 부분으로 사용자가 실제로 사용하는 소스 코드를 의미한다.
- **테스트 코드(test code)** 는 프로덕션 코드가 정상적으로 동작하는지를 확인하는 코드이다.

</br>

## TDD란?

- TDD = TFD(Test First Development) + 리팩토링

- TDD란 프로그래밍 의사결정과 피드백 사이의 간극을 의식하고 이를 제어하는 기술이다. - 켄트벡, Test Driven Development by Example 중
- TDD의 아이러니 중 하나는 테스트 기술이 아니라는 점이다. TDD는 분석 기술이며, 설계 기술이기도 하다. - 켄트벡, Test Driven Development by Example 중

</br>

## TDD를 하는 이유

- 디버깅 시간을 줄여준다.
- 동작하는 문서 역할을 한다.
- 변화에 대한 두려움을 줄여준다.

</br>

## TDD 사이클

![tdd cycle](https://articles.tbscg.com/wp-content/uploads/2015/11/tdd-cycle.png)

- 실패하는 테스트를 구현한다.
- 테스트가 성공하도록 프로덕션 코드를 구현한다.
- 프로덕션 코드와 테스트 코드를 리팩토링한다.

</br>

## TDD 원칙

- 원칙 1 - 실패하는 단위 테스트를 작성할 때까지 프로덕션 코드(production code)를 작성하지 않는다.
- 원칙 2 - 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
- 원칙 3 - 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.
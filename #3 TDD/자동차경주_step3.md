# 자동차 경주 Step3

## 제네릭과 배열

### 공변 (covariant)

자기 자신과 자식 객체를 허용한다.

- Java의 `<? extends T>`

- Java의 **배열**

  ```java
  Object[] objects = new Long[1];
  objects[0] = "타입이 다름" // 런타임시 에러가 발생할 수 있다.
  ```

  - `Long`은 `Object`의 하위 타입이므로 `Long[]`은 `Object[]`의 하위 타입이 된다.

</br>

### 불공변 (invariant)

상속 관계에 관계없이 자신의 타입만 허용한다.

- Java의 **제네릭**

  ```java
  List<Object> objects = new ArrayList<Long>(); // 컴파일시 에러가 발생한다.
  objects.add("타입이 다름");
  ```

</br>

### 제네릭과 배열

- 타입의 안정성을 위해 제네릭 배열은 사용하지 않는 것이 좋다.
- 배열 대신 리스트를 이용한다.

</br>

### 참고

[이펙티브 자바 item 28 - 배열보다는 리스트를 사용하라](https://velog.io/@kskim/아이템-28.-배열보다는-리스트를-사용하라)

</br>

## 반환 타입으로는 스트림보다 컬렉션이 낫다

원소 시퀀스를 반환하는 public API 메소드는 `Collection`이나 그 하위 타입을 반환하는 것이 일반적으로 최선이다.

- `Stream` 인터페이스는 `Iterable` 인터페이스를 확장하지 않았기 때문에 반복을 지원하지 않는다.

  > `Stream`을 이용하여 반복하는 방법은 있지만 추가적인 변환이 필요하다.

- `Collection` 인터페이스는 `Iterable` 하위 타입이고, `stream()` 메소드를 제공하므로 두 요구사항을 모두 충족할 수 있다.

</br>

### 용도에 따른 구분

- 스트림에서만 사용한다면 `Stream` 반환
- 반복문 사용이 필요하다면 `Iterable` 반환
- 둘 다 지원해야 한다면 `Collection` 반환

</br>

### 참고

[이펙티브 자바 item 47 - 반환 타입으로는 스트림보다 컬렉션이 낫다.](https://jaehun2841.github.io/2019/02/16/effective-java-item47/#서론)

</br>

## 전략 패턴

동적으로 알고리즘을 선택할 수 있는 행위 디자인 패턴

</br>

### 구현 핵심

- 객체들이 할 수 있는 행위를 전략 클래스를 생성하고 유사한 행위를 캡슐화하는 인터페이스를 정의한다.
- 객체의 행위를 동적으로 바꾸고 싶으면 전략 클래스를 교체해주기만 하면 된다. (유연함)

</br>

### 자바 API에서 사용 예

- [`java.util.Comparator#compare()`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html#compare-T-T-), executed by among others `Collections#sort()`.
- [`javax.servlet.http.HttpServlet`](http://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServlet.html), the `service()` and all `doXXX()` methods take `HttpServletRequest` and `HttpServletResponse` and the implementor has to process them (and not to get hold of them as instance variables!).
- [`javax.servlet.Filter#doFilter()`](http://docs.oracle.com/javaee/7/api/javax/servlet/Filter.html#doFilter-javax.servlet.ServletRequest-javax.servlet.ServletResponse-javax.servlet.FilterChain-)

</br>

### 참고

[Examples of GoF Design Patterns in Java's core libraries](https://stackoverflow.com/questions/1673841/examples-of-gof-design-patterns-in-javas-core-libraries)

</br>

## 테스트 코드를 위한 getter

테스트 코드를 위해서 비즈니스 로직에 메소드를 추가하는 것과 마찬가지이므로 추천하지 않는다.

### getter 없이 테스트하기

1. 리플렉션

   > 객체 지향적인 방법은 아닌 것 같다.

2. 객체와 객체간의 동등성, 논리적 동치성 비교 (equals, hashcode 재정의)

3. 객체의 메소드가 결과값을 반환하도록 한다.

</br>

### getter가 불가피한 경우

다른 레이어로 객체가 넘어갈 때는 **상태 조회**라는 기능을 제공한다는 명분으로 사용할 수 있다. (DTO의 getter)

> 도메인 객체가 View 레이어로 넘어갈 때

</br>

### 참고

[getter 없이 테스트하기 - PR](https://github.com/next-step/java-racingcar/pull/1194#discussion_r468587350)

[getter가 불가피한 경우 - PR](https://github.com/next-step/java-racingcar/pull/1194#discussion_r470307966)

</br>

## 테스트 코드에서 의존성 주입

의존성을 주입할 수 있는 구조라면 테스트가 편리해진다. 

- 람다를 비롯하여 다른 **테스트 전용 구현체**를 사용할 수 있기 때문

  > 이번 과제에서 **전략 패턴**을 이용한 이유이기도 하다.

- 인터페이스를 잘 활용해보자.

  > **전략 패턴과 의존성 주입**을 활용하여 테스트 코드에서 객체의 이동 여부를 제어했다.

  ```java
  // 상황(프로덕션 or 테스트)에 따라 다른 행위를 해야하므로 전략 클래스를 인터페이스로 구현했다.
  public interface MoveStrategy {
    public boolean canMove();
  }
  ```

  ```java
  // 항상 이동하는 행위를 디자인 (for.테스트)
  public class AlwaysMoveStrategy {
    @Override
    public boolean canMove() {
      return true;
    }
  }
  ```

  ```java
  // 항상 이동하지 않는 행위를 디자인 (for.테스트)
  public class NeverMoveStrategy {
    @Override
    public boolean canMove() {
      return false;
    }
  }
  ```

  ```java
  // 랜덤값에 따라 이동하는 행위를 디자인 (for.프로덕션)
  public class RandomMoveStrategy {
    // ...
    @Override
    public boolean canMove() {
      return random.nextInt(TOTAL_CASES) >= MOVE_CONDITION;
    }
  }
  ```

</br>

## 객체 역할 구분하기

- 도메인 객체와 View 레이어 객체의 의존성을 최대한 분리하자

  > 도메인 객체의 비즈니스 로직을 수행하기 위해 View 레이어까지 알고 있어야 한다면 의존성을 분리해야 한다는 신호이다.

  > 테스트하기 어렵다는 생각이 들면 의존적인 구조인지 의심해보자.

- 단순한 primitive 타입도 객체로 포장하면 객체의 역할과 책임이 분명해진다.

</br>

### 참고

[PR](https://github.com/next-step/java-racingcar/pull/1194#discussion_r470307966)

</br>

## 일급 컬렉션

### 개념

Collection을 **Wrapping**하면서, **그 외 다른 멤버 변수가 없는 상태**를 말한다.

</br>

### 예시

> 일반적인 컬렉션 사용

```java
Map<String, String> map = new HashMap<>();
map.put("1", "A");
map.put("2", "B");
map.put("3", "C");
```

> 멤버 변수가 컬렉션로만 이루어진 클래스를 정의한다.

```java
public class GameRanking {
  
  private Map<String, String> ranks;
  
  public GameRanking(Map<String, String> ranks) {
    this.ranks = ranks;
  }
}
```

</br>

### 특징

- 비즈니스에 종속적인 자료구조를 제공한다. [#1](#1 비즈니스에 종속적인 자료구조를 제공한다.)
- Java에서 final은 재할당만 금지한다. [#2](#2 불변)
- 상태와 행위를 한 곳에서 처리한다. [#3](#3 상태와 행위를 한 곳에서 처리한다.)

</br>

### #1 비즈니스에 종속적인 자료구조를 제공한다.

도메인의 속성을 가지고 있는 하나의 자료구조로 만든다.

- 해당 컬렉션이 필요한 모든 곳에서 매번 검사 로직(유효성 검사)을 작성할 필요가 없다.

- 컬렉션에 들어가는 요소에 대한 조건을 컬렉션과 묶어서 클래스로 표현할 수 있다.

  > 로또 번호가 저장된 컬렉션과, 해당 컬렉션에 저장될 수 있는 숫자들의 조건

</br>

### #2 불변

컬렉션의 불변을 보장한다.

- Java의 `final`은 엄밀히 말하면 불변을 보장해주는 것이 아니고 **재할당만 금지한다.**

  > `final`은 컬렉션에 값이 추가되는 것을 막지 못한다.

  ```java
  final Map<String, Boolean> collection = new HashMap<>();
  
  collection.put("1", true);
  collection.put("2", true);
  collection.put("3", true);
  collection.put("4", true);
  
  assertThat(collection.size()).isEqualTo(4); // 테스트 통과
  ```

  > `final`은 재할당을 막는다.

  ```java
  final Map<String, Boolean> collection = new HashMap<>();
  
  collection = new HashMap<>(); // 컴파일 에러 발생
  ```

- 불변 객체를 만들기 위해 일급 컬렉션과 래퍼 클래스를 사용해야 한다.
  
  - 컬렉션의 값을 변경할 수 있는 메소드가 없는 Wrapper 클래스를 만들면 된다.

</br>

### #3 상태와 행위를 한 곳에서 처리한다.

값과 로직이 함께 존재한다.

- 값과 로직의 관계를 코드로 표현할 수 없다면 똑같은 기능을 수행하는 메소드를 여러 곳에서 중복 생성할 가능성이 높다.

- 컬렉션을 처리하는 로직을 강제한다.

  > 여러 페이들이 저장된 컬렉션과 페이 종류에 따라 계산하는 로직을 묶어서 강제할 수 있다.

</br>

### 참고

[일급 컬렉션 (First Class Collection)의 소개와 써야할 이유](https://jojoldu.tistory.com/412)


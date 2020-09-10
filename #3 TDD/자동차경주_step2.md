# 자동차 경주 Step2

## 클래스의 설계

### 유틸리티 클래스 vs 객체

- 유틸리티 클래스는 정적 팩토리 메소드로만 이루어진 클래스로 상태 값이 따로 필요없다.
- 객체는 상태 값과 행동으로 역할과 책임을 가진다.

</br>

### 권장하는 이상적인 클래스

- 2개 이하의 필드를 가진 클래스만 관리한다.
- 메소드의 인자도 2개 이하로 줄인다.

</br>

### 참고

[참고 PR](https://github.com/next-step/java-racingcar/pull/1136#discussion_r466417229)

[객체지향 프로그래밍으로 유틸리티 클래스를 대체하자.](https://www.mimul.com/blog/oop-alternative-to-utility-classes/)

</br>

## private 메소드의 테스트

### 테스트를 위해 메소드의 접근 지정자를 변경한다?

- 테스트만을 위해서 접근 지정자를 변경하는 것을 지양한다.

  > 견해 차이가 존재할 수 있다.

  - private 메소드는 public 메소드를 통해 호출되기 마련이고, 해당 public 메소드를 통해 간접적으로 테스트가 되기 때문이다.
  - 객체 지향 관점에서 테스트 코드는 인터페이스를 테스트하는 것이라고 볼 수 있다. 즉, 클라이언트가 사용할 인터페이스의 pubilc 메소드가 테스트의 대상이다. 그리고 private 메소드는 public 메소드들을 리팩토링 하는 과정에서 만들어진 메소드일 확률이 높다. 인터페이스의 구현 내용(=리팩토링)에 관계없이 여전히 테스트 코드는 통과해야 한다. 이러한 관점에서 private  메소드는 public 메소드를 통해 검증하는 것이 맞다.

- 다음과 같은 경우에는 **메소드를 별도의 클래스에 분리해야하는 것은 아닌지 의심해봐야 한다.**

  > - 요구 사항에 변경되어 해당 메소드가 다른 클래스나 패키지에서 사용된다.
  > - 메소드의 코드가 복잡해져서 반드시 테스트를 해야한다.

</br>

### private 메소드 테스트하기

- 리플렉션을 이용한 방법

  > 추후에 자세히 알아보기

</br>

### 참고

[참고 PR]()

[테스트 상태인 Private 메소드를 Public메소드로 변환시 Unit Testing은 어떻게 해야하나?](https://www.slipp.net/questions/253)

[객체 지향 관점에서 바라본 '비공개 메서드를 테스트 해야 하는가?'](https://justhackem.wordpress.com/2017/09/29/should-private-methods-be-tested/)

[private 메소드는 인터페이스에 노출되지 않아 목킹이 불가능하므로 테스트 대상이 아니다?](https://justinchronicles.net/ko/2014/07/14/dont-do-testing-private-methods/)

</br>

## Enum 값 해싱하기

### 문제 상황

Enum의 `name`이 아닌, **프로퍼티**를 기준으로 그에 매칭되는 Enum 객체를 가져오고 싶다!

```java
Operator operator = Operator.of("+");
```

</br>

### 해결 방안

1. static 메소드에서 `values()`로 순회하면서 파라미터에 매칭되는 Enum 객체를 반환한다. (**캐싱 X**)

   ```java
   public static Operator valueOfSymbol(String symbol) {
       for (Element e : values()) {
           if (e.symbol.equals(symbol)) {
               return e;
           }
       }
       return null;
   }
   ```

2. `Map`을 이용하여 필요할 때마다 순회하지 않고 미리 값을 **캐싱**한다.

   ```java
   public enum Operator {
   	
       // ... enum values
    
       private static final Map<String, Element> BY_SYMBOL = new HashMap<>();
       
       static {
         BY_SYMBOL.put("+", Operator.PLUS);
         // ... caching
       }
    
      // ... fields, constructor, methods
    
       public static Operator valueOfSymbol(String symbol) {
           return BY_SYMBOL.get(symbol);
       }
   }
   ```

   - `static` 블록은 클래스가 처음 로딩될 때 한번만 수행된다. 주로 static 변수를 초기화할 때 사용한다.

</br>

### 참고

[Attaching Values to Java Enum](https://www.baeldung.com/java-enum-values)

[java enum에서 값에 해당하는 enum 가져오기](https://www.slipp.net/questions/563)
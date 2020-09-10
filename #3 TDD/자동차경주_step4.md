# 자동차 경주 Step4

## 테스트 전용 코드?

- 테스트 코드만을 위한 **메소드**를 생성하는 것은 지양해야 한다.
- 그러나 테스트 코드를 위한 **생성자나 정적 팩토리 메소드**는 괜찮다.
  - 테스트 코드에서 복잡한 객체 생성이 필요하면 생성자나 정적 팩토리 메소드를 추가해서 사용하자 

</br>

## 정적 팩토리 메소드

### 개념

- 객체 생성을 캡슐화하는 기법
- 객체를 생성하는 메소드를 만들고 `static`으로 선언한다.
- 참고로 GoF 디자인 패턴에서 나오는 팩토리 메소드와 다른 개념이다.

</br>

### 예시

```java
BigInteger answer = BigInteger.valueOf(42L); // BigInteger 42를 리턴한다
```

- 자주 사용되는 정적 팩토리 메소드 이름들

  > `valueOf`
  >
  >  `of`
  >
  >  `getInstance`
  >
  >  `newInstance` 
  >
  > `getType`
  >
  >  `newType`

</br>

### 장점

- 생성자에 비해 가독성이 좋다.

  > 아래 코드처럼 생성자의 숫자만으로는 캐릭터의 직업을 알아보기 어렵다.

  ```java
  Character warrior = new Character(5, 15, 20, 3);
  Character mage = new Character(15, 5, 10, 15);
  ```

  > 아래 코드처럼 정적 팩토리 메소드를 사용하면 좀 더 읽기 쉽다.

  ```java
  Character warrior = Character.newWarrior();
  Character mage = Character.newMage();
  ```

- 호출할 때마다 새로운 객체를 생성할 필요가 없다.

  - Immutable 객체를 캐시해서 사용한다면 매번 `new` 같은 비싼 연산을 사용할 필요가 없다.

  > Java의 `BigInteger` 내부 구현

  ```java
  public static final BigInteger ZERO = new BigInteger(new int[0], 0);
  
  private final static int MAX_CONSTANT = 16;
  private static BigInteger posConst[] = new BigInteger[MAX_CONSTANT+1];
  private static BigInteger negConst[] = new BigInteger[MAX_CONSTANT+1];
  
  static {
      /* posConst에 1 ~ 16까지의 BigInteger 값을 담는다. */
      /* negConst에 -1 ~ -16까지의 BigInteger 값을 담는다. */
  }
  
  public static BigInteger valueOf(long val) {
      // 미리 만들어둔 객체를 리턴한다
      if (val == 0)
          return ZERO;
      if (val > 0 && val <= MAX_CONSTANT)
          return posConst[(int) val];
      else if (val < 0 && val >= -MAX_CONSTANT)
          return negConst[(int) -val];
  
      // 새로운 객체를 만들어 리턴한다
      return new BigInteger(val);
  }
  ```

- 하위 자료형 객체를 반환할 수 있다.

  - 반환하는 객체는 같은 인터페이스를 구현하거나 같은 부모 클래스를 갖도록 하면 된다.

    ```java
    public static Pay createPayment(PayType payType) {
    	switch(payType) {
        case CREDIT:
          return new CreditCard(payType.amount());
        case DOLLAR:
          return new Dollar(payType.amount());
    	}
    }
    // ...
    class CreditCard extends Pay { /* ... */ }
    class Dollar extends Pay { /* ... */ }
    ```

  - 파일을 분리하기 애매한 클래스는 private class를 활용한다.

    > `java.util.Collections` 의 `EMPTY_MAP`

    ```java
    @SuppressWarnings("rawtypes")
    public static final Map EMPTY_MAP = new EmptyMap<>(); // java.util.Collections에 정의된 내부 클래스
    
    /**
     * Returns an empty map (immutable).  This map is serializable.
     */
    @SuppressWarnings("unchecked")
    public static final <K,V> Map<K,V> emptyMap() {
        return (Map<K,V>) EMPTY_MAP; // 캐스팅된 인스턴스를 반환한다.
    }
    
    private static class EmptyMap<K,V> extends AbstractMap<K,V> implements Serializable {
        /* 생략 */
    }
    ```

- 형인자 자료형 객체를 만들 때 편리하다. (Java 1.7 이전에만 해당된다.)

  - 형인자 자료형 객체

    ```java
    Map<String, List<String>> list = new HashMap<String, List<String>>(); // 자료형을 일일이 명시해야한다.
    ```

    > 아래 코드처럼 정적 팩토리 메소드를 사용하면 자료형을 매번 다 명시하지 않아도 된다.

    ```java
    Map<String, List<String>> list = HashMap.newInstance();
    ```

  - Java 1.7 이후로 형인자 자료형은 생략이 가능해졌기 때문에 의미가 없다.

    ```java
    Map<String, List<String>> list = new HashMap<>();
    ```

</br>

### 단점

- 정적 팩토리 메소드만 있는 클래스라면 생성자가 없으므로 하위 객체를 만들지 못한다?

- 정적 팩토리 메소드는 다른 정적 메소드와 잘 구분되지 않는다.

</br>

### 기타

- 정적 팩토리 메소드도 생성자의 역할을 하므로 다른 메소드 위에 위치하면 좋을 것 같다.

</br>

## 참고

[Step4 PR](https://github.com/next-step/java-racingcar/pull/1231)

[정적 팩토리 메서드(static factory method)](https://johngrib.github.io/wiki/static-factory-method-pattern/)


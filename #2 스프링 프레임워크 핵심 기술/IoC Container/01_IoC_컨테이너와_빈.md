# IoC 컨테이너 1부

## 스프링 IoC 컨테이너

### IoC 컨테이너

- 스프링 빈을 생성 및 관리한다.

  > 애플리케이션 컴포넌트의 중앙 저장소!

- 빈들 간의 **의존성 주입**을 관리해준다!

  > 의존성 주입 - 어떤 객체가 사용하는 의존 객체를 직접 만들어 사용하는게 아니라 외부에서 주입 받아 사용하는 방법

</br>

### `BeanFactory`

- IoC 컨테이너의 가장 최상위 인터페이스

</br>

### `ApplicationContext`

- `BeanFactory`의 구현체
- 사용자가 가장 많이 접하는 IoC 컨테이너 객체
- 빈 관련 다양한 기능을 지원한다. (추후에 살펴볼 예정)

</br>

## 스프링 빈

### 스프링 빈이란?

- IoC 컨테이너가 관리하는 객체

</br>

### 빈 등록 방법

- 스프링 초기에는 xml 파일을 사용하여 빈을 등록했다.

- 최근에는 **어노테이션** 기반으로 빈을 등록한다.

  - IoC 컨테이너가 초기화되는 시점에 컴포넌트 스캔을 통해 컴포넌트 관련 어노테이션이 붙은 클래스를 생성하여 IoC 컨테이너에 등록한다.
  - 자바 설정 파일(`@Configuration`)을 이용하여 직접 `@Bean` 어노테이션을 붙여 등록하기도 한다.

</br>

### 왜 빈으로 관리할까?

> 빈의 장점들을 살펴보자

- [**의존성 주입**](#의존성 주입)이 가능하다.

- 객체를 [**싱글톤**](#싱글톤)으로 생성한다.

- [**라이프사이클 인터페이스**](#라이프사이클 인터페이스)를 사용할 수 있다.

</br>

## 부연 설명

### 의존성 주입

- 테스트 코드를 작성할 때, 아직 구현되지 않은 컴포넌트의 기능을 `Mock`으로 대체하고 싶다면 의존 객체를 직접 생성하는 것보다 의존성을 외부에서 주입하는 구조가 테스트 구현에 쉽다. 

```java
@Repository
pubilc class BookRepository {
 
  public Book save(Book book) {
    return null;  // 아직 기능이 구현되지 않았다!
  }
}
```

> 테스트를 진행하고 싶은 대상

```java
@Service
public class BookService {
  
  private BookRepository bookRepository; // 의존 객체
  
  public Book save(Book book) {
    book.setCreated(new Date());
    book.setBookStatus(BookStatus.DRAFT);
    return bookRepository.save(book); // 아직 구현되지 않은 메소드를 호출한다.
  }
}
```

> 테스트 코드

```java
public class BookServiceTest {
  
  @Test
  public void save() {
    Book book = new Book();

    // given
    BookRepository bookRepository = new BookRepository();
    BookService bookService = new BookService(bookRepository); // 의존성 주입

    // when
    Book result = bookService.save(book); // 내부에서 호출하는 메소드가 아직 미구현 상태라 아래 테스트는 통과하지 않는다.

    // then
    assertThat(book.getCreated()).isNotNull();
    assertThat(book.getBookStatus()).isEqualTo(BookStatus.DRAFT);
    assertThat(result).isNotNull();
  }
}
```

- 테스트하고자 하는 것은 `BookService`의 `save` 메소드
- 그러나 해당 메소드 내부에서 호출하는 `BookRepository.save()`는 아직 구현되지 않았다.

> 의존성 주입을 이용한 테스트

```java
@RunWith(SpringRunner.class)
public class BookServiceTest {
  
  @Mock
  BookRepository bookRepository;
  
  @Test
  public void save() {
    Book book = new Book();

    // given
    when(bookRepository.save(book)).thenReuturn(book); // Mock 객체를 이용하여 마치 실제로 구현된 것처럼 반환 데이터를 지정한다.
    BookService bookService = new BookService(bookRepository); // 의존성 주입 방식을 채택했기 때문에 Mock을 활용할 수 있다.

    // when
    Book result = bookService.save(book);

    // then
    assertThat(book.getCreated()).isNotNull();
    assertThat(book.getBookStatus()).isEqualTo(BookStatus.DRAFT);
    assertThat(result).isNotNull();
  }
}
```

- `Mock` 을 이용한 가짜 객체를 만들어서 의존성을 주입해주면 위 테스트는 성공한다.

</br>

### 싱글톤

- 빈의 스코프는 **싱글톤**이다.

  - 애플리케이션 전체에서 단 한개의 인스턴스를 사용한다.
  - 애플리케이션 내에서 전역적으로 접근이 가능하다.
- 런타임시에 성능 최적화에 유리하다.

  - 비용이 비싼 객체 (DB Connection 등)는 싱글톤 스코프로 생성하는 것이 효율적이다.
- 멀티 스레드 환경이라면 여러 스레드에서 동시에 싱글톤 객체에 접근할 수 있기 때문에 설계에 주의해야 한다.
  - 상태 변경에 유의해야 하므로 무상태(stateless)로 설계해야 한다.

> 싱글톤에 대조되는 스코프는 **프로토타입** 스코프
>
> - 필요할 때마다 매번 다른 객체를 생성한다.

</br>

### 라이프사이클 인터페이스

- 빈의 라이프사이클 시점과 관련하여 부가적인 기능을 수행할 수 있도록 지원한다.

  > 빈의 `@PostContruct`는 해당 빈이 <u>생성된 바로 이후에</u> 실행된다.
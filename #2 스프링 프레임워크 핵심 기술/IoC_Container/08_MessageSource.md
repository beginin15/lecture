# IoC 컨테이너 1부

##  `MessageSource`

- 국제화 (i18n) 기능을 제공하는 인터페이스
  - 즉, 메세지 다국화를 지원한다.

- `ApplicationContext`가 상속받고 있는 인터페이스 중 하나

</br>

### 스프링 부트 지원

-  `ResourceBundleMessageSource`가 빈으로 등록되어 있다.

  - 해당 빈이 기본적으로 `messages.properties` 번들을 읽는다.

- 별다른 설정 필요없이 `message.properties` 사용할 수 있다.

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/f0ec1f54a00c5567fc7894164dab16e9226d58f0)

</br>

### 리로딩 지원

- `ReloadableResourceBundleMessageSource` 구현체는 애플리케이션 구동 중에 메세지를 리로드하는 기능을 제공한다.

  - 스프링 부트의 default 구현체는 `ResourceBundleMessageSource`이므로 직접 리로딩 구현체를 빈으로 등록해준다.

- 리로딩은 빌드된 디렉토리 안에 있는 메세지 번들을 기준으로 수행하므로, 메세지를 수정하고 반드시 `Build Project`를 수행해줘야 한다.

  ![build_project](https://user-images.githubusercontent.com/33659848/88701752-6a9f6100-d145-11ea-9b7e-b9861fde9b88.png)

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/22fd78d3fc08fa17af8f1ddc80fc78db8301fc5d)

</br>

## 기타

### i18n

- 소프트웨어의 국제화

### l10n

- 지역화
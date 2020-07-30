# IoC 컨테이너 1부

## `Environment`

- 현재 애플리케이션이 실행중인 환경을 나타내는 인터페이스
- `EnvironmentCapable`를 통해 접근할 수 있다. [📎](###`EnvironmentCapable`)
- 2가지 기능을 제공한다.
  - [프로파일](##프로파일)
  - [프로퍼티](##프로퍼티)

</br>

## 프로파일

- 빈의 논리적 그룹

- 프로파일을 이용하여 환경을 구성 및 구분한다.

  > 배포 환경, 개발 환경 등

- `default` 프로파일은 항상 적용되는 프로파일을 의미한다.

- 프로파일 표현식을 제공한다.

  ```java
  @Profile("!prod") // 프로필이 prod가 아닌 경우에 적용
  ```

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/4eec35422dca0e1c17b32c961ae923cf632cbb82)

</br>

### 설정 파일 프로파일

- 각 환경에 따라 사용하는 빈이 달라지는 경우, 자바 설정 파일에 프로파일을 적용한다.

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/81e88c22fbbf11c8e4b9760922eea231dec93e90)

</br>

### 빈 프로파일

- 특정 환경에서만 사용하는 빈의 경우, 빈에 프로파일을 적용한다.
- *설정 파일 프로파일*과 비교
  
  - 설정 파일에 프로파일을 적용하는 것은 환경에 따라 <u>빈 그룹을 교체할 때</u> 사용한다. (대체)
  - 빈에 프로파일을 적용하는 것은 <u>특정 환경에서만 특정 빈을 사용할때</u> 사용한다. (선택)
  
  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/00a20cd8519c44b403a566e986528a975ca4e1ce)

</br>

## IDE에서 실행 옵션으로 프로파일 설정하기

### 1. Active profile

- IDE 제품에 따라서 존재하지 않을 수도 있다.

![ide_active_profile](https://user-images.githubusercontent.com/33659848/88297072-73092d80-cd3a-11ea-8ccd-0233ced27bb8.png)

### 2. VM options

![ide_vm_options](https://user-images.githubusercontent.com/33659848/88297058-6f75a680-cd3a-11ea-817b-f86b312ce74a.png)

</br>

## 기타

### `EnvironmentCapable`

- `ApplicationContext`가 상속받고 있는 인터페이스 중 하나
- `Environment`에 대한 정보를 제공한다.
- 즉, `ApplicationContext`를 이용하여 `Environment`를 가져올 수 있다.
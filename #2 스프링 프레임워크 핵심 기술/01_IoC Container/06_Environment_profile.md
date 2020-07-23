# IoC 컨테이너 1부

## `EnvironmentCapable`

- 현재 실행 환경에 대한 정보 (``Environment``)를 가지고 있다.
- `ApplicationContext`는 `EnvironmentCapable`을 상속받았기 때문에 ``ApplicationContext`` 을 이용하여 `Environment` 를 가져올 수 있다.



### `Environment` 제공 기능

- 프로파일
- 프로퍼티



## 프로파일

- 배포 환경, 개발 환경 등을 구분할 때 사용한다. 즉, 환경을 의미한다.
- `default` 프로파일은 프로파일에 관계없이 항상 적용된다.



### 설정 파일 프로파일

- 각 환경에 따라 사용하는 빈이 달라지는 경우, 자바 설정 파일에 프로파일을 적용한다.



### 빈 프로파일

- 특정 환경에서만 사용하는 빈의 경우, 빈에 프로파일을 적용한다.



## IDE에서 프로파일 설정하기

1. Active profile

   - IDE 버전에 따라서 존재하지 않을 수도 있다.

   ![ide_active_profile](https://user-images.githubusercontent.com/33659848/88297072-73092d80-cd3a-11ea-8ccd-0233ced27bb8.png)

2. VM options

   ![ide_vm_options](https://user-images.githubusercontent.com/33659848/88297058-6f75a680-cd3a-11ea-817b-f86b312ce74a.png)
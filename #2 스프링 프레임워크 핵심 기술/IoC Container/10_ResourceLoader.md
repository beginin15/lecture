# IoC 컨테이너 1부

## `ResourceLoader`

- 리소스를 읽어오는 기능을 제공하는 인터페이스

- `ApplicationContext`가 상속받고 있는 인터페이스 중 하나

  > [구현 코드](https://github.com/beginin15/spring-framework-core/commit/68f4100ef63422e09f37752c96ebcec12059510a)

</br>

### 리소스 

- 앱 애플리케이션에 필요한 다양한 자원들 (클래스, 서블릿, 파일, 프로퍼티 등)
- 프로젝트가 빌드되면 `resources` 폴더의 빌드 결과물이 `target` (`build`) 폴더 밑으로 들어간다.

</br>

## 기타

### Classpath

- 자바 가상머신이 프로그램을 실행할 때 필요한 클래스 파일과 패키지의 경로
- `target 혹은 build/class`가 classpath의 루트

</br>

### 리소스를 인식하지 못하는 경우

- 파일을 추가한 뒤, `ResourceLoader`를 이용하여 classpath 기준으로 리소스를 가져올 때 인식하지 못하는 문제 발생

  => Gradle - Refresh 한번 해주면 정상적으로 인식되었다.
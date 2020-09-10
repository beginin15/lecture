# Null Safety

> 스프링 프레임워크 5에 추가된 Null 관련 어노테이션에 대해 살펴보자.

</br>

## 목적

- 어노테이션과 툴의 지원으로 NullPointerException을 컴파일 타임에 방지하기 위해 사용한다.

- Reactor와 Spring Data에서 Null-Safety 관련 어노테이션을 사용하고 있다.

</br>

## 종류

### `@NonNull`

### `@Nullable`

- 컴파일 시점에 경고 알림을 띄울 수 있도록 IDE에서 약간의 설정이 필요하다.

  ![ide_setting_notnull](https://user-images.githubusercontent.com/33659848/92752885-4c738480-f3c4-11ea-85f6-a7215fe54247.png)

  - `+` 버튼을 눌러서 `java.lang` 패키지의 `Nullable`과 `NonNull`를 추가한다.

  - `V` 버튼을 눌러서 활성화시켜준다.

    </br>

  > 적용된 화면

  ![notnull_warning](https://user-images.githubusercontent.com/33659848/92752929-572e1980-f3c4-11ea-994d-b4731d32f692.png)

  ![notnull_warnings_2](https://user-images.githubusercontent.com/33659848/92752940-585f4680-f3c4-11ea-9514-f9abf4537514.png)

</br>

### `@NonNullApi`

- 패키지 레벨에서 non-null를 선언할 수 있다.

- 해당 어노테이션으로 명시한 패키지 이하의 모든 API의 파라미터와 반환값에 기본적으로 non-null 속성을 적용한다.

- 적용 방법

  - 패키지 설정 파일을 생성한다.

    ![create_package_info](https://user-images.githubusercontent.com/33659848/92753776-2bf7fa00-f3c5-11ea-8679-d4151044efc5.png)

  - 패키지 설정 파일 최상단에 `@NonNullApi`를 명시한다.

    ```java
    @NonNullApi
    package com.course.spring_framework.core;
    
    import org.springframework.lang.NonNullApi;
    
    ```

    

### `@NonNullFields`

- 패키지 레벨에서 non-null를 선언할 수 있다.
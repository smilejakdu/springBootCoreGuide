# 스프링 부트에서의 유효성 검사

스프링 부트의 유효성 검사 기능은 spring-boot-starter-web 에 포함돼 있었습니다.

하지만 스프링 부트 2.3버전 이후로 별도의 라이브러리로 제공하고 있습니다.



스프링 부트에서 유효성 검사를 위해 주로 사용되는 라이브러리는 `Hibernate Validator`입니다. 이는 Bean Validation API의 구현체 중 하나로, 객체의 필드에 대한 유효성 검사를 쉽게 할 수 있게 해줍니다.

스프링 부트 프로젝트에 Hibernate Validator를 추가하기 위해서는 `pom.xml` (Maven을 사용하는 경우) 또는 `build.gradle` (Gradle을 사용하는 경우) 파일에 의존성을 추가해야 합니다.

Maven을 사용하는 경우, `pom.xml` 파일에 다음 의존성을 추가합니다:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Gradle을 사용하는 경우, `build.gradle` 파일에 다음 의존성을 추가합니다:

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-validation'
}
```

스프링 부트 2.3 버전 이후부터는 `spring-boot-starter-web`에 `spring-boot-starter-validation`이 포함되지 않기 때문에, 웹 애플리케이션을 개발하는 경우에도 명시적으로 위의 의존성을 추가해야 합니다.

이 의존성을 추가한 후에는, `@Valid` 어노테이션과 함께 Java Bean Validation API의 어노테이션들(`@NotNull`, `@Size`, `@Min`, `@Max` 등)을 사용하여 객체의 필드에 대한 유효성 검사 규칙을 정의할 수 있습니다. 예를 들어, 다음과 같이 사용할 수 있습니다:

```java
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class User {

    @NotNull
    private Long id;

    @Size(min = 2, max = 30)
    private String name;

    // getters and setters
}
```

그리고 컨트롤러에서 `@Valid` 또는 `@Validated` 어노테이션을 사용하여 유효성 검사를 적용할 수 있습니다:

```java
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;

@RestController
public class UserController {

    @PostMapping("/users")
    public User createUser(@Validated @RequestBody User user) {
        // ...
    }
}
```

이렇게 하면 스프링 부트 애플리케이션에서 유효성 검사를 적용할 수 있습니다.

```java
package com.springboot.valid_exception.data.dto;

import lombok.*;

import javax.validation.constraints.*;

// 예제 10.2
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString
@Builder
public class ValidRequestDto {

    // @Null : Null 값만 허용
    // @NotNull : Null을 허용하지 않음. "", " "는 허용
    // @NotEmpty : null, ""을 허용하지 않음. " "는 허용
    // @NotBlank : null, "", " " 모두 허용하지 않음
    @NotBlank
    private String name;

    // @Email : 이메일 형식을 검사. ""는 허용
    @Email
    private String email;

    // @Pattern : 정규식을 검사
    @Pattern(regexp = "01(?:0|1|[6-9])[.-]?(\\d{3}|\\d{4})[.-]?(\\d{4})$")
    //@Telephone
    private String phoneNumber;

    // DecimalMin(value = "$numberString") : $numberString 이상의 값을 허용
    // DecimalMax(value = "$numberString") : $numberString 이하의 값을 허용
    // @Min(value = $number) : $number 이상의 값을 허용
    // @Max(value = $number) : $number 이하의 값을 허용
    @Min(value = 20)
    @Max(value = 40)
    private int age;

    // @Size(min = $minNumber, max = $maxNumber) : 문자열의 길이를 제한
    @Size(min = 0, max = 40)
    private String description;

    // @Positive : 양수를 허용
    // @PositiveOrZero : 0을 포함한 양수를 허용
    // @Negative : 음수를 허용
    // @NegativeOrZero : 0을 포함한 음수를 허용
    @Positive
    private int count;

    // @AssertTrue : true 체크, null 값은 체크하지 않음
    // @AssertFalse : false 체크, null 값은 체크하지 않음
    @AssertTrue
    private boolean booleanCheck;

    // @Future : 현재보다 미래의 날짜를 허용
    // @FutureOrPresent : 현재를 포함한 미래의 날짜를 허용
    // @Past : 현재보다 과거의 날짜를 허용
    // @PastOrPresent : 현재를 포함한 과거의 날짜를 허용
    // Date birthDay;

    // @Digits : 수치의 범위를 설정합니다.
    // private Integer digits;

}

```

각 필드에 어노테이션이 선언된 것을 볼 수 있다.

각 어노테이션은 유효성 검사를 위한 조건을 설정하는데 사용된다.

검색하면 자료는 많이 나온다.



@Null: null 값만 허용합니다.

@NotNull: null 을 허용하지 않습니다.

@NotEmpty: null, "" 을 허용하지 않습니다. " " 는 허용합니다.

@NotBlank: null, "", " "을 허용하지 않습니다.

나머지는 검색하면 많이 나온다.



## Validated 활용

Validated 은 Valid 어노테이션의 기능을 포함하고 있기 때문에 Validated 로 변경할 수 있습니다.

또한 Validated 는 유효성 검사를 그불으로 묶어 대상을 특정할 수 있는 기능이 있습니다.

검증 그룹은 별다른 내용잉 없는 마커 인터페이스를 생성해서 사용합니다.

```java
package com.springboot.valid_exception.data.dto;

import com.springboot.valid_exception.config.annotation.Telephone;
import com.springboot.valid_exception.data.group.ValidationGroup1;
import com.springboot.valid_exception.data.group.ValidationGroup2;
import lombok.*;

import javax.validation.constraints.*;

// 예제 10.6, 예제 10.10
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString
@Builder
public class ValidatedRequestDto {

    @NotBlank
    private String name;

    @Email
    private String email;

    @Pattern(regexp = "01(?:0|1|[6-9])[.-]?(\\d{3}|\\d{4})[.-]?(\\d{4})$")
    //@Telephone(groups = ValidationGroup2.class)
    //@Telephone
    private String phoneNumber;

    @Min(value = 20, groups = ValidationGroup1.class)
    @Max(value = 40, groups = ValidationGroup1.class)
    //@Min(value = 20)
    //@Max(value = 40)
    private int age;

    @Size(min = 0, max = 40)
    private String description;

    @Positive(groups = ValidationGroup2.class)
    //@Positive
    private int count;

    @AssertTrue
    private boolean booleanCheck;

}

```

위와 같이 requestDto 를 작성한다.&#x20;

이제 requestDto 를 적용할 Controller 로 가서 수정을 한다.

```java
package com.springboot.valid_exception.controller;

import com.springboot.valid_exception.data.dto.ValidRequestDto;

import javax.validation.Valid;

import com.springboot.valid_exception.data.dto.ValidatedRequestDto;
import com.springboot.valid_exception.data.group.ValidationGroup1;
import com.springboot.valid_exception.data.group.ValidationGroup2;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

// 예제 10.3, 예제 10.7
@RestController
@RequestMapping("/validation")
public class ValidationController {

    private final Logger LOGGER = LoggerFactory.getLogger(ValidationController.class);

    @PostMapping("/valid")
    public ResponseEntity<String> checkValidationByValid(
            @Valid @RequestBody ValidRequestDto validRequestDto) {
        LOGGER.info(validRequestDto.toString());
        return ResponseEntity.status(HttpStatus.OK).body(validRequestDto.toString());
    }

    @PostMapping("/validated")
    public ResponseEntity<String> checkValidation(
            @Validated @RequestBody ValidatedRequestDto validatedRequestDto) {
        LOGGER.info(validatedRequestDto.toString());
        return ResponseEntity.status(HttpStatus.OK).body(validatedRequestDto.toString());
    }

    @PostMapping("/validated/group1")
    public ResponseEntity<String> checkValidation1(
            @Validated(ValidationGroup1.class) @RequestBody ValidatedRequestDto validatedRequestDto) {
        LOGGER.info(validatedRequestDto.toString());
        return ResponseEntity.status(HttpStatus.OK).body(validatedRequestDto.toString());
    }

    @PostMapping("/validated/group2")
    public ResponseEntity<String> checkValidation2(
            @Validated(ValidationGroup2.class) @RequestBody ValidatedRequestDto validatedRequestDto) {
        LOGGER.info(validatedRequestDto.toString());
        return ResponseEntity.status(HttpStatus.OK).body(validatedRequestDto.toString());
    }

    @PostMapping("/validated/all-group")
    public ResponseEntity<String> checkValidation3(
            @Validated({ValidationGroup1.class,
                    ValidationGroup2.class}) @RequestBody ValidatedRequestDto validatedRequestDto) {
        LOGGER.info(validatedRequestDto.toString());
        return ResponseEntity.status(HttpStatus.OK).body(validatedRequestDto.toString());
    }
}
```



* @Validated 어노테이션에 특정 그룹을 설정하지 않은 경우에는 groups 가 설정되지 않은 필드에 대해 유효성 검사를 수행
* @Validated 어노테이션에 특정 그룹을 설정하는 경우에는 지정된 그룹으로 설정된 필드에 대해서만 유효성 검사를 수행

그룹을 지정해서 유효성 검사를 실시하는 경우에는 어떤 상황에 사용할지를 적절하게 설계해야 의도대로 유효성 검사를 실시할 수 있습니다.

만약 이를 제대로 설계하지 않으면 비효율적이거나 생상적이지 못한패턴을 의미하는 안티 패턴이 발생하게 된다.



## 커스텀 Validation 추가

ConstraintValidator와 커스텀 어노테이션을 조합해서 별도의 유효성 검사 어노테이션을 생성 할 수 있습니다.

TelephoneValidator 클래스를 생성합니다.

```java
package com.springboot.valid_exception.config.annotation;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

// 예제 10.8
public class TelephoneValidator implements ConstraintValidator<Telephone, String> {

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        if(value==null){
            return false;
        }
        return value.matches("01(?:0|1|[6-9])[.-]?(\\d{3}|\\d{4})[.-]?(\\d{4})$");
    }
}

```

TelephoneValidator 클래스를 ConstraintValidator 인터페이스의 구현체로 정의합니다.

인터페이스를 선언할 때는 어떤 어노테이션 인터페이스인지 타입을 지정해야한다.

ConstraintValidator 인터페이스는 invalid() 메서드를 정의하고 있다.

이 메서드를 구현하려면 직접 유효성 검사 로직을 작성해야 한다.

로직에서 false 가 리턴되면 MethodArgumentNotValidException 예외가 발생한다.

```java
package com.springboot.valid_exception.config.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import javax.validation.Constraint;

// 예제 10.9
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = TelephoneValidator.class)
public @interface Telephone {
    String message() default "전화번호 형식이 일치하지 않습니다.";
    Class[] groups() default {};
    Class[] payload() default {};
}
```



* @Target 어노테이션은 이 어노테이션을 어디서 선언할 수 있는지 정의하는 데 사용된다.
* @Retention 어노테이션은 이 어노테이션이 실제로 적용되고 유지되는 범위를 의미한다.
  * RetentionPolicy.RUNTIME: 컴파일 이후에도 JVM 에 의해 계속 참조합니다. 리플렉션이나 로깅에 많이 사용되는 정책이다.
  * RetentionPolicy.CLASS: 컴파일러가 클래스를 참조할 때까지 유지한다.
  * RetentionPolicy.SOURCE: 컴파일 전까지만 유지됩니다. 컴파일 이후에는 사라집니다.

인터페이스 내부에는 message(), groups(), payload() 요소를 정의해야 합니다.

각 항목은 다음과 같은 의미를 가지고 있습니다.

* meesage() : 유효성 검사가 실패할 경우 반환되는 메시지를 의미한다.
* groups() : 유효성 검사를 사용하는 그룹으로 설정합니다.
* payload() : 사용자가 추가 정보를 위해 전달하는 값입니다.

이제 직접 생성한 새로운 유효성 검사 어노테이션을 적용해 보겠습니다.

ValidatedRequestDto 클래스에서 phoneNumber 변수의 어노테이션을 변경합니다.

```java
    @Telephone
    private String phoneNumber;
```

@Pattern 어노테이션을 @Telephone 어노테이션으로 변경합니다.

메서드를 호출하면 유효성 검사에서 형식 오류를 감지하는것을 볼 수 있다.

별도의 그룹을 지정하지 않았기 때문에 checkValidation() 메서드를 호출했을 때 오류가 발생합니다.



아래는 전체 코드 입니다.&#x20;

```java
package com.springboot.valid_exception.data.dto;

import com.springboot.valid_exception.config.annotation.Telephone;
import com.springboot.valid_exception.data.group.ValidationGroup1;
import com.springboot.valid_exception.data.group.ValidationGroup2;
import lombok.*;

import javax.validation.constraints.*;

// 예제 10.6, 예제 10.10
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString
@Builder
public class ValidatedRequestDto {

    @NotBlank
    private String name;

    @Email
    private String email;

    @Pattern(regexp = "01(?:0|1|[6-9])[.-]?(\\d{3}|\\d{4})[.-]?(\\d{4})$")
    //@Telephone(groups = ValidationGroup2.class)
    //@Telephone
    private String phoneNumber;

    @Min(value = 20, groups = ValidationGroup1.class)
    @Max(value = 40, groups = ValidationGroup1.class)
    //@Min(value = 20)
    //@Max(value = 40)
    private int age;

    @Size(min = 0, max = 40)
    private String description;

    @Positive(groups = ValidationGroup2.class)
    //@Positive
    private int count;

    @AssertTrue
    private boolean booleanCheck;

}

```

# foundByIdTest

## Autowired

* **목적**: `@Autowired`는 Spring의 의존성 주입(Dependency Injection) 기능을 사용하여, 선언된 필드에 해당하는 빈(Bean)을 Spring 컨테이너에서 자동으로 주입합니다.
* **사용 예시**: 클래스가 다른 빈에 의존하는 경우, `@Autowired`를 사용하여 해당 의존성을 주입받습니다. 이는 생성자, 세터 메서드, 또는 필드에 직접 적용할 수 있습니다.
* **동작 방식**: Spring은 `@Autowired`가 붙은 필드, 생성자, 또는 메서드를 찾고, 해당 타입과 일치하는 빈을 컨테이너에서 찾아 자동으로 연결합니다.

## MockBean

* **목적**: `@MockBean`은 Spring Boot 테스트에서 사용되며, 테스트 실행 시 특정 빈을 모의 객체(Mock Object)로 교체합니다. 이는 주로 통합 테스트에서 실제 컴포넌트 대신 가짜 객체를 사용하고자 할 때 유용합니다.
* **사용 예시**: 데이터베이스 접근, 외부 서비스 호출 등의 실제 동작을 모의 객체로 대체하여, 테스트가 이러한 외부 의존성으로부터 독립적으로 실행될 수 있도록 합니다.
* **동작 방식**: 테스트 실행 시, `@MockBean`으로 지정된 타입의 빈은 모의 객체로 교체되며, 이 모의 객체는 Mockito 프레임워크를 통해 관리됩니다. 이를 통해 실제 빈의 동작을 모의하거나 검증할 수 있습니다.



## 실패 테스트&#x20;

만약에&#x20;

```java
    @Transactional
    public FindTeacherByIdResponseDto findOneTeacherById(
            Long teacherId
    ) {
        Teacher teacher = queryFactory
                .selectFrom(QTeacher.teacher)
                // Teacher의 ID가 메서드 파라미터로 전달된 teacherId와 같은 경우를 조건으로 합니다.
                .where(QTeacher.teacher.id.eq(teacherId))
                .fetchOne();

        if (teacher == null) {
            throw new NotFoundException("해당 선생님을 찾을 수 없습니다.");
        }

        List<CommentDto> commentDtos = queryFactory.selectFrom(qComments)
                .where(qComments.teacher.eq(teacher))
                .fetch()
                .stream()
                .map(comment -> CommentDto.builder()
                        .id(comment.getId())
                        .content(comment.getContent())
                        .likes(comment.getLikes())
                        .userId(comment.getUser().getId())
                        .build())
                .collect(Collectors.toList());

        return FindTeacherByIdResponseDto.builder()
                .teacher(teacher)
                .commentDtoList(commentDtos)
                .build();
    }

```

위의 코드를 통과하기 위한 테스트코드를 연습으로 작성한다.

```java
package com.example.showmeyourability.service.teacherApplication;

import com.example.showmeyourability.teacher.application.FindTeacherApplication;
import com.example.showmeyourability.teacher.domain.Teacher;
import com.example.showmeyourability.teacher.infrastructure.repository.TeacherRepository;
import com.example.showmeyourability.users.domain.GenderType;
import com.example.showmeyourability.users.domain.User;
import com.example.showmeyourability.users.infrastructure.repository.UserRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.security.crypto.bcrypt.BCrypt;
import org.webjars.NotFoundException;

import static org.junit.jupiter.api.Assertions.*;


@SpringBootTest
public class FindTeacherApplicationTest {

    @Autowired
    TeacherRepository teacherRepository;
    @Autowired
    UserRepository userRepository;

    @BeforeEach
    void setUp() {
        String hashedPassword = BCrypt.hashpw("1234", BCrypt.gensalt());
        User user = User.builder()
                .id(1L)
                .email("robertxsd@gmail.com")
                .genderType(GenderType.MALE)
                .age(20)
                .img("img")
                .password(hashedPassword)
                .build();
        userRepository.save(user);

        Teacher teacher = Teacher.builder()
                .id(1L)
                .user(user)
                .career("경력")
                .skill("스킬")
                .comments(null)
                .orders(null)
                .build();
        teacherRepository.save(teacher);
    }

    @Test
    void notFoundTeacherByIdTest() {
    }
}

```

위와 같이 테스트 할 수 있게 준비한다.

TDD 방식이라면 테스트코드를 먼저 작성하게 된다.

작성한 테스트 코드 기반으로 기능 개발을 하게 된다.&#x20;



분명 TDD 방식으로 작업한다면 위의 방식을 맞을것이다.

하지만 회사의 사정에 따라서 작업하면 될것같다.

회사 상황에 따라 다르지만 List 를 보여주게 될때 데이터가 없다면 빈 리스트를 보여주면 될것 같다&#x20;

하지만 위에선 Id 값으로 특정 teacher 를 찾으려고 하는데 없는 not found 테스트를 하려고 하니까 `throw new` 에러를 반환 하도록 한다.



우선 테스트 하기위해서 기능개발쪽에 null 값 반환하도록 한다.



```java
    @Transactional
    public FindTeacherByIdResponseDto findOneTeacherById(
            Long teacherId
    ) {
        Teacher teacher = null;
        if (teacher == null) {
            throw new NotFoundException("해당 선생님을 찾을 수 없습니다.");
        }
        return new FindTeacherByIdResponseDto();
    }

```

이후 테스트 코드를 작성한다.

```java
package com.example.showmeyourability.service.teacherApplication;

import com.example.showmeyourability.teacher.application.FindTeacherApplication;
import com.example.showmeyourability.teacher.domain.Teacher;
import com.example.showmeyourability.teacher.infrastructure.repository.TeacherRepository;
import com.example.showmeyourability.users.domain.GenderType;
import com.example.showmeyourability.users.domain.User;
import com.example.showmeyourability.users.infrastructure.repository.UserRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.security.crypto.bcrypt.BCrypt;
import org.webjars.NotFoundException;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.when;


@SpringBootTest
public class FindTeacherApplicationTest {

    @Autowired
    TeacherRepository teacherRepository;
    @Autowired
    UserRepository userRepository;
    @MockBean
    FindTeacherApplication findTeacherApplication;

    @BeforeEach
    void setUp() {
        String hashedPassword = BCrypt.hashpw("1234", BCrypt.gensalt());
        // User user 를 3개 만들어줘
        int[] numbers = {1, 2, 3, 4};
        for (int number : numbers) {
            Long numberL = (long) number;
            User user = User.builder()
                    .id(numberL)
                    .email("robertvsd" + number + "@gmail.com") // 숫자를 문자열로 변환하여 결합
                    .genderType(GenderType.MALE)
                    .age(20)
                    .img("img")
                    .password(hashedPassword)
                    .build();
            userRepository.save(user);
        }

        long [] teacherNumList = {1, 2};
        for (long teacherNumber : teacherNumList) {
            User user = userRepository.findById(teacherNumber).get();
            Teacher teacher = Teacher.builder()
                    .id(teacherNumber)
                    .user(user)
                    .career("경력")
                    .skill("스킬")
                    .comments(null)
                    .orders(null)
                    .build();
            teacherRepository.save(teacher);
        }
    }

    @Test
    void notFoundTeacherByIdTest() {
        // given
        Long teacherId = 3L;

        when(findTeacherApplication.findOneTeacherById(teacherId))
                .thenThrow(new NotFoundException("해당 선생님을 찾을 수 없습니다."));

        // then
        assertThrows(NotFoundException.class, () -> {
            findTeacherApplication.findOneTeacherById(teacherId);
        });        // NotFoundException: 해당 선생님을 찾을 수 없습니다.
    }
}

```

위와 같이 테스트 코드를 작성했으면 이제 성공 테스트에 대한 테스트 코드를 작성한다.



## 성공 테스트

```java
    @Test
    void foundTeacherByIdTest() {
        Long teacherId = 1L;

        // 필요한 User 및 Teacher 객체 생성
        User mockUser = new User();
        mockUser.setId(teacherId);
        mockUser.setEmail("robertvsd1@gmail.com");
        // mockUser의 다른 필드도 필요에 따라 설정

        Teacher mockTeacher = new Teacher();
        mockTeacher.setId(teacherId);
        mockTeacher.setUser(mockUser);
        // mockTeacher의 다른 필드도 필요에 따라 설정

        // FindTeacherByIdResponseDto 객체 생성 및 Teacher 객체 설정
        FindTeacherByIdResponseDto mockResponse = new FindTeacherByIdResponseDto();
        mockResponse.setTeacher(mockTeacher);

        // 모의 동작 설정
        when(findTeacherApplication.findOneTeacherById(teacherId)).thenReturn(mockResponse);

        // 테스트 실행
        FindTeacherByIdResponseDto actualResponse = findTeacherApplication.findOneTeacherById(teacherId);

        // 검증
        assertEquals(teacherId, actualResponse.getTeacher().getId());
        assertEquals("robertvsd1@gmail.com", actualResponse.getTeacher().getUser().getEmail());
    }

```


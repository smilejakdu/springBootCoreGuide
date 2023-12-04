# 10 유효성 검사와 예외 처리

유효성 검사의 예로는 여러 계층에서 들어오는 데이터에 대해 의도한 형식대로 들어오는지 체크하는 과정이 있습니다.

이 같은 유효성 검사는 프로그래밍에서 매우 중요한 부분이며, 자바에서 가장 신경 써야 하는 것 중 하나로 NullPointException 예외가 있습니다.



## 일반적인 애플리케이션 유효성 검사의 문제점

계층별로 진행하는 유효성 검사는 검증 로직이 각 클래스별로 분산돼 있어 관리하기가 어렵다.

그리고 검증 로직에 의외로 중복이 많아 여러 곳에 유사한 기능의 코드가 존재할 수 있다.

마지막으로 검증해야할 값이 많다면 검증하는 코드가 길어진다.



이러한 문제로 코드가 복잡해지고 가독성이 떨어진다.

이같은 문제를 해결하기 위해 자바 진영에서는 2009년부터 Bean Validation 이라는 데이터 유효성 검사 프레임워크를 제공한다.

Bean Validation 은 어노테이션을 통해 다양한 데이터를 검증하는 기능을 제공한다.

Bean Validation 을 사용한다는 거은 유효성 검사를 위한 로직을 DTO 같은 도메인 모델과 묶어서 각 계층에서 사용하면서 검증 자체를 도메인 모델에 얹는 방식으로 수행한다는 의미이다.

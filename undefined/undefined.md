# 레이어드 아키텍처



유사 관심사를 기준으로 레이어로 묶어 수평적으로 구성한 구조를 의미한다.

어떻게 설계하느냐에 따라 용어와 계층의 수가 달라진다.



일반적으로 레이어드 아키텍처라 하면 3계층 또는 4계층 구성을 많이 한다.

<figure><img src="../.gitbook/assets/스크린샷 2023-11-25 오후 10.19.18.png" alt=""><figcaption></figcaption></figure>

* 프레젠테이션 계층: \
  애플리케이션의 최상단 계층으로, 클라이언트의 요청을 해석하고 응답하는 역할이다.\
  UI 나 API 를 제공한다.\
  프레젠테이션 계층은 별도의 비즈니스 로직을 포함하고 있지 않으므로 비지니스 계층으로 요청을 위임하고 받은 결과를 응답하는 역할만 수행한다.

프레젠테이션 계층에서는 왜 로직을 포함하지 않을까?

로직을 포함해도 된다. 하지만 책임분리, 유지보수성, 재사용성, 테스트 용이성 때문이다.



* 비지니스 계층:\
  애플리케이션이 제공하는 기능을 정의하고 세부 작업을 수행하는 도메인 객체를 통해 업무를 위임하는 역할을 수행한다. \
  DDD 기반의 아키텍처에서는 비즈니스 로직에 도메인이 포함되기도 하고, 별도로 도메인 계층을 두기도 한다.

DDD 기반의 아키텍처는 해야할 얘기가 많아서 따로 파트로 나눠서 얘기해보도록 한다.

* 데이터 접근 계층\
  데이터베이스에 접근하는 일련의 작업을 수행한다.\
  상황에 따라 영속 계층이라고도 한다.

영속 계층이라는게 뭘까 ?

데이터 엑세스 계층과 영속 계층은 중요한 역할을 하는 계층이며, 종종 서로 교환 가능하게 사용되는 용어이다.

그러나 엄밀히 말하면 두 계층 사이에는 약간의 차이가 존재한다.

데이터 엑세스 계층의 주요 목적은 데이터 소스와 application의 나머지 부분 사이의 인터페이스를 제공하여, 데이터소스에 대한 접근을 추상화 하는 것이다.

영속 계층은 데이터를 영구적으로 저장하는 역할을 한다.

영속 계층의 핵심 목표는 데이터의 영속성을 관리하는 것으로, application 을 종료하더라도 데이터가 유지되도록 보장한다.

**데이터 액세스 계층**은 데이터베이스와 같은 데이터 소스에 접근하여 데이터를 조작하는 기능을 제공하는 반면, **영속 계층**은 데이터의 영구 저장과 관리에 초점을 맞춘다.



<figure><img src="../.gitbook/assets/스크린샷 2023-11-25 오후 10.54.27.png" alt=""><figcaption></figcaption></figure>


## Transaction
트랜잭션은 여러과정을 하나의 행위로 묶을 때 사용된다. 트랜잭션은 트랜잭션 범위 내에 있는 처리 과정 중 한가지라도 실패할 경우 전체 과정을 취소시킴으로써 데이터의 무결성을 보장한다. 즉 트랜잭션은 모두 반영하거나 모두 반영하지 않는 방식을 취한다.
트랜잭션을 설명할 때 보통 네가지 특성인 ACID를 이용한다.
#### 원자성(Atomicity)

#### 일관성(Consistency)

#### 고립성(Isolation)

#### 지속성(Durability)

스프링은 코드 기반의 트랜잭션 처리 뿐만 아니라 선언적 트랜잭션을 지원하고 있다. 스프링은 데이터베이스 연동 기술에 상관없이 동일한 방식으로 트랜잭션을 처리할 수 있도록 하고 있다. 예를들어 jpa를 사용하거나, jdbc를 사용하더라도 스프링은 동일한 코드를 이용해서 트랜잭션처리가 가능하다.

![](http://i.imgur.com/rkflDWP.jpg)

스프링은 PlatformTransactionManager 인터페이스를 이용해서 트랜잭션 처리를 추상화하였고, 데이터베이스 연동기술에 따라 알맞은 PlatformTransactionManager 구현 클래스를 제공하고 있다. 위그림은 주요 구현 클래스간의 관계를 보여준다.

스프링을 이용해서 트랜잭션을 처리하려면 사용하는 DB연동 구현 기술에 알맞은 트랜잭션 관리자를 등록해주어야한다.

#### 예를들어 JPA의 트랜잭션 처리의경우아래와같다.
```xml
<bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
  ...
</bean>

<bean id="transactionManager" class="org.springframework.orm.jpa.jpaTransactionManager">
  <property name="entityManagerFactory" ref="entityManagerFactory">
</bean>    
```

jpaTransactionManager는 entityManagerFactory 프로퍼티를 통해 전달받은 entityManagerFactory를 이용해서 트랜잭션을 관리한다.


## propagation - 전파옵션 (Spring 기본값 : REQUIRED)
스프링은 새로운 트랜잭션을 새로 생성하는 것 뿐만 아니라 기존 트랜잭션을 그대로 사용하거나 기존에 트랜잭션이 진행중인 상태에서 현재 코드를 실행하는 등의 트랜잭션 전파와 관련된 부분을 설정으로 지정할 수 있도록 지원하고 있다.

* REQUIRED
디폴트 속성으로 모든 트랜잭션 매니저가 지원하며, 대개 이속성이면 충분. 미리 시작된 트랜잭션이 있으면 참여하고 없으면 새로 시작. 자연스럽고 간단한 트랜잭션 전파 방식이지만 사용해보면 매우 강력하고 유용하며 하나의 트랜잭션이 시작된 후에 다른 트랜잭션 경계가 설정된 메소드를 호출하면 자연스럽게 같은 트랜잭션으로 묶인다.
* SUPPORTS
이미 시작된 트랜잭션이 있으면 참여하고 그렇지 않으면 트랜잭션 없이 진행. 트랜잭션이 없긴 하지만 해당 경계 안에서 Connection이나 하이버네이트 Session 등을 공유할 수 있음.
* MANDATORY
REQUIRED와 비슷하게 이미 시작된 트랜잭션이 있으면 참여하는 반면에 트랜잭션이 시작된 것이 없으면 새로 시작하는 대신 예외를 발생시킨다. 혼자서는 독립적으로 트랜잭션을 진행하면 안 되는 경우에 사용.
* REQUIRES_NEW [항상 트¡랜잭션을 새로운걸 시작하는듯]
항상 새로운 트랜잭션을 시작. 이미 진행 중인 트랜잭션이 있으면 트랜잭션을 잠시 보류함.
* NOT_SUPPORTED
트랜잭션을 사용하지 않게 함. 이미 진행 중인 트랜잭션이 있으면 보류시킴.
* NEVER
트랜잭션을 사용하지 않도록 강제함. 이미 진행 중인 트랜잭션도 존재하면 안되며 있다면 예외를 발생시킴.
* NESTED
이미 진행중인 트랜잭션이 있으면 중첩 트랜잭션을 시작하고. 중첩 트랜잭션은 트랜잭션 안에 다시 트랜잭션을 만듦. 하지만 독립적인 트랜잭션을 만드는 REQUIRES_NEW와는 다름



참고
 * [현실도피](http://ssmlim.tistory.com/45)
  * [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /

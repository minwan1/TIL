## Spring-JPA-OSIV

### 준영속 상태와 지연로딩
일반적으로 서비스와 레파지토리 계층에서는 영속성 컨텍스에 속하고 컨트롤러나 뷰단에서는 비영속상태가 된다. 따라서 뷰나,컨트롤러에서 변경감지나, 지연로딩이 작동하지 않는다.그럼으로 컨트롤러나 뷰에서 객체그래프같은 기능을 사용하게되면 익셉션이 발생한다. 만약 하이버네이트 구현체를 사용한다면 org.hibernate.LazyInitializationException예외가 발생한다. 이러한 문제를 해결하게 나온것이 OSIV이다.

### OSIV란?
Spring Boot에서 Default로 설정해주고 있는 Open Session In View Pattern에 대한 내용이다. 해당 패턴은 객체-관계 매핑(ORM, Object-Relational Mapping)의 사용으로 등장하게 된 패턴이다. Open Session In View에 대해 미리 정리를 하자면, Open Session In View는 영속성 컨텍스트를 뷰 렌더링이 끝나는 시점까지 개방한 상태로 유지하는 것입니다.

### Transaction란?
Transaction는 쪼갤 수 없는 업무처리의 단위로 작업의 원자 단위라고 보면 되며, 영속성 컨텍스트는 Transaction과 1:1로 연결됩니다

### 트랜잭션과 영속성객체의 관계
Transaction동안 수정된 객체의 모든 상태는 영속성 컨텍스트 내에 저장되어, Transaction이 종료될 때 데이터 저장소에 동기화(flushing)됩니다

```java
@Transactional
public void moveTeam(Long memberIdx, Team team){
  Member member = memberRepository.findOne(memberIdx);
  member.moveTeam(team);
}

```
위 moveTeam 메서드가 실행될 때 @Transactional 어노테이션에 의해 자동으로 Session이 열리게 됩니다.

조회(findOne)를 통해 가져온 Member 객체는 영속성 콘텍스트에 캐쉬되고, Transaction동안 Member 객체의 변경 사항이 추적됩니다. 이 때 Member 객체는 영속성으 갖는다고 말합니다.

moveTeam에 의해 영속성을 갖는 Member 객체가 변경된 것을 영속성 컨텍스트가 감지하게 됩니다. Transaction이 종료되는 시점인 메서드 종료 시점에 영속성 컨텍스트가 캐시하고 있는 객체의 변경 상태를 저장소와 동기화하게 되며, 이때 update 쿼리가 자동으로 실행되게 됩니다.

## 페치 전략
페치 전략이란 쿼리 수행과 관련된 객체와 연관 관계를 맺고 있는 객체나 컬렉션 을 어느 시점에서 가져올지에 대한 전략 입니다. 즉 데이터베이스에서 데이터를 가져 오는 전략을 정의합니다.



페치 전략
페치 전략이란 쿼리 수행과 관련된 객체와 연관 관계를 맺고 있는 객체나 컬렉션 을 어느 시점에서 가져올지에 대한 전략 입니다. 즉 데이터베이스에서 데이터를 가져 오는 전략을 정의합니다.

JPA에서 정의하는 전략은 두 가지가 있습니다.

EAGER - 데이터를 즉시 가져오는 전략으로 즉시 로딩이라고 합니다.

LAZY - 데이터가 처음 액세스 될 때 가져오는 전략으로 지연 로딩이라고 합니다.

JPA에서는 연관관계의 종류에 따라서 기본으로 갖는 페치 전략을 다르게 가지고 있습니다.

즉시로딩(EAGER)

@ManyToOne
@OneToOne
지연로딩

@OneToMany
@ManyToMany
프록시 객체는 주로 연관된 데이터를 지연로딩할 때 사용됩니다.


[개발노트 - kingbbode](http://kingbbode.tistory.com/27)

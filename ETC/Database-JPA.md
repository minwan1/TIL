## Spring-data-JPA
[createQuery]
### Spring-data
spring-data는 최근 나와있는 다양한 데이터관리솔루션(mysql,mongdb,radis)을 동일한 방식의 인터페이스로 사용할수 있게해주는 프로젝트이다.

### Spring-data-JPA
자바 ORM 표준 JPA는 SQL 작성없이 객체를 데이터베이스 직접 저장할 수 있게 도와준다. 그리고 이러한 JPA의 구현체로 하이버네이트,EclipseLink, openJPA가 존재한다. 이러한 spring-data의 인터페이스로써 JPA를 다룰수 있게해주는게 spring-data-jpa 이다.
![](http://i.imgur.com/Dr6tjoO.png)
### JPA 장점
* CRUD SQL을 작성할 필요가 없음.
* 조회된 결과를 객체로 매핑하는것도 자동화가 되어있음.
* jpa에서 제공하는 네이티브 sql로 실제 sql로도 사용가능함.
* 데이터베이스 조회성능 이슈의 문제는 jpa문제라기보다는 sql문제임
* 생산성이 좋아지고 유지보수할게 없어짐
* 데이터베이스 종류변환에서도 flexible하게 이동가능


## 자바 -> 데이터베이스 데이터 CRUD
기존에는 CRUD 를 작성하려면 너무많은 sql과 jdbc api를 코드로 작성해야한다. 왜냐하면 데이터베이스는 객체 구조와는 다른 데이터 중심의 구조를 가지므로 객체를 데이터베이스 직접 저장하거나 조회할수는 없다. 이러한문제를 해결하기위해 나온것이 JPA이다.

### Spring-data-JPA 원리
Spring-data-JPA는 Reposytory인터페이스만 작성하면 나머지는 Spring-data-JPA에서 상속받은 Reposytory을 검색하여 런타임시기에 그메소드에 맞게 구현한 레파지토리에 객체를 생성해서 스프링 빈으로 등록한다.


##
Reposytory<Employee,Long> 2번쨰는 식별(프라이머리키 타입)을넣는것, 여기에서는 id가 long형이기때문에 long을넣음

##리파지터리 인터페이스 메서드 작성 규칙
Spring-data-JPA모듈은 리파지터리로 사용할 인터페이스는 o.s.data.reposytory.Reposytory 인터페이스를 상속받아야 한다.



## JPA
개발자는 자바중심(객체지향)에만 집중해서 개발하고 SQL은 JPA에게 맡긴다.
### JPA와 객체 그래프 탐색
일반적으로 SQL을 직접 다루면 처음 실행하는 SQL에 따라 객체 그래프를 어디까지 탐색 할 수 있는지 정한다.
```java
class MemberService{
  public void process(){
    Member member = memberDao.find(memberId);
    member.getTeam()//?
    member.getOrder().getDelivery()//?
  }
}
```
member를 통해 객체를 조회했지만 팀과 delivery의 정보를 얻어올 수 있는지 findDao를 열어봐야안다.이것은 엔터티가 sql에 의존적인 이유로 발생한다.
***Jpa*** 는 연관된 연관된 객체를 신뢰하고 마음껏 조회할 수 있다. 이기능은 실제 데이터베이스 조회를 미룬다고 해서 ***지연 로딩*** 이라 한다.

```SQL
Member member = jpa.find(member.class,memberId);

Order order = member.getOrder();
order.getOrderDate(); //Order를 사용하는 시점에 SELECT
```
이렇게 실행되는 시점에 조회를 하기때문에 효율적으로 사용 할 수 있다.

```
== // 주소가같은지 비교
equals 값이 같은지 비교
```


## 어플리케이션 개발
코드는 크게 3가지로 분류된다
* 엔티티 매니저 설정
* 트랜잭션 관리
* 비지니스 로직


## 영속성 관리
### 1. 엔터티 매니저 팩토리 생성
* 영속성 유닛(datasource 등 설정)찾아서 엔터테 매니저를 생성한다. 이때 커넥션풀도 생성하므로 엔터티 매니저 팩토리르 생성하는 비용은 아주 크기때문에 공유해서 사용해야한다.
### 2. 엔티티 매니저 생성
```java
EntityManager em = emf.createEntityManager();
```
* 매니저 팩토리에서 엔티티 매니저를 생성한다.
* 엔티티매니저를 통해 CRUD를 할수 있다.
* 엔티티매니저는 내부에 데이터소를(데이터베이스 커넥션)을 유지하면서 데이터베이스와 통신한다.
* 엔티티매니저는 데이터베이스 커넥션과 밀접한 관계가 있으므로 스레드간에 공유하거나 재사용하면 안된다.
### 3. 트랜잭션관리
JPA를 사용하면 항상 트랜잭션 안에서 데이터를 변경해야 예외가 발생하지 않는다. 아니면 예외발생한다, 그리고 시작하려면 엔티티매니저로부터 트랜잭션 API를 받아와야 한다.
```java
EntityTransaction tx = em.getTransaction() //트랜잭션 API
try{
  tx.begin();
  logic(em);
  tx.commit();
}catch (exception e){
  tx.roollbak();
}
```
#### 4. JPQL
JPQL은 엔티티 객체를 대상으로 쿼리한다. 쉽게말해서 클래스와 필드를 대상으로 쿼리한다.(좀오바해서 사실상 데이터베이스에 있는 데이터를 다가지고와서 객체로 바꾸어서 자바로 다시찾는격)

## 영속성 관리
### 엔터티 매니저
엔티티를 저장하는 가상의 데이터베이스라고 생각하면된다.

EntityManagerFacotry를 생성하는데는 많은 자원이든다. 그래서 EntityManagerFacotry를 한번만 생성하고 그것을 공유해 EntityManager를 만든다. EntityManagerFacotry를 이용해 EntityManager 를생성하는것은 많은 자원이 드는 일은 아니다.

### 영속성 컨텍스트란(엔터티를 영구저장하는 환경)
* 엔터티 매니저로 엔터티를 저장하거나 조회하면 엔터테 매니저는 영속성 컨텍스트에 보관하고 관리한다.
* 엔터티 매니저를 하나 생성할 때 영속성 컨텍스가 하나 만들어진다.

### 엔터티 생명주기
* 비영속:영속성 컨텍스트와 전혀 과계가 없는상태
* 영속:영속성컨텍스트에 저장된상태(em.find(),jpql로 조회한상태,persist)
* 준영속:영속성상태에 저장되었다가 분리된상태(em.close,em.clear,em.detach)
  * em.detach(em이 관리하는 객체들중 하나의 객체만 영속성 제거하는기능)
  * em.clear(em이 관리하는 모든 객체들의 영속성 제거)
  * em.close()
* 삭제:삭제된상태(엔터티를 데이터베이스에서 삭제한다.)
![](http://i.imgur.com/Jmkkufs.jpg)

#### 영속성 컨텍스트 특징
* flush:영속성컨텍스트에 새로저장된 엔터티를 저장하는 행위.
* 필드가 많으면 다이나믹업데이트로 바꿔서 직접 업데이트 시키는게 성능최적화됨(기존에는 항상 모든 필드를 다업데이트하기때문에)
* 영속성 컨텍스트는 어플리케이션과 데이터베이스 중간에서 객체를 보관하는 가상의 데이터베이스같은 역할을한다.

## 엔터티 매핑

@OneToOne
@JoinTable(name = "transfer_mapping",
  joinColumns = @JoinColumn(name = "transfer_id"),
  inverseJoinColumns = @JoinColumn(name = "soda_id"))
private TransferMapping transferMapping;



#### 연관관계 삭제코드
```java
Member member1 = em.find(Member.class,"member1");
member1.setTeam(null);
```

#### 연관관계의 주인
* 두 객체 연관관계 중 하나를 정해서 테이블의 외래키를 관리해야하는데 이것을 연관관계의 주인
* 외래키를 가지고있는게 주인, 그외래키를 프라이머리키로쓰는데가 노예
* manytoone은 항상주인이므로 mappedBy속성이 없다.

#### 양방향 연관관계 신경쓰기 클래스
양방향 연관관계 신경쓰기 클래스
```java
public void setTeam(Team team){
  if(this.team != null){
    this.team.getMembers().remove(this);
  }

  this.team = team;
  team.getMembers().add(this); //this 여기서는 멤버
}
```
* 위와같이 양방향으로 데이터를 안넣어주면 데이터베이스에 저장하는데는 문제가 발생하지 않지만 순수 객체 상태에서 team이 멤버의 수자를 조회하게되면 조회할수없다 아래예제와같이
```java
member1.setTeam(team1);
member2.setTeam(team1);
List<Member> members = team1.getMembers();
```
위에서 멤버의길이가 2일것으로 예상되지만 답은 0이다. 만약 위에 정보들이 디비에 들어가있던 정보에서 List<Member> members = team1.getMembers() 조회하면 2의정보가 조회되겠지만 위에 정보는 객체이기때문에 객체는 단방향이기때문에 저러한 결과를 초래한다. 그래서 양방향 연관관계 신경쓰기 클래스를 주입해주는게 좋다.


참고
* [시나몬 브레드](http://adrenal.tistory.com/23)


manytoone
member class
@JoinColumn
##
onetomany
team class
mappedby

* Optional
*```
  * 기본 true로 되어있고 false로 설정하면 연관된 엔터티가 항상있어야한다.

http://projects.spring.io/spring-hateoas/
JSON형식을 만들어줌

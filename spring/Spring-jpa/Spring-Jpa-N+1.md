## N+1 issue
만약 글로벌 페치전략이 eager경우 n+1문제가 발생할수있다.
```java
@Entity
public class Order {
  @Id @GeneratedValue
  private Long id;

  @manyToOne(fetch = FetchType.EAGER) //부르는 즉시 로딩됨
  private Member member;
}
```
문제는 아래와같이 호출할경우에 n+1의 문제가 발생한다는것이다.
```java
List<Order> orders = em.createQuery("select o from Order o",Order.class).getResultList();
```

```sql
select * from Order //jpql로 실행된 SQL
select * from Member where id=? //EAGER로 실행된 SQL
select * from Member where id=? //EAGER로 실행된 SQL
select * from Member where id=? //EAGER로 실행된 SQL
...
```
JPA가 JPQL을 분석할때 글로벌 페치전략을 참고하지 않고 오직JPQL만 생각하여 충실하게 SQL을 만들어낸 결과이다.
1. order을 전체 조회한다.
2. eager전략이기 때문에 멤버객체에 들어가 영속성 컨텍스를 찾는다.
3. 영속성컨텍스트가 존재하지 않으면 쿼리로 멤버를 조회한다.

이러한 문제를 해결하기위해 N+1문제는 JPQL페치 조인으로 해결가능하다.

페치 조인 적용전
```sql
JPQL : select o from Order o
SQL : select * from Order
```
페치 조인 적용후
```sql
JPQL : select o from Order o join fetch o.member
SQL : select o.*, m.* from Order o join Member m on o.Member_ID=m.MEMBER_ID
```
페치조인을 사용하게되면 조인 대상까지 함께 조회한다.(연관된 엔터티를 이미 로딩했으므로 글로벌 페치 전략은 무의미하다.)



## 새로 테스트 한내용
아카데미와 제목은 1대 다 관계
```java
        for(Academy ac : academies){
            System.out.println(ac.getSubjects().get(0).getName()); // for문 돌떄마다 n+1하나씩 발생.
        }
```

find all로 academy안에 one to one 이나 manytoone의 글로벌 페치 전략이 eargar여도 n+1이 발생하기 떄문에 fetch전략으로 조회를 해야한다.

대신 여기에서 onetoone의 관계에서는 lazy로 변경해주면 조히할떄 n+1이 발생하지 않는다.
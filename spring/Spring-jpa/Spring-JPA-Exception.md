Spring JPA Flush




HHH000099: an assertion failure occured (this may indicate a bug in Hibernate, but is more likely due to unsafe use of the session): org.hibernate.AssertionFailure: null id in com.example.toy.user.User entry (don't flush the Session after an exception occurs)

트랜잭션이 시작되고 데이터를 디비에 저장하려고할때 NULL제약 조건에 의해 Exception발생하였고 그 Exeception을 후속처리를 하려고했는데 위와 같은 에러가 발생했다. 후속처리로 데이터베이스에서 특정 유저 이름을 찾아서 특정 API를 호출했어야 했다. 그런데 save, findOne(프라머리키) 조회같은 것은 Exception이 발생하지 않는데, JPQL, Repository를 통해 접근할때 Exception이 발생하는것이다.

찾아보니까 JPQL을 실행할때 Flush가 일어나는것이였다. 그래서 이것은 flush가 일어날때 그전에 Exception이력을 가지고있으니까 에러가 발생하는거였다. save,findOne은 flush가 일어나지 않는다.

jpql을 실행할때 Flush가 일어나는 이유는 아래와같은 예제 때문이다.

```java
em.persit(memberA); //1
em.persit(memberB); //2
em.persit(memberC); //3
                    //4
query = em.createQuery("select m from Member m", Member.clss);  //5
List<Member> members = query.getResultList(); //6
```
만약 1, 2, 3라인이 디비에 반영이 되지않고 유저를 조회하게 된다면 위에 멤버 3명은 조회할 수 없을것이다. 그렇기 때문에 jpql이 실행되기전에 flush가 된다.

find나 save가 flush를 안쓰는 이유는 jpql같은경우에는 무조건 db를 먼저 조회한후에 영속성컨텍스트를 확인하는 반면 save나 find는 먼저 영속성컨텍스트를 확인하고 디비를 확인하기때문에 위와같은 이슈가 발생할일이 없기 때문이다.


 영속성 컨텍스트를 플러시하는 방법은 3가지가 있다.
1. 직접 호출 하는 방법인데 엔티티 매니저의 flush() 메서드를 직접 호출해서 영속성 컨텍스트를 강제로 플러시 하면 도니다. 테스트나 다른 프레임워크와 JPA를 함께 사용할 때를 제외하고 거의 사용하지 않는다.
2. 트랜잭션 커밋이 자동으로 플러시가 호출된다.
3. JPQL 쿼리 실행 할때에도 플러시가 자동으로 호출된다.



jpql을 하는순간 플러시가 발생하는데 이떄 Exception이력이 있으니까 이것을 디비에 반영을 못하는 그런 상황임.

하이버네이트에서도 session이 exception이 발생했을떄는 session을 복구하지말고 그냥 롤백하고 close하라는데는 다이유가있었다.



Entity Manager에 의해 던져진 예외는 데이터베이스 트랜잭션을 롤백하고 즉시 EntityManager를 닫아야한다는 것을 의미한다. EntityManager가 애플리케이션에 바인딩되어 있다면 애플리케이션을 중지해야합니다. 데이터베이스 트랜잭션을 롤백해도 비즈니스 오브젝트가 트랜잭션 시작시의 상태로 되돌아 가지 않습니다. 이는 데이터베이스 상태와 비즈니스 오브젝트가 동기화되지 않음을 의미합니다. 예외는 복구 할 수 없으며 어쨌든 롤백 후에 작업 단위를 다시 시작해야하기 때문에 일반적으로 이것은 문제가되지 않습니다.


//findAll 한번하면 그건 메모리에 올리나보네 ㅋ
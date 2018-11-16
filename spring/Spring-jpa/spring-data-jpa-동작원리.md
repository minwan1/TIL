Spring Data jpa에서는 따로 CURD기능을 구현하지 않고 JpaRepository interface를 상속을하면 CRUD 기능이 구현되어져있다. 

![](https://i.imgur.com/XJiCnwS.jpg)
이러한 이유는 JPA레파지토리의 구현체는 애플리케이션 실행 시점에 스프링 데이터 JPA가 생성해서 주입해준다.


![](https://i.imgur.com/l8uXN3f.jpg)

Spring Data respository 에서 구현되어진 메소드외에 특정 메소를 제공하여도 메소드 이름을 분석해서 JPQL을 생성해준다.




다음은 Spring data와 Spring Data Jpa의 구조이다. Repository, CrudRepository, PaginAndSortingRepository까지는 Spring Data 의 공통 기능 인터페이스이다. 그다음 JpaRepository는 Spring Data Jpa에서 제공해주는 인터페이스이다.
![](https://i.imgur.com/DOiOPUz.jpg)

JpaRepository를 상속받게되면 Spring Data와 Spring Data Jpa에서 제공해주는 메소드의 기능을 사용할 수 있게 된다.


## 쿼리 메소드 기능
쿼리 메소드 기능은 스프링 데이터 JPA가 제공하는 기능인데 메소드만으로 적절한 JPQL을 생성해서 실행한다. Spring 데이터 JPA가 제공하는 쿼리 메소드 기능은 크게 3가지다.
* 메소드 이름으로 쿼리생성
* 메소드 이름으로 JPA NameQuery 호출
* @Query 어노테이션을 사용해서 리포지토리 인터페이스에 쿼리 직접 정의

이기능들을 활용하면 인터에피스만으로 대부분의 쿼리를 작성가능하다.

### 메소드 이름으로 쿼리 생성
다음은 이메일과 이름으로 회원을 조회하고 작성한 메소드이다.
```java
public interface MemberRepository extends Repository<Memberm Long>{
    List<Member> findByEmailAndName(String email, String name);
}
```

Spring data jpa는 인터페이스에 정의한 findByEmailAndName(...) 메소드를 실행하면 스프링 데이터는 JPA는 메소드이름을 분석해서 JPQL을 생성하고 실행한다. 물론 정해진 규칙에 따라서 메소드 이름을 생성해야한다.


### JPA Named Query
스프링 데이터 JPA는 메소드 이름으로 JPA Named 쿼리르 호출하는 기능을 제공한다. JPA Named 쿼리는 이름 그대로 쿼리에 이름을 부여해서 사용하는 방법인데, 아래와 같이 어노테이션이으로 작성 가능하다.

```java
@Entity
@NamedQuery(
    name="Member.findByUsername"
    query="select m from Member m where m.username = :username"
)
public class Member{
    ....
}
```
Spring Data Jpa에서는 아래와같이 named query 기반으로 조회 가능하다.
```java
public interface MemberRepository extends JpaRepository<Member, Long>{
    List<Member> findByUsername(@Param("username") String username);
}
```

### @Query, 리포지토리 메소드에 쿼리 정의
리포지토리 메소드에 직접 쿼리르 ㄹ정의하려면 아래와같이 사용 가능하다. 



```java
public interfae MemberRepository extends JpaRepository<Member, Long>{
    @Query("select m from Member m where m.username = ?1")
    Member findByUsername(String username)
}
```



## Spring 데이터 JPA가 사용하는 구현체

```java
@Repository
@Transactional(readonly = true)
public class SimpleJpaRepository<T, ID extends Serializable> implements JpaRepository<T, ID>, JpaSpecificationExecutor<T>{
    @Trnsactional
    public <S extends T> S save(S entity){
        if(entityInformation.isNew(entity)){
            em.persist(entity);
            return entity;
        }else{
            return em.merge(entity);
        }
    }
    ...
}
```

`@Transactional 어네토이션으로 트랜잭션 적용.`
*  스프링 데이터 JPA에서는 Transactional어노테이션이 있어야 등록, 수정, 삭제가 가능하다.

`@Transactional(readonly = true)`
* 이설정은 flush를 발생시키지 않기때문에 좀 더 효율적이다.


SimpleJpaRepository

@ Transaction

Spring data jpa의 쿼리 생성은 queryLookupStragy 인터페이스에서 정의된 resolveQuery 메소드에의해 순서가 결정되어진다.
1. 먼저. query 어노테이션에 쿼리가있으면 그것을 기반으로 쿼리를 실행한다.
2. 그다음 메소드에 이름기반으로 그쿼리를 리턴한다.
3. 이것도 없으면 네임드쿼리를 가져와 리턴한다.



Spring data jpa는 CRUD를 처리하기 위한 공통 인터페이스가 존재 한다. 개발할때 인터페이스만 작성하면 실행 시점에 Spring data jpa가 구현 객체를 동적으로 생성해서 주입해준다. 그래서 우리는 구현 클래스 없이 인터페이스만 작성해도 개발할 수 있다.
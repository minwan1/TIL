## EntityManagerFactory, EntityManager, PersistenceContext란?

### EntityManagerFactory란
데이터베이스를 하나만 사용하는 애플리케이션들은 일반적으로 EntityManagerFactory를 한개만 생성한다. 이 엔터티 매니저 팩토리로 엔터티 매니저를 생성할 수 있다. EntityManagerFactory는 Thread Safe해서 여러 스레드가 동시에 접근해도 안전하므로 서로 다른 스레드간에 공유해새 사용한다.

#### EntityManagerFactory 정리
* Hibernate에서는 SessionFactory(EntityManagerFactory)
* 엔티티 매니저 팩토리는 여러 스레드가 동시에 접근해도 안전하다.(thread-safe)
* EntityManager를 생성한다.


### EntityManager란
엔터티 매니저는 엔터티를 저장하는 메모리상의 데이터베이스라고 생각하면 될 것같다. 엔터티 매니저는 엔터티를 저장하고 수정하고 삭제하고 조회하는 등 엔터티와 관련된 모든일을 한다. 하지만 위의 EntityManagerFactory는 달리 Thread Safe하지 않기때문에 동시성 문제가 발생하므로 스레드간에 절대 공유하면 안 된다. 그래서 일반적으로 EntityManager를 EntityManagerFactory를 이용하여 생성한것을 사용하는것이 아닌 스프링에서 관리하는 EntityManager를 아래와 같이 선언하여 사용한다. 이렇게되면 스프링에서 알아서 EntityManager를 Proxy로 깜싼 EntityManager를 생성 하여 주입해주기 때문에 Thread-Safety를 보장 한다.

```java
@PersistenceContext
private EntityManager entityManager;
```
#### EntityManager 정리
* Hibernate에서는 Session(EntityManager)으로 불리운다.
* 엔티티 매니저는 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안된다.


### PersistenceContext(영속성 컨텍스트)란
* 영속성 컨텍스트란 엔티티(Entity)를 영구 저장 하는 환경을 말한다. 엔티티 매니저로 엔티티를 저장하거나 조회하면 엔티티 매니저는 영속성 컨텍스트에 엔티티를 보관하고 관리한다.
* 영속성 컨텍스트는 엔티티 매니저(Session)를 생성할 때 하나 만들어진다. 그리고 엔티티 매니저(Session)를 통해서 영속성 컨텍스트에 접근할 수 있고 영속성 컨텍스트를 관리 할 수 있다.
* 여러 엔티티 매니저(Session)가 같은 영속성 컨텍스트에 접근할 수도 있다.


참조
* [자바 ORM 표준 JPA 프로그래밍](http://book.naver.com/bookdb/book_detail.nhn?bid=9252528)


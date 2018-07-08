##Spring DATA Jpa
Spring 데이터 모듈은 구현클래스를 만들필요없이 정해진 규칙에 따라 인터페이스를 만들기만하면, 런타임에 알맞은 구현객체를 생성해주는 기능을 제공한다.


### EntityManagerFactory , EntityManager
Hibernate에서는 SessionFactory(EntityManagerFactory), Session(EntityManager)으로 불리운다.
엔티티 매니저 팩토리는 여러 스레드가 동시에 접근해도 안전하다.(thread-safe)
엔티티 매니저는 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안된다.


### 영속성 컨텍스트
영속성 컨텍스트란 엔티티(Entity)를 영구 저장 하는 환경을 말한다. 엔티티 매니저로 엔티티를 저장하거나 조회하면 엔티티 매니저는 영속성 컨텍스트에 엔티티를 보관하고 관리한다.

영속성 컨텍스트는 엔티티 매니저(Session)를 생성할 때 하나 만들어진다. 그리고 엔티티 매니저(Session)를 통해서 영속성 컨텍스트에 접근할 수 있고 영속성 컨텍스트를 관리 할 수 있다.

여러 엔티티 매니저(Session)가 같은 영속성 컨텍스트에 접근할 수도 있다.(참고 : 후에 공부할것!) 일단은 하나의 엔티티 매니저에 하나의 영속성 컨텍스트가 만들어진다고 생각하자.





Spring data jpa에서 만약 Exception이 발생하면 이제 해당 Session으로는 무언가 추가 작업을 하면안된다. 만약 추가 작업을 하기위해서는 새로운 세션을 생성해야한다. 이미 하이버네이트는 Exception 복귀 될수없다고 문서화가 되있다.



@PersistenceContext
private EntityManager em; 공유되어진 프록시를 가져온다.



출처: http://wckhg89.tistory.com/10 [줌구의 개발일기]


참조
* [자바 ORM 표준 JPA 프로그래밍](http://book.naver.com/bookdb/book_detail.nhn?bid=9252528)

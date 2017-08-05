## EntityManagerFactory , EntityManager
* Hibernate에서는 SessionFactory(EntityManagerFactory), Session(EntityManager)으로 불리운다.
* 엔티티 매니저 팩토리는 여러 스레드가 동시에 접근해도 안전하다.(thread-safe)
* 엔티티 매니저는 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안된다.

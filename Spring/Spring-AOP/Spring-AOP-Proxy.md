## Proxy
Spring AOP는 두가지 Type의 Proxy를 지원하고 있다. 그 첫번째는 interface가 있는 경우에만 프록시를 만둘수 있는데 이것은 JDK의 Proxy 기능을 이용하는 것이고, 두번째 방법은 CGLIB의 Enhancer 클래스를 이용하는 것이다. 이 두가지 Proxy의 차이점을 이해하고 사용하는 Spring AOP를 제대로 사용하는 것이 될 것이다.

#### JDK Dynamic Proxy
이것은 하나이상의 인터페이스를 구현하고 있으면 그 모든 인터페이스를 구현한 JDK Dynamic Proxy를 사용한다. Proxy의 핵심적인 기능은 원하는 메써드가 호출(Invocation)될 때 이 메써드를 가로채어 우리가 원하는 특정 기능들을 추가할 수 있도록 지원하는 것이다. 이러한 기술을 이용하여 transaction을 구현할 수 있다. 먼저 Spring에서 Dynamic Proxy를 적용하여 transaction을 구현하려고하면 Interface가 있어야하고 구현체는 아래와같은 소스가 필요하다.
```java

@Service
public class UserServiceImpl  implements UserService {


  @Autowired
  private UserDao userDao;

 @Transactional
 public void create(User user) {
  userDao.create(user);
 }

}

```

```java
public DatabaseConfig{// transaction configuration
  @Bean
  public PlatformTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
    return new JpaTransactionManager(entityManagerFactory);
  }

}
```
create 메소드가 트랜잭션처리가되는 이유는 애플리케이션이 실행되기전에 모든 @Transactional 어노테이션을 스캔하여 스캔된 대상의 메소드들을 트랜잭션 처리하게된다. 그 트랜잭션의 구현방법은 해당 애플리케이션이 시작되면서 PROXY라는 새로운 클래스를 생성하여 트랜잭션을 구현한다.
구현방법은 이렇다.
1. Transactional 어노테이션이 적혀있는 메소드를 호출하기전에 proxy클래스에서 transaction 시작
2. UserServiceImpl create 메소드 호출
3. proxy클래스 해당 트랜잭션 commit 또는 Rollback
크게 이 3가지의 흐름대로 진행된다(내부적으로는 더많은 흐름이 진행된다.)

좀더 가시적으로 프록시클래스를 확인하려면 아래와같이 확인할수있다.
```java
UserService userService = (UserService) applicationContext.getBean(UserService.class);
String userServiceClassName = userService.getClass().getName();
System.out.println(accountServiceClassName); //$Proxy13
```
만약 transaction을 적용하지 않는다면 아롸아같이 클래스이름 그대로를 확인할수있다.
```java
UserService userService = (UserService) applicationContext.getBean(UserService.class);
String userServiceClassName = userService.getClass().getName();
System.out.println(accountServiceClassName); //userServiceImpl
```


#### CGLIB
구현한 인터페이스가 하나도 없는 경우라면 자동으로 CGLib가 적용된다.


[스프링 트랜잭션의 프록시원리|작성자 jkim0702](http://blog.naver.com/PostView.nhn?blogId=jkim0702&logNo=30171598301&parentCategoryNo=&categoryNo=9&viewDate=&isShowPopularPosts=false&from=postView)





































```java
//@EnableAspectJAutoProxy(proxyTargetClass = true)
```


EnableAspectJAutoProxy << 이걸 정리한번해야겟다


The bean ‘partnerMachnet’ could not be injected as a ‘com.sodatransfer.web.partner.PartnerMachnet’ because it is a JDK dynamic proxy that implements:
    com.sodatransfer.web.partner.Partner



    The bean 'partnerMachnet' could not be injected as a 'com.sodatransfer.web.partner.PartnerMachnet' because it is a JDK dynamic proxy that implements:
	com.sodatransfer.web.partner.Partner

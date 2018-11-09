# PlatformTransactionManager 

스프링은 트랜잭션 경계설정을 위해 PlatformTransactionManager 를 제공하는데, 이 추상 인터페이스는 스프링 트랜잭션 서비스 추상화의 핵심 인터페이스이다. 모든 스프링의 트랜잭션 기능과 그 코드는 이 인터페이스를 통해 로우레벨의 트랜잭션 서비스가 가능하다.

![](https://i.imgur.com/ClspsNL.png)

## 트랜잭션 관리자 구현체 선정하기


PlatformTransactionManager는 다음 세 트랜잭션 작업 메서드를 제공한다.
* TransactionStatus getTransaction(TransactionDefinition definition) thros TransactionException
* void commit(TransactionStatus status) throws TransactionException
* void rollback(TransactionStatus status) throws TransactionException


PlatForTransactionManager는 전체 스프링 트랜잭션 관리자를 포괄한 인터페이스로, 스프링에는 
여러가지 트랜잭션 관리 API에 적용 가능한, 이인터페이스의 기본 구현체가 이미 탑재 되어있다.


스프링은 다양한 로우레벨 트랜잭션 기술을 지원하기 위해, PlatformTransactionManager 인터페이스를 구현한 여러 TransactionManager를 미리 제공하는데, DataSourceTransactionManager, JpaTransactionManager, JmsTransactionManager 등을 제공하고 있다.



## ResourceHolderSupport
스프링 트랜잭션 추상화 레이어에서 사용하는 클래스 중의 하나로, 이 클래스와 다음에 설명할 TransactionSynchronizationManager과 함께 트랜잭션과 관련된 리소스를 관리한다.

타임아웃을 걸거나 리소스의 레퍼런스 카운트를 증감시키는 등의 메소드가 구현되어 있으며 이 클래스를 구현할 때는 자신이 구현하는 트랜잭션 기술에서 사용하는 리소스 (Connection이나 기타 자원들)를 여기에 두면 된다.

리소스를 관리하는데에 있어서 필요한 디폴트 로직은 이미 ResourceHolderSupport 클래스에 구현되어 있다. 단 연결 해제와 같은 리소스 해제와 같은 로직이 필요하다면 clear() 메소드를 오버라이드하는 것이 좋다.
```java
public abstract class ResourceHolderSupport implements ResourceHolder {
  ...
  public void clear() {
    this.synchronizedWithTransaction = false;
    this.rollbackOnly = false;
    this.deadline = null;
  }
}
```

## JTA Transaction
관리되는 트랜잭션을 Application Server에 위임하고 여러 자원에서 복잡한 트랜잭션을 처리하려면 JtaTransactionManager를 사용해야하지만 대부분의 경우에는 필요하지 않습니다.
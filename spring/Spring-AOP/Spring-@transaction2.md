어노테이션 트랜잭션처리시에 try catch로 잡게되면 이것은 롤백이 작업하지 않는다.

```java
class A{

    @Transactional
    public Result doStuff(){
        Result res = null;
        try {
          // do stuff
        } catch (Exception e) {

        }
        return res ;
    }
}
```

롤백하기위해서는 프로그래밍티적으로 아래와같이 선언할수있다.
```java
@Transactional(rollbackFor={MyException1.class, MyException2.class, ....})
public Result doStuff(){
   ...
}

```

아니면 아래와같이 TransactionAspectSupport을 선언할수 있다.
```java
public Result doStuff(){
  try {
    // business logic...
  } catch (Exception ex) {
    // trigger rollback programmatically
    TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
  }
}
```


스프링의 AOP는 기본적으로 다이내믹 프록시 기법을 이용해 동작한다. 다이내믹 프록시를 적용하려면 인터페이스가 있어야 한다.

```xml

<!-- Transaction Manager -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <property name="dataSource" ref="dataSource" />
</bean>
<tx:advice id="txAdvice" transaction-manager="transactionManager">
  <tx:attributes>
    <tx:method name="get*" read-only="true" />
    <tx:method name="delete*" />
  </tx:attributes>
</tx:advice>

<aop:config>
  <aop:pointcut id="transactionPointcut" expression="execution(* com.syaku.bbs.dao.BbsDao.*(..))"/>
  <aop:advisor advice-ref="txAdvice" pointcut-ref="transactionPointcut" />
</aop:config>

<bean id="bbsDao" class="com.syaku.bbs.dao.BbsDao"/>

```
설명을 토대로 위 소스를 해석한다면 get으로 시작하는 모든 메서드는 읽기전용으로 설정하고, delete으로 시작하는 모든 메서드는 RuntimeException이 발생했을 때 트랜잭션을 통해 롤백을 처리한다.


 스프링 트랜잭션 AOP는 default 옵션으로 unchecked Exception(file io,sql인젝션에 관한것들)인 RuntimeException에 대해서만 롤백을 해준다.








출처: http://syaku.tistory.com/269 [Syaku 샤쿠]

참고
[stack overflow](https://stackoverflow.com/questions/25738883/spring-transactional-annotation-when-using-try-catch-block)

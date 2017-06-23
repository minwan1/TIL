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


## 트랜잭션 Exception 주의
Exception.class는 체크 예외로 분류되어 롤백안되고 왜냐하면 RuntimeException을 상속해야 언체크예외로 분류된다.. RuntimeException을상속해주면 롤백자동으로됨,하지만 RuntimeException을 상속하지 않거나 예를들어 SQL EXCEPTION,Exception(체크예외)들은 롤백처리가 되지않는다.





출처: http://syaku.tistory.com/269 [Syaku 샤쿠]















출처: http://springsource.tistory.com/134 [Rednics Blog]

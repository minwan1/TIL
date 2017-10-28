## AspectJ를 사용해서 같은 클래스내에도 새로운 트랜잭션 만들기
스프링에서 트랜잭션을 처리하기 위해서는 일반적인 방법은 Proxy 클래스를 이용해 트랜잭션을 처리하는 것입니다. Spring AOP는 두 가지 Type의 Proxy를 지원하고 있습니다. 그 첫번째는 interface를 이용한 JDK Dynamic Proxy 기능을 이용하는 것이고, 두번째 방법은 CGLIB를 이용해서 서브 클래스를 생성하여 AOP를 구현하는 것입니다. 하지만 두 방법다 내부에서 @Transactional(propagation = Propagation.REQUIRES_NEW) 설정된 메소드를 호출하게 되면 그 전 트랜잭션과 동일한 트랜잭션으로 묶이게 됩니다. 먼저 아래의 소스를 보시겠습니다.

```java
@Service
public class TestService {

	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void testTransaction1(){
		System.out.println(TransactionSynchronizationManager.getCurrentTransactionName()); //com.example.demo.TestService.testTransaction1
		testTransaction2();

	}

	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void testTransaction2(){
		System.out.println(TransactionSynchronizationManager.getCurrentTransactionName()); //com.example.demo.TestService.testTransaction1
	}

}
```

위와같이 2번째 메소드를 호출했음에도 불구하고 같은 트랜잭션으로 묶이게 됩니다. 이러한 문제를 해결하기 위해서는 스프링에서 일반적으로 사용하는 AOP를 사용하는게 아닌 AspectJ 라이브러리를 추가하여 사용해야 합니다. 이 AspectJ를 구현하는  방법은 2가지 방법이 있는데 Load-Time Weaver방식과 Compile-Time Weaver이 존재합니다.

#### Load-Time Weaver

```
객체를 Load 할때, AspectJ에 의해서 wearving된 객체를 넘겨주는 방식입니다. 아래와 같은 방식으로 동작하게 됩니다.

application context에 로드된 객체의 loading
aspectj weaver에 의한 객체 weaving (@Transaction annotation이 있는 class, method에 대한 transaction 처리가 된 객체로 변경)
객체의 이용
위 순서를 보시면 아실 수 있듯이, 이는 객체의 사용에 대해서 약간의 performance의 하락을 가지고 오게 됩니다. application context에서 객체를 load 할 때, aspectj weaver에서 하는 또 다른 일들을 지정하게 됩니다.
```

#### Compile-Time Weaver

```
객체를 Load 할 때, 위와 같은 문제가 있기 때문에, compile 시에 aspectj에서 간섭해서 필요한 객체에 weaving을 시켜서 class를 만들어내는 방식입니다. 이렇게 되면, application context의 load시에 다른 절차가 없기 때문에, performance의 하락도 없는 거의 완벽한 방법으로 구성이 가능합니다.

주의사항
lombok과 같은 compile시에 간섭하는 여러 plugin들과의 매우 다채로운 충돌이 발생한다. 특히 lombok과는 같이 사용하지 못한다고 생각해도 과언이 아니다.
```

현재 사용하려는것은 Compile-Time Weaver의 AspectJ 주입입니다. AspectJ를 사용하기 위해서는 아래와 같이 메이븐을 추가해야합니다.

```xml
<!-- Spring AOP + AspectJ -->
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-aop</artifactId>
</dependency>

<dependency>
<groupId>org.aspectj</groupId>
<artifactId>aspectjrt</artifactId>
</dependency>

<dependency>
<groupId>org.aspectj</groupId>
<artifactId>aspectjweaver</artifactId>
</dependency>



<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>aspectj-maven-plugin</artifactId>
      <version>1.6</version>
      <executions>
          <execution>
              <goals>
                  <goal>compile</goal>
                  <!-- <goal>test-compile</goal> -->
              </goals>
          </execution>
      </executions>
      <configuration>
          <complianceLevel>1.7</complianceLevel>
          <forceAjcCompile>true</forceAjcCompile>
          <weaveDirectories>
              <weaveDirectory>${project.build.outputDirectory}</weaveDirectory>
          </weaveDirectories>
          <aspectLibraries>
              <aspectLibrary>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-aspects</artifactId>
              </aspectLibrary>
          </aspectLibraries>
      </configuration>
  </plugin>
```
만약 위의 플러그인을 사용하지 않는다면 compile시 weaving을 하지 않을것입니다. 그럼으로 필수로 플로그인을 넣어줘야합니다.

```java
@Configuration
@EnableTransactionManagement(proxyTargetClass = true, mode = AdviceMode.ASPECTJ)
@EnableAspectJAutoProxy
@ComponentScan({ "com.example"})
public class AppConfig {
}

```
그 다음 추가된 AspectJ를 사용하기위해서는 Spring Configuration을 설정해줘야 합니다. @EnableAspectJAutoProxy을 설정하여 Aspect을 적용 시킵니다. 물론 여기서 proxyTargetClass = ture로 설정함으로 CGLIB를 사용한다는 설정 또한 해줘야합니다. AOP모드 또한 proxy모드에서 ASPECTJ로 변경해줍니다. 이렇게 설정하고 아까의 소스를 다시 실행시키면 트랜잭션이 분리된 모습을 확인할 수 있습니다.


```java
@Service
public class TestService {

	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void testTransaction1(){
		System.out.println(TransactionSynchronizationManager.getCurrentTransactionName()); //com.example.demo.TestService.testTransaction1
		testTransaction2();

	}

	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void testTransaction2(){
		System.out.println(TransactionSynchronizationManager.getCurrentTransactionName()); //com.example.demo.TestService.testTransaction2
	}

}
```

-Weaving
Advice를 핵심로직코드에 적용하는것을 weaving이라고 한다. 즉 공통코드를 핵심로직코드에 삽입하는것을 weaving이라고한다

출처
* [Programming is Fun](http://netframework.tistory.com/entry/LTW-CTW를-이용한-Transactional의-사용)
* [Spring document](https://docs.spring.io/spring/docs/4.2.x/spring-framework-reference/html/transaction.html)

일반적인 AspectJ
@Before – 메소드가 실행되기전에
@After – 메소드 결과가 리턴한후에
@AfterReturning – 메소드 결과가 리턴된후 그 결과값도 인터셉트한다
@AfterThrowing – 익셉션이 던저진후에 들르는곳
@Around – Run around the method execution, combine all three advices above.



```xml

<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<!-- Spring AOP + AspectJ -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>1.6.11</version>
		</dependency>

		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>1.6.11</version>
		</dependency>

```

##
```xml
<aop:aspectj-autoproxy />
```
이태그의 의미는 Spring에서 AOP를 구현시 AOP를 구현하는 대표적인 프레임워크 중 하나인 AspectJ스타일로 AOP를 구현한다는 의미이다. 좀 더 설명하면 Spring에서 AOP를 구현하는 방식은 Interface를 이용해 프록시 클래스를 만들어내 AOP를 구현하지만 AspectJ는 Java클래스의 Byte Code를 조작하여 AOP를 구현하게 된다. AspectJ문법을 이용하기 위해서는 아래와같은 어노테이션이 추가되어져있어야 한다.
```
@EnableAspectJAutoProxy
```
@EnableAspectJAutoProxy 어노테이션 속성으로 proxyTaretClass란 것이 있다. 위에서 말한대로 Spirng AOP는 Proxy class를 생성함으로 AOP를 구현한다. AOP의 대상이 되는 클래스가 Interface를 구현하고 있을 경우 Spring은 Interface를 이용하는 JDK Dynamic Proxy를 만들어서 AOP를 구현하고 Interface를 구현하지 않고 클래스에 대해서는 CG-LIB를 이용한 대상클래스의 상속을받아 서브클래스를 만들어 이를 Proxy삼아 구현하게 된다. 이속성 값을 주지 않으면 기본값은 false이기 때문에 대상이 되는 클래스가 interface를 이용한것은 자동으로 JDK Dynamic Proxy를 이용해서 AOP를 생성하고 아닌경우에는 CG-LIB를 이용해 클래스  Proxy로 AOP를 구현하게된다. 이속성을 true로 주게되면 인터페이스도 CG-LIB를 이용해 서브클래스 생서하는 방식으로 AOP를 구현하게 된다.


참조
https://www.mkyong.com/spring3/spring-aop-aspectj-annotation-example/
http://zgundam.tistory.com/81

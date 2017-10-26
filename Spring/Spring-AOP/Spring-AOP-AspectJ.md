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


https://www.mkyong.com/spring3/spring-aop-aspectj-annotation-example/
참조

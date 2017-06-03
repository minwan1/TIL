## Spring Swagger 2 설치
REST API를 만들다 보면 문서의 필요성을 절실하게 느낀다. 하지만 문서작성과 코딩을 따로따로 작성하기에는 너무 많은 시간이 들고 무엇보다 두개가 동기화가 되지 않는다. 이러한작업을 자동으로 동기화시켜주는것을 Swagger라고한다.


의존성 주입
```xml

	<dependency>
		<groupId>io.springfox</groupId>
		<artifactId>springfox-swagger2</artifactId>
		<version>2.2.2</version>
	</dependency>

	<dependency>
		<groupId>io.springfox</groupId>
		<artifactId>springfox-swagger-ui</artifactId>
		<version>2.2.2</version>
	</dependency>

```

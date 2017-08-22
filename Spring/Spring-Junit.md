## Junit


ROLE_ANONYMOUS
[ROLE_ANONYMOUS]


[ROLE_USER]


```java


@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration
public class ContextConfigLocationTest {

	@Test
	public void test1() {
	}

	@Test
	public void test2() {
	}

}
```
2011-05-06
@RunWith()
스프링의 테스트 컨텍스트 프레임워크 JUnit 확장 기능 지정
Junit은 각각의 테스트가 서로 영향을 주지 않고 독립적으로 실행됨을 원칙으로 하기에 @Test 마다 오브젝트를 생성한다.
이와 같은 Junit의 특성으로 인하여 ApplicationContext도 매번 새로 생성되기 때문에 테스트가 느려지는 단점이 있다.
그러나 @RunWith 애노테이션은 각각의 테스트 별로 오브젝트가 생성 되더라도 싱글톤의 ApplicationContext를 보장한다.
@RunWith() 대신 AbstractJUnit4SpringContextTests를 상속받아 사용할 수 있음.

@ContextConfiguration()
spring bean 메타 설정 파일의 위치를 지정할 때 사용되는 애노테이션이며 경로를 지정하지 않으면 테스트 클래스 파일이 있는 패키지 내에서 다음의 설정 파일을 사용한다.



@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)

## 리팩토링을 두려워할수록 코드는 썩어들어가진다.



1.실패
2.해결
3.개선



1. 억지로 실패하게 한다 ?



픽스처(테스트를 수행하는데 필요한 정보나 오브젝트를 픽스처(fixture)라고 한다.
테스트를 위한 애플리케이션 컨특세트 관리


@RunWith(SpringJUnit4ClassRunner.class) // Spring 테스트 컨텍스트 프레임워크의 JUnit확장 기능 지정
@ContextConfiguration(classes={DaoFactory.class}) // 테스트 컨텍스트가 자동으로 만들어줄 어플리케이션 컨텍스트의 위치




<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>2.8.9</version>
</dependency>

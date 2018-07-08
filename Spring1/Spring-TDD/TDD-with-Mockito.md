TDD란
Test-drive development이란 테스트케이스들을 먼저 작성하고, 이테스트들을 통과시키는 코드를 작성하는것이다. 이테스트들이 모두 통과되었을 때 코드를 리팩토링하는것이다.

TDD를 하면 가지는 이점
1. 개발자들이 여러가지 비지니스,기능에맞게 테스트할 수 있는 다야항 테스트케이스들을 생각하게하고, 그단계에서 많은 버그들을 잡을 수 있다.

2. 쉽게 리팩토링할 수 있다






https://semaphoreci.com/community/tutorials/stubbing-and-mocking-with-mockito-2-and-junit

데이터베이스에 의존하면 안좋은 티디디 설명
\











@Before
 public void setup() {
   MockitoAnnotations.initMocks(this);
   mockService = new MockService(mockRepository);
 }




https://m.blog.naver.com/PostView.nhn?blogId=duco777&logNo=220906658410&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F

spring, mock ㅇ이용방법



http://wonwoo.ml/index.php/post/1453 모키토 예쩨

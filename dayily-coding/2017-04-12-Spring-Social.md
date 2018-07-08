# Daily Coding 2017-04-11일


abstract class(추상 클래스)와 interface 는 선언만 있고 구현 내용이 없는 클래스이다.
추상클래스를 extends 받거나, interface를 implements 한 자식만이 객체를 생성할 수 있다.
상속받은 자식이 구현을 반드시 하도록 해야할 때 사용한다.



* social-core
  * ConnectionFactoryLocatorInterface,UsersConnectionRepositoryInterface,
* social-web
  * ProviderSigninControllerInterface,SignInAdapterInterface




  ![](http://i.imgur.com/bbZPJjm.png)
  ![](http://i.imgur.com/rKJRsvq.png)
![](http://i.imgur.com/aef3jJZ.png)
구현이아니라 바로 위에 3개기능을 가져다 쓰는것임,(인터페이스를 어떻게 구현하냐느냐에 따라 기능이 달라질듯)

* ConnectionFactoryLocator



참고
* [Take Action](http://marobiana.tistory.com/58)

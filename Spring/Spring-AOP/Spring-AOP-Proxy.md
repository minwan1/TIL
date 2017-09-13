## Proxy
Spring AOP는 두가지 Type의 Proxy를 지원하고 있다. 그 첫번째는 interface가 있는 경우에만 프록시를 만둘수 있는데 이것은 JDK의 Proxy 기능을 이용하는 것이고, 두번째 방법은 CGLIB의 Enhancer 클래스를 이용하는 것이다. 이 두가지 Proxy의 차이점을 이해하고 사용하는 Spring AOP를 제대로 사용하는 것이 될 것이다.

#### JDK Dynamic Proxy
이것은 하나이상의 인터페이스를 구현하고 있으면 그 모든 인터페이스를 구현한 JDK Dynamic Proxy를 사용한다.
#### CGLIB
구현한 인터페이스가 하나도 없는 경우라면 자동으로 CGLib가 적용된다.

Proxy의 핵심적인 기능은 원하는 메써드가 호출(Invocation)될 때 이 메써드를 가로채어 우리가 원하는 특정 기능들을 추가할 수 있도록 지원하는 것이다.


```java
//@EnableAspectJAutoProxy(proxyTargetClass = true)
```


EnableAspectJAutoProxy << 이걸 정리한번해야겟다


The bean ‘partnerMachnet’ could not be injected as a ‘com.sodatransfer.web.partner.PartnerMachnet’ because it is a JDK dynamic proxy that implements:
    com.sodatransfer.web.partner.Partner



    The bean 'partnerMachnet' could not be injected as a 'com.sodatransfer.web.partner.PartnerMachnet' because it is a JDK dynamic proxy that implements:
	com.sodatransfer.web.partner.Partner

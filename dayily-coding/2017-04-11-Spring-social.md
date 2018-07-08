# Daily Coding 2017-04-11일


## Spring social Configuration
* ConnectController 1나 또는 2개이상의 Provider(e.x. google,facebook,etc)의 ConnectionFactory들로 작업한다.

ConnectionController는 FacebookConnectionFactory을 찾기 위해서 ConnectionFacetoryRegistry를 등록해줘야한다.
```java
@Configuration
public class SocialConfig {

    @Bean
    public ConnectionFactoryLocator connectionFactoryLocator() {
        ConnectionFactoryRegistry registry = new ConnectionFactoryRegistry();
        registry.addConnectionFactory(new FacebookConnectionFactory(
            environment.getProperty("facebook.clientId"),
            environment.getProperty("facebook.clientSecret")));
        return registry;
    }
}
```
이렇게하면
facebookconnectionfactory는 ConnectionFactoryLocator 에 등록된다.

parent context (loaded by the ContextLoaderListener 가아닌 the local context (the one loaded by the DispatcherServlet)만 검색되고 있다.


![](http://i.imgur.com/vWucncb.png)
![](http://i.imgur.com/rKJRsvq.png)
![](http://i.imgur.com/bbZPJjm.png)


참고
* [http://docs.spring.io/](http://docs.spring.io/spring-social-facebook/docs/1.0.x/reference/html/connecting.html)

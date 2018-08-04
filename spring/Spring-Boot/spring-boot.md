# dev-tips
dev-tips



* [[test]]





SpringServletContainerInitializer 은 WebApplicationInitializer를 implements한 class를 찾아내어 onStartup()를 수행한다.

ContextLoaderListener 클래스는 DispatcherServlet 클래스의 로드보다 먼저 동작하여 비즈니스 로직층을 정의한 스프링 설정 파일을 로드한다.

ContextLoaderListener와 DispatcherServlet은 각각 다른 인스턴스턴스를 생성한. ContextLoaderListener가 생성한 Context는 RootContext가되고 DispatcherServlet이 생성한 Context는 child Context가 된다.


//

WebApplicationInitializer  , WebMvcConfigurerAdapter//


스프링은 디스펫처 서블릿을 사용하고 그 디스펫처 서블릿안


## Spring security filter chain
원래 XML 기반의 설정에서는 web.xml에 org.springframework.web.filter.DelegatingFilterProxy라는 springSecurityFilterChain을 등록하는 것으로 시작합니다만, 자바 기반의 설정에서는 WebSecurityConfigurerAdapter를 상속받은 클래스에 @EnableWebSecurity 어노테이션을 명시하는 것만으로도 springSecurityFilterChain가 자동으로 포함되어집니다.




보안 관련 3요소(스프링 시큐리티 3대요소)
## 접근 주체 (Principal)
## 인증 (Authentication)
## 인가 (Authorize)


### FilterChainProxy 스프링 빈으로 등록


![](https://i.imgur.com/B37WO8L.png)
![](https://i.imgur.com/w0WKZgu.jpg)


크게
스프링 DelegatingFilterProxy를 이용해 스프링 필터빈들을 등록할 수 있다.

그래서 Spring security의 SecurityFilterChain를 DelegatingFilterProxy에 등록하여서 Spring Security를 활성화 가능하다.



 그래서 UsernamePasswordAuthenticationFilter 를 제거하고 OAuth를 지원하는 Filter를 구현해서 제공하여야 합니다.





 token 방식에서는 세션방식을 사용하지 않는다.


 FilterRegistrationBean << 이녀석을 통해서 필터를 선언한다.



 ## Spring MVC's DelegatingFilterProxy?
 DelegatingFilterProxy 필터이다. DelegatingFilterProxy의 목적은 Filter Interface를구현하는 스프링 빈에게 역할 위임하는것이다. DelegatingFilterProxy 필터는 스프링 컨텍스트에 있는 타겟된 빈들을 찾아서 발동 시킨다.

 springSecurityFilterChain

 ![](https://i.imgur.com/BYBVcpX.png)

## Spring Web MVC 동작원리

### MVC
먼저 Spring Web MVC를 알기전에 MVC단어를 살펴 보자. MVC(Model View Controller)란 비즈니스 규칙은 표현하는 도메인 모델(Model)과 프레젠테이션을 표현하는 View를 분리하기 위하여 양측 사이에 컨트롤러를 배치하도록 설계한 디자인 패턴이다. Spring Web MVC모듈의 전체적인 구조는 이패턴을 중심으로 만들어졌다. 아래는 그림으로 표현한 MVC 모델이다.

![](https://i.imgur.com/pm2EhxT.png)

### Spring DispatcherServlet
Spring은 이러한 MVC패턴을 구현하기위해 다른 웹 MVC 프레임워크처럼 Front Controller 패턴을 사용하며 프레임워크의 여러가지 기능을 제공하는 servlet 중심으로 설계되어 있다. 스프링에서 Front Controller 역할을하는것이 바로 DispatcherServlet이다. 스프링의 DispatcherServlet은 단순히 Front Controller  기능만 하는게아니라 스프링 IoC 컨테이너와 완전히 통합되어 스프링이 가진 모든 다른 기능을 사용할 수 있게 한다. Spring DispatcherServlet은 Spring MVC의 핵심 요소이다. DispatcherServlet은 Spring MVC의 웹요청 Life Cycle을 주관한다 할 수 있다.

>FrontController 패턴이란 모든 Web Application에 대한 요청들을 FrontController로 받고 그 요청을 Controller로 분배해주는 패턴을 의미한다.

>서블릿이란 클라이언트 요청을 처리하고 그 결과를 다시 클라이언트에게 전송하는 Servlet 클래스의 구현 규칙을 지킨 자바 프로그램이다.

아래는 스프링 DispatcherServlet의 역할을 그림으로표현한것이다.
![](https://i.imgur.com/7xlCWY9.png)

아래 그림은 위그림의 동작 순서의 그림이다.
![](https://i.imgur.com/eWjZX8j.png)


스프링 부트에서 DispatcherServlet설정은 WebApplicationInitializer를 구현함으로써 설정해 줄 수있다.
```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext container) {
        ServletRegistration.Dynamic registration = container.addServlet("dispatcher", new DispatcherServlet());
        registration.setLoadOnStartup(1);
        registration.addMapping("/*");
    }

}
```
위소스는 Web Application에 들어오는 어떤 요청도 DispatcherServlet을 통해 관리 되어질것이다. AbstractDispatcherServletInitializer라는 추상클래스를 사용하면 DispatcherServlet을 좀더 쉽게 설정할 수 있다.

### DispatcherServlet, ContextLoaderListener
스프링이 구동되기 위해서는 위 DispatcherServlet 설정 뿐아니라 Service, Dao등의 빈들도 등록되어야한다. 스프링이 기본적으로 구동되면서 DispatcherServlet이 생성하는 Context를 포함하여 2가지의 Context를 load한다. 먼저 생성되는 하나는 ContextLoaderListener에 의해서 만들어지는 Root WebApplicationContext이고 그다음으로 생성되는것은 위에서말한 DispatcherServlet에 의해서 만들어지는  WebApplicationContext이다. 전자는 보통 서비스계층과 DAO를 포함한, 웹 환경에 독립적인 빈 들을 담아두고 후자는 DispatcherServlet이 직접 사용하는 컨트롤러를 포함한 웹 관련 빈을 등록하는 데 사용한다. 그리고 이 둘이 parent-child ApplicationContext 관계로 맺어진다. 그래서 ContextLoaderListener로 만들어지는 컨텍스트를 Root WAC라고 부른다.

RootContext(parent): 서비스,레파지토리등 [ContextLoaderListener]
childContext(child): 컨트롤러, 스프링 mvc관련 설정 [DispatcherServlet]

![](https://i.imgur.com/IUf4orm.png)


아래는 Spring MVC 의 생명주기 그림이다.
![](https://i.imgur.com/G8y0Pqa.jpg)




 참고

* [헤르메스 LIFE](http://hermeslog.tistory.com/156)

* [장인개발자를 꿈꾸는 :: 기록하는 공간](http://devbox.tistory.com/entry/Spring-스프링-MVC-패턴-개요)

 * [풍풍풍](https://m.blog.naver.com/PostView.nhn?blogId=sorkelf&logNo=40136444536&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)

* [토비님 블로그](http://toby.epril.com/?p=934)
* [Springdocs](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html)
* [jhyoon0801](http://jhyoon0801.github.io/blogs/blogs/spring-mvc-base-1/)

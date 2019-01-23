
# 서블릿 컨테이너, 스프링부트 동작 과정
<!-- TOC -->

- [서블릿 컨테이너, 스프링부트 동작 과정](#서블릿-컨테이너-스프링부트-동작-과정)
- [서블릿 컨테이너](#서블릿-컨테이너)
- [서블릿 구현](#서블릿-구현)
    - [Servlet](#servlet)
    - [GenericServlet](#genericservlet)
    - [HttpServlet](#httpservlet)
    - [서블릿 실행순서](#서블릿-실행순서)
- [Spring boot와 Servlet](#spring-boot와-servlet)
    - [스프링 부트 실행과정](#스프링-부트-실행과정)
    - [클라이언트 요청으로부터 DispatchServlet의 전체 흐름](#클라이언트-요청으로부터-dispatchservlet의-전체-흐름)
    - [Application context](#application-context)

<!-- /TOC -->
톰켓 즉 서블릿 컨테이너에 의해 개발자가 정의한 프로그램들이 실행되고, 더 나아가 스프링 부트는 어떻게 실행될까 해서 글을 정리하게 되었습니다.

# 서블릿 컨테이너
먼저 서블릿컨테이너를 정의하면 다음과 같을 수 있다. 서블릿 컨테이너는 웹 애플리케이션 서버중에서 HTTP 요청을 받아 처리하는 기초 역할을 맡고 있다. 대부분의 웹 프레임워크가 제공하는 기능은 서블릿 컨테이너 위에서 동작하는 서블릿, 필터, 이벤트 리스너등을 적절하게 구현한 것이다. 따라서 사용자가 웹 프레임워크로 작성한 웹애플리케이션은 결국 서블릿 컨테이너 위에서 동작한다. 서블릿컨테이너의 종류로는 아파치 톰켓, 제티등이 서블릿 컨테이너로 현재 널리사용 된다.

# 서블릿 구현
먼저 톰캣 즉 서블릿 컨테이너에 의해 프로그램이 실행되기 위해서는 표준 즉 Servlet interface를 구현해줘야 한다.**사용자 정의 서블릿은 서블릿 컨테이너 내에 등록된 후 서블릿 컨테이너에 의해 생성, 호출, 소멸이 이루어진다.**



때로는 서블릿은 자신의 상태 변경 시점을 알아내 적절한 리소스 획득/ 반환 등의 처리를 해야 하므로 Servlet 인터페이스에 int/destroy 메서드가 정의됩니다. 다시 말해 서블릿 컨테이너는 서블릿의 생명주기에 따라 서블릿의 상태를 변경하면서 서블릿 인터페이스에 정의된 각 메서드를 불러준다.

`HTTP 프로토콜`로 전달된 메시지는 서블릿 컨테이너에서 해석되고 재조합돼 웹프로그래머가 작성한 서블릿으로 전달되는 과정을 거친다. 

## Servlet
Servlet은 서블릿 프로그램을 개발할 때 반드시 구현해야 하는 메서드를 선언하고 있는 인터페이스이다. 이 표준을 구현해야 서블릿 컨테이너가 해당 서블릿을 실행할 수 있다.

## GenericServlet
GenericServlet은 Servelet 인터페이스를 상속하여 클라이언트 - 서버 환경에서 서버단의 애플리케이션으로서 필요한 기능을 구현한 추상 클래스이다. service() 메서드를 제외 한 모든 메서드를 재정의하여 적절한 기능으로 구현했다. GenericServlet 클래스를 상속하면 애플리케이션의 프로토콜에 따라 메서드 재정의 구문을 적용해야 한다.

## HttpServlet
일반적으로 서블릿이라하면 거의 대부분 HttpServlet을 상속받은 서블릿을 의미한다. HttpServlet은 `GenericServlet`을 상속받았으며, GenericServlet의 유일한 추상 메서드인 service를 HTTP 프로토콜 요청 메서드에 적합하게 재구현해놨다.

이미 DELETE, GET, HEAD, OPTIONS, POST, PUT, TRAC를 처리하는 메소드가 모두 정의되어 있다.


## 서블릿 실행순서
서블릿의 실행 순서는 개발자가 관리하는 게 아닌 서블릿 컨테이너가 관리를 한다. 즉 서블릿에 의해 사용자가 정의한 서블릿 객체가 생성되고 호출되고 사라진다. 즉 이렇게 개발자가 아닌 프로그램에 의해 객체들이 관리되는 것을 Ioc(Inversion of Control)이라고 한다.

다음은 서블릿 컨테이너의 생명주기를 도식화한것이다. 
![](https://i.imgur.com/pQLeJ9z.jpg)

* 서블릿 컨테이너는 클라이언트로 부터 처음 요청이 들어오면 현재 실행할 서블릿이 최초의 요청인지 판단하고 없으면 해당 서블릿을 새로 생성한다. 이작업은 최초 1회만 일어난다.
* init() 메소드는 해당 사용자 서블릿이 최초 생성되고 바로 호출되는 메소드이다.
* service() 메소드는 최초의 요청이든 2번쨰 요청이든 계속 호출되는 메소드이다.

>여기에서 서블릿컨테이너가 종료된다면 사용자 정의 HttpServlet의 destroy() 메소드가 호출될것이다.

다음은 HttpServlet의 구현체 내부이다.

```java

protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String protocol = req.getProtocol();
        String msg = lStrings.getString("http.method_get_not_supported");
        if (protocol.endsWith("1.1")) {
            resp.sendError(405, msg);
        } else {
            resp.sendError(400, msg);
        }

}
...//post, put 등등에 메소드가 존재함.

protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String method = req.getMethod();
        long lastModified;
        if (method.equals("GET")) {
            lastModified = this.getLastModified(req);
            if (lastModified == -1L) {
                this.doGet(req, resp);
            } else {
                long ifModifiedSince;
                try {
                    ifModifiedSince = req.getDateHeader("If-Modified-Since");
                } catch (IllegalArgumentException var9) {
                    ifModifiedSince = -1L;
                }

                if (ifModifiedSince < lastModified / 1000L * 1000L) {
                    this.maybeSetLastModified(resp, lastModified);
                    this.doGet(req, resp);
                } else {
                    resp.setStatus(304);
                }
            }
        } else if (method.equals("HEAD")) {
            lastModified = this.getLastModified(req);
            this.maybeSetLastModified(resp, lastModified);
            this.doHead(req, resp);
        } else if (method.equals("POST")) {
            this.doPost(req, resp);
        } else if (method.equals("PUT")) {
            this.doPut(req, resp);
        } else if (method.equals("DELETE")) {
            this.doDelete(req, resp);
        } else if (method.equals("OPTIONS")) {
            this.doOptions(req, resp);
        } else if (method.equals("TRACE")) {
            this.doTrace(req, resp);
        } else {
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[]{method};
            errMsg = MessageFormat.format(errMsg, errArgs);
            resp.sendError(501, errMsg);
        }

    }
```

위에서 설명한 Servlet들의 상속 구조는 다음과 같을 것이다. 개발자가 정의한 MemberServlet이 구현한 service를 서블릿 컨테이너가 실행할 것이다. 만약 service가 아닌 method 방식으로 처리하고 싶다면 해당 HTTP Method 방식을 구현하면 된다.

![](https://i.imgur.com/NF8qy4D.jpg)

위에서 말한 대로 개발자는 HttpServlet을 상속받고 HTTP Method에 맞게 서블릿을 구현해 기능을 구현할 수 있다. 그런데 여기에서 HTTPServlet을 상속하고 구현한 클래스들이 많을 텐데 어떻게 요청된 URL에 따라 각각에 서블릿으로 보내줄 수 있을까.

답은 web.xml 또는 @WebServlet어노테이션이다. 서블릿2.5까지만해도 web.xml로만 제어가 가능했지만 3.0부터 어노테이션으로 클라이언트에 접근을 제어할 수 있게 되었다. 다음과 같이 URL에 맞게 서블릿을 매핑할 수 있다.

```xml
<servlet>
    <servlet-name>member<servlet-name>
    <servlet-class>com.wan.test.MemberServlet<servlet-class>
</servlet>
<Servlet-mapping>
    <servlet-name>member</servlet-name>
    <url-pattern>/member</url-pattern>
</Servlet-mapping>
```

```java
@WebServlet("/member")
public class MemberServlet extends HttpServlet{
    ...
}
```

하지만 위에 방식대로 url마다 모두 매핑해서 사용해야 한다면 유지 보수, 확장성 등을 생각한다면 저러한 방법으로 개발을 계속 해나가는 것은 무모하다. 그래서 여기에서 한 단계 더 진보한 것 MVC 패턴이다. MVC 패턴은 모델(비즈니스로 직), 뷰(화면), Controller(최초 Request를 받는 곳)으로 나누고 개발을 하는 것이다.

그럼 MVC 패턴에서는 어떻게 사용자 URL을 받는 것일까. 그것은 FrontController 패턴이다. FrontController 패턴은 모든 클라이언트에 요청을 최 앞단에 FrontController를 두고 각각에 컨트롤러에 매핑을 해주는 방식이다.

모든 요청을 다음과같이 frontController로 넘긴다.
```xml
<servlet>
    <servlet-name>front</servlet-name>
    <servlet-class>com.wan.test.FrontController</servlet-class>
</servlet>

<servlet-name>front</servlet-name>
<uri-pattern>*</uri-pattern>

```


그리고 다음과 같이 HttpServlet을 상속한 FrontController를 구현한다.
```java
public class FrontController extends HttpServlet{
    HashMap<String, Controller> controllerUrls = null;

    @Override
    public void init(ServletConfig sc) throws ServletException{
        controllerUrls = new HashMap<String, Controller>();
        controllerUrls.put("/memberInsert.do", new MemberInsertController()));
        controllerUrls.put("/memberDelete.do", new MemberDeleteController()));
    }

    public void service(HttpServletRequest request, HttpServletResponse response){
        String uri = request.getRequestURI();
        Controller subController = controllerUrls.get(uri);
        subController.execute(request, response);
    }

}

public interface Controller {
    public void execute(HttpServletRequest, HttpServletResponse);
}

public class MemberInsertController implements Controller{

    public void execute(HttpServletRequest, HttpServletResponse){
        //...
    }
}
...
```

FrontController는 이제 요청되어진 URI에 따라 등록되어진 Contrller를 실행할것이다. 컨트롤러는 그뒤에 서비스, 레파지토리를 실행하고 최종적으로 뷰를 화면에 그리게 될것이다.


# Spring boot와 Servlet
Spring boot는 내부적으로 내장 톰켓을 가지고 있다. 즉 스프링 부트가 실행되면서 내부적으로 내장톰켓 즉 서블릿 컨테이너가 실행된다. 스프링 부트에서 사용자 정의 프로그램를 구현한 프로그램인 서블릿은 DispatchServlet이다. 스프링부트에서 DispatchServlet이 FrontController 역할을 한다.

스프링 부트에 DispatchServlet의 상속 구조이다.


![](https://i.imgur.com/hIBT4mn.jpg)


## 스프링 부트 실행과정
Spring boot는 ServletContainerInitializer를 구현한 TomcatStarter의 onStartup 메소드를 먼저 실행한다. 톰켓이 실행되고 다음 조건이 만족하면 디스팻처 서블릿이 등록 되어진다.

DispatcherServletAutoConfiguration.clas에 구성되어져있는 DispatchServlet 빈 등록으로 자동 등록되어진다. 다음은 해당 소스 내용이다.

```java
org.springframework.boot.autoconfigure.web;
DispatcherServletAutoConfiguration.class
@Configuration
@Conditional({DispatcherServletAutoConfiguration.DefaultDispatcherServletCondition.class})
@ConditionalOnClass({ServletRegistration.class})
@EnableConfigurationProperties({WebMvcProperties.class})
protected static class DispatcherServletConfiguration {
        @Bean(
            name = {"dispatcherServlet"}
        )
        public DispatcherServlet dispatcherServlet() {
            DispatcherServlet dispatcherServlet = new DispatcherServlet();
            dispatcherServlet.setDispatchOptionsRequest(this.webMvcProperties.isDispatchOptionsRequest());
            dispatcherServlet.setDispatchTraceRequest(this.webMvcProperties.isDispatchTraceRequest());
            dispatcherServlet.setThrowExceptionIfNoHandlerFound(this.webMvcProperties.isThrowExceptionIfNoHandlerFound());
            return dispatcherServlet;
        }
}
```

* 디스팻처 서블릿이 스프링에 빈으로 등록되어진다.
* 서블릿 컨테이너(디스팻처서블릿) 컨텍스트에 서블릿을등록한다.
* 서블릿 컨테이너 필터에 등록설정 해놓은 필터들을 등록한다.

* 디스팻처서블릿에 각종 핸들러 매핑(자원 URL)들이 등록 한다.(컨트롤러 빈들이 다 생성되어 싱글톤으로 관리되어진다.)



![](https://i.imgur.com/CvVBmhQ.png)




## 클라이언트 요청으로부터 DispatchServlet의 전체 흐름
FrameworkServlet은 HttpServlet를 상속하고 있다. DispatcherServlet은 FrameworkServlet를 상속하고 있다. 이말은 즉슨 DispatcherServlet이 FrontController라는것이다. 다음은 요청후 실행 순서를 설명한것이다. ```여기에서 주의할점은 서블릿컨테이너처럼 요청이왔을때 객체를 생성하는게 아닌 이미 컨트롤러들이 빈으로 등록되어져 있다는것을 생각해야한다.```

* FrameworkServlet.service()를 먼저 탄다.
* FrameworkServlet.service()는 dispatch.doService()를 호출한다.
* dispatch.doService()는 dispatch.doDispatch()를 실행한다.
    * doDispatch는 AbstractHandlerMapping매핑에서 핸들러(컨틀롤러)를 가져온다.
    * 인터셉트 등을 지나서 해당 컨트롤러 메소드로 이동한다.
    * 해당 핸들러는 MV를 리턴한다.
    * @Restoroller 컨트롤 같은경우 컨버터를 이용해 바로 결과값을 리턴한다.
    * 만약 view에 대한 정보가 있으면 viewresolver에 들려 뷰객체를 얻는다.
    * 뷰를 통해 렌더링을한다.


도식화한다면 다음과 같을 수 있다.



![](https://i.imgur.com/BJUx5fJ.jpg)




## Application context

여기에서 Dispatcher 서블릿이 생성되면서 주의할 점이 하나 있다. 디스패처 서블릿이 생성되면서 WebapplicationContext가 생성된다. 하나는 dispatch에 의해 생성되는 WebApplicationContext 그리고 스프링에 ContextLoader에 의해 생성되는 Root WebapplicationContext가 있다. 이 둘은 부모 자식 관계이다. 구조는 아래와 같을 수 있다.

그러면 최종적으로 아래와 같은 구조로 스프링이 돌아가게 된다.

![](https://i.imgur.com/VGR5aQx.png)

위와같이 구성이유는 2개이상의 DispatchServlet을 등록을하게 되면 RootWebApplicationContext를 공유하기위해서 사용할 수 있다.
```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { App1Config.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/app1/*" };
    }
}
```

참고

* [처음 해보는 Servlet & JSP 웹 프로그래밍](http://www.yes24.com/24/Goods/37235901?Acode=101)
* [https://docs.spring.io/](https://docs.spring.io/)
* [스프링 부트로 배우는 자바 웹 개발](http://www.yes24.com/24/goods/61793313)
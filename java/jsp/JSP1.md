## HTTP 프로토콜의 간략한 소개

### POST FROM 전송
바디값으로 name=1&submit=submit 이런식으로 넘어간다

### Multipart/form-data(바이너리 데이터 전달)
1. 요청 시작행을 읽어들인 후, POST 방식임을 알고 HTTP 바디가 들어올 것임을 예상한다.
2. Content-Length가 781239이라는것을 보고 HTTP 바디를 얼마나 읽으면 메시지가 완료되지는 파악한다.
3. Content-Type:Multipart/form-data;boundary=--------------------------------------------7dc42a2808d6을 보고 이것을 기반으로 나눈다.
4. 위에 바운더리랑 길이기반으로 form으로 넘긴값을 구한다.




## 서블릿 이해
**서블릿은 서블릿 컨테이너 내에 등록된 후 서블릿 컨테이너에 의해 생성, 호출, 소멸잉 이루어진다.**

때로는 서블릿은 자신의 상태 변경 시점을 알아내 적절한 리소스 획득/ 반환 등의 처리를 해야하므로 Servlet 인터페이스에 int/destroy 메소드가 정의됩니다. 다시 말해 서블ㄹ릿 컨테이너는 서블릿의 생명주기에 따라 서블릿의 상태를 변경하면서 서블릿 인터페이스에 정의된 각 메서드를 불러준다.

`HTTP 프로토콜`로 전달된 메시지는 서블릿 컨테이너에서 해석되고 재조합돼 웹프로그래머가 작성한 서블릿으로 전달되는 과정을 거친다. 이때 해당 서블릿의 매개변수로 ServletRequest와 ServletResponse를 가지는 Service 메서드가 호출되며 클라이언트로부터 전달 받은 메시지는 ServletRequest인터페이스를 통해 전달된다.

### GenericServlet
서블릿 인터페이스만 구현만하면 서블릿 컨테이너가 생성, 소멸등의 생명주기 관리 작업을 수행할 수 있다. 하지만 이것을 직접 다구현하기보다는 서블릿 스펙에서 제공해주는 GenericServlet만 구현해주면된다. 이것은 abstract void service(ServletRequest req, ServletResponse res)를 제외하고는 모두 구현된 일종의 서블릿을 위한 어댑터 역할을 제공한다.

### HttpServlet
일반적으로 서블릿이라하면 거의 대부분 HttpServlet을 상속받은 서블릿을 의미한다. HttpServlet은 `GenericServlet`을 상속 받았으며, GenericServlet의 유일한 추상 메서드인 service를 HTTP 프로토콜 요청 메서드에 적합하게 재구현해야 한다.
DELETE, GET, HEAD, OPTIONS, POST, PUT, TRAC를 처리하는 메소드가 모두 정의되어 있다. 그리고 아래와같이 각 메소드에 맞게 메소드를 호출하면된다. 개발자는 그 호출되어진 함수를 정의하면 된다.

```java
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


### 아파치 톰켓


## 서블릿컨테이너
서블ㄹ릿 컨테이너는 웹 애플리케이션 서버중에서 HTTP 요청을 받아 처리하는 기초 역할을 맡고 있다. 대부분의 웹 프레임워크가 제공하는 기능은 서블릿 컨테이너 위에서 동작하는 서블릿, 필터, 이벤트 리스너등을 적절하게 구현한것이다. 따라서 사용자가 웹 프레임워크로 작성한 웹애플리케이션은 결국 서블릿 컨테이너 위에서 동작한다.


## 서블릿 컨테이너 안내
아파치 톰켓, 제티등이 서블릿 컨테이너로 혀재 널리사용된다.


## 스프링 부트 실행 동작 순서

* 내장 톰켓은 실행한다.




## 스프링 부트는 다음과 같이 실행된다.

* Spring boot는 ServletContainerInitializer를 구현한 TomcatStarter의 onStartup 메소드를 먼저 실행한다.(어딘가에 구성되어져있는 dispatch 서블릿 빈등록으로 자동 등록되어진다.)(이것을 통해 서블릿컨테이너 서블릿, 필터등을 등록한다.)
    * 서블릿컨테이너가 ContextLoader 를 내려 초기 셋팅을한다.
    * 서블릿 컨테이너 컨텍스트에 서블릿을등록한다. (이 과정에서 서블릿은 디스패처 서블릿을 의미한다.)
    * 서블릿 컨테이너 필터에 등록설정 해놓은 필터들을 등록한다.

* 디스팻처서블릿에 각종 핸들러 매핑(자원 URL)들이 등록 한다.(컨트롤러 빈들이 다 생성되어 싱글톤으로 관리되어진다.)


![](https://i.imgur.com/CvVBmhQ.png)




## 그후 클라이언트 요청으로부터 디스팻처서블릿은 어떻게 호출되어질까.

* FrameworkService.service()를 먼저 탄다.
* FrameworkService.service()는 dispatch.doService()를 호출한다.
* dispatch.doService()는 dispatch.doDispatch()를 실행한다.
    * doDispatch는 AbstractHandlerMapping매핑에서 핸들러(컨틀롤러)를 가져온다.
    * 인터셉트 등을 지나서 해당 컨트롤러 메소드로 이동한다.
    * 해당 핸들러는 MV를 리턴한다.
    * @restoroller 컨트롤 같은경우 컨버터를 이용해 바로 결과값을 리턴한다.
    * 만약 view에 대한 정보가 있으면 viewresolver에 들려 뷰객체를 얻는다.
    * 뷰를 통해 렌더링을한다.


요청이 들어오면 디스팻처서블릿 클래스로 이동한다.

* AbstractHandlerMapping여기에서 handler할 매핑을 가져온다.






서블릿컨테이너 이니셜라이저가 실행됨







리


```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletCxt) {

        // Load Spring web application configuration
        AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
        ac.register(AppConfig.class);
        ac.refresh();

        // Create and register the DispatcherServlet
        DispatcherServlet servlet = new DispatcherServlet(ac);
        ServletRegistration.Dynamic registration = servletCxt.addServlet("app", servlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/app/*");
    }
}
```

```xml
<web-app>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/app-context.xml</param-value>
	</context-param>
	<servlet>
		<servlet-name>app</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value></param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>app</servlet-name>
		<url-pattern>/app/*</url-pattern>
	</servlet-mapping>
</web-app>
```
# 애플릿
자바 애플릿은 응용을 뜻하는 application과 작다는 의미의 접미사 let을 조합한 합성어로 자바 언어로 작성된 소프트웨어를 뜻합니다. 웹 상에서 실현하게 해 주는 응용 프로그램들이 자바 애플릿입니다. 좀 더 쉽게 말해 HTML 위에 자바 응용 프로그램을 올려 구동되는것을 말합니다. 초창기 웹은 정적인 페이지만 보여줄 수 있었습니다. 그런데 자바 언어로 구현된 애플릿이 등장하면서 소규모 애니메이션, 그림, 주가정보 표시등의 간단한 기능을 처리하는 프로그램을 만들어 사용할 수 있게 되었습니다.

# 서블릿
애플릿이 자바의 성장을 이끌었지만, 클라이언트 측에 내려 받아서 실행되는 약점이 있었다. 서블릿은 클라이언트 웹브라우저를 통해 요청하면 서버에서 실행한 후 결과값만 클라이언트로 전송합니다. 
또한 HTTP 프로토콜로 통신하는 웹의 특징과 속성들을 자유롭게 활용할 수 있는 api를 제공함으로써, 클라이언트의 요청과 서버의 응답에 관한 처리를 작업할 수 있게 했습니다.


# 서블릿 이해
**서블릿은 서블릿 컨테이너 내에 등록된 후 서블릿 컨테이너에 의해 생성, 호출, 소멸잉 이루어진다.**

때로는 서블릿은 자신의 상태 변경 시점을 알아내 적절한 리소스 획득/ 반환 등의 처리를 해야하므로 Servlet 인터페이스에 int/destroy 메소드가 정의됩니다. 다시 말해 서블ㄹ릿 컨테이너는 서블릿의 생명주기에 따라 서블릿의 상태를 변경하면서 서블릿 인터페이스에 정의된 각 메서드를 불러준다.

`HTTP 프로토콜`로 전달된 메시지는 서블릿 컨테이너에서 해석되고 재조합돼 웹프로그래머가 작성한 서블릿으로 전달되는 과정을 거친다. 이때 해당 서블릿의 매개변수로 ServletRequest와 ServletResponse를 가지는 Service 메서드가 호출되며 클라이언트로부터 전달 받은 메시지는 ServletRequest인터페이스를 통해 전달된다.

## GenericServlet
서블릿 인터페이스만 구현만하면 서블릿 컨테이너가 생성, 소멸등의 생명주기 관리 작업을 수행할 수 있다. 하지만 이것을 직접 다구현하기보다는 서블릿 스펙에서 제공해주는 GenericServlet만 구현해주면된다. 이것은 abstract void service(ServletRequest req, ServletResponse res)를 제외하고는 모두 구현된 일종의 서블릿을 위한 어댑터 역할을 제공한다.

## HttpServlet
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

그러면 최종적으로 아래와같은 순서로 호출이 일어날것이다.
![](https://i.imgur.com/alHA0zr.jpg)
1. 서블릿 컨테이너는 클라이언트에 요청을 기다린다.(서블릿 컨테이너는 요청마다 쓰레드를 생성한다.)
2. 클라이언트는 GET또는 POST기반으로 서블릿컨테이너(톰켓)에게 Request 요청을 날린다.
3. 서블릿 컨테이너는 Web.xml에서 유저가 요청을 URL을 기반으로 서블릿을 찾아 서블릿 객체를 생성한다.
    * 여기서는 HelloServlet을 생성했다고 가정하겠다.
    * 최초 요청이라면 서블릿 객체를 생성하고 2번째이상 부터는 생성되어진 서블릿 객체를 사용한다.
4. 서블릿 컨테이너는 HttpServletRequest, HttpServletResponse를 생성하고 유저에 요청이 어떤 Method(GET, POST ...)인지 확인하고 거기에 맞게 서블릿객체 메소드 호출과 함께 생성한 HttpServletRequest, HttpServletResponse를 넘긴다.



# 서블릿컨테이너
서블ㄹ릿 컨테이너는 웹 애플리케이션 서버중에서 HTTP 요청을 받아 처리하는 기초 역할을 맡고 있다. 대부분의 웹 프레임워크가 제공하는 기능은 서블릿 컨테이너 위에서 동작하는 서블릿, 필터, 이벤트 리스너등을 적절하게 구현한것이다. 따라서 사용자가 웹 프레임워크로 작성한 웹애플리케이션은 결국 서블릿 컨테이너 위에서 동작한다.

# JSP
JSP는 서블릿과 똑같은 기능을 가지고 있다. 차이가 있다면 표현하는 방법과 웹 애플리케이션에서의 역할이 다르다. 첫 번재 차이는 서블릿은 완벽하게 자바언어로 구현해야하지만, JSP는 HTML페이지 안에서 스크립트 형태로 구현해야한다. 또 한, 일부 서블릿 객체는 변수 선언과 초기화 작업 없이 바로 사용해서 코드가 훨씬 간단할 수 있다.

두번째 차이는 서블릿은 컨트롤러 페이지를 만들때 사용하지만 JSP는 HTML 페이지 안에서 스크립트 형태로 구현한다. 


![](https://i.imgur.com/26ONL6S.png)
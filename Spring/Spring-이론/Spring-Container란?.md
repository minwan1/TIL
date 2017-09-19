## 서블릿
* 컨테이너에 의해 생성되고 관리된다.
* HTML 변경시 Servlet을 재컴파일 해야 하는 단점이 있다.

Servlet 동작과정
![](https://i.imgur.com/alHA0zr.jpg)
① 사용자가 URL을 클릭하면 HTTP Request를 Servlet Container에 보낸다.
② Servlet Container는 HttpServletRequest, HttpServletResponse 두 객체를 생성한다.
③ 사용자가 요청한 URL을 분석하여 어느 서블릿에 대한 요청인지 찾는다. (DD를 참조하여 분석)
④ 컨테이너는 서블릿 service() 메소드를 호출하며, POST, GET여부에 따라 doGet() 또는 doPost()가 호출된다.
⑤ doGet() or doPost() 메소드는 동적인 페이지를 생성한 후 HttpServletResponse객체에 응답을 보낸다.
⑥ 응답이 완료되면 HttpServletRequest, HttpServletResponse 두 객체를 소멸시킨다.

![](http://i.imgur.com/TyXKBjl.jpg)

### 서블릿 생명주기
init() - 서버가 켜질때 한번만 실행
service - 모든 유저들의 요청을들을 받는다.
destroy() - 서버가 꺼질대 한번만실행
![](http://i.imgur.com/JaIUPwx.jpg)


## 컨테이너
개발자가 웹서버와 통신하기 위하여 소켓을 생성하고, 특정 포트에 리스닝하고, 스트림을 생성하는 등의 복잡한 일들을 할 필요가 없게 해줍니다.
컨테이너는 servlet의 생성부터 소멸까지의 일련의 과정을 관리한다.
컨테이너는 요청이 들어올 때마다 새로운 자바 스레드를 하나 만듭니다.



톰켓같은 was가 java파일을 컴파일해서 Class로 만들고 메모리에 올려 servlet객체를 만든다.




기억해야 할 점은, 초기화된 서블릿이 클라이언트의 요청이 있을 때 마다 Thread를 생성해서 병렬적으로 service를 수행한다는 것. 서블릿 객체는 여러개 생성되지 않습니다


보시면 알겠지만, HTML 내부에 Java 코드가 있어 HTML 코드를 작성하기 쉽습니다. 단, 로직과 디자인이 한 파일내에 섞여있어 유지보수가 어렵답니다. 클래스 단위로 분해해서 OOP적으로 코드

##### 모델 1 (쌩 JSP)
![](http://i.imgur.com/3HIwnCW.jpg)
##### 모델 2 (비지니스로직이 어느정도 분리됨)[자바코드 중심적 개발]
![](http://i.imgur.com/Hh15qRn.jpg)
* 자바빈(DAO,DTO)


##### Spring 구조
![](https://i.imgur.com/YgegN5O.jpg)

- DispatcherServlet이 모든 요청을 받는다.
- HandlerMapping을 통해서 해당 요청을 처리할 Controller를 검색한다.
- Controller로 요청을 전달한다.
- Controller는 DB에서 처리된 결과(ModelAndView)를 리턴한다
- ViewResolver를 통해서 결과를 보여줄 View를 검색한다.
- View에게 응답을 출력할 것을 요청하고 View는 클라이언트에게 전송할 응답을 생성한다.



##### IOC란
개발자는 JAVA코딩시 New연산자, 인터페이스 호출,팩토리 호출방식으로 객체를 생성하고 소멸시킨다. IOC란 인스턴스의 생성부터 소멸까지의 객체 생명주기 관리를 개발자가하는 대신 스프링(컨테이너)가 관리한다




참고
* [Old Lisper](http://anster.tistory.com/128)
* [고물라디오 같은 블로그](http://10albatross.tistory.com/4)






















[프로그래머 야용이 ^^](http://yayongi.tistory.com/entry/컨테이너란-무엇일까)

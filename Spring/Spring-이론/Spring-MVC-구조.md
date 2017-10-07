
##### 모델 1 (쌩 JSP)
![](http://i.imgur.com/3HIwnCW.jpg)
##### 모델 2 (비지니스로직이 어느정도 분리됨)[자바코드 중심적 개발]
![](http://i.imgur.com/Hh15qRn.jpg)
* 자바빈(DAO,DTO)

##### Spring MVC 구조
![](https://i.imgur.com/YgegN5O.jpg)

- DispatcherServlet이 모든 요청을 받는다.
- HandlerMapping을 통해서 해당 요청을 처리할 Controller를 검색한다.
- Controller로 요청을 전달한다.
- Controller는 DB에서 처리된 결과(ModelAndView)를 리턴한다
- ViewResolver를 통해서 결과를 보여줄 View를 검색한다.
- View에게 응답을 출력할 것을 요청하고 View는 클라이언트에게 전송할 응답을 생성한다.

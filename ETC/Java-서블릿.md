## 서블릿
servlet은 톰캣 위에서 동작하는 java 프로그램입니다. 더 정확히 말하자면 servlet 이 초기화 되는 과정을 보면 됩니다. 여기 에 의하면, 서블릿 초기화는 다음과 같은 과정을 거칩니다.
![](http://i.imgur.com/TyXKBjl.jpg)

톰켓같은 was가 java파일을 컴파일해서 Class로 만들고 메모리에 올려 servlet객체를 만든다.


### 서블릿 생명주기
init() - 서버가 켜질때 한번만 실행
service - 모든 유저들의 요청을들을 받는다.
destroy() - 서버가 꺼질대 한번만실행
![](http://i.imgur.com/JaIUPwx.jpg)

기억해야 할 점은, 초기화된 서블릿이 클라이언트의 요청이 있을 때 마다 Thread를 생성해서 병렬적으로 service를 수행한다는 것. 서블릿 객체는 여러개 생성되지 않습니다


보시면 알겠지만, HTML 내부에 Java 코드가 있어 HTML 코드를 작성하기 쉽습니다. 단, 로직과 디자인이 한 파일내에 섞여있어 유지보수가 어렵답니다. 클래스 단위로 분해해서 OOP적으로 코드

모델 1 (쌩 JSP)
![](http://i.imgur.com/3HIwnCW.jpg)
모델 2 (비지니스로직이 어느정도 분리됨)[자바코드 중심적 개발]
![](http://i.imgur.com/Hh15qRn.jpg)

* 자바빈(DAO,DTO)

참고
* [Old Lisper](http://anster.tistory.com/128)

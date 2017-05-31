## Spring Boot

***application.properties***
Srping application.properties는 합리적인 오버라이딩을 하도록 설계 되어져있다.

Spring boot는 기본적으로 classpath:templates/+뷰이름+.html로 화면으로 이동된다.
```
@SpringBootApplication 은 기존에 우리가 쓰던  @Configuration + @EnableAutoConfiguration + @ComponentScan 을 합쳐 놓은 어노테이션이라고 볼 수 있다.
```

***@Configuration*** : 현재 클래스가 Spring의 설정 파일임을 알려주는 어노테이션

***@EnableAutoConfiguration*** : spring boot 클래스패스 세팅 및 다양한 Bean 추가등을 시켜주는 어노테이션, classpath에 tomcat-embed-core.jar가 존재하면 톰캣 서버가 세팅되고, spring-webmvc.jar가 존재하면 자동으로 web.xml을 생성해 DispatcherServlet을 등록해준다

***@ComponentScan*** : 다른 컴포넌트, 서비스, 설정등을 찾을 수 있게 도와주는 어노테이션

```java
@EnableAutoConfiguration
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
````
이렇게하면 일반 자바프로그램 실행되듯이 임베디드 톰켓부터시작해서 자동으로 빌드 해주면서 프로그램이 실행이된다.

DI 방법
1. new로 생성하는방법
2. getter/setter로 하는방법

IOC컨테이너란
* configuration에서 각클래스에서 필요한 의존성(부품)을 주입해주는것

#### Configuration
* @EnableAutoConfiguration 임베디드 톰켓 관련된거인듯 ?
#### Configuration
  * 애너테이션을 붙여 이클래스가 JavaConfig용 클래스임을 컴파일러에 알린다.
#### @bean
  * 관리할 메소드에 @Bean 입력,기본으로 메소드이름이 빈으로 등록됨
  * 기본적으로 싱글톤 패턴이며 DI컨테이너별로 인스턴스가 한개망생성됨


  ## @ComponentScan 을이용한 자동으로 빈등록하기
  그다음으로 해결해야 할 문제가 바로 Bean을 하나하나 정의 하기가 번거롭다는 것이다. @ComponentScan 어노테이션을 사용하게되면 쉽게 전체 클래스를 스캔할수 있음.
  * @ComponentScan 애너테이션을 붙이면 이클래스의 패키지내부에 모든 클래스를 검색하여 @Component와 같은 특정 애너테이션이 붙은 자바 클래스를 찾아내 DI컨테이너에 등록
  * @Import(AppConfig.class) 어노테이션은 이제 지워도된다.@ComponentScan으로 자동으로 빈을 찾기떄문이다.

  ```java
  @EnableAutoConfiguration
  @ComponentScan
  public class App { //실행하는앱

  	public static void main(String[] args){
          try (ConfigurableApplicationContext context = SpringApplication.run(App.class, args)) {
          	Frontend frontend =  context.getBean(Frontend.class);
          	frontend.run();
          }
  	}
  }
  ```

  밑에와 같이 class에 @Component 어노테이션을 붙이게 되면 DI컨테이너에 등록 시킬수 있다.
  ```java
  @Component
  public class AddCalculator implements Calculator{
  	@Override
  	public int calc(int a, int b){
  		return a+b;
  	}
  }
  ```

  이렇게 되면 @Configuration 어노테이션은 필요없게된다. 따로 bean을 등록해줄필요가 없기 때문이다. Appconfig class삭제.

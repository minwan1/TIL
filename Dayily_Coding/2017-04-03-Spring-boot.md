# Daily Coding 2017-03-26일 spring boot


## time
* timestamp
  * 일반적으로 가장많이 쓰이는형식 2017-05-03
  * 1970-01-01 00:00:00 ~ 2037-12-31 23:59:59
  * 효율이 더좋다고함
* unix_timestamp
  * 1~~~~~~~ 로 long type으로 저장함
* datetime
  * 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59


## DI 예제
```java
public interface Calculator { //인터페이스
	public int calc(int a, int b);
}
```

```java
public class AddCalculator implements Calculator{ //구현 클래스

	@Override
	public int calc(int a, int b){
		return a+b;

	}

}
```

```java
@Configuration
public class AppConfig { //DI컨테이너가 bean을관리 할 수 있도록 정의해줘야함
	@Bean
	Calculator calculator(){
		return new AddCalculator();
	}
}
```
```java
public class App{


public static void main(String[] args){ //실행
      try (ConfigurableApplicationContext context = SpringApplication.run(App.class, args)) {
          System.out.print("Enter 2 numbers like 'a b' : ");
          Scanner scanner = new Scanner(System.in);
          int a = scanner.nextInt();
          int b = scanner.nextInt();
          Calculator calculator = context.getBean(Calculator.class);
          int result = calculator.calc(a, b);

          System.out.println("result = " + result);
        }
      }
    }
```
### DI진행순서
 1.인터페이스생성(또는 클래스생성)
 2.구현체생성
 3.@Configuration bean 설정
 4.실행.

* 하지만 위에 방법으로 하게 되면 너무 ***context.getBean()*** 호출이 너무 많아진다.
* 이런 문제를 해결해주는것이 DI이다.(자동으로 주입할 클래스를 찾아 주입해준다.)

```java
public class Frontend {

	@Autowired
	ArgumentResolver argumentResolver;
	@Autowired
	Calculator calculator;
	public void run() {
		System.out.println("Enter 2 numbers like 'a b' : ");
		Argument argument = argumentResolver.resolve(System.in);

		int result = calculator.calc(argument.getA(), argument.getB());
		System.out.println("result = "+ result);
	}

}
```

만약 위에 클래스를 config 빈등록을 하게 된다면 아래와같이 진행하면된다. (Autowired방식)
```java
@Bean
Frontend frontend(){
  return new Frontend();
}

```
Autowired방식이 아닌 자바 방식으로 등록하게되면 아래와같이 진행한다.
```java
@Bean
Frontend frontend(){
  Fronted fronted = new Frontend();
  frontend.setCalculator(calculator());
  frontend.setArgumentResolver(argumentResolver());
  return frontend;
}

```

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











참고
* [OKIHOUSE](http://okihouse.tistory.com/entry/Facebook-간단한-Login-인증-만들기)
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /

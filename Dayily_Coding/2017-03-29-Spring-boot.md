# Daily Coding 2017-03-26일 spring boot

```
@SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan
```
```
@SpringBootApplication 은 기존에 우리가 쓰던  @Configuration + @EnableAutoConfiguration + @ComponentScan 을 합쳐 놓은 어노테이션이라고 볼 수 있다.
```
```
@Configuration : 현재 클래스가 Spring의 설정 파일임을 알려주는 어노테이션
@EnableAutoConfiguration : spring boot 클래스패스 세팅 및 다양한 Bean 추가등을 시켜주는 어노테이션
@ComponentScan : 다른 컴포넌트, 서비스, 설정등을 찾을 수 있게 도와주는 어노테이션
```


## Angular select
```javascript
<select class="cs-select cs-skin-elastic" ng-model="recipientBankInfo" id="korea_bank_select" ng-options="bank.value for bank in koreanBankList">
  <option ng-bind="gettextCatalog.getString('transfer.select.bank')" value=""></option>
```


## Spring boot

## DI
```JAVA
1. Hello hello = new Hello();
2.
```
DI 방법
1. new로 생성하는방법
2. getter/setter로 하는방법

IOC컨테이너란
* configuration에서 각클래스에서 필요한 의존성(부품)을 주입해주는것

#### Configuration
  * 애너테이션을 붙여 이클래스가 JavaConfig용 클래스임을 컴파일러에 알린다.
#### @bean
  * 관리할 메소드에 @Bean 입력,기본으로 메소드이름이 빈으로 등록됨
  * 기본적으로 싱글톤 패턴이며 DI컨테이너별로 인스턴스가 한개망생성됨


```java
ConfigurableApplicationContext context = SpringApplication.run(App.class, args)//ApplicationContext를 반환한다.
```
```java
Calculator calculator = context.getBean(Calculator.class);//bean을 얻어온다
```

참고
* [OKIHOUSE](http://okihouse.tistory.com/entry/Facebook-간단한-Login-인증-만들기)
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /

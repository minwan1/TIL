# Daily Coding 2017-04-04일 spring boot , gson객체 추가방법, 익명클래스

gson 객체 추가 방법
```java
User user = new User;
String str = gson.toJson(user);
JsonParser parser = new JsonParser();
JsonObject json1 = parser.parse(str).getAsJsonObject();
json1.addProperty("credentialsNonExpired", true);
json.addProperty("user", json1.toString());
```

## Spring boot

### CommandLineRunner이용하기
* 스프링부트에서는 App class에서 DI컨테이너가 App클래스에도 주입할수 있게해주는 CommandLineRunner Interface가 존재한다.
* CommandLineRunner Interface를 구현하는 클래스는 의존성 주입을 할수 있게 된다.

```java
@Autowired
ArgumentResolver argumentResolver;

@Override
public void run(String... args) throws Exception {
  // TODO Auto-generated method stub
    Argument argumnet = argumentResolver.resolve(System.in);
}

```
이렇게 위와같이 run메소드안에서만 의존성주입을 할 수 있다.

### @ComponentScan 스캔이 되는 애너테이션
1. @Controller
2. @Service
    * 기능면에서는 @ComponentScan와 다르지 않음
3. @Repository
    * 이 애너테이션이 붙은 클래스안에서 발생한 예외는 특수한 규칙에 따라 스프링이 제공하는 DataAccessException으로 교체 된다.


### Spring jdbc를 사용한 디비 접속

```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
</dependency>
```
* 자바내에 h2 db이용




### 익명클래스
```java
public class Test{
    private int num = 1;
    public int getNum(){
        return this.num;
    }
    public void setNum(int num){
        this.num = num;
    }
}
```
```java
Test t1 = new Test(){
    public int num = 10;
    @Override
    public int getNum(){
        return this.num;
    }
};  

```
* 약간 상속해서 쓴다고 생각하면 될것같다.






참고
* [개발자로 홀로 서기](http://mommoo.tistory.com/16)
* [가장 빨리 만나는 스프링 부트](http://storefarm.naver.com/sosobook/products/575628365?NaPm=ct%3Dj13jxq7c%7Cci%3Ddf62af99640375f2283b1abee1eefb6268635987%7Ctr%3Dsls%7Csn%3D421722%7Chk%3D141e7e343542920c7d3dfa4256b85462873a4dab)
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /

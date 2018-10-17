# Daily Coding 2017-04-17일 security-remember-me

## Spring rememberme

일반적으로 우리는 브라우저를 닫는순간 세션이 끊기고 브라우저를 다시 켜게되면 로그인을 통해 세션을 연결해준다.하지만 rememberme 기능을 이용하면 브라우저를 열면 자동 로그인을 하게 된다.  일반적으로 브라우저로 쿠키를 보냄으로써 세션에 의해 쿠키가 발견되고 자동적으로 로그인이 일어날것이다. spring security 에서는 2가지방식을 제공한다.  하나는 쿠키기반의 token의 안전을 보호하기위해 hashing 사용, 다른 하나는 스토리지나,데이터베이스에 만들어진 토큰을 저장하여 사용한다. 두개의 구현방식은 Spring security UserDetailsService을 요구한다.

### 1. Simple Hash-Based Token Approach(Hashing 방법)
쿠키는 인증을 성공하자마자 브라우저로 다음과 같은형식의 쿠키를 전송한다
```
base64(username + ":" + expirationTime + ":" +
md5Hex(username + ":" + expirationTime + ":" password + ":" + key))
```
username : UserDetailsService에서 식별가능
password : 이것은 UserDetailsService에서 검색하는것과 같은값의 비번이다.
expirationTime : token이 만료되는 시간
key : rememberme token의 수정을 막는 개인키

rememberme토큰은 특정기간동안만 유효하고 username,password,key는 변경되지 않는다. rememberme을 사용하기위해서는 그냥 추가하면된다. 아래 element를 추가하면된다.

```
<http>
  <remember-me key="myAppKey"/>
</http>
```

쿠키방식에는 TokenBasedRememberMeServices(구현체)가 있다. 14일정도 유효하다. (보안성이 좋지는 않다고한다.)

### 2. Persistent Token Approach(데이터베이스 사용방법)
persistentTokenBasedRememberMeService 을 hash-based기반으로도 구현할수있지만 그러나 일반적으로 토큰을 저장하기위해 PersistentTokentRepository Interface 을 구현하여 사용한다. 일반적으로 persistentTokenBasedRememberMeService의 일반생성자는 deprecated 되었고 생성자로 세개의 파라미터로 필요로한다. key,userDetailsService,persistentTokenRepository 3개의 파라미터가 필요로한다. 여기에서 persistentTokentRepository인터페이스는 두가지의 구현캘래스를 제공하는데 , InMemoryTokenRepositoryImpl, JdbcTokenRepositoryImpl을 제공한다.
```
create table persistent_logins (username varchar(64) not null,
                                series varchar(64) primary key,
                                token varchar(64) not null,
                                last_used timestamp not null);
```
데이터베이스가 존재해야한다. persistent_logins는 token키와 series키를 만든다. 유저가 remember-me을 체크하면 브라우저에 "remember me"cookie가 저장된다. 브라우저에 유효한 "remember me"cookie가 존재한다면 자동적으로 로그인될것이다. 유저가 "remeber me"쿠키를 통해 로그인하게 된다면 user details는 변경하지 못한다.(도난 당한 쿠키로 정보를 변경하지 못하게하지 못하는 좋은 예이다.)

이솔루션은 유저를 유니크한 series식별자로 유저를 구별한다. 이것은 유저가 로그인할때마다 토큰이 랜덤하게 다시 생성된다.


SPRING_SECURITY_REMEMBER_ME의 이름으로 쿠키에저장되며 브라우저의 내부에 hash기반의 만들어진 토큰으로 저장되어진다.

결론
```
체크박스 remember-me(true) 체크 ->userDetailsService 유저네임조회 -> 토큰생성;
홈페이지이동 -> userDetailsService 유저네임조회 -> 토큰검사후 - 자동로그인 처리
```


http://www.mkyong.com/spring-security/spring-security-remember-me-example/
http://blog.naver.com/whydda/220748235260 시리즈 정보에대해 조금알수있는듯?
http://www.concretepage.com/spring/spring-security/remember-me-in-spring-security-example 정리해야할듯


참고
* [TEBICA STORY](http://earlybird.kr/1584)
* [Nirman's Tech Blog](http://technirman.blogspot.kr/2014/01/spring-security-remember-me-example.html)
* [https://docs.spring.io](https://docs.spring.io/spring-security/site/docs/3.0.x/reference/remember-me.html)

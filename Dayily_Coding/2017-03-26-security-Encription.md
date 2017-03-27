# Daily Coding 2017-03-26일 spring security 암호화


## 개요
처음에 Spring-security을 이용해 DaoAuthenticationProvider와 UserDetailsService을 이용해 인증기능(로그인기능)을 구현하게되면 한가지 문제점이 존재한다. 보안사고가 발생할경우 비밀번호가 평문으로 되어있다는것이다. Spring에서는 plaintext, sha, sha256, md4, md5의 암호화 기능을 지원한다. Spring-security에서 DaoAuthenticationProviderClass는 암호화기능을 지원한다. 일반적으로 설정을 하지않을경우에는 PlaintextPasswordEncoder를 사용한다. PlaintextPasswordEncoderclass는 단순 문자열로 비교해서 비밀번호 일치여부를 판단한다. 만약 비밀번호를 암호화하고 이를 기준으로 암호를 비교하고싶다면 PasswordEncoderclass를 스프링 빈으로 등록하고 이것을 사용하면된다.
> 기존의 org.springframework.security.authentication.encoding.PasswordEncode 인터페이스가 있었는데 이클래스는 salt방식으로 암호화처리를했는데 보안문제인지 모르겠지만 spring3.1+부터는org.springframework.security.crypto.passwordPasswordEncode인터페이스로 다시 재정의 했다. 여기에서 salt는 회원의 정보를 이용해 해커가 단어를 유추해 해킹을 방지하기위해 회원의 비번에 회원정보를 삽입해 비번을 만드는 방식이다.위에방식은 org.springframework.security.crypto.password.PasswordEncode인터페이스를 이용하는 방식이다. crypto.passwordPasswordEncode인터페이스는 salt방식의 데이터를 생성할때 데이터를 생성할때 유저개인정보가아닌 데이터를 랜덤하게 생성해서 암호화를 하는 방식으로 지원한다.

## 내용
***org.springframework.security.crypto.password.PasswordEncode인터페이스*** 는 2개의 메소드가 존재한다.
* String encode(CharSequence rawPassword);
  * 비번을 입력하면 암호화된 패스원드를 return한다
* boolean matches(CharSequence rawPassword, String encodedPassword);
  * 비번의 일치여부의 결과를 돌려준다
  * 1번째 인자는 암호화되지않은 값을, 2번째는 암호화된값을 넣어주면된다.


org.springframework.security.crypto.password.PasswordEncode인터페이스를 구현할 클래스
* org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder
  * org.springframework.security.crypto.bcrypt클래스를 이용해서 암화를 처리한다.
  * Spring-security에서 추천하고 있는방식
* org.springframework.security.crypto.password.NoOpPasswordEncoder
  * 테스트용 목적으로 구현된 클래스이다. 실제로 값을 넣어도 암호화되지 않는다.
* org.springframework.security.crypto.password.StandardPasswordEncoder
  * 별도의 설정이없으면 sha-256으로 암호화를 한다.

```xml
<security:authentication-provider user-service-ref="customeUserDetailsService">
  <security:password-encoder hash="md5"></security:password-encoder>
</security:authentication-provider>
```
위에방식의 xml로 진행하게되면 md5로 hash값을 입력하게되면 md5로 진행하게된다.

```xml
<bean id="passwordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"></bean>

<security:authentication-manager alias="authenticationManager">
  <security:authentication-provider ref="customeAuthenticationProvider">
  </security:authentication-provider>
    <security:authentication-provider user-service-ref="customeUserDetailsService">
      <security:password-encoder ref="passwordEncoder" />
    </security:authentication-provider>
</security:authentication-manager>

```
하지만 일반적으로 비밀번호를 등록을해야하기때문에 위에 xml방식으로 구현하게 된다.
## 결론
```java
@Autowired
BCryptPasswordEncoder passwordEncoder;

passwordEncoder.encode(userPassword);//암호환값을 디비에저장한다
```
디비에 저정할 때 위에와 같이 암호화해서 저장을한다.
```xml
<security:password-encoder ref="passwordEncoder" />
```
xml설정을 위에와 같이 설정해주게되면 비밀번호를 체크를 할 때 자동으로 BCryptPasswordEncoder class 를통해 암호화 인증을 진행하게된다.

참고
* [사랑이 고픈프로그래머..](http://zgundam.tistory.com/54)
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /

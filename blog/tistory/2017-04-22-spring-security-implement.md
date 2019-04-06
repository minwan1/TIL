# Spring Security 로그인 인증 구현

## 개요
Spring Security에서 인증 사용할때 최상위 인터페이스 AuthenticationProviderManager Interface를 사용한다. 이인터페이스는 단순하게 아래와같이 정의 되어있다.
```java
public interface authenticationManager{
  Authentication authenticate(Authentication authetication) throws AuthenticationException;
}
```
이 인터페이스는 로그인을 시도하는 유저의 비밀번호가 일치하는지를 비교해서 인증에 성공하게 되면 Aunthetication 객체를 리턴해주는것을 구현해야하는 인터페이스이다. 그리고 일반적으로 Srping 에서 authenticationManager 구현체로 ProviderManger를 사용하는데 이 ProviderManger는 이권한을 AuthencationProvider Interface에게 권한을 넘긴다. AuthencationProvider의 구현체로 Spring에서 몇가지 기본 구현체를 제고하는데 특수한 상황이 아니면 이것을 사용하면된다.그리고 일반적으로 그구현체로는 DaoAuthenticationProvier를 사용한다. DaoAuthenticationProvier는 인증을 하기위해 내부적으로 UserDetailsService를 이용해서 디비에서 유저정보를 읽어와 유저가 입력한정보를 대조해 인증에 성공하게되면 Authentication을 돌려주게되고 아니면 AuthenticationException이 발생하게 된다.

AuthencationProvider가 : 로그인 인증처리를 구현(비밀번호 대조)
UserDetailsService : 유저정보를 디비에서 불러오는 역할<br>


## security 기본 설정
### web.xml 설정
가장먼저 web.xml에 내용을 추가합니다.
```xml
<filter-name>springSecurityFilterChain</filter-name>
<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class> //filter 처리을 spring security로 위임하는것
</filter>

<filter-mapping>
<filter-name>springSecurityFilterChain</filter-name>
<url-pattern>/*</url-pattern>
</filter-mapping>
<filter>
```

web.xml에 security-context.xml 추가해준다.

```xml
<context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>/WEB-INF/spring/security-context.xml</param-value>
</context-param>
<listener>
```


그다음 메이븐 의존성을 추가합니다.

### 메이븐 의존성 추가
```xml
<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-core</artifactId>
<version>3.1.3.RELEASE</version>
</dependency>

<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-taglibs</artifactId>
  <version>3.1.3.RELEASE</version>
</dependency>

<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-config</artifactId>
  <version>3.1.3.RELEASE</version>
</dependency>
```
### spring-security.xml 설정 (로그인 주소,로그아웃주소등 설정)

```xml
<security:http auto-config="true" use-expressions="true">
  // expressions을 추가해야 hasRole,permitAll등의 다양한 옵션들을 쓸수 있음
<security:form-login login-page="/loginForm"/> //로그인 url
<!-- <security:form-login login-page="/loginForm" authentication-failure-url="성공유알엘 "/> -->
  <security:intercept-url pattern="/admin/**" access="hasAuthority('ROLE_ADMIN')"/>// 관리자만 들어갈수있음
<security:intercept-url pattern="/manager/**" access="hasRole('ROLE_MANAGER')"/> //위랑 같은뜻 매니저만 들어갈수있음
<security:intercept-url pattern="/member/**" access="isAuthenticated()"/> //인증한사람만 들어갈수있음
<security:intercept-url pattern="/user" access="hasRole('ROLE_USER')" />
<security:intercept-url pattern="/welcome" access="hasRole('ROLE_ADMIN')" />
<security:intercept-url pattern="/**" access="permitAll" /> //누구나 다들어갈수 있음
```

* <security:form-login login-page="/loginForm"/> 로그인실패하면 여기에서 설정해둔 url로 이동하게 된다.


**사용자 커스터 마이징**
아래 표는 우리가 Spring Security을 커스터마이징해서 사용할 수 있는 내용들이다.
 ![](http://i.imgur.com/Hhpxz9N.png)

**화면 input 설정**
* 아이디를 입력하는 input의 name은 j_username이다
* 비밀번호를 입력하는 input은  j_password이다.
* action은 j_spring_security_check이다. (post)

**login 커스터마이징**
```xml
<security:form-login
 username-parameter="loginid" //form name 변경할 수 있음
 password-parameter="loginpwd" //form name 변경할 수 있음
 login-page="/login" //권한없는 url접근하면 자동 여기에서 지정한 로그인으로가짐
 default-target-url="/welcome" // 로그인하고 지정한 url이없을떄 defalut로 이동되는 url
 authentication-failure-url="/login?fail=true"//로그인 실패시 이동되는 url 설정
 />

```

* 로그인 실패시 세션에 자동으로에러 메시지를 출력해주는 부분이 있는데 그걸 사용하게되면 로그인을 할때 마다 세션메시지가 계속 출력되게되고 이러다보면 was에 메모리가 가득차 full gc가 지속적으로 발생하게되면서 전반적으로 웹페이지가 느려질 수 있다.

### db 연동

먼저 root-context.xml에 디비정보를 추가해준다.
```xml
<!-- Root Context: defines shared resources visible to all other web components -->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
<property name="url" value="jdbc:mysql://디비주소"></property>
<property name="username" value="디비아이디"></property>
<property name="password" value="디비비번"></property>
</bean>
```

secuirty-context.xml에 내용을 추가해준다.
```xml
<security:authentication-manager alias="authenticationManager">
  <security:authentication-provider ref="customeAuthenticationProvider">
  </security:authentication-provider>
  <security:authentication-provider user-service-ref="customeUserDetailsService">
     <security:password-encoder ref="passwordEncoder"></security:password-encoder>
  </security:authentication-provider>
</security:authentication-manager>
```


**데이터베이스**
```sql
CREATE TABLE `users` (
  `username` varchar(50) NOT NULL,
  `password` varchar(100) NOT NULL,
  `enabled` tinyint(1) NOT NULL,
  `age` varchar(10) DEFAULT NULL,
  PRIMARY KEY (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

//이부분은 사용하지 않지만 사용하실분들은 권한설정 테이블을 만드셔도됩니다.
CREATE TABLE `authorities` (
  `username` varchar(50) NOT NULL,
  `authority` varchar(50) NOT NULL,
  UNIQUE KEY `ix_auth_username` (`username`,`authority`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

```

| 테이블      |           설명          |                                   컬럼                                   |
|-------------|:-----------------------:|:------------------------------------------------------------------------:|
| users       | 사용자 이름과 암호 정보 | username:사용자이름(로그인ID) <br>password:인증암호<br> enabled:계정사용가능여부 <br> age:사용자정보 나이 |
| authorities | 사용자-권한 매핑 정보   | username:사용자 이름(로그인ID)<br> authority:권한

age는 나중에 유저정보를 확인하기위해 넣어습니다. (추가적으로 유저정보를 더넣고싶으면 더넣어도 무방함)

예) user Table

| username | password | enabled | age |
|----------|:--------:|:-------:|:-------:|
|   admin  |   1234   |    1    | 33|
|  bkchoi  |   1234   |    1    |23|


authorities Table

| username |   authority   |
|----------|:------------:|
|   admin  |     USER     |
|   admin  | USER_MANAGER |
|  bkchoi  |     USER     |


### 실제 구현체들 구현
그다음 customeAuthenticationProvider, customeUserDetailsService을 구현해주면 된다.

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

	@Inject
	UserDao dao;

	@Override
	public user loadUserByUsername(String username) throws UsernameNotFoundException {
		user userInfo = null;
		try {
			userInfo = dao.read(username); //디비 정보를 불러와 유저정보 조
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
  }
}
```

CustomUserDetailsService 이곳에서 유저가입력한 아이디를 통해 유저정보를 불러와서 CustomAuthenticationProvider class로 정보를 넘겨주게된다.

```java
@Component
public class CustomAuthenticationProvider implements AuthenticationProvider { //authenticationManager
  @Override
  public Authentication authenticate(Authentication authentication) throws AuthenticationException {
    UsernamePasswordAuthenticationToken authToken = (UsernamePasswordAuthenticationToken) authentication; //유저가 입력한 정보를 이이디비번으으로만든다.(로그인한 유저아이디비번정보를담는다)

    user userInfo = customeUserDetailsService.loadUserByUsername(authToken.getName()); //UserDetailsService에서 유저정보를 불러온다.
    if (userInfo == null) {
      throw new UsernameNotFoundException(authToken.getName());
    }

    if (!matchPassword(userInfo.getPassword(), authToken.getCredentials())) {
      throw new BadCredentialsException("not matching username or password");
    }

    List<GrantedAuthority> authorities = (List<GrantedAuthority>) userInfo.getAuthorities();

    return new UsernamePasswordAuthenticationToken(userInfo,null,authorities);
  }

  private boolean matchPassword(String password, Object credentials) {
    return password.equals(credentials);
  }

  @Override
  public boolean supports(Class<?> authentication) {
    return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);
  }

}
```
그럼 CustomAuthenticationProviderclass에서 유저가입력한 정보와 디비에 유저정보를 통해 인증 처리를 하게된다.


## 결론
1. 스프링은 크게 2가지인터페이스를 구현하여 인증/인가 처리를 도와준다. (AuthenticationProvider,UserDetailsService)
2. UserDetailsService 는 데이터베이스의 유저정보를 불러오는 역활을한다
3. AuthenticationProvider 는 UserDetailsService에서 리턴해준 유저정보와 사용자가 입력한 유저의 비밀번호를 매칭하여 로그인 인증처리를 한다

[깃허브 예제 소스](https://github.com/minwan1/securitybasis/tree/security-basis)

참고<br>
* [사랑이 고픈 프로그래머..](http://zgundam.tistory.com/45)<br>
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9)<br>

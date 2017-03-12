# Spring security



## security db연동

전체적인 로그인 처리를 구현하는데는 두개의 인터페이스가 존재한다. UserDetailsService(디비에서 유저정보를 불러오는 클래스)와 AuthencationProvider(디비정보와 로그인한 정보를 이용하여 로그인 인증을 해주는 클래스)가 있다.

### 인증 처리과정
1. 접속자가 계정과 암호를 입력한다.
2. 접속자가 입력한 계정을 이용하여 데이터베이스에서 사용자 정보를 조회한 결과를 UserDetailsService로 담는다.
```
정확하게 말해  UserDetailsService에 담는것이 아니라 비지니스 로직을 처리하는일을하고 UserDetails객체에 담는다. 기본적으로 계정 암호 권한룰 그외 몇가지만 설정되어 있어 필요따라 항목 추가함
```
3. AuthenticationProvider: ProviderManager로부 인증처리를 위임받은 AuthenticationProvider가 최종적으로 접속자가 입력한 로그인정보랑 사용자 정보랑 값이 일치하는 확인을 한다.
```
1. 위의 조건은 <jdbc-user-service> 태그를 설정할경우에 AuthenticationProvider의 구현클래스로 DaoAuthenticationProvider를 사용하는것이다
2.UserDetailsService의 구현체로 jdbcUserDetailsManager클래스를 사용하는것 (그래서 <jdbc-user-service> 여기에 datasource을 삽입해주는것)
3.다시한번말해 그래서 jdbcUserDetailsManager로 데이터정보를 불러와 DaoAuthenticationProvider에서 인증 처리를 진행한다.
```

```xml
<!-- Root Context: defines shared resources visible to all other web components -->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
<property name="url" value="jdbc:mysql://디비주소"></property>
<property name="username" value="디비아이디"></property>
<property name="password" value="디비비번"></property>
</bean>
```


```xml
<security:authentication-manager alias="authenticationManager">
	<security:authentication-provider>
		<security:jdbc-user-service data-source-ref="dataSource"
		authorities-by-username-query="SELECT username, authority FROM authorities WHERE username = ?"
					users-by-username-query="SELECT username, password, enabled FROM users WHERE username = ?"/>
	</security:authentication-provider>
</security:authentication-manager>
```
* <jdbc-user-service>태그을 설정하면 AuthenticationProvider의 구현 클래스로 DaoAuthenticationProvider을 사용한다.
* DaoAuthenticationProvider는 사용자 정보를 읽어 올때 UserDetailsService타입의 객체를 사용한다.
* DaoAuthenticationProvider의 authenticate()메소드는 다음과 같은 과정을 처리한다.
  * 아이디,비번을 대조하여 확인이 되면 UserDetails객체로부터 Authentication객체를 생성해서 리턴한다.
* 실제 DB에서 사용자 정보를 읽어오는것은 UserDetailsService가 책임지며,





```sql
CREATE TABLE `users` (
  `username` varchar(50) NOT NULL,
  `password` varchar(100) NOT NULL,
  `enabled` tinyint(1) NOT NULL,
  PRIMARY KEY (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;


CREATE TABLE `authorities` (
  `username` varchar(50) NOT NULL,
  `authority` varchar(50) NOT NULL,
  UNIQUE KEY `ix_auth_username` (`username`,`authority`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

```

| 테이블      |           설명          |                                   컬럼                                   |
|-------------|:-----------------------:|:------------------------------------------------------------------------:|
| users       | 사용자 이름과 암호 정보 | username:사용자이름(로그인ID) <br>password:인증암호<br> enabled:계정사용가능여부 |
| authorities | 사용자-권한 매핑 정보   | username:사용자 이름(로그인ID)<br> authority:권한        

예) user Table
| username | password | enabled |
|----------|:--------:|:-------:|
|   admin  |   1234   |    1    |
|  bkchoi  |   1234   |    1    |

authorities Table
| username |   authority   |
|----------|:------------:|
|   admin  |     USER     |
|   admin  | USER_MANAGER |
|  bkchoi  |     USER     |

위에처럼 생성하게되면 따로 dao연결과 같은 설정필요없이 알아서 아이디 비번을 디비에서 확인하게된다.(테이블 이름과 칼럼이름이 위와 동일해야함)








## 프로젝트 실행 방법(Mac 기준)

```
$ git clone https://github.com/minwan1/securitybasis.git
eclipse - import - 실행

```


참고
* [Syaku(샤쿠)](http://syaku.tistory.com/286)

# Spring security



## security db연동

먼저 spring security에 들어가기전에 알아야할것은 전체적인 로그인 처리를 구현하는데는 크게 두개의 인터페이스가 존재한다. UserDetailsService(디비에서 유저정보를 불러오는 클래스)와 AuthencationProvider(디비정보와 로그인한 정보를 이용하여 로그인 인증을 해주는 클래스)가 있다는것을 알아햐한다.

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

##
### UserDetailsService와 UserDetails
* username에대해 해당하는 정보가 있으면 유저(디비에저장되어있는) 정보를 넘겨준다.
```java
public interface UserDetailsService{
  UserDetails loadUserByUsername(String username)throws UsernameNotFoundEception;
}
```
* UserDetails interface 이다.
```java
public interface UserDetails extends Serializable{
  Collection<? extends GrantedAuthority> getAuthorities();
  String getPassword();
  String getUsername();
  boolean isAccountNonExpired();
  boolean isCredentialsNonExpired();
  boolean isEnabled();
}
```

## <jdbc-user-service> 태그의 쿼리변경 관련 속성
* users-by-username-query : 사용자이름으로 유저정보를불러오는 쿼리설정
* authority-by-username-query: 사용자 권한을 알아오는 쿼리


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
* 실제 DB에서 사용자 정보를 읽어오는것은 UserDetailsService가 책임진다.

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



##
### Reference
### jdbcUserDetailsManager
* 테스트 용으로 사용하면 좋을 참고사항.
* jdbcUserDetailsManager(UserDetails 구현체)로 유저의정보를 CRUD할 수 있다.
* <jdbc-user-service id="jdbcUserService">을 추가하게되면  jdbcUserDetailsManager을 조작할 수 있다.

```java
public class UserJoinService {

	private UserDetailsManager userDetailsManager;

	public void setUserDetailsManager(UserDetailsManager userDetailsManager){
		this.userDetailsManager = userDetailsManager;
	}

	@Transactional
	public void join(NewUser newUser){
		UserDetails user = new User(newUser.getName(),newUser.getPassword(),Arrays.asList(new SimpleGrantedAuthority("USER")));

		try {
			userDetailsManager.createUser(user);
		} catch (DuplicateKeyException ex) {
			ex.printStackTrace();
			// TODO: handle exception
		}
	}

}
```
* 유저아이디,유저패스워드,유저권한을 삽입한다.
* create말고 CRUD해서 사용 할 수 있음.
* 하지만 위에 방법으로 하게 된다면 유저아이디,이름,권한설정에 정보밖에 저장을하지못한다.
* 이것을 이제 커스텀해서 UserDetailsService를 등록하는 방법을 사용해야하 한다.
##### 이방식이 아니라 자신이 만든 방식으로 회원의  CRUD을 관리해도된다. 아마 그래야만 할 것 이다.

##
### UserDetailsService 구현 사용하기

보통 Spring security에서 인증을 처리애햐 한다면 다음3가지중 한개를 적용해야한다
* AuthenticationManager 인터페이스
* AuthencationProvider 인터페이스 (인증하는클래스)
* UserDetailsService 인터페이스 (유저정보를 디비에서 불러오는 역할)
```
암호가 일치할경우 UserDetails로부터 Authetication 객체를 생성해서 리턴한다
이것들을 AuthencationProvider가 인증을 진행한다.
```

먼저 2개의 클래스를 만든다.
userInfo.java
``` java
public class UserInfo {

	private String id;
	private String name;
	private String password;

	public UserInfo(String id, String name, String password) {
		this.id = id;
		this.name = name;
		this.password = password;
	}

	public String getId() {
		return id;
	}

	public String getName() {
		return name;
	}

	public String getPassword() {
		return password;
	}

}
```
UserPermission.java
```java
public class UserPermission {

	private Long id;
	private String name;

	public UserPermission(Long id, String name) {
		this.id = id;
		this.name = name;
	}

	public Long getId() {
		return id;
	}

	public String getName() {
		return name;
	}

}


```
두개는 유저의 정보와 권한을 관리해주는 클래스들이다.
```java
public class CustomUserDetailsService implements UserDetailsService {

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		UserInfo userInfo = findUserInfo(username); //유저 정보 조회
		if (userInfo == null)
			throw new UsernameNotFoundException(username);

		List<UserPermission> perms = loadPermission(userInfo.getId());
		List<GrantedAuthority> auth = new ArrayList<>();
		for (UserPermission perm : perms) {
			auth.add(new SimpleGrantedAuthority(perm.getName()));
		}
		return new User(username, userInfo.getPassword(), auth); //유저 정보 리턴
    //User는 UserDetails의 구현체중 하나이다.
	}

	private UserInfo findUserInfo(String username) {
		return userMap.get(username);
	}

	private List<UserPermission> loadPermission(String username) {
		return permMap.get(username);
	}

	private Map<String, UserInfo> userMap = new HashMap<>();
	private Map<String, List<UserPermission>> permMap = new HashMap<>();

	public CustomUserDetailsService() { //생성자
		userMap.put("system", new UserInfo("system", "시스템", "sys"));
		permMap.put("system", Arrays.asList(new UserPermission(1L, "SYSTEM_USER")));
	}
}
```
* CustomUserDetailsService 클래스를 추가해줬으면 밑에와같이 xml을 추가해준다.

```xml
<bean id="costomeUserDetailsService" class="com.wan.basis.CustomUserDetailsService"></bean>

<security:authentication-manager alias="authenticationManager">
  <security:authentication-provider user-service-ref="customeUserDetailsService">
  </security:authentication-provider>
</security:authentication-manager>
```
* 커스터마이징한 클래스를 추가해준다.


## 커스텀 AuthenticationProvider 구현하기
사용자 정보 조회뿐만 아니라 암호 비교까지 직접 구현해야한다면 AuthenticationProvider을 구현해야한다.
```java
public interface AuthenticationProvider{
  Authentication authenticate(Authentication authentication) throws AuthenticationExcption;
  boolean supports(Class<?> authentication);
}
```
* AuthenticationProvider의 supports메소드는 해당타입으로 인증여부를 확인 할 수 있다.
* authenticate 메소드는 인증처리를 지원하지않는다면 null을 리턴,인증실패시는 exception, 인증에 성공하면 authentication 객체 리턴

```java
public class CustomAuthenticationProvider implements AuthenticationProvider {

	@Override
	public Authentication authenticate(Authentication authentication) throws AuthenticationException {
		UsernamePasswordAuthenticationToken authToken = (UsernamePasswordAuthenticationToken) authentication;

		UserInfo userInfo = findUserById(authToken.getName());
		if (userInfo == null) {
			throw new UsernameNotFoundException(authToken.getName());
		}

		if (!matchPassword(userInfo.getPassword(), authToken.getCredentials())) {
			throw new BadCredentialsException("not matching username or password");
		}

		List<GrantedAuthority> authorities = getAuthorities(userInfo.getId());
		return new UsernamePasswordAuthenticationToken(
				new UserInfo(userInfo.getId(), userInfo.getName(), null),
				null,
				authorities);
        //인증된 객체에대한 정보를 리턴해준다.
	}

	private UserInfo findUserById(String id) {
		return userMap.get(id);
	}

	private List<GrantedAuthority> getAuthorities(String id) {
		List<UserPermission> perms = permMap.get(id);
		if (perms == null)
			return Collections.emptyList();

		List<GrantedAuthority> authorities = new ArrayList<>(perms.size());
		for (UserPermission perm : perms) {
			authorities.add(new SimpleGrantedAuthority(perm.getName()));
		}
		return authorities;
	}

	private boolean matchPassword(String password, Object credentials) {
		return password.equals(credentials);
	}

	@Override
	public boolean supports(Class<?> authentication) {
		return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);
	}

	private Map<String, UserInfo> userMap = new HashMap<>();
	private Map<String, List<UserPermission>> permMap = new HashMap<>();

	public CustomAuthenticationProvider() {
		userMap.put("cron", new UserInfo("cron", "스케줄러", "cronpw"));
		permMap.put("cron", Arrays.asList(new UserPermission(1L, "SCHEDULER")));
	}

}

```
* authenticate()메서드는 인증에 성공할경우 UsernamePasswordAuthenticationToken을 반환한다.

```xml
<bean id="customeAuthenticationProvider" class="com.wan.basis.customprovider.CustomAuthenticationProvider"></bean>
<security:authentication-manager alias="authenticationManager">
  <security:authentication-provider ref="customeAuthenticationProvider">
  </security:authentication-provider>
</security:authentication-manager>
```
* customeAuthenticationProvider을 추가해주면된다.


##
결론

1. 스프링은 크게 2가지 인증/인가 처리를 도와준다.
2. o.s.security.core.Authentication은 시큐리티에서 접근한 외부에서 접근한 정보를 보관하는 역할을한다.(이름,권한,인증여부등을 조회함)
3. 이러한 Authentication 정보는 o.s.s.core.context.SecurityContextHolder안에서 가지고온다.
4. 그리고 Authentication의 정보는 최초 SecurityContextHolder 맨처음에 접근하는 세션이 이곳에 먼저 저장되어져 위에와같은 로직이 진행되는 것이다.
    * 이것들이 필터체인에 순서대로적용된다. 이순서가 필터중에서 가장먼저 실행됨.
5. 최종적으로 SecurityContextHolder.getContext().getAuthentication()으로 유저 정보를 불러오게 된다.
5. 스프링 시큐리가 제공해주는 authentication-provider을 이용해 인증을 진행하게되는데 유저 인증은 UserDetailsService에 UserDetails객체를 이용해 인증을 진행하게 된다.
    * <jdbc-user-service>을 사용하게되면 인증을 알아서 진행하는듯, 단 유저정보를 가지고올수있는 제약이걸림 사실상 사용못함.
6. 그렇게 UserDetailsService에서 데이터를 가지고와서 DaoAuthenticationProvider에서 인증과정을 거친다.(UserDetailsService의 loadUserByUsername메소드를 이용함)
7. 만약에 UserDetailsService를 커스텀 한다면 loadUserByUsername에서 회원정보를 꼭리턴해줘야한다. 왜냐하면 아까 말했듯이 이것을 통해 인증을 진행하기때문이다.





* 직접아이디 비번을 대조해야한다. 내가 직접.
* 왜냐하면 이걸로 유저 인증을해서 컨텍스트에 넣어야하니까.
* 유저정보를 불러오려면 UserDetailsService에서 내가 직접구현해야한다. 쿼리까지 그러면 테이블명과 필드명등을 추가하거나 뺄 수 있다.
* jdbcUserDetailsManager 구현체에 crud가 정해져있는데 이것은 아까 말햇듯이 <jdbc-user-service> 특화되어있기때문에 못쓴다.







## 프로젝트 실행 방법(Mac 기준)

```
$ git clone https://github.com/minwan1/securitybasis.git
eclipse - import - 실행

```


참고
* [Syaku(샤쿠)](http://syaku.tistory.com/286)
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /

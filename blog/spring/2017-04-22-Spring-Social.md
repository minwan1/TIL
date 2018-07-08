# Daily Coding 2017-04-22일 spring boot-social-facebook

## Spring social

Spring social 로그인은 두가지 구현 방법이 있다. Connect Framework/Provider Sign In 방법이 있다.
```
"/connection/facebook" post
"/signin/facebook" post
```
* Connect 방법은 타임라인에 글을 보거나 쓰는용도로 많이쓰인다.
* signin은 일반적으로 로그인을 구현할때 많이 사용된다.

지금 구현하려는 방식은 signin방식이다.
먼저 signin방식을 사용하기위해서 ProviderSignInController을 알아야한다.
ProviderSignInController는 컨트롤러로 구성되어있다. /signin/{provider} (provider는 facebook/twitter etc)
예를들어 /signin/facebook을 보내주게되면 컨트롤러에서 facebook맞는 인증처리 작업을 수행하게된다.

그리고 Spring social에서 지정해준 table을 사용해야한다.
```sql
create table UserConnection (userId varchar(255) not null,
	providerId varchar(255) not null,
	providerUserId varchar(255),
	rank int not null,
	displayName varchar(255),
	profileUrl varchar(512),
	imageUrl varchar(512),
	accessToken varchar(512) not null,
	secret varchar(512),
	refreshToken varchar(512),
	expireTime bigint,
	primary key (userId, providerId, providerUserId));
create unique index UserConnectionRank on UserConnection(userId, providerId, rank);
```


#### SocialConfigurerAdapter,SocialConfigurer
spring social 기본 설정을 해주는 클래스를 구현해줘야한다. SocialConfigurerAdapter 추상클래스를 extends해서 사용해도 되고 SocialConfigurer interface를 구현해도 무방하다.

#### ConnectionSignUp
이 interface는 유저가 처음 로그인할때 어플리케이션(자신이 만들고있는)에 회원가입을 구현할수있는 곳이다. provider에서 로그인이 성공적으로 진행하면 Userconnection테이블에서 현재 이페이스북으로 가입한유저가있는지 providerId,providerUserId 을통해 검색한다. 만약 Userconnection테이블에서 유저 정보가 없다면 ConnectionSignUp의 execute(Connection<?> connection) 메소드가 호출된다. 이메소드에서 provider에서 제공해주는 유저의 정보을 얻어 올수 있다. 첫번째 로그인이후에는 userconnection테이블에서 유저을 찾게되면 이메소드는 더이상 호출되지 않는다.

#### SignInAdapter
provider측에서 인증이 끝난후에 userconnection에 정보가 저장된후에 구현해야할 인터페이스이다. SignInAdapter의 signIn메소드는 어플리케이션의 로그인 로직을 구현하여 로그인 기능을 구현하면된다.


#### 메이븐 추가

```xml
<!-- Spring Social -->
<dependency>
	<groupId>org.springframework.social</groupId>
	<artifactId>spring-social-web</artifactId>
	<version>1.1.1.RELEASE</version>
</dependency>

<!-- Spring Social Facebook -->
<dependency>
	<groupId>org.springframework.social</groupId>
	<artifactId>spring-social-facebook</artifactId>
	<version>2.0.3.RELEASE</version>
</dependency>
```

#### Configuration
```java
@Configuration
public class SocialConfig extends SocialConfigurerAdapter{
	@Autowired
	private Environment environment;
	@Autowired
	private DataSource dataSource;
	@Autowired
	private UserRepository userRepository;

	@Bean
	public SignInAdapter signInAdapter() {
		return new PostSocialSignInAdapter(userRepository);
	}

	@Override
	public UsersConnectionRepository getUsersConnectionRepository(ConnectionFactoryLocator connectionFactoryLocator) {
		JdbcUsersConnectionRepository repository = new JdbcUsersConnectionRepository(dataSource,
				connectionFactoryLocator, Encryptors.text(environment.getProperty("social.security.encryptPassword"),
						environment.getProperty("social.security.encryptSalt")));
		repository.setConnectionSignUp(new SocialImplicitSignUp(userRepository));
		return repository;
	}
}
```
JdbcUsersConnectionRepository Userconnection테이블과 연결되어지는 spring social로부터 사용되어지는 DAO이다. 어플리케이션이 사용하는 데이터베이스를 연결하려면 필수로 datasource 등을 생성자로 넣어줘야한다. 첫번쨰로는 datasource을 2번째인자로는 provider들을 관리해주는 connectionFactoryLocator을 넣는다. 3번째로는 암호화방식을 넣어준다. Encryptors.noOpText()로 설정하게되면 평문으로 디비에 정보가 저장된다. 만약 provider들을 더 추가하고싶은면 addConnectionFactories 메소드를 추가해서 오버라이딩해서 provider들을 추가해주면 된다. 솔직히 전체적인 로직을 추가하면 provider들을 추가하는건 어렵지않다.

```java
public List<String> findUserIdsWithConnection(Connection<?> connection) {
  ConnectionKey key = connection.getKey();
  List<String> localUserIds = jdbcTemplate.queryForList("select userId from " + tablePrefix + "UserConnection where providerId = ? and providerUserId = ?", String.class, key.getProviderId(), key.getProviderUserId());		
  if (localUserIds.size() == 0 && connectionSignUp != null) {
    String newUserId = connectionSignUp.execute(connection);
    if (newUserId != null)
    {
      createConnectionRepository(newUserId).addConnection(connection);
      return Arrays.asList(newUserId);
    }
  }
  return localUserIds;
}
```
위는 spring-social 라이브러리 내부인데 providerId = ? and providerUserId = ? 을 통해 userconnection에서 유저정보를 불러와 유저정보가없다면 connectionSignUp을 타게된다. 그렇게되면 아래의 클래스가 실행된다. 그리고 provider에서 보내준 유저정보를 통해 어플리케이션의 회원가입처리를 해준다. 그다음 return 해준값으로 userconnection테이블에 userid에 값이 저장 된다.
```java
public class SocialImplicitSignUp implements ConnectionSignUp {
	private UserRepository userRepository;

	public SocialImplicitSignUp(UserRepository userRepository){
		this.userRepository = userRepository;
	}

	@Override
	public String execute(Connection<?> connection) {
		UserProfile profile = connection.fetchUserProfile();
		User user = new User(profile.getFirstName() + " " + profile.getLastName(), profile.getEmail(), "N/A",
				connection.getImageUrl());
		userRepository.save(user);
		return user.getEmail();
	}
}
```
회원가입처리가 끝나고 밑에 로그인 어댑터를 실행하게된다. security를 사용하면 security로그인처리를 하면되고, 아니라면 아래와같이 로그인 처리를 하면된다.
```java
public class PostSocialSignInAdapter implements SignInAdapter {
	private UserRepository userRepository;

	@Inject
	public PostSocialSignInAdapter(UserRepository userRepository) {
		this.userRepository = userRepository;
	}

	@Override
	public String signIn(String localUserId, Connection<?> connection, NativeWebRequest request) {
		UserProfile profile = connection.fetchUserProfile();
		User user = userRepository.findByEmail(profile.getEmail());
		HttpServletRequest req = ((ServletWebRequest) request).getRequest();
		req.getSession().setAttribute("loggedUser", user);
		return "/"; // will return to postSignInUrl 로그인한이후에 이동될 사이트인듯
	}

}

```
예제에서는 아래의 테이블도 추가해줘야한다.
```sql
CREATE TABLE `user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `email` varchar(128) NOT NULL,
  `name` varchar(128) NOT NULL,
  `password` varchar(64) NOT NULL,
  `thumbnail_url` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;
```

### 결론
1. SocialConfigurerAdapter을 통해 기본 spring-social 설정
2. ConnectionSignUp 회원가입 구현
3. SignInAdapter 웹어플리케이션 로그인 구현



## [깃허브 소스](https://github.com/minwan1/Spring-social-facebook)

참고
* [JavaCents](http://javacents.com/adding-social-sign-in-to-a-spring-boot-app/)

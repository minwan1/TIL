# Daily Coding 2017-04-08일 spring boot-JPA DB연결처리

## Spring social

Spring social은 두가지 방법이 이있다. Connect Framework/Provider Sign In 방법이 있다.
```
"/connection/facebook" post
"/signin/facebook" post
```
* Connect 방법은 타임라인에 글을 보거나 쓰는용도로 많이쓰인다고
* signin은 일반 페이스북에 인증/인가와 성공한 비슷한 효과를 낸다.

내가 구현하는 방식은 signin방식이다.
먼저 signin방식을 사용하기위해서 ProviderSignInController을 알아야한다.
ProviderSignInController는 컨트롤러로 구성되어있다. /signin/{provider} (provider는 facebook/twitter etc)

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

#### JdbcUsersConnectionRepository
Userconnection테이블과 연결되어지는 spring social로부터 사용되어지는 DAO이다.


#### ConnectionSignUp
이 interface는 유저가 처음 로그인할때 어플리케이션(자신이 만들고있는)에 회원가입을 구현할수있는 곳이다. provider에서 로그인이 성공적으로 진행하면 Userconnection테이블에 프로바이더유저정보가 추가된다. 그리고 ConnectionSignUp의 execute(Connection<?> connection) 메소드가 호출된다. 이메소드에서 provider에서 제공해주는 유저의 정보을 얻어 올수 있다. 첫번째 로그인이후에는 userconnection테이블에서 유저을 찾게되면 이메소드는 더이상 호출되지 않는다.

#### ConnectionSignUp
provider측에서 인증이 끝난후에 userconnection에 정보가 저장된후에 구현해야할 인터페이스이다. ConnectionSignUp의 signIn메소드는 어플리케이션의 로그인 로직을 구현하면된다.


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

#### 3개의  인터페이스 구현
* SocialConfigurerAdapter Interface
	* getUsersConnectionRepository 메소드 오버라이딩을 통한 Datasource 처리
	* addConnectionFactories 메소드 오버라이딩을 통한 소셜로그인처리
* ConnectionSignUp Interface
	* 처음 소셜로그인시에 userconnection에 정보가없으면 테이블에 데이터를 넣고 이곳에 execute메소드 실행, 이곳에서 어플리케이션 회원가입처리
* SignInAdapter Interface
	* provider측에서 인증이 끝난후에 userconnection에 정보가 저장된후에 구현해야할 인터페이스이다. ConnectionSignUp의 signIn메소드는 어플리케이션의 로그인 로직을 구현하면된다.


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


543500195237-hiedj86ed53crcf3ra1i09t606tguc2i.apps.googleusercontent.com
uL_-KUNXPXSTfzK3M6qSH6ZS


* [내일의 나를 만드는 방법](http://choong0121.tistory.com/entry/DataSource-란)
* [갱짱.study](http://gangzzang.tistory.com/entry/Java-내부클래스inner-class)
* [JavaCents](http://javacents.com/adding-social-sign-in-to-a-spring-boot-app/)

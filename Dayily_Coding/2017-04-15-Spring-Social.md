# Daily Coding 2017-04-11일 oauth 2.0



social 워크 프롤우
```java
@RequestMapping(value="/{providerId}", method=RequestMethod.POST)
public RedirectView signIn(@PathVariable String providerId, NativeWebRequest request)
```
-
페이스북에서 로그인상태이거나 로그인되면 아래 메소드탐(allow버튼 클릭안해도 탄다.)
```java
@RequestMapping(value="/{providerId}", method=RequestMethod.GET, params="code")
public RedirectView oauth2Callback(@PathVariable String providerId, @RequestParam("code") String code, NativeWebRequest request) {
  try {
    OAuth2ConnectionFactory<?> connectionFactory = (OAuth2ConnectionFactory<?>) connectionFactoryLocator.getConnectionFactory(providerId);
    Connection<?> connection = connectSupport.completeConnection(connectionFactory, request);
    return handleSignIn(connection, connectionFactory, request);
  } catch (Exception e) {
    logger.error("Exception while completing OAuth 2 connection: ", e);
    return redirect(URIBuilder.fromUri(signInUrl).queryParam("error", "provider").build().toString());
  }
}
```
- 위에 handleSignIn 메소드를 타게 되면 되면 userconnection의 providerId, providerUserId 정보가없으면 유저가 밑에처럼  구현해놓은 회원가입창으로 넘어가진다.(user테이블에서 유저정보 생성후 userconnection에 정보생성됨)

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
- 위에 유저 테이블에 정보 생성후 이 중간단계에서 userconnection정보가 생성됨
- 아래와같이 userconnection에서 유저정보를 조회하고 정보가없으면 userconnection에 정보를 생성하게 된다.
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




- 그다음에 이제 아래와같이 유저가 구현해놓은 로그인 로직을통해 로그인됨.
```java
@Override
public String signIn(String localUserId, Connection<?> connection, NativeWebRequest request) {
  UserProfile profile = connection.fetchUserProfile();
  User user = userRepository.findByEmail(profile.getEmail());
  HttpServletRequest req = ((ServletWebRequest) request).getRequest();
  req.getSession().setAttribute("loggedUser", user);
  return "/"; // will return to postSignInUrl 로그인한이후에 이동될 사이트인듯
}

```
그동안의 에러가 버전 문제였음,


참고
* [TEBICA STORY](http://earlybird.kr/1584)

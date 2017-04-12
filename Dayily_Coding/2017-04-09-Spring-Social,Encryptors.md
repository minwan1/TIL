# Daily Coding 2017-04-09일 spring boot-JPA

```java
@Override
public UsersConnectionRepository getUsersConnectionRepository(ConnectionFactoryLocator connectionFactoryLocator) {
	JdbcUsersConnectionRepository repository = new JdbcUsersConnectionRepository(dataSource,connectionFactoryLocator, Encryptors.text(environment.getProperty("social.security.encryptPassword"),environment.getProperty("social.security.encryptSalt")));
	repository.setConnectionSignUp(new SocialImplicitSignUp(userRepository));
	return repository;
}
```

SocialConfigurerAdapter에서 에서 오버라이딩하는 메소드 중에 getuserConnectironRepository 가 있다. Spring social에서 userconnection에 접근하기 위해 사용되는 메소드인데 여기서 JdbcUsersConnectionRepository을 return해준다. 이클래스에 인자로는 Datasource, connectionFactoryLocator, 3번째가 org.springframework.security.crypto.encrypt.TextEncryptor textEncryptor 인데 이것이 이제 유저정보를 userconnection에 저장할떄 암호화를 설정해주는것이다.  Encryptors.noOpText()로 저장하게되면 userconnection에는 평문으로 provider로부터 받은 유저의 정보를 평문으로 저장헤게 된다.

### Encryptors클래스의 메소드 DOC

![](http://i.imgur.com/artX9I5.png)
1. noOpText : 어떠한 암호화도 하지않는다.
2. 표준 비밀번호기반 암호화를 이용하여서 쿼리가느한 문자열을 위한 암호생성기를 만든다.
3. 256bit의 AES 암호화를 이용하여서 표준 비밀번호 기반의 byte 암호 생성기를 만든다.
4. 표준 비밀번호기반 암호생성기를 만든다.

스프링 암호생성기에서는 AES 256 bit 방식을 쓴다는데, 자바에서는 128 bit 까지만 지원된다. 그것을 해결하기위해서는 기존의 jar파일을 덮어써줘야한다.
[AES256 암호화시 java.security.InvalidKeyException: Illegal key size 해결 방안](https://dukeom.wordpress.com/2013/01/03/aes256-%EC%95%94%ED%98%B8%ED%99%94%EC%8B%9C-java-security-invalidkeyexception-illegal-key-size-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EC%95%88/)
이사이트를 통해 진행하면 된다.










참고
* [아둔한사의 개발공부와 테스트^^](http://adunhansa.tistory.com/194 )
* [http://docs.spring.io](http://docs.spring.io/autorepo/docs/spring-security/3.2.0.RELEASE/apidocs/org/springframework/security/crypto/encrypt/TextEncryptor.html)

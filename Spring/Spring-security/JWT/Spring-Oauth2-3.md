##
앞에 부분은 API서버와 OAuth2 서버가 하나의 웹 어플리케이션에서 같이 올라가는 형태를 취하고 있다. 하지만 실제 서비스를 하기 위해서는  각각 다른 인스턴스 형태로 서비스를 해야한다.


```java
@EnableResourceServer
@EnableAuthorizationServer
@SpringBootApplication
public class DemoApplication extends ResourceServerConfigurerAdapter {
...
}
```

위에 EnableResourceServer, EnableAuthorizationServer 어노테이션으로 API 서버와 인증서버를 한곳에서 이용가능하게 했다. 이 두개를 나누면 아래와같이 구성 할 수 있다.

API서버
```java
@Configuration
@EnableResourceServer
class ResourceServerConfiguration extends ResourceServerConfigurerAdapter {
...
}
```


AuthorizationServer
```java
@Configuration
@EnableAuthorizationServer
class AuthorizationServerConfiguration {
...
}
```

한곳에 있던 설정을 두개의 클래스로 나누었다. 그리고 이전에 설정한 Jdbc TokenSotre부분은 AuthorizationServer에서만 사용하게된다. API서버는 OAuth2 서버의 Token조회 API를 통해서 토큰을 조회하게 된다. 아래는 어떻게 API서버가 AuthorizationServer에서 맞는 토큰인지 인증하는것을 설명하는 소스이다.

AunthorizationServer
```java
@EnableAuthorizationServer
@SpringBootApplication
public class OAuth2Application {
   @Bean
   public TokenStore jdbcTokenStore(DataSource dataSource) {
      return new JdbcTokenStore(dataSource);
   }
// ...
}
```

API서버에서 Token의 정보를 가져가기 위한 요청을 활성화 시켜줘야한다.
(기본은 비활성화 되어 있다. OAuth2 서버와 API서버가 같이 있을 때에는 필요 없는 부분이기 때문이다.)

AuthorizationServer 의 application.properties파일설정을 수정해줘야한다.
```
# AuthorizationServer 서버의 application.yml
...
# Token 정보를 API(/oauth/check_token)를 활성화 시킨다. ( 기본은 denyAll )
security.oauth2.authorization.check-token-access: isAuthenticated()
```

API서버 설정 부분
```java
@EnableResourceServer
@SpringBootApplication
public class ApiApplication {

   @Bean
   public ResourceServerConfigurerAdapter resourceServerConfigurerAdapter() {      
      //...
   }
//...
}
```

API 서버에서 token 체크 URL을 표시해줘야한다.
```
# OAuth2 서버에서 기본적으로 Token정보를 받아오는 URL
security.resource.token-info-uri: http://localhost:8080/oauth/check_token
```

테스트방법

```
$ curl -F "grant_type=client_credentials" -F "scope=read" "http://foo:bar@localhost:8080/oauth/token"
```

```
{
  "access_token":"6dfb79ab-46cc-49ad-9b46-b4da66e9e103",
  "token_type":"bearer",
  "expires_in":42760,
  "scope":"read"
}
```

```
curl -H "Authorization: Bearer 6dfb79ab-46cc-49ad-9b46-b4da66e9e103" "http://localhost:8081/members"
```

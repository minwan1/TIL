다음은 스프링 Oauth 동작에대해 좀더 상세히 알아볼것이다

@EnableResourceServer
API 서버를 Oauth2 인증받게 만들도록하고 자원서버를 이용 가능하게해준다. 기본적으로 모든 API의 모든 요청을 Oauth2인증을 받게한다. 자원서버에 대해 좀더 세부적인 설정을 하기위해서는 ResourceServerConfigurerAdapter 클래스를 상속받아 구현하면 된다.


```java

@Configuration
@EnableResourceServer
public class ResourceServerConfiguration extends ResourceServerConfigurerAdapter {

        @Override
      public void configure(ResourceServerSecurityConfigurer resources) {

      }

      @Override
      public void configure(HttpSecurity http) throws Exception {

      }

}
```
확장을 하지 않고 기본 옵션은 모든 API는 인증이 필요한 형태로 설정된다. Oauth2인증을 확인하기 위하여 Oauth2 토큰 스토어을 지정해야하며, 직접 설정을 하지 않았으면, 인메모리에 형태로 사용되어진다. 만약 Oauth2서버와 api서버가 같은곳에서  처리되는 형태라면 같은 기본적으로 인메모리 토큰 스토어를 서로 공유하게 된다.


@EnableAuthorizationServer
@EnableResourceServer 어노테이션이 자원서버의 설정을 활서화시켰다면 이것은 AuthorizationServer를 활성화 시킨다는 의미의 애노테이션이다. OAuth2인증을 위한 액세스 토큰, 리프레시토큰발급과 발급된 토큰을 통한 Oauth2인증등 핵심기능을 활성화 시켜준다.

내부적으로 /oauth/token,oauth/authorize등의 기본적인 토큰 얻기, 인증의 URI를 가지고있다.

```java

@Configuration
@EnableAuthorizationServer
public class DemoApplication extends AuthorizationServerConfigurerAdapter {
// ...
   @Override
   public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
// OAuth2 인증서버 자체의  보안 정보를 설정하는 부분
   }

   @Override
   public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
// Client 에 대한 정보를  설정하는 부분
   }

   @Override
   public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
// OAuth2 서버가 작동하기 위한 Endpoint에 대한 정보를 설정
   }
   // ...
}
```

위에 내용들을 구현하지 않으면 프로덕트용으로는 사용하지 못할것이다. 그이유를 한번나열할것이다.
1. 기본적으로 설정하지 않으면 인증, 권한, 토큰, 권한 코드, 클라이언트, 등 영속성이 필요한 정보를 메모리에서 관리해야한다. 이렇게 되면 두개이상의 서버를 같이 운영해서 사용할 수 가 없다.
2. 인증형태를 모두 Basic형태로 하기 때문에 실제로 사용하기에는 부족한다.
3. 인증서버와 API서버가 같이 존재하기 때문에 api서버를 추가하거나 변경할때 인증서버도 같이 종료되는 문제가 있다.

첫 번째로 변경해볼 부분은 초큰(AccessToken, RefreshTokn)을 메모리가 아니라 외부에 저장하는 부분을 진행할것이다.

토큰을 저장하는 곳 이름이 TokenStore라고 한다.TokenStore명칭 자체가 토큰을 저장하는 저장소를 의미하기도 하지만 내부에서 사용하는 인터페이스 명을 지칭하기도 한다. 지금까지는 TokenStore는 JVM내부 메모리를 사용한 저장소를 사용하는 형태였기때문에 테스트 서버를 재부팅할 때마다 토큰 정보가 초기화 된다. 그래서 발급된 AccessToken을 가지고 API접근 테스트할 때 조차 많이 불편했다. 이토큰 정보 부분을 외부 저장소로 저장하는 작업을 해보자.

#### Spring security Oauth2의 기본 TokenStore종류
1. org.springframework.security.oauth2.provider.token.store.InMemoryTokenStore
- JAVA 내부에서 Map, Queue 구조의 메모리를 사용한 저장소 (기본)

2. org.springframework.security.oauth2.provider.token.store.JdbcTokenStore
- JDBC를 사용해서 DB에 저장하는 방식

3. org.springframework.security.oauth2.provider.token.store.JwtTokenStore
- 외부 저장소가 아닌 JWT(Token에 JSON 정보를 인코딩 하여 저장하는 방식)를 이용하는 방식

4. org.springframework.security.oauth2.provider.token.store.redis.RedisTokenStore
- Redis ( Key Value 저장소 )에 Token 정보를 저장하는 방식

기본적인 설정은 InMemoryTokenSotre를 사용하기 때문에 서버가 리붓되는 동시에 데이터가 초기화된다. 그래서 먼저 JdbcTokenStore를 사용하여 토큰을저장해보자.

#### JDBCTokenSotre를 구현하기 위한 사전 준비사항
1. Oauth2 토큰을 저장하기 위한 DB스키마 생성
Oauth2 토큰을 저장하기 위한 [DB스키마 생성](https://github.com/spring-projects/spring-security-oauth/blob/master/spring-security-oauth2/src/test/resources/schema.sql)을 해야한다. 이것은 스프링 Security Oauth에서 제공을 해주고있다. 단 H2 DB의 스키마 형ㅇ태만 제공하기 때문에 만약 Mysql, 오라클등 다른 DB를 사용할 때에는 그 DB에 맞도록 적절하게 스키마를 변경해야한다. 대신 칼럼명을 변경하면 귀찮아진다. 위의 스키마 데이터를 복사하여 resource/ 디렉터리 아래에 schema.sqml 파일을 생성시켜 스키마를 붙여 넣기한다.("schema.sql 파일명과 동일하게 기술해야 따로 설정없이 관련 서버 시작시 자동으로 ddl쿼리르 실행한다.")

2. 내장 H2 DB 웹 콘솔을 접속하기 위한 셋팅
H2 DB에 토큰이 저장되는지 스키마가 생성되었는지 데이터 확일할 수 있는 내장 H2 DB의 웹콘솔을 활성화시켜야한다. 기본적으로는 비활성화가 되있다. 스프링 부트 1.3이후부터는 아래와같이 설정만 해도 바로 H2 console에 접속 가능하다.
```
# resources/application.properties
spring.h2.console.enabled = true
spring.h2.console.path=/h2-console
```
위와같이 설정해주면 Spring boot에서 H2가 활성화 된다. 그리고 http://localhost:8080/h2-console << 로 접속하게되면 JDBC URL을 아래와같이 변경한다음에 접속해야한다.
```
jdbc:h2:mem:testdb
```
이값이 스프링 부트안에서 H2데이터 소스를 접근할 수 있는 기본 접속 정보이다. 스프링 시큐리티를 사용하고 있기 때문에 헤더부분이 충돌나서 화면이 보이지않는다면 security 설정이필요하다.


3. JdbcTokenStore 설정
그리고 이제 JdbcTokenStore 아래와같이 빈을 등록해주면 된다.
```java
@Bean
pblic TokenStore JdbcTokenStore(Datasource datasource){
  return new JdbcTokenStore(dataSource);
}
```

이부분만 추가되면 DB에 토큰을 저장할 준비가 완료되었다. 이제 h2디비에 토큰이 저장되는것을 확인할수있을것이다. 하지만 실제 OAUTH_ACCESS_TOKEN에 발급된 토큰과 비교해보면 발급된 토큰과 다른것을 알 수 있다. 이것은 비번과 마찬가지로 암호되어 토큰이 암호화되어 저장되기 때문이다.

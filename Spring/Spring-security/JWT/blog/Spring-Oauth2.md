## OAuth2인증이란 ?
[OAuth2](https://minwan1.github.io/2018/02/24/2018-02-24-OAuth/)


## Spring으로 OAuth2구현
Spring Oauth2를 사용하기위해서는 먼저 아래와같이 데이터베이스를 구성해야한다.

[Mysql 구성](https://github.com/spring-projects/spring-security-oauth/blob/master/spring-security-oauth2/src/test/resources/schema.sql)
### AuthorizationServer 구성
```java
@Configuration
@EnableAuthorizationServer
public class AuthServerOAuth2Config
  extends AuthorizationServerConfigurerAdapter {

    @Autowired
    @Qualifier("authenticationManagerBean")
    private AuthenticationManager authenticationManager;

    @Override
    public void configure(
      AuthorizationServerSecurityConfigurer oauthServer)
      throws Exception {
        oauthServer
          .tokenKeyAccess("permitAll()")
          .checkTokenAccess("isAuthenticated()");
    }

    @Override
    public void configure(ClientDetailsServiceConfigurer clients)
      throws Exception {
        clients.jdbc(dataSource())
          .withClient("sampleClientId")
          .authorizedGrantTypes("implicit")
          .scopes("read")
          .autoApprove(true)
          .and()
          .withClient("clientIdPassword")
          .secret("secret")
          .authorizedGrantTypes(
            "password","authorization_code", "refresh_token")
          .scopes("read");
    }

    @Override
    public void configure(
      AuthorizationServerEndpointsConfigurer endpoints)
      throws Exception {

        endpoints
          .tokenStore(tokenStore())
          .authenticationManager(authenticationManager);
    }

    @Bean
    public TokenStore tokenStore() {
        return new JdbcTokenStore(dataSource());
    }
}

```
* tokenstore는 token을 영속성 시킨다.
* client grant_type을 implicit,password,authorization_code,refresh_token를 등록하낟.
* password를 사용하기위해서 authenticationManager 빈을 등록하는게 필요하다.



### ResourceServer 구성

### 토큰 Validation 방법
먼저 자원서버에 접근하려면 인증서버로 부터 발급되어진 토큰 인증을 해야한다. 인증을 하기위해서는 크게 2가지 방법이있는데 디비를 공유하는 방법과 URL을 호출하여 토큰이 유효한지 검사하는 방법이 있다. 아래는 그 2가지 방법에대한 내용이다.

1. TokenStore를 이용한 데이터베이스 공유방법
아래와같이 TokenStore를 빈으로 등록하면 tokenStore로 인증서버와 자원서버의 토큰이 공유 되어질것이다. 이것을 통해 토큰의 인증 처리를 한다.
```java
@Bean
public TokenStore tokenStore() {
    return new JdbcTokenStore(dataSource());
}
```
2. API호출을 통한 토큰 Validation 방법
아래는 RemoteTokenServices를 이용하여 URL을 호출하는 방식이다.
```java
@Primary
@Bean
public RemoteTokenServices tokenService() {
    RemoteTokenServices tokenService = new RemoteTokenServices();
    tokenService.setCheckTokenEndpointUrl(
      "http://localhost:8080/spring-security-oauth-server/oauth/check_token");
    tokenService.setClientId("fooClientIdPassword");
    tokenService.setClientSecret("secret");
    return tokenService;
}

```








## client password 방식으로 OAuth2구현

```java
@EnableAuthorizationServer // OAuth2 권한 서버
@EnableResourceServer  // API 서버 인증(또는 권한 설정
@Configuration
public class ResourceServerConfigurerAdapterImpl extends ResourceServerConfigurerAdapter {

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.headers().frameOptions().disable();
        http.authorizeRequests()
                .antMatchers("/members", "/members/**").access("#oauth2.hasScope('ROLE_USER')")
                .anyRequest().authenticated();
    }

    //authorization server
    @Bean
    public TokenStore JdbcTokenStore(@Qualifier("dataSource") DataSource dataSource) {
        return new JdbcTokenStore(dataSource);
    }

}
```
client password로 접근하기
```
curl -F "grant_type=client_credentials" -F "scope=read" "http://foo:bar@localhost:8080/oauth/token"
```







## JWT란

jwt단점은 토큰에 유저정보를 담기떄문에 토큰의 길이가 커질수가있다는것이다. 또다른단점은 쉽게 취소할수없다. refesh_token으로 취소해야한다.

```
<!-- oauth2-server/pom.xml, api-server/pom.xml -->
<dependency>
   <groupId>org.springframework.security</groupId>
   <artifactId>spring-security-jwt</artifactId>
</dependency>
```

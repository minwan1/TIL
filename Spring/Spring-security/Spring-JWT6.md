## Medium JWT

JWT는 액세스토큰을 저장할필요가없고, 다루기 까다로운 세션을 신경쓰지 않아도된다.
### 1.Configure Spring Security
Spring boot의 자동구성은 덕분에 최소환의 커스마이징만 하면된다. 우선 application properties를 설정해줘야한다.


```
security.oauth2.resource.filter-order=3

security.signing-key=MaYzkSjmkzPC57L
security.encoding-strength=256
security.security-realm=Spring Boot JWT Example Realm

security.jwt.client-id=testjwtclientid
security.jwt.client-secret=XY7kmzoNzl100
security.jwt.grant-type=password
security.jwt.scope-read=read
security.jwt.scope-write=write
security.jwt.resource-ids=testjwtresourceid
```


그이후에 security configuration class 를 설정해줘야한다.
```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

   @Value("${security.signing-key}")
   private String signingKey;

   @Value("${security.encoding-strength}")
   private Integer encodingStrength;

   @Value("${security.security-realm}")
   private String securityRealm;

   @Autowired
   private UserDetailsService userDetailsService;

   @Bean
   @Override
   protected AuthenticationManager authenticationManager() throws Exception {
      return super.authenticationManager();
   }

   @Override
   protected void configure(AuthenticationManagerBuilder auth) throws Exception {
      auth.userDetailsService(userDetailsService)
              .passwordEncoder(new ShaPasswordEncoder(encodingStrength));
   }

   @Override
   protected void configure(HttpSecurity http) throws Exception {
      http
              .sessionManagement()
              .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
              .and()
              .httpBasic()
              .realmName(securityRealm)
              .and()
              .csrf()
              .disable();

   }

   @Bean
   public JwtAccessTokenConverter accessTokenConverter() {
      JwtAccessTokenConverter converter = new JwtAccessTokenConverter();
      converter.setSigningKey(signingKey);
      return converter;
   }

   @Bean
   public TokenStore tokenStore() {
      return new JwtTokenStore(accessTokenConverter());
   }

   @Bean
   @Primary //Making this primary to avoid any accidental duplication with another token service instance of the same name
   public DefaultTokenServices tokenServices() {
      DefaultTokenServices defaultTokenServices = new DefaultTokenServices();
      defaultTokenServices.setTokenStore(tokenStore());
      defaultTokenServices.setSupportRefreshToken(true);
      return defaultTokenServices;
   }
}
```
@EnableWebSecurity : spring security 를 가능하게해주고 spring boot가 기본적인 설정을 하게해준다.

@EnableGlobalMethodSecurity: 컨트롤러 메소드에 접근하게 하기위해

**JwtTokenStore and JwtAccessTokenConverter beans** : JwtTokenStore빈은 authorization server에 구성되어져야한다. JwtAccessTokenConverter는 토큰을 디코드하기위해 자원서버, 인증서버 두곳에 존재해야한다. * 여기에서 양방향 서명키를 사용한다.

**UserDetailsService** : 이것은 스프링 시큐리티에서 기존에 구현하던 그 UserDetailsService의 인터페이스와 같이 유저를 조회하는 용도로 사용된다.

**Encoding** : SHA-256 이것은 암호 해쉬화를 위해 사용된다.

**Realm** : 제공자, 롤 유저로부터 시큐리솔루션을 정의하는것이다( 좀더 공부를해봐야할듯)


### 2. Configure Authorization Server

```java
@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

   @Value("${security.jwt.client-id}")
   private String clientId;

   @Value("${security.jwt.client-secret}")
   private String clientSecret;

   @Value("${security.jwt.grant-type}")
   private String grantType;

   @Value("${security.jwt.scope-read}")
   private String scopeRead;

   @Value("${security.jwt.scope-write}")
   private String scopeWrite = "write";

   @Value("${security.jwt.resource-ids}")
   private String resourceIds;

   @Autowired
   private TokenStore tokenStore;

   @Autowired
   private JwtAccessTokenConverter accessTokenConverter;

   @Autowired
   private AuthenticationManager authenticationManager;

   @Override
   public void configure(ClientDetailsServiceConfigurer configurer) throws Exception {
      configurer
              .inMemory()
              .withClient(clientId)
              .secret(clientSecret)
              .authorizedGrantTypes(grantType)
              .scopes(scopeRead, scopeWrite)
              .resourceIds(resourceIds);
   }

   @Override
   public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
      TokenEnhancerChain enhancerChain = new TokenEnhancerChain();
      enhancerChain.setTokenEnhancers(Arrays.asList(accessTokenConverter));
      endpoints.tokenStore(tokenStore)
              .accessTokenConverter(accessTokenConverter)
              .tokenEnhancer(enhancerChain)
              .authenticationManager(authenticationManager);
   }

}
```
OAuth2와 가장큰차이점은 JwtAccessTokenConverter가 추가 된것이다. JWT토큰 방식의 좋은 점은 db가 필요없다는것이다. 서명을 통한 인증만 하면된다.

@EnableAuthorizationServer: authorization을 이용가능하게한다.

**AuthorizationServerConfigurer** : 이 클래스의 존재여부로써 기존에 자동화된 설정이 꺼지고 Spring boot가 자동으로 위의 클래스에서 설정한대로 AuthorizationServerConfigurer 인터페이스가 구현현된다.

**Client id** : 이것도 좀 공부를 해봐야겟다

**Client secret** : 이게 아마 시그니처에 들어가는 서명인것같다.

**Grant type** : 좀더 스터디 필요

**scope** 일기전용과 쓰기 전용으로 정의한다.

**Resource Id** : 이것은 리소스 서버에도 기재되어야만한다.

**AuthenticationManager** : 유저의 인증을 처리한다.


**TokenEnhancerChain** :


### 3. Configure Resource Server

```java
@Configuration
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {
    @Autowired
    private ResourceServerTokenServices tokenServices;

    @Value("${security.jwt.resource-ids}")
    private String resourceIds;

    @Override
    public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
        resources.resourceId(resourceIds).tokenServices(tokenServices);
    }

    @Override
    public void configure(HttpSecurity http) throws Exception {
                http
                .requestMatchers()
                .and()
                .authorizeRequests()
                .antMatchers("/actuator/**", "/api-docs/**").permitAll()
                .antMatchers("/springjwt/**" ).authenticated();
    }
}
```

**@EnableResourceServer** : 자원 서브를 이용가능하게 해준다. 이것은 기본적으로 이것은 OAuth2를 통해 넘오는 요청들은 인증하는 필터를 만든다. 그리고 여기에서 application.properties에서 security.oauth2.resource.filter-order = 3  추가함으로써 필터를 추가할 수 있다. 여기에서 인증서버와 인가서버는 같은 토큰 서비스를 사용한다.

### step1 : 토큰생성
일반적인 토큰 생성 양식 URL이다
curl client:secret@localhost:8080/oauth/token -d grant_type=password -d username=user -d password=pwd

위 어플리케이션을 돌리려면 아래와같이 CURL을 호출하면된다.
curl testjwtclientid:XY7kmzoNzl100@localhost:8080/oauth/token -d grant_type=password -d username=john.doe -d password=jwtpass.

### RESTful API에 토큰을 이용해 접근하기
header에 Authorization:Bearer+token http://localhost:8080/springjwt/cities  호출하면 결과값을 받을 수 있다.




토큰 생성 첫 엔드 포인트 TokenEndpoint
JWT 전체적인순서
1. 기본적으로 베이직 Auth 진행(clientid : client secret)[베이직 auth filter]
2. oauth/token url로 이동
3. 유저 아이디비번 조회


[Medium](https://medium.com/@nydiarra/secure-a-spring-boot-rest-api-with-json-web-token-reference-to-angular-integration-e57a25806c50)





https://virgo47.wordpress.com/2014/07/27/restful-spring-security-with-authentication-token/

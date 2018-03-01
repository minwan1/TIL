## Oauth2 가이드

### Oauth 2 Provider 구현

Outh2에서 Provider 역할은 크게 인증서버, 자원서버로 나뉘어진다. 인증서버와 자원서버를 하나에 서버에서 관리할 수 도있고, 두개로 나뉘어 관리할 수 도있다. 이것은 선택사항이다. 토큰을 위한 요청들은 controller endpoint로부터 관리되어진다. 그리고 이런 보호되어진 리소스에대한 접근은 Spring security request filter들로부터 관리된다.

그리고 oauth2를 사용하기위해서는 다음 엔드포인터들이 spring security filter chain에서 요구되어진다.
* **AuthorizationEndpoint** 는 권한 요청을 위해 사용되어진다. Default URL: /oauth/authorize.
* **TokenEndpoint** 는 접근 토큰에대한 요청에 사용 되어진다. Default URL: /oauth/token.

그리고 다음은 자원 서버에서 구현되어야져할 필터이다.
*  OAuth2AuthenticationProcessingFilter 는 인증되어진 토큰의 요청을 위한 인증을 하기위한 필터이다.

모든 Oauth2 Provider(자원서버, authorization 서버등)은 @Configuration adapter들을 사용하여 간단하게 구성 할 수 있다.

### Authorization 서버 구성
권한 서버를 구성 할 때 클라이언트가 유저로부터 접근토큰(예 : authorize code, credential, refreshtoke)을 얻는 데 구별하기 위한 **grant type** 을 만들어야한다. Authorization 서버 구성은 유저의 상세정보, 토큰 서비스의 구현을 담당한다. 여기에서 유저들은 특정 권한에대한 토큰을 받게 구성 할 수 있다.

@EnableAuthorizationServer 는 AuthorizationServerConfigurer을 구현하을 위해 사용된다. 보통은 AuthorizationServerConfigurer을 구현하기위해  AuthorizationServerConfigurerAdapter를 사용한다. 그리고 이것은 Oauth2.0의 인증서버 구성을 의미한다.

다음 기능은 Spring으로부터 만들어지고 AuthorizationServerConfigurer로 전달되어 AuthorizationServerConfigurerAdapter 에서 우리가 커스터마이징해서 사용할 수 있다.

* ClientDetailsServiceConfigurer : grantType , scope 등 설정을 할 수 있다.
* AuthorizationServerSecurityConfigurer : 토큰 엔드포인트에 대한 시큐리티 제약조건 정의 가능
* AuthorizationServerEndpointsConfigurer : authenticationManager, accessTokenConverter설정등이 이루어진다.

Provider(Authrization 서버) 구성의 중요한 측면은 인증코드가 OAuth 클라이언트(프론트엔드)에게 전달되는것이다. 인증코드는 OAuth클라이언트가 유저를 인증 페이지로 이동시켜 유저를 인증 시킴으로써 인증 코드를 얻는다.


#### Configuring Client Details
ClientDetailsServiceConfigurer(AuthorizationServerConfigurer으로부터 콜백되어지는)은 클라이언트의 세부정보를 인메모리 또는 jdbc로 관리할 수 있다. 다음은 클라이언트의 중요한 속성들이다.

* clientId: (required) 클라이언트 ID
* secret: (required for trusted clients) 클라이언트의 시크릿
* scope: 클라이언트의 스코프 범위를 제한한다. undefined or empty 가능
* authorizedGrantTypes: 클라이언트가 사용하기 위해 인가되어진 권한. Default value is empty.
* authorities: 클라이언트에게 부여져있는 Authorities
Client details정보관리는 jdbc ClientDetailsService등으로 구현할 수 있다.


#### 토큰 관리
AuthorizationServerTokenServices는 OAuth 2.0토큰을 관리하는 역할을 한다.
* 액세스 토큰이 만들어질떄 나중에 access token을 허용하는 자원들이 참조할 수 있도록 저장되어야한다.

여기에서 JWT을 사용하기위해서는 JwtAccessTokenConverter 을 등록해줘야한다.

#### Grant Types



### Resource Server Configuration
자원서버는 Authorization Server와 분리해서 단독 자원서버로 사용하거나 Authorization서버와 같은 서버에서 사용할 수 도 있다. 그리고 이자원들은 OAuth2토컨으로부터 보호 된다. Spring OAuth 는 이것을 인증하는 필터를 제공하여 자원에 접근을 제어한다. 자원 서버의 기 능을 사용하기 위해서는 먼저 ResourceServerConfigurerAdapter를 상속하고 클래수이에 @EnableResourceServer을 설정해줌으로써 기능을 커스터마이징할 수 있다.

* tokenServices : 서비스토큰을 정의한 bean;
* resourceId : 자원 id

@EnableResourceServer는 OAuth2AuthenticationProcessingFilter 를 Spring Security filter chain에 추가한다.

ResourceServerTokenServices 설정은 ResourceServerTokenServices와 AuthorizationServer를 같이쓴다면 그냥 기본 DefaultTokenServices를 사용하면되독 아닌경우에는 ResourceServerTokenService를 구현해서 사용해야한다.

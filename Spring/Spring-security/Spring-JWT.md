기존 oauth2의 문제점

기존의 OAuth2의 단점은 API를 호출할때마다 accessToken이 유효한지 실제 oauth서버에 통해 검증하는것이다. 이때 매번 oauth서버에 상당한 부담을 준다는것이다. 이러한 문제점을 개선하기위해서 토큰 자체에 만료일을 체크하는 부분을 첨부하고 아울러서 사용자에 대한 추가정보등을 저장하고 있고 이를 사용하면 oauth서버에 부다음 상당히 줄이게된다.






ResourceServerConfigurerAdapter api설정

AuthorizationServerConfigurerAdapter 인증설정



## Oauth
Oauth에 의해서 발급되는 access_token은 random_string으로 토큰 자체에는 특별한 정보를 가지고있지 않는 일반적인 String 형태이다.

API나 서비스를 제공하는 서버입장에서는 그 access_token을 통해서 사용자에 연관된 권한(scope 같은것들)을 식별한뒤 권한을 허용해주는 구조이다.
![](https://i.imgur.com/xlZsxRy.png)

## JWT
JWT는 Claim기반이라는 방식을 사용하는데,Claim이라는 사용자에대한 프로퍼티나 속성을 이야기한다. 토큰 자체가 토큰의 정보, 유저의 정보등을 가지고 있는 형태이다.

![](https://i.imgur.com/83XJCo7.png)







[조대협의 블로그](http://bcho.tistory.com/999)



## Spring OAuth2
Authrization Server(권한서버) 설정


토큰생성 url
curl -u bar:foo http://localhost:8080/oauth/token -d "grant_type=password&username=admin&password=test"



/oauth/token?" + query + "&grant_type=authorization_code&client_id=normal-app&redirect_uri={redirectUrl}




## Spring OAuth2 With JWT Sample (https://dzone.com/articles/spring-oauth2-with-jwt-sample)


https://medium.com/@nydiarra/secure-a-spring-boot-rest-api-with-json-web-token-reference-to-angular-integration-e57a25806c50
## Medium JWT

JWT는 액세스토큰을 저장할필요가없고, 다루기 까다로운 세션을 신경쓰지 않아도된다.
### 1.Configure Spring Security
Spring boot의 자동구성은 덕분에 최소환의 커스마이징만 하면된다.

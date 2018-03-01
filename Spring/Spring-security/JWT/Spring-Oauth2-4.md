## JWT로 변경
지금까지 작성했던 Oauth2기본 동작 방식은 Access Token을 사용해서 교환하여 인증하는 방식이다. 이렇게 하는 방식에는 단점이 존재한다. Access Token만 교환하기 때문에 다시 토큰을 가지고 인증 정보를 조회하기 위해 Oauth2서버로 다시 요청하여 인증된 정보를 얻어와야한다.

* 그리고 API서버에서는 인증과 관련된 정보를 가지고 있지 않기 때문에 호출시마다 AccessToken을 사용해서 Oauth2서버로 요청하여 정보를 가져와야한다. 나중에 사용자가 많아지고 트래픽이 늘어나게되면 API서버에서 적절한 캐시를 사용해서 컨트롤해야하는 부분이 있을 수 도 있다. 하지만 Access Token이 유효한지 문제가 없는지 계속 확인해야하기때문에 Oauth2서버에도 주기적으로 체크를 해줘야한다. 이부분을 잘컨트롤 해줘야 많은 트래픽에 견딜 수 있는 인증서버 API서버를 만들 수 있다.

이러한 무제를 해결하는것이 JWT이다. JWT는 이미 토큰안에 유저의 정보를 인증하고 있기때문에 굳이 Oauth2서버를 호출하지않아도 되기 때문이다. 먼저 앞에서 Oauth2서버를 호출하여 받은 AccessToken을 살펴보자.

```
{
  "access_token":"6dfb79ab-46cc-49ad-9b46-b4da66e9e103",
  "token_type":"bearer",
  "expires_in":42760,
  "scope":"read"
}
```
다음은 JWT를 통한 토큰을 발급한 토큰 형태이다.

```
{
  "access_token":"eyJhbGciOiJIUzI1NiJ9.eyJzY29wZSI6WyJyZWFkIl0sImV4cCI6MTQ1NzY0OTg5NiwiYXV0aG9yaXRpZXMiOlsiUk9MRV9VU0VSIl0sImp0aSI6IjY1NDI5NWJlLWQyZmEtNDkxYi1hMTQwLTU5MTY0OTIxOWM2NSIsImNsaWVudF9pZCI6ImZvbyJ9.6LH9C0EP64Nh70O6t3WIqL009VfyzfavNLQEwEILxqw",
  "token_type":"bearer",
  "expires_in":43199,
  "scope":"read",
  "jti":"654295be-d2fa-491b-a140-591649219c65"
}
```
위에 토큰정보안에 권한,유저정보등이 넣을 수 있다. 하지만 header body는 베이스형태로 저장되기때문에 복화하가 가능하므로 중요한정보는 안넣는것이 좋다.


그리고 Access Token을 사용하는 방법이 바뀌었기 때문에 설정 부분도 바꿔 줘야 한다.

먼저 JWT 토큰에서 서명할 때 사용하는 key 값이 필요하다. (JWT는 전송 중에 데이터가 변경되지 않았다는 서명을 가지는데 HMAC이라고 부른다.) 그때 사용할 수 있는 방법이 직접 클라이언트와 서버에 RSA (private, public) 키값을 기술해주거나 서버에서 얻어오는 방법이 있다.
여기서는 심플하게 서버에서 Key값을 얻어오는 방법으로 하겠다.


```
# application.yml ( OAuth2 서버 )
security.oauth2.authorization.token-key-access: isAuthenticated()
```

위의 설정은 JWT에서 사용된 서명(HMAC) 정보를 검증할 key를 얻어오기 위한 API를 열어주기 위한 설정이다.
(기본은 설정은 denyAll()로 되어 있다.)
참고로 기본 Key 값은 OAuth2 서버가 시작할 때 랜덤으로 결정된다.  

```
# application.yml ( API 서버 )
security.oauth2.jwt.key-uri: http://localhost:8080/oauth/token_key
```

여기에서 아마 키값을 처음에 한번만 가지고오는듯, 그뒤로는 그냥 jwt로 처리하는듯

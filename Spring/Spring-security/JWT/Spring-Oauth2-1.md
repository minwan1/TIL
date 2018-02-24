


## OAuth2.0 구성

Resource owner: 사용자.
Client: Resource Server 에서 제공하는 자원을 사용하는 애플리케이션 (예, 페이스북의 뉴스를 모아서 보여주는 앱)
Resource server(API server): 자원을 호스팅하는 서버, (예, 페이스북 사진 비디오 등)
Authorization Server: 사용자의 동의를 받아서 권한을 부여하는 서버, 일반적으로 Resource Server 와 같은 URL 하위에 있는 경우가 많음.
![](https://i.imgur.com/yhsC9VH.jpg)


클라이언트가 Authorization grant를 넘기면 Authorization server는 access 토큰을 넘길것이다.



## 인증 종류
OAuth 2.0의 인증종류는 아래 4가지 입니다.

**Authorization Code Grant**
**Implicit Grant**
**Resource Owner Password Credentials Grant**
**Client Credentials Grant**

#### Authorization Code Grant
서버사이드 코드로 인증하는 방식
권한서버가 클라이언트와 리소스서버간의 중재역할.
Access Token을 바로 클라이언트로 전달하지 않아 잠재적 유출을 방지.
로그인시에 페이지 URL에 response_type=code 라고 넘긴다.

#### Implicit Grant
token과 scope에 대한 스펙 등은 다르지만 OAuth 1.0a과 가장 비슷한 인증방식
Public Client인 브라우저 기반의 어플리케이션(Javascript application)이나 모바일 어플리케이션에서 이 방식을 사용하면 된다.
OAuth 2.0에서 가장 많이 사용되는 방식이다.
권한코드 없이 바로 발급되서 보안에 취약
주로 Read only인 서비스에 사용.
로그인시에 페이지 URL에 response_type=token 라고 넘긴다.

#### Password Credentials Grant
Client에 아이디/패스워드를 저장해 놓고 아이디/패스워드로 직접 access token을 받아오는 방식이다.
Client 를 믿을 수 없을 때에는 사용하기에 위험하기 때문에 API 서비스의 공식 어플리케이션이나 믿을 수 있는 Client에 한해서만 사용하는 것을 추천한다.
로그인시에 API에 POST로 grant_type=password 라고 넘긴다.

#### Client Credentials Grant
어플리케이션이 Confidential Client일 때 id와 secret을 가지고 인증하는 방식이다.
로그인시에 API에 POST로 grant_type=client_credentials 라고 넘긴다.

기본적으로 아래와같이 인증코드가 존재한다.
```
.authorizedGrantTypes("authorization_code", "password", "client_credentials", "implicit", "refresh_token")
```


## 클라이언트의 의미
OAuth2서버를 통해 API에 접근을 허가한 클라이언트를 말한다. 클라이언트 종류로는 보통 웹, 아이폰 pc 등이 있다. 페이스북 개발자사 사이트에서도 예를 볼 수 있을것이다.
페이스북같은경우에는 APP ID, 앱시크릿 코드갈 될것이다.

```java
configurer
        .inMemory()
        .withClient(clientId)
        .secret(clientSecret);
```
클라이언트 id와 Client Secret 설정은 다음과 같이 AuthorizationServer에 설정할수있는데 보통은 이값들을 디비에서 관리한다.



### 권한 코드 방식 (Authorization Code Grant)
일반적인 웹사이트에서 소셜로그인과 같은 인증을 받을 떄 가장 많이 쓰는 방식으로 기본적으로 지원하고 있는 방식이다. 액세스토큰을 받기 위한 테스트가 다른 방식에 비해 복잡하다. 먼저 아래와같이 클라이언트 id와 redirect rl을 설정해주면 코드값을 autorization server에서 인증코드를 넘겨준다.
```
http://localhost:8080/oauth/authorize?response_type=code&client_id=testid&redirect_uri=http://localhost:8080/test/authorization-code&scope=read
```

```
http://localhost:8080/test/authorization-code?code=6DGZdV
```
여기 코드는 1회성인것같다.

```
curl -F "grant_type=authorization_code" -F "code=1jS0a0" -F "scope=read" -F "client_id=foo" -F "client_secret=bar" -F "redirect_uri=http://localhost:8080/test/authorization-code" "http://foo:bar@localhost:8080/oauth/token"
```
여기서 주의할점은 베이직인증을 client id와 client secret으로 베이직 인증처리를 해줘야한다는것이다. 그럼 아래와같이 인증코드를 받을 수 있다. 데이터는 폼데이터형식으로 넘겨야한다.

```
{
    "access_token": "9bbce699-9c1d-4fd8-a376-4a814a97d104",
    "token_type": "bearer",
    "refresh_token": "85ea4cd2-e676-4fc6-b722-35e08efa6ab1",
    "expires_in": 40505,
    "scope": "read"
}
```

### Implicit Grant 암묵적인 동의 방식(Implicit Grant flow)
보통 클라이언트 사이드에서 Oauth2인증 하는 방식이다. 다음과같은방식으로 호출한다.
```
http://user:test@localhost:8080/oauth/authorize?response_type=token&redirect_uri=http://localhost:8080&client_id=foo&scope=read

```
그럼 다음과같이 토큰값을 받는다.
```
http://localhost:8080/#access_token=e81131a9-ff4f-4230-a1d7-9c3a86e0f06c&token_type=bearer&expires_in=43199
```

해쉬토큰(#)으로 보내기 때문에 서버에서는 액세스 토큰을 받지 못한다. 즉 프론트에서만 사용하도록 하는것을 권장하는 형태이다.

### Resource Owner Password 자원 소유자 비밀번호(Implicit Grant flow)
자원 소유자 즉 아이디와 비밀번호로 액세슨 토큰을 발급한다.
```
$ curl foo:bar@localhost:8080/oauth/token -d grant_type=password -d client_id=foo -d scope=read -d username=user -d password=test
```

이것도 마찬가지로 basic auth 방식으로하여 foo,bar를 넣고 신청하면된다.
```
{
    "access_token": "75933a64-a270-4e01-b3e3-548294e4c793",
    "token_type": "bearer",
    "expires_in": 42989,
    "scope": "read"
}
```

##클라이언트 인증플로우 (Client Credentials flow)
클라이언트가 직접 자신의 정보를 통해 엑세스 토큰을 발급한다.(리프레시 토큰을 제공하지 않는다.
```
$ curl -F "grant_type=client_credentials" -F "scope=read" "http://foo:bar@localhost:8080/oauth/token"
```
아래와같이 토큰을 발급받을 수 있다.
```
{
    "access_token": "75933a64-a270-4e01-b3e3-548294e4c793",
    "token_type": "bearer",
    "expires_in": 42989,
    "scope": "read"
}
```

### 리프러시 토큰을 통한 액세스 토큰 재발
기존에 저장해둔 리프러시 토큰이 존재할 때 엑세스토큰 재발급 받을 필요가 있을 때 사용한다. 그리고 기존 액세스는 토큰이 만료된다.
```
$ curl -F "grant_type=refresh_token" -F "scope=read" -F "refresh_token=발급된 Refresh Token" "http://foo:bar@localhost:8080/oauth/token"
```
위와같이 호출하면 아래와같이 리프레쉬 토큰을 받을 수 있다.
```
{
    "access_token": "abdff03b-47c8-4d3b-8b2a-f627e54d5266",
    "token_type": "bearer",
    "refresh_token": "58517048-b9c3-423f-98e2-43eb26660671",
    "expires_in": 43199,
    "scope": "read"
}
```

### 액세스 토큰을 통한 API에 접근
발급받은 토큰을통해 아래와같이 호출하면 API에 접근할 수 있다.
$ curl -H "Authorization: Bearer 05e63e85-9614-446a-8904-aa6cc556bb1b" "http://localhost:8080/users"






token_type
OAuth 응답에는 token_type이 필수로 넘어온다. 대부분 Bearer이 넘어오는데 단어가 생소해서 찾아보니 무언가를 옮기는 사람이라고 하네요. token_type: bearer은 access_token을 bearer_token으로 준다는 의미입니다.

RFC에는 아래와 같이 나와있습니다.

A security token with the property that any party in possession of the token (a “bearer”) can use the token in any way that any other party in possession of it can. Using a bearer token does not require a bearer to prove possession of cryptographic key material (proof-of-possession).

키 인증없이 resource에 접근 할 수 있도록 하는 의미로 access_token이 만료되면 refresh_token으로 갱신해야 하기 때문에 bearer_token을 씁니다. RFC에는 mac_token이라고 타입이 하나 더 나오긴하는데 설명은 제대로 쓰여있지 않습니다.


참고 https://brunch.co.kr/@sbcoba/4
쩐다

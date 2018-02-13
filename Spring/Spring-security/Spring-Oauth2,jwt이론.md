how does oauth2 work
![](https://i.imgur.com/i7qfXyD.png)


### OAuth2.0 구성

Resource owner: 사용자.
Client: Resource Server 에서 제공하는 자원을 사용하는 애플리케이션 (예, 페이스북의 뉴스를 모아서 보여주는 앱)
Resource server(API server): 자원을 호스팅하는 서버, (예, 페이스북 사진 비디오 등)
Authorization Server: 사용자의 동의를 받아서 권한을 부여하는 서버, 일반적으로 Resource Server 와 같은 URL 하위에 있는 경우가 많음.
![](https://i.imgur.com/yhsC9VH.jpg)


클라이언트가 Authorization grant를 넘기면 Authorization server는 access 토큰을 넘길것이다.




## 클라이언트의 의미
OAuth2서버를 통해 API에 접근을 허가한 클라이언트를 말한다. 클라이언트 종류로는 보통 웹, 아이폰 pc 등이 있다. 페이스북 개발자사 사이트에서도 예를 볼 수 있을것이다.
페이스북같은경우에는 APP ID, 앱시크릿 코드갈 될것이다.


### 인증 종류
OAuth 2.0의 인증종류는 아래 4가지 입니다.

**Authorization Code Grant**
**Implicit Grant**
**Resource Owner Password Credentials Grant**
**Client Credentials Grant**

####Authorization Code Grant
서버사이드 코드로 인증하는 방식
권한서버가 클라이언트와 리소스서버간의 중재역할.
Access Token을 바로 클라이언트로 전달하지 않아 잠재적 유출을 방지.
로그인시에 페이지 URL에 response_type=code 라고 넘긴다.

####Implicit Grant
token과 scope에 대한 스펙 등은 다르지만 OAuth 1.0a과 가장 비슷한 인증방식
Public Client인 브라우저 기반의 어플리케이션(Javascript application)이나 모바일 어플리케이션에서 이 방식을 사용하면 된다.
OAuth 2.0에서 가장 많이 사용되는 방식이다.
권한코드 없이 바로 발급되서 보안에 취약
주로 Read only인 서비스에 사용.
로그인시에 페이지 URL에 response_type=token 라고 넘긴다.

####Password Credentials Grant
Client에 아이디/패스워드를 저장해 놓고 아이디/패스워드로 직접 access token을 받아오는 방식이다.
Client 를 믿을 수 없을 때에는 사용하기에 위험하기 때문에 API 서비스의 공식 어플리케이션이나 믿을 수 있는 Client에 한해서만 사용하는 것을 추천한다.
로그인시에 API에 POST로 grant_type=password 라고 넘긴다.

####Client Credentials Grant
어플리케이션이 Confidential Client일 때 id와 secret을 가지고 인증하는 방식이다.
로그인시에 API에 POST로 grant_type=client_credentials 라고 넘긴다.










token_type
OAuth 응답에는 token_type이 필수로 넘어온다. 대부분 Bearer이 넘어오는데 단어가 생소해서 찾아보니 무언가를 옮기는 사람이라고 하네요. token_type: bearer은 access_token을 bearer_token으로 준다는 의미입니다.

RFC에는 아래와 같이 나와있습니다.

A security token with the property that any party in possession of the token (a “bearer”) can use the token in any way that any other party in possession of it can. Using a bearer token does not require a bearer to prove possession of cryptographic key material (proof-of-possession).

키 인증없이 resource에 접근 할 수 있도록 하는 의미로 access_token이 만료되면 refresh_token으로 갱신해야 하기 때문에 bearer_token을 씁니다. RFC에는 mac_token이라고 타입이 하나 더 나오긴하는데 설명은 제대로 쓰여있지 않습니다.


참고 https://brunch.co.kr/@sbcoba/4
쩐다

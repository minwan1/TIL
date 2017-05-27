## Spirng ouath

Spring + oauth2 + angular 검색자료 [클릭](http://www.baeldung.com/rest-api-spring-oauth2-angularjs)
클릭[샤쿠](http://syaku.tistory.com/287)


### 사용자 액세스 토큰
앱에서 특정 사용자 대신 Facebook의 데이터를 읽고 수정하고 쓰기위해 api를 호출할 때마다 이유형의 토큰이필요함.
### 앱 엑세스 토큰
Facebook 페이지에 속한 데이터를 읽거나 쓰거나 수정하는 API에 대한 권한을 제공한다. 페이지에 액세스 토큰을 확보하려면 사용자 액세스 토큰이 필요하다.
### 클라이언트 토큰
많이 사용하지 않음

### 사용자 액세스 토큰 생성
페이스북 로그인 인증 - access token

### 페이스북 ouath 인증과정
![](http://i.imgur.com/7eowTjI.png)
![](http://i.imgur.com/5OdT2aB.png)


buildAuthorizeUrl(GrantType, OAuth2Parameters)

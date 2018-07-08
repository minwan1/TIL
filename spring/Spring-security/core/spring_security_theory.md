a
## Spring Security 전체적 구조
![](http://i.imgur.com/m5dfYew.png)
<center>***[이미지 1]***</center>

* ***spring security*** 는 ***authenticationManager*** 을 통해 인증을 구현한다.
* ***authenticationManager*** 의 구현체로 ***ProviderManager*** 가 존재한다.
* ***ProviderManager*** 는 인증구현을 ***AuthenticationProvider*** 에게 위임한다.
* 일반적으로 ***AuthenticationProvider*** 을 커스터마이징해서 인증을 구현한다.
* ***AuthenticationProvider*** 구현체는 3개가 있고, 이것에 인증에 성공하지 못할경우 예외처리가 발생한다.
  * 구현체로 ***DaoAuthenticationProvider, LadpAuthenticationProvider, OpenIdAuthenticationProvider*** 가 존재한다.
![](http://i.imgur.com/ByfYXXm.png)  
<center>[이미지 2]</center>
* **이미지2** 는 AuthenticationProvider구현체까지 구현된 class다이어그램이다.
* DaoAuthenticationProvider는 내부적으로 UserDetailsService를 이용해서 사용자 정보를 읽어오는데 아래 이미지 3은 spring security에서 UserDetailsService의 구현체인 jdbcDaoImpl까지 구현된 class다이어그램이다.
![](http://i.imgur.com/nCawRsL.png)
<center>[이미지 3]</center>
* authenticate메소드에서 authentication을 리턴할때 스프링에서 구현해놓은 UsernamePasswordAuthenticationToken 사용해도 무방한다.UsernamePasswordAuthenticationToken(UserDetailsService.loadUserByUsername(String username),null(보통은널),권한) 사용한다.
* 결론은 ***UserDetailsService*** 에 loadUserByUsername(String username)로 디비에서 유저정보를 조회 해오면 ***AuthenticationProvider*** 에서 authenticate(Authentication authentication)메소드로 ***UserDetailsService.loadUserByUsername(String username)*** 메소드로 가지고온 디비유저정보와 authenticate(Authentication authentication)로 접속한유저 정보(authentication)로 비밀번호를 인증을 처리하게되고 ***AuthenticationProvider***  인증에 성공하게되면 ***Authentication객체*** 를 돌려준게된다.
  * 디비에서 유저정보를 불러오려면 UserDetailsService interface를 직접 구현하면 된다.
  * 비밀번호비교를 직접 구현하려면 AuthenticationProvider 구현하면 된다.
  * AuthenticationProvider을 따로 구현안해도 UserDetailsService로 유저정보를 추가할수 있다.

* 아이디/패스워드 사용자 정보를 넣고 실제 가입된 사용자인지 체크한후 인증에 성공하면 사용자의 principal과 credential 정보를 Authentication에 담습니다.
![](http://i.imgur.com/77uaOY2.png)
Spring Security에서 방금 담은 Authentication을  SecurityContext에 보관합니다.
![](http://i.imgur.com/0PY4TWC.png)
이 SecurityContext를 SecurityContextHolder에 담아 보관하게 됩니다.
![](http://i.imgur.com/a3pQv6A.png)

참고
* [버리야 놀자](http://flyburi.com/584)
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /
* [Syaku(샤쿠)](http://syaku.tistory.com/286)

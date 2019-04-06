## Spring Security 구조
먼저 Spring Security에서 유저 인증을 구현할 때 최상위 인터페이스로는 아래 그림 같이 Authentication Manager Interface가 존재한다. 그리고 ***authenticationManager*** Interface를 통해 유저 인증 구현 한다.
![](http://i.imgur.com/m5dfYew.png)
***<center>[이미지 1]</center>***
위 그림과 같이 ***authenticationManager*** 의 구현체로 ***ProviderManager*** 가 존재한다. ***ProviderManager*** 는 인증구현을 ***AuthenticationProvider Interface*** 에게 위임한다. 일반적으로 ***AuthenticationProvider*** 을 커스터마이징해서 인증을 구현한다. ***AuthenticationProvider*** 구현체는 3개가 있고, 이것에 인증에 성공하지 못할경우 예외처리가 발생한다. 구현체로 ***DaoAuthenticationProvider, LadpAuthenticationProvider, OpenIdAuthenticationProvider*** 가 존재한다.
![](http://i.imgur.com/ByfYXXm.png)

***<center>[이미지 2]</center>***
**이미지2** 는 AuthenticationProvider구현체까지 구현된 class다이어그램이다. 그림에서 보듯이 DaoAuthenticationProvider는 내부적으로 UserDetailsService를 이용해서 사용자 정보를 읽어오는데 아래 이미지 3은 Spring Security에서 UserDetailsService의 구현체인 jdbcDaoImpl까지 구현된 class다이어그램이다. 유저가 로그인을 하게되면 UserDetailsService의 loadUserByUsername으로 유저를 조회해 AuthencationProvider로 리턴해주고 AuthencationProvider의 authenticate메소드에서 유저가 입력한 비번과 대조하게된다.

![](http://i.imgur.com/nCawRsL.png)
***<center>[이미지 3]</center>***
인증에 성공하게 되면 authenticate메소드에서 authentication 객체를 리턴해야한다. authentication 객체는 스프링에서 구현해놓은은 UsernamePasswordAuthenticationToken 사용해도 무방하다.UsernamePasswordAuthenticationToken(UserDetailsService.loadUserByUsername(String username),null(보통은널),권한) 을 생성하게되면 Aunthetication이 생성되어지고 최종적으로 이것을 리턴해주게되면 security 인증이 끝나게 된다.

## Security 인증 순서
Security 인증 순서는 ***UserDetailsService*** 에 loadUserByUsername(String username)로 디비에서 유저정보를 조회 해오면 ***AuthenticationProvider*** 에서 authenticate(Authentication authentication)메소드로 ***UserDetailsService.loadUserByUsername(String username)*** 메소드로 가지고온 디비유저정보와 authenticate(Authentication authentication)로 접속한유저 정보(authentication)로 비밀번호를 인증을 처리하게되고 ***AuthenticationProvider***  인증에 성공하게되면 ***Authentication객체*** 를 돌려준게된다.
* 디비에서 유저정보를 불러오려면 UserDetailsService interface를 직접 구현하면 된다.
* 비밀번호 비교를 직접 구현하려면 AuthenticationProvider 구현하면 된다.
* AuthenticationProvider을 따로 구현안해도 UserDetailsService로 유저정보를 추가할수 있다.

아이디/패스워드 사용자 정보를 넣고 실제 가입된 사용자인지 체크한후 인증에 성공하면 사용자의 principal과 credential 정보를 Authentication에 담습니다.
![](http://i.imgur.com/77uaOY2.png)
Spring Security에서 방금 담은 Authentication을  SecurityContext에 보관합니다.
![](http://i.imgur.com/0PY4TWC.png)
이 SecurityContext를 SecurityContextHolder에 담아 보관하게 됩니다.
![](http://i.imgur.com/a3pQv6A.png)
## 결론

1. 스프링 시큐리는 크게 2가지 인증/인가 처리를 도와준다. (AuthenticationProvider,UserDetailsService)
2. o.s.security.core.Authentication은 시큐리티에서 접근한 외부에서 접근한 정보를 보관하는 역할을한다.(이름,권한,인증여부등을 조회함)
3. 이러한 Authentication 정보는 o.s.s.core.context.SecurityContextHolder안에서 가지고온다.
4. 그리고 Authentication의 정보는 최초 SecurityContextHolder 맨처음에 접근하는 세션이 이곳에 먼저 저장되어져 위에와같은 로직이 진행되는 것이다.
    * 이것들이 필터체인에 순서대로적용된다. 이순서가 필터중에서 가장먼저 실행됨.
5. 최종적으로 SecurityContextHolder.getContext().getAuthentication()으로 유저 정보를 불러오게 된다.
6. 스프링 시큐리가 제공해주는 authentication-provider을 이용해 인증을 진행하게되는데 유저 인증은 UserDetailsService에 UserDetails객체를 이용해 인증을 진행하게 된다.
    * <jdbc-user-service>을 사용하게되면 인증을 알아서 진행하는듯, 단 유저정보를 가지고올수있는 제약이걸림 사실상 사용못함(userdetailsserver을 구현해서 사용해야함).
7. 그렇게 UserDetailsService에서 데이터를 가지고와서 DaoAuthenticationProvider에서 인증과정을 거친다.(UserDetailsService의 loadUserByUsername메소드를 이용함)
8. 만약에 UserDetailsService를 커스텀 한다면 loadUserByUsername에서 회원정보를 꼭리턴해줘야한다. 왜냐하면 아까 말했듯이 이것을 통해 사용자가 입력한 암호를 비교 하기때문이다.


참고<br>
* [버리야 놀자](http://flyburi.com/584) <br>
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /<br>
* [Syaku(샤쿠)](http://syaku.tistory.com/286)<br>

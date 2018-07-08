

보안 관련 3요소(스프링 시큐리티 3대요소)
## 접근 주체 (Principal)
## 인증 (Authentication)
## 인가 (Authorize)


### FilterChainProxy 스프링 빈으로 등록


![](https://i.imgur.com/B37WO8L.png)
![](https://i.imgur.com/w0WKZgu.jpg)


크게
스프링 DelegatingFilterProxy를 이용해 스프링 필터빈들을 등록할 수 있다.

그래서 Spring security의 SecurityFilterChain를 DelegatingFilterProxy에 등록하여서 Spring Security를 활성화 가능하다.



 그래서 UsernamePasswordAuthenticationFilter 를 제거하고 OAuth를 지원하는 Filter를 구현해서 제공하여야 합니다.

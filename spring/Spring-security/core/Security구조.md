## Spring security 구조


폴더 전체 구조
-access
-authentication
-concurrent
-context
-core
-crypto
-provisioning
-scheduling
-task
-util




폴더 세부 구조
- access
- authentication
  - dao
    - AbstractUserDetailsAuthenticationProvider.java
    - DaoAuthenticationProvider.java
  - AuthenticationManager.java
  - ProviderManager.java
  - AuthenticationProvider.java

- concurrent
- context
- core
  - userdetails
    - UserDetailsService.java
- crypto
- provisioning
- scheduling
- task
- util


![](https://i.imgur.com/OldTpVj.png)

- 초록색 점선이 구현을 의미
- 파란색 선이 상속을 의미
- 다이아몬드 선이 변수에 의존 넣었을때 사용
- 그냥 점선은 화살표는 의존관계를 가짐



매니저 아래개념으로 프로바이더가 있고 그 프로바이더가 인증기능을 선택한다.



http://lorenzo-dee.blogspot.kr/2016/08/spring-security-oauth2-with-google.html


보안 관련 3요소(스프링 시큐리티 3대요소)
## 접근 주체 (Principal)
## 인증 (Authentication)
## 인가 (Authorize)


보통

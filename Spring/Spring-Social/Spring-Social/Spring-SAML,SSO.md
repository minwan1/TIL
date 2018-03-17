SAML, OAuth의 차이


## SAML
서로 다른 기업 혹은 도메인간의 SSO로 연계된다.
예를 들어 A회사와 B회사가 파트너십관계를 맺었다고 가정해보자, A회사 인증(로그인)기능을 담당하고 B회사는 여행에대한 예약을 담당한다고 가장 해보자. 여기에서 A회사는 사용자의 인증 및 로그인 처리를 제공하므로 Identity Provider(IdP)라 불리고, B여행사는 A회사에서 인증한 사용자에게 예약 서비스를 제공하므로 Service Provider라한다.
예를 들어보겟다.
1. A회사에서 로그인
2. A회사에서 내의 출장 예약 서비스 링크(B회사) 클릭
3. B사이트로 Redirection
4. B사이트에서 해당 사용자 A사이트에서 인증되었는지의 여부 체크
5. 인증된 사용자의 경우 예약 서비스 화면을 표시.


위의 Workflow는 A회사에서 인증을 시작외었으므로 Idp-initiated SSO 라고 불린다. 이와 다르게 SP에서 시작하는 SP-initated SSO도 생각 할 수 있다.

1. 북마크를 이용해 B회에서 예약 서비스 페이지 접근
2. B에 해당 사용자가 아직 로그인 하지 않았으므로 인증 정보 요청과 함께 A사이트로 Redirection
3. A에서 사용자 인증 로그인
4. 인증결과와 함께 B로 redirection
5. B의 예약 서비스 페이제엇 사용자 인증 여부체크후 페이지 표시





Security Assertion Markup Language(SAML)은 OASIS의 Security Service Technical Committe에서 정의한 보안 도메인간에 인증(authentication)과 권한부여(authorization)에 관련된 자료를 교환할 수 있는 XML 기반의 표준이다. 보안 도메인은 Identity Provider(검증의 생산자)와 Service Provider(검증의 소비자)로 구성되있다.






demo user demouser 을 psosamldemo.net.여기로 로그인 시킨다.

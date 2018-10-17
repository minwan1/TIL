

##
CORS와 CSRF와 관련된 이상한 에러를 만났다. Spring security를 사용하여 SOP을 준수했을때  CSRF가 인증처리 잘되었다. (참고로 Spring, angular 조합이였다. angularjs는 알아서 서버로부터 발급받은 csrf를 헤더에 넣어서 서버로 전송한다.) 

그런데 SOP를 준수하지 않으면 csrf를 준수하지않으면 CSRF가 읹증처리가 되지 않는 이슈를 만났다. 여기에대해 좀 더 스터디가 필요할것같다.


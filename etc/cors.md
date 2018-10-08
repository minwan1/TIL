# CORS(Cross Origin Resource Sharing)의 종류


XMLHttpRequest는 동일 출처 정책(SOP, Same Origin Policy)을 따르기에, XMLHttpRequest을 사용하는 웹 애플리케이션은 자신과 동일한 도메은으로 HTTP 요청을 보내는 것만 가능하다. 하지만 서버쪽에서 CORSCross Origin Resource Sharing)를 허용해주면 다른 도메인이여도 리소스에 접근이 가능하다.

동일 출처 정책은 브라우저 보안 정책이기 때문에 Backend 영역은 이 제약에서 자유롭다.


CORS 크게 4종류로 나누어 볼 수 있다. 4가지 종류는 아래와 같습니다.

* Simple Request
* Preflight Request
* Credential Request
* NonCredential Request

## Simple Request

GET, HEAD, POST 중의 한 가지 방식을 사용해야 한다.
POST 방식일 경우 Content-type이 아래 셋 중의 하나여야 한다.
* application/x-www-form-urlencoded
* multipart/form-data
* text/plain

## Preflight Request

Simple Request 조건에 해당하지 않으면 브라우저는 Preflight Request 방식으로 요청한다.

따라서, Preflight Request는

* GET, HEAD, POST 외의 다른 방식으로도 요청을 보낼 수 있고,
* application/xml 처럼 다른 Content-type으로 요청을 보낼 수도 있으며,
* 커스텀 헤더도 사용할 수 있다.

이름에서 짐작할 수 있듯, Preflight Request는 예비 요청과 본 요청으로 나뉘어 전송된다.

먼저 서버에 예비 요청(Preflight Request)를 보내고 서버는 예비 요청에 대해 응답하고,
그 다음에 본 요청(Actual Request)을 서버에 보내고, 서버도 본 요청에 응답한다.

## Credential Request
자바스크립트는 웹브라우저 엔진을 통해 HTTP를 요청하게 되는데 이때 도메인이 다르면 쿠키가 유지되지 않는다. 이것을 가능하게해주는게 `xhr.withCredentials = true` 설정이다. 이것은 쿠키, 인증헤더, TLS client같은 설정들 사용하기위해서 설정할 수 있다. 자바스크립트쪽에서 HTTP요청시 `xhr.withCredentials = true`를 설정하고 요청하면 쿠키가 유지 된다. 서버는 `Access-Control-Allow-Credentials: true`설정을 해줘야한다. 

클라이언트가 요청을 할 때 이설정을 해주지않으면 쿠키가 유지되어지지 않기때문에 response시에 서버쪽에서 session-id를 새롭게 발급하여 Set-Cookie를 하여 response를 해주게된다.

만약 Credentials을 true설정을 해주게되면 Request-Header 쿠키에 제대로 session-id가 담겨 request되고 세션이 유지되는것을 볼 수 있다.




## NonCredential Request

CORS 요청은 기본적으로 Non-Credential 요청이므로, `xhr.withCredentials = true`를 지정하지 않으면 Non-Credential 요청이다.



# 결론 
* XMLHttpRequest type의 요청의 경우 host뿐만 아니라 protocol 및 port가 다를 경우 호출 자체가 안되거나 쿠키가 보존이 안되어 세션이 끊긴다.
* 이 때에는 전자의 호출이 안 되는 경우 서버단에 Access-Control- 계열의 header 설정을 추가해야 하며, 후자의 session이슈의 경우 client 호출 코드에서 xhr.withCredentials = true 와 서버단의 Access-Control-AllowCredentials: true 설정으로써 session이슈를 해결 가능하다.
* 서버측에서 CORS 처리를 위한 Filter는 반드시 인증 처리하는 Filter 이전에 있어야 한다.




참고 
* [Tmax-Soft](https://technet.tmaxsoft.com/ko/front/technology/viewTechnology.do?paging.page=4&kss_seq=KSSQ-20160707-000001)
* [배워가는블로거](http://zamezzz.tistory.com/137 )
* [김코딩](http://huns.me/development/1291)

httpServletRequest.getHeader("Origin")
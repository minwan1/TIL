int INT_RESULT_SUCCESS=200;
int INT_RESULT_FAIL=400;
int INT_RESULT_SERVER_ERROR=500;
INT_RESULT_NOT_FOUND=404;
INT_RESULT_EXPIRED=700;

_result결과물
result결과코드
resultMessage결과코드




Response Class Code
Response Class 의미
설명
1
Informational (정보)
리퀘스트를 받고, 처리 중에 있음.
2
Success (성공)
리퀘스트를 정상적으로 처리함.
3
Redirection (리디렉션)
리퀘스트 완료를 위해 추가 동작이 필요함.
4
Client Error (클라이언트 오류)
클라이언트 요청을 처리할 수 없어 오류 발생
5
Server Error (서버 오류)
서버에서 처리를 하지 못하여 오류 발생

4번과 5번은 에러이기는 한데, 간단히 차이점을 설명하자면 4xx 에러는 클라이언트의 잘못된 요청으로 서버에서 처리하지 못한 것이고, 5xx 에러는 클라이언트의 요청은 문제가 없으나, 서버에서 처리중에 서버 문제로 인해 오류가 발생한 것이라고 생각하시면 됩니다.


아래는 자주 접하는 코드를 위주로 작성하였고, 더 상세한 정보는 아래 참고 자료의 위키피디아 링크를 참조해주세요.~


* 200 번대 응답(Response) : 성공(Success)

200
OK
* 요청 정상 처리.
204
No Content
* 요청 정상 처리하였지만, 돌려줄 리소스 없음.
* 응답에 어떠한 엔티티 바디(Entity Body)도 포함하지 않음.
* 서버에서 처리 후, 클라이언트에 정보를 보낼 필요가 없는 경우 사용.
206
Partial Content
* Range가 지정된 요청인 경우, 지정된 범위만큼의 요청을 받았다는 것을 알려줌.



* 300 번대 응답(Response) : 리디렉션(Redirection)

301
Moved Permanently
* 요청된 리소스에는 새로운 URI가 지정되어 있기 때문에, 이후로는 새 URI를 사용해야 한다는 것을 나타냄. (영구적인 URI 변경)
302
Found
* 요청된 리소스에는 새로운 URI가 지정되어 있기 때문에, 이후로는 새 URI를 사용해야 한 다는 것을 나타냄. 301과 유사하지만, 302는 일시적인 URI 이동)
303
See Other
* 이 응답은 요청에 대한 리소스는 다른 URI에 있기 때문에 GET 메서드를 사용해서 얻어야 한다는 것을 나타냄. 302 코드와 같지만, 303은 리디렉션 위치를 GET 메서드를 통해 얻어야 한다고 명확하게 되어 있음.
304
Not Modified
* 요청한 리소스가 마지막 요청 이후 변경된 적이 없기 때문에 기존 클라이언트의 로컬 캐시 리소스를 사용하도록 알려줌.
300번대로 분류되어 있지만, 리디렉션과는 관계없는 처리를 함.
307
Temporary Redirect
* 임시로 페이지를 리다이렉트 함.



* 400 번대 응답(Response) : 클라이언트 에러 (Client Error)

400
Bad Request
* 클라이언트의 요청 구문이 잘못됨.
* 브라우저는 이 응답을 200 OK 응답과 동일한 형태로 취급함.
401
Unauthorized
* 요청 처리를 위해 HTTP 인증(BASIC 인증, DIGEST 인증) 정보가 필요함을 알려줌.
접근 허용을 차단함. 최초 요청에는 인증 다이얼로그 표시하고, 두번째는 인증 실패 응답을 보냄.
403
Forbidden
* 접근 금지 응답. Directory Listing 요청(서버 파일 디렉토리 목록 표시) 및 관리자 페이지 접근 등을 차단하는 경우의 응답. (파일 시스템 퍼미션 거부, 허가 되지 않은 IP 주소를 통한 액세스의 거부 등)
* 서버는 엔티티 바디에 접근 거부에 대한 이유를 명시하여 보낼 수 있음.
404
Not Found
* 클라이언트가 요청한 리소스가 서버에 없음
405
Mothod Not Allowed
* 허용되지 않는 HTTP 메서드를 사용함.



* 500 번대 응답(Response) : 서버 에러 (Server Error)

500
Internal Server Error
* 서버에서 클라이언트 요청을 처리 중에 에러가 발생함.
503
Service Unavailable
* 서버가 일시적으로 요청을 처리할 수 없음.
* 서버가 과부하 상태이거나 점검중이므로 요청을 처리할 수 없음을 알려줌.
504
Gateway Timeout
* 서버를 통하는 게이트웨이에 문제가 발생하여 시간이 초과됨.
505
HTTP Version Not Supported
* 해당 HTTP 버전에서는 지원되지 않는 요청임을 알려줌.





참고
* [이러쿵저러쿵](http://ooz.co.kr/260 )

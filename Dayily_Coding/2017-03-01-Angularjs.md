# Daily Coding 2017-03-01-Angular/API server connection

### 프록시란?
사전적 의미로는 대리인(agent)
* 웹크라이언트의 요청 URL을 해당 서버가 아닌 프록시 서버로 요청
* 웹브라우저에서 원하는 주소를 입력시 바로 웹서버로 접속되는것이 아닌 프록시 서버로 전송되고 그다음 웹서버로 갈때 경유하는 곳을 프록시 서버라고한다.

#### 프록시 서버를 굳이 경유 하는 이유는 ?
* 익명성 혹은 막혀있는 웹사이트를 우회하여 접속할 수 있다는 이점이 있다.
* 내부 네트워크 및 방화벽으로 인해 P2P사이트, 한국 IP들을 막아놓 사이트등에 접속이 가능하다.
* 프록시서버로 접속하면 자신의 아이피 정보를 남기지 않고 접속할 수 있다.

프록시로 연결할까 했는데 더좋은 방법이 있을까 찾아봐야할듯,

Constant 방법
``` javascript
var ngModule = angular.module('app', []);

// Example configuration stored as constant
ngModule.constant('config', {  
  apiUrl: 'https://your-api.com',
  baseUrl: '/',
  enableDebug: true
});
```
```javascript
ngModule.service('api', ApiService);

// Inject config so the api service does
// not have to be aware of the actual API url.
function ApiService(config){

  this.getUsers = function getUsers(){
    return $http
      .get(config.apiUrl + '/users.json')
      .then(function(response){
        return response.data;
      });
  };

}
```
음 이런식으로 할수 있다고하는데 좋은 방법은 아니라고한다.
config에 의존적이라서 배포하는데 힘들다,

오늘도 API연동 못할것같은 안좋은 기분이 든다.
책보면서해야지


### ANGULARJS 구조
1. HTML APP이름과 같은이름의 JS생성
2. 앱이름으로 모듈생성
3. 서브 모듈들 주입
4. 공통 모듈 생성

### angular 서비스
* Angular js에서는 전체 애플리케이션이 공유할 수 잇는 공통의 기능들
*








참고링크
* [네트워크/WEB 이야기](http://bment.tistory.com/375)
* [jvandemo.com](http://www.jvandemo.com/how-to-configure-your-angularjs-application-using-environment-variables/)
* AngularJS 인 액션

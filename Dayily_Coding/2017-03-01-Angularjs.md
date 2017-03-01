# Daily Coding 2017-03-01-Angular/API server connection
엥귤러와 API서버 연결

### 프록시란?
사전적 의미로는 대리인(agent)
* 웹크라이언트의 요청 URL을 해당 서버가 아닌 프록시 서버로 요청
* 웹브라우저에서 원하는 주소를 입력시 바로 웹서버로 접속되는것이 아닌 프록시 서버로 전송되고 그다음 웹서버로 갈때 경유하는 곳을 프록시 서버라고한다.

#### 프록시 서버를 굳이 경유 하는 이유는 ?
* 익명성 혹은 막혀있는 웹사이트를 우회하여 접속할 수 있다는 이점이 있다.
* 내부 네트워크 및 방화벽으로 인해 P2P사이트, 한국 IP들을 막아놓 사이트등에 접속이 가능하다.
* 프록시서버로 접속하면 자신의 아이피 정보를 남기지 않고 접속할 수 있다.

프록시로 연결할까 했는데 더좋은 방법이 있을까 찾아봐야할듯,
##

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

##

### ANGULARJS 구조
여기에서 파일구조는 [angularjs-in-action](https://github.com/angularjs-in-action/angello) 책으로 설명

1. HTML APP(index.html)이름과 같은이름의 JS생성
```html
<html ng-app="Angello">
```
2. 앱이름으로 모듈생성 (Angello.js 에서 엥귤러 config를 구성함)
```javascript
var myModule = angular.module('Angello');
```
3. 서브 모듈 생성
``` javascript
var myAppModule = angular.module('Angello.Storyboard', []);
angular.module('Angello.Storyboard').controller('StoryboardCtrl', function (STORY_TYPES) {

});
```

4. StoriesModel을 Common 모듈의 service로 정의
```javascript
angular.module('Angello.Common') //Angello Common
    .service('StoriesModel',
        function () {
          //보통 서비스를 넣는다.
        });
```


5. 서브 모듈들 주입
```javascript
var myModule = angular.module('Angello', //angello 모듈선언하고, 각종 서브모듈들 선언
    [
        'ngRoute',
        'ngAnimate',
        'firebase',
        'ngMessages',
        'Angello.Common',
        'Angello.Dashboard',
        'Angello.Login',
        'Angello.Storyboard',
        'Angello.User',
        'auth0',
        'angular-jwt',
        'angular-storage'
    ]);
```



6. StoriesModel.js(common모듈)공통 모듈로 다른 앱연결 (사용자 접근권한등은 전역에서 활용할 수 있기때문에)
* 예를들어
```javascript
angular.module('Angello.Storyboard', ['Angello.Common']);
```
위에처럼 Common 모듈을 넣어주면 storybord 앱에서도 common 모듈을 사용할 수 있다.




### Angular 서비스
* Angular js에서는 전체 애플리케이션이 공유할 수 잇는 공통의 기능들
### Angular 모델
* 클라이언트 외부에 데이터를 영속적으로 저장하는 로직을 저장
* 서버와의 통신은 내장된 $http 서비스를 이용해 서버와 통신

영속적 : 영원히 계속되는. 또는 그런것


### URL 파라미터 값 읽어오기
```javascript
function getQuerystring(paramName){

	var _tempUrl = window.location.search.substring(1); //url에서 처음부터 '?'까지 삭제
	var _tempArray = _tempUrl.split('&'); // '&'을 기준으로 분리하기

	for(var i = 0; _tempArray.length; i++) {
		var _keyValuePair = _tempArray[i].split('='); // '=' 을 기준으로 분리하기

		if(_keyValuePair[0] == paramName){ // _keyValuePair[0] : 파라미터 명
			// _keyValuePair[1] : 파라미터 값
			return _keyValuePair[1];
		}
	}
}

console.log(getQuerystring('name'))  // --> 'hyewon' 출력
console.log(getQuerystring('age'))  // --> '25' 출력
```

위의 내용과 상관없이 참조할것
```javascript
function getQuerystring(paramName){

	 window.location.search.substring(1); 

}

```





참고링크
* [네트워크/WEB 이야기](http://bment.tistory.com/375)
* [jvandemo.com](http://www.jvandemo.com/how-to-configure-your-angularjs-application-using-environment-variables/)
* AngularJS 인 액션
* [오니님의짱깬보](http://elena90.tistory.com/entry/Java-Script-url-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%EA%B0%92-%EC%9D%BD%EC%96%B4%EC%98%A4%EA%B8%B0)

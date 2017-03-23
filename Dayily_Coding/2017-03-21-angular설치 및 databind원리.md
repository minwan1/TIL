# Daily Coding 2017-03-21일 angular 데이터바인딩,및 팩토리서비스 예제 설명

## angular 설치과정
```
// bower 설치
$ npm install -g bower
// grunt 설치
$ npm install -g grunt-cli
//yo man설치
 yo angular
```
## mybatis
데이터베이스에 id,enabled,password,age 가있으면
id,enabled,password 이렇게 dto가존재할경우 위에정보를 에러없이 3개는 가져온다.
* 따로 getter/setter없이도 파일에 입력된다(이건 좀의외인듯)


##angular JS의 서비스와 팩토리 차이점

### Service
* 여러코드에서 반복적으로 사용되는 코드를 분리할 때 사용되는 기능으로, 해당 서비스가 필요한곳 의존성을 주입하여 사용한다.
* 싱글톤패턴으로 사용된다.
* 팩토리와달리 리턴이되는값이 존재하지않는다.

```javascript
var app = angular.module('myApp', []);

// Service definition
app.service('testService', function(){
    this.sayHello= function(text){
        return "Service says \"Hello " + text + "\"";
    };        
});
// AngularJS Controller that uses the service
function HelloCtrl($scope, testService)
{
    $scope.fromService = testService.sayHello("World");
}
```

### factory
```javascript
var app = angular.module('myApp',[]);

app.factory("testFactory",function(){
  return {
    sayHello : function(text){
      return "Factory says"+text;
    }
  }
});
// AngularJS Controller that uses the factory
function HelloCtrl($scope, testFactory)
{
    $scope.fromFactory = testFactory.sayHello("World");
}

```

### 데이터 바인딩의 원리
### 일반적으로 HTML의 입력 일리먼트들은 사용자의 입력이 있을 경우 이벤트 리스너가 발생된다.
* 뷰에서 사용자 입력이 일어나면 모델이 변경된다.
 ![](http://mblogthumb1.phinf.naver.net/20131217_232/jjoommnn_1387286496086d7B0U_JPEG/binding01.jpeg?type=w2)
* 두번째는 어떤식으로든 모델의 변경이 되었다는것을 알리는 이벤트가 있어야한다
![](http://mblogthumb3.phinf.naver.net/20131217_14/jjoommnn_13872865126980kkcM_JPEG/binding02.jpeg?type=w2)
* 여기에서 중요한점은 Model은 HTML이 아니라 Javascript객체이기 때문에 이벤트가 발생할 일이 없다.
* 그래서 Angular에서는 모델이 언제 변경되는지를 체크한다.
  * HTML 이벤트 헨들러가 일어났을 때 (ng-click, ng-change 등)
  * ajax통신의 결과가 도착했을 때 ($http등)
  * timer에 의해 발생된 tick 이벤트가 발생했을 때($timer등)
* 위에 경우에 관해서 angular js가  Model을 View 에 반영시키도록 하는과정을 dijest loop라고한다.
![](http://mblogthumb1.phinf.naver.net/20131217_272/jjoommnn_1387286559370YEt27_JPEG/binding03.jpeg?type=w2)
* 위그림에서 보듯이 scope에 등록된 watch가 model을 view에 반영하는 코드가되고 watch등 주기적으로 실행하는것을 dijest loop라고한다.
* !! 여기에서 중요한점은 digest loop가 작동시키는것은 angularjs이므로 위에 3가지외에 방법으로 처리하게되면 digetstloop가 발생하지않는다.(jquery 이벤트를 처리할경우이다.)

```HTML
<body ng-controller="fooCtrl">
    <div>{{text}}</div>
    <input id="btn1" type="button" value="버튼" ng-click="btnClick()" />
    <input id="btn2" type="button" value="버튼" />
</body>
```
```javascript
function fooCtrl( $scope )
{
    $scope.text = "";
    $scope.btnClick = function()
    {
        $scope.text = "btn1 click"; //변경 내용이 화면에 나타남
    }

    jQuery( "#btn2" ).click( function()
    {
        $scope.text = "btn2 click"; //변경 내용이 화면에 나타나지 않음
    });
}
```
그래서 이것을 digest loop를 강제로 동작시켜주는것이 $scope.apply()이다.
```javascript
function fooCtrl( $scope )
{
    ...
    jQuery( "#btn2" ).click( function()
    {
        $scope.text = "btn2 click";
        $scope.$apply();
    }
}
```
#### angularjs는 Model 하나하나의 개별적인 이벤트에 대해 처리하는 것이 아니라, 모든 Model을 전체적으로 한꺼번에 다루게 되는 구조가 된다.즉 위에3가지 경우가 발생하면, 등록된 모든 watch에 대해 digest loop가 수행되게 된다.
* 물론 watch를 등록할 때 데이터 표현식(해당 모델지정)을 넣어서 해당 데이터가 변경된 경우에만 핸들러가 수행하기도하지만 그래도 watch을 실행시킬경우 다른 watch들도 실행되기때문에 쓸데없이 많은 watch를 등록하지 말아야한다.


참고
* [조대협의 블로그](http://bcho.tistory.com/851)
* [백발의 개발자](http://m.blog.naver.com/jjoommnn/130181901609)

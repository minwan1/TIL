## 자바스크립트 의존성 주입
```javascript
function demoCtrl() {
    var bookmark = new UserResource(new Ajax(), new JsonParser());
}
```
일반적으로 bookmarkRes함수를 사용하려면 **ajax와 JsonParser의 인자** 까지 알고 있어야한다.이러한 문제를 해결할 수 있는게 의존성 주입이다.

데모컨트롤러가 UserRsource객체의 생성 책임을 가지면 어떨까 ? 예를들어 UserRsource가 ajax요청이 아닌 db나 쿠키를 통해정보를 가지고온다거나 json 형식이 아닌 xml을 이용한다거나 이럴경우에는 UserResource의 생성자 모두 수정해야할것이다.

UserRsource가 생성자에 의존적이지 않게 factory함수를 전역변수로 만들어 이러한 문제를 없애보자.

```javascript
var factory = {
    getBookmarkResource: function(){ return new BookmarkResource(factory.getAjax(), factory.getJsonParser(); },
    getAjax : function(){ return new Ajax(); },
    getJsonParser : function(){ return new JsonParser(); }
}
function demoCtrl(){
    var bookmarkResource = factory.getBookmarkResource();
}
```

위 코드와 같이 팩토리를 이용해 bookmarkResource 객체를 얻게 되면 데모 컨트롤러는 BookmarkResource가 어떻게 생성되는지 몰라도 된다. 필요할때만 getter setter방식으로 사용할 수 있게 된다. factory.getBookmarkResource() 함수를 통해 bookmarkResource를 생성하게되면 어떻게 BookmarkResource가 생성되었는지 몰라도 된다. 그러나 여전히 전역객체로 사용되기때문에 facetory객체를 여러군대에서 테스트하기에는 어려움이많다. fatory1,factory2등등등 이렇게 하나하나 생성해서 쓸수도 없는 노릇이지 않는가.. 그래서 밑에처럼 의존성 주입을 받아 각 컨트롤러에서 사용 할 수 있다.

```javascript
function demoCtrl(BookmarkResource){
    var bookmarkResource = BookmarkResource.get();
}
```
위에서는 BookmarkResource 을 의존받아 demoCtrl에서 얼마든지 필요한 가짜 BookmarkResource들을 받아 단위 테스트를 할 수 있다.

## Angular service
angular에서 모듈함수를 이용해 모듈을 만들면 모듈 인스턴스를 얻을 수 있다. 이만들어진 모듈 인스턴스로 서비스를 만들 수 있다. 아래와같이
```javascript
angular.module('sampleApp', [])
    //factory 메소드의 첫번째 인자로 서비스 이름을 주고 다음으로 서비스를 제공하는 팩토리 함수를 정의한다.
    .factory('hello', [function () {
        return {
            say : function (name) {
                return "hello " + name;
            }
        };
    }])
    //서비스 이름인 hello를 컨트롤러 함수 인자로 주면 위 hello 팩토리 함수가 반환하는 객체가 주입된다.
    controller('mainCtrl', function ($scope, hello) {
        $scope.greeting = hello.say("제이");
     });
```
위그림에서 hello서비스를 주입받아 hello서비스의 say메소드를 리턴받아 사용하고 있다.
아래는 angular에서 어떻게 서비스객체가 만들어지고 다른 컴포넌트로 주입이 되는지 설명하는 그림이다.

![](http://i.imgur.com/wWmbDtD.png)

위 그림을 보면 $provide.factory(...)를 볼 수 있다. 사실 모듈 API의 factory 함수는 $provide.factory 함수의 레퍼런스에 불과 하다. 편의성을 위해 모듈 인스턴스의 factory 메소드를 통해 $provide.factory 메소드를 호출한다. 이렇게 등록된 서비스 팩토리 함수는 나중에 $injector가 서비스 객체를 생성할 때 호출된다. 위 그림에서 보는 바와 같이 AngularJS 내부에서 등록된 컨트롤러를 호출할때 $injector.invoke 메소드를 사용하는데 이때 컨트롤러가 주입받고자 하는 인자의 이름을 $injector.get("인자 이름")으로 가지고와 해당 인자이름에 해당하는 팩토리 함수를 실행시켜 객체를 반환받아 그 객체를 invoke하는 함수의 인자로 전달한다.


참고
* [palpit`s log-b](http://palpit.tistory.com/893)

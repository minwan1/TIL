지금까지 느낀 팩토리패턴은 뭔가 확장성 있게 사용하기 위해 생겨 난것같다.

```javascript
function demoCtrl(){
    var bookmark = new UserResource(new Ajax(), new JsonParser());
}
```
예를 들어 위와같이 bookmark를 객체를 생성한다했을때 UserResource는 너무 적나라하게 보여진다. 이렇게되면 UserResource가 ajax가아닌 local데이터를 이용해 사용하거나, JsonParser도 json이 아닌 xml을 사용하게된다면 모든 컨트롤러에 등록되어진 북마크 객체를 수정해야한다.


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
하지만 위와같이 전역 패토리를 이용해 bookmarkResource를 팩토리 전역변수에 관리하게된다면 그부분만 고쳐주면되니까 일이 한결 수월해진다. 뭔가 여기에서 스프링 인터페이스같은 느낌? 스프링 빈등록같은 느낌을 받았다. factory.getBookmarkResource();위와같이 생성하면 북마크가 어떻게 생성되는지 컨트롤러들은 몰라도 된다.
```javascript
function demoCtrl(BookmarkResource){
    var bookmarkResource = BookmarkResource.get();
}
```

angular에 두개의 service Component가 있는데 스프링 applicationContext같은거인듯

$injector
$injector is used to retrieve object instances as defined by provider, instantiate types, invoke methods, and load modules.

$provide
The $provide service has a number of methods for registering components with the $injector. Many of these functions are also exposed on angular.Module.



[참고문헌](http://webframeworks.kr/tutorials/angularjs/dependency_injection_with_angularjs/)

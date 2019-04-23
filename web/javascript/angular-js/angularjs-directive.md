쉽게 말해 "AngularJS의 HTML Compiler에 의해 해석된 특정한 행위의 기능을 가진 DOM 엘리먼트"입니다.

디렉티브를 이해하고 생성하기 앞서 사전지식으로 HTML Compiler에 대해 알아두어야할 필요가 있습니다. built-in된, 또는 사용자가 새롭게 생성한 사용자정의 디렉티브를 HTML에서 사용할 수 있는 이유는 AngularJS의 HTML Compiler가 HTML의 DOM을 돌면서 디렉티브 이름과 같은 DOM 엘리먼트를 찾아내기 때문입니다. AngularJS의 HTML Complier의 절차는 다음의 2 단계로 축약할 수 있습니다.

compile 단계 : HTML의 DOM 엘리먼트들을 돌면서 디렉티브를 찾는다. (attribute name, tag name, comments, class name을 이용하여 디렉티브를 매칭시킨다.) 결과로 link function을 리턴한다.
link 단계 : 디렉티브와 HTML이 상호작용(동적인 view) 할 수 있도록 디렉티브에 event listener를 등록하며 scope와 DOM 엘리먼트간에 2-way data binding을 위한 $watch를 설정한다. 위의 HTML Compiler의두 단계를 거쳐 HTML에서 디렉티브를 사용할 수 있게 됩니다.

link함수는 컨트롤러나 비지니스로직에서 다루지 못하는 Dom문서 객체모델을 다루는 공간이다.


scope : false -> 새로운 scope 객체를 생성하지 않고 부모가 가진 같은 scope 객체를 공유. (default 옵션)
scope : true -> 새로운 scope 객체를 생성하고 부모 scope 객체를 상속.
scope: { ... } -> isolate/isolated scope를 새롭게 생성.

```javascript
var myApp = angular.module('myApp', []);

myApp.controller('myCtrl', ['$scope', function ($scope) {
  $scope.data = 'AAA';
}]);

myApp.directive('myDirective', function() {
  return {
    restrict: 'E',
    scope: false,
    template: '<div>{{data}}</div>',
    link: function(scope) {
      scope.data = 'BBB';
    }
  }
});

```

link 는 myCtrl에있는 $scope를 link에서 사용 가능하다.

```javascript
var myApp = angular.module('myApp', []);

myApp.controller('myCtrl', ['$scope', function ($scope) {
  $scope.data = 'AAA';
}]);

myApp.directive('myDirective', function() {
  return {
    restrict: 'E',
    scope: true,
    template: '<div>{{data}}</div>',
    link: function(scope) {
      scope.data = 'BBB';
    }
  }
});
```
그러나 위와 같이 scope 속성을 true로 주게 되면 Directive는 별도의 스코프(Isolated scope)를 가지게 되어 myDirective에서 넣어준 값이 myCtrl에는 적용되지 않는 것을 확인할 수 있다.
Directive scope 속성의 기본값은 false이므로, 별도로 스코프 지정을 하지 않는다면 부모의 스코프를 사용한다.

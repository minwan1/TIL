## angular 서비스 팩토리 차이점
AngularJS에서 서비스(Service)와 팩토리(factory)는 서로 상당한 유사성을 갖고 있기 때문에 쉽게 혼동할 수 있다. 일단 가장 큰차이는 service는 싱글톤 패턴이다. Service와 Factory 메소드의 차이는 객체를 생성하는 방법에서 차이가 있다.

서비스는 항시 Singleton이다 즉, 하나의 Instance만 존재한다.

팩토리는 다른 컨트롤러에서 인스턴스화해야할 constructor 함수가 있을때 사용될 수 있다.


```javascript
app.service('MyService', function () {
  this.sayHello = function () {
    console.log('hello');
  };
});
```

//서비스는 싱글톤


```javascript
app.factory('MyService', function () {
  return {
    sayHello: function () {
      console.log('hello');
    }
  }
});
```
Service는 인스턴스를 반환하지만 계속해서 new 키워드로 새로운 인스턴스를 만드는게 아니라 하나의 인스턴스만을 반환합니다. 즉, 싱글톤 패턴입니다.


참고
[윤영식님블로그](http://mobicon.tistory.com/329)


angularjs dto[https://medium.com/opinionated-angularjs/advanced-routing-and-resolves-a2fcbf874a1c]
https://gist.github.com/ddluc/c8aa1eb5ab6c65a3623c

## prototype


```javascript
function Person() {
  this.eyes = 2;
  this.nose = 1;
}
var kim  = new Person();
var park = new Person();
console.log(kim.eyes);  // => 2
console.log(kim.nose);  // => 1
console.log(park.eyes); // => 2
console.log(park.nose); // => 1
```

위와 같이 생성을 하면 kim과 park은 eyes와 nose를 공통적으로 가지고 있는데, 메모리에는 eyes와 nose가 두 개씩 총 4개 할당됩니다. 객체를100개 만들면 200개의 변수가 메모리에 할당되겠죠? 바로 이런 문제를 프로토타입으로 해결할 수 있습니다.


```javascript
function Person() {}
Person.prototype.eyes = 2;
Person.prototype.nose = 1;
var kim  = new Person();
var park = new Person():
console.log(kim.eyes); // => 2
...
```


간단히 설명하자면 Person.prototype이라는 빈 Object가 어딘가에 존재하고, Person 함수로부터 생성된 객체(kim, park)들은 어딘가에 존재하는 Object에 들어있는 값을 모두 갖다쓸 수 있습니다.


## Prototype Link와 Prototype Object
자바스크립트에는 Prototype Link 와 Prototype Object라는 것이 존재합니다. 그리고 이 둘을 통틀어 Prototype이라고 부릅니다

### Prototype Object
모든 객체(Object)의 조상은 함수(Function)입니다.

```javascript
function Person() {} // => 함수
var personObject = new Person(); // => 함수로 객체를 생성
```

personObject 객체는 Person이라는 함수로부터 파생된 객체입니다. 이렇듯 언제나 객체는 함수로부터 시작됩니다. 여러분이 많이 쓰는 일반적인 객체 생성도 예외는 아닙니다.

```javascript
var obj = {};
```

얼핏보면 함수랑 전혀 상관없는 코드로 보이지만 위 코드는 사실 다음 코드와 같습니다.
```javascript
var obj = new Object();
```
위 코드에서 Object가 자바스크립트에서 기본적으로 제공하는 함수입니다.
![](https://i.imgur.com/Dtbu7MS.png)

### 1.해당 함수에 Constructor(생성자) 자격 부여








사용자 정의 생성자 함수
내장 생성자가 아니라 직접 사용자가 정의한 생성자 함수 호출은 다음과 같다.

var Person =  function (name) {
this.name = name;
this.say = function () {
  return &amp;quot;I am &amp;quot; + this.name;
};
};
위의 함수는 사실 아래와 같다.

var Person =  function (name) {
// var this = {};

this.name = name;
this.say = function () {
  return &amp;quot;I am &amp;quot; + this.name;
};

// return this;
};
따라서, 생성자 함수 내부의 마지막에 다른 객체가 명시적으로 반환되지 않으면, 내부적으로 this 로 참조된 객체가 반환된다.
한가지 더 유의할 점은, 이 Person 함수의 say() 메서드는 객체 생성시에 항상 default로 따라 붙기 때문에 메모리 관점에서 비효율적이다. 그렇기 때문에 아래와 같이

Person.prototype.say = function() {
return &amp;quot;I am &amp;quot; + this.name;
}

# 자바스크립트 Execution Context
실행 가능한 코드(Executable Code) Global code, eval code, Function code 들을 만날때마다, 처음에는 Global context가 생성되고 그다음 실행가능한 코드들을 만날때마다 Execution context가 생성되고 그안에 또 Excution context들이 스택처럼 쌓인다. 각각의 Context는 언제나 1개의 변수 환경(Variable Environment), 1개의 Lexical Environment, 그리고 1개의 ThisBinding 속성, 총 3개의 부분으로 구성되 있다.

1. LexicalEnviroment
* 구성환경객체(Lexical Environment Object): 해당 Context안의 함수, 변수 등을 저장, 또한 바로 상위 단계의 정보도 저장
2. VariableEnviroment
* 변수환경객체(Variable Environment Object): 함수, 변수 등을 저장한다는 점에서 Lexical Environment와 같지만, Lexical Environment의 값은 실행 중 변하는 반면, Variable Environment의 값은 변하지 않는다는 차이점이 있다.
3. ThisBinding
* This Binding Object: 현재 Context가 참조하고 있는 객체를 가리키는 this object를 저장. Execution Context에서 사용자가 유일하게 접근 가능한 부분이다. This 가 가리키는 object는 해당 함수가 어떻게 호출되었는지에 따라 달라진다.

![](http://i.imgur.com/2tWB9In.jpg)
<center>[이미지1]</center>

이미지 1처럼 Executable code만큼 execution Context가 생성된다. 현재는 global context를 포함해 4개의 context가 생성되었다.
```javascript
var name = 'Hee'
function simple(){
  var car = 'Audi';
  demo1();
  function demo1(){
    console.log(car);
    demo2();
  }
  function demo2(){
    console.log(name);
  }
}
simple();
```

예를들어 simple()을 호출하게되면 Execution Context Stack에는 아래와같이 쌓인다.

```javascript
demo2() <<가장 먼저 실행됨.
demo1()
simple()
Global Execution Context //항상 밑에 포함된다.
```

처음에 프로그램실행시 Global Execution Context가 생성되고 simple 컨텍스트가 생성되고 demo1 context가 생성되고 demo2가 차례대로 스택에 쌓여 생성된다. 그렇게 되면 스택은 LIFO 의원리로 demo2부터 실행하게 된다. 이렇게 순서대로 스택이 쌓이므로 demo1, demo2함수는 전역변수에 접근 할 수 있지만 Global scope에서 지역 scope로는 접근이 불가능한것이다.

참고링크<br>
* [chanlee blog](http://chanlee.github.io/2013/12/10/javascript-variable-scope-and-hoisting/)<br>
* [Young-Beom Rhee](https://www.slideshare.net/youngbeomrhee/javascript-executable-code-execution-context-lexical-environment)<br>
* [ggobugi's Blog](http://ggobugi.tistory.com/248)

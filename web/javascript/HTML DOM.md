# Daily Coding 2017-02-25-HISTORY 조작


### DOM이란 ?
* HTML,XML의 문서 프로그래밍 Interface이다.
* DOM은 프로그래밍언어로 문서구조, 스타일 ,내용을 바꿀수있게 돕는다.
* 웹페이지는 일종의문서 (웹브라우저를 통해 화면에 나타남)
* DOM은 웹페이지의 객체지향 표현이다

### DOM의 트리구조

![](http://chie.co.kr/150225/images/pic_htmltree.gif)
* HTML 문서는 중첩된 태그로 구성된 계층적인 구조이다.
* 이는 DOM에서 객체 트리로 표현된다.
* DOM 트리에는 HTML 태그나 엘리먼트를 나타내는 노드가 담긴다.
* 각엘리먼트나 태그 심지어 속성 하나하를 노드로 표현했다
* DOM을통해 문서 구석구석을 탐험 할 수 있다.
* jquery는 $()을 사용하여 쉽게 접근하는 반면 자바스크립트는 브라우저 내장함수로 접근
* jQuery를 통해 선택하게 되면 jQuery객체, 순수 JavaScript를 통해 선택하면 native JavaScript객체를 반환한다

내장함수등을 지원해주는 부라우저에따라 쓸수있는 브라우저와 없는 브라우저가 존재하기때문에 속도가 느려도 jquery를 사용하는것이 효율적일 것 같음.


* DOM의 window 객체는 history 객체를 통해 브라우저 히스토리에 접근할 수 있다.
* history 객체는 사용자 히스토리에서의 앞 뒤 이동이 가능하도록 유용한 메서드와 속성들을 제공하며, history stack의 내용을 조작할 수 있게 합니다.
    window.history.back();
    window.history.forward();
* 히스토리에서 특정 위치로 가기



참고링크
* [developer.mozilla.org ^^](https://developer.mozilla.org/ko/docs/Gecko_DOM_Reference/%EC%86%8C%EA%B0%9C)
* [http://chie.co.kr/150225/html/step_01.php](http://chie.co.kr/150225/html/step_01.php)

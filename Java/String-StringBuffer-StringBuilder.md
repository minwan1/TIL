## String, StringBuffer, StringBuilder 차이

### String
String 클래스는 변경이 불가능한 immutable 클래스이다. String 클래스를 사용하여 String의 문자열을 추가할 때 기존 할당된 메모리 힙영역에 문자열을 수정할 수 없다. 예를 들어 String 문자열에 +를 하여 문자열을 추가 할경우에 내부적으로 새로운 객체를 생성하게 된다.
```java
String data = "ABC";
data += "DEF";
```

예를 들어 "ABC" 에 "DEF"가 추가되었기 때문에 한개의 String 객체가 사용되었다고 생각할 수 있지만, String 객체는 내부 데이터를 수정할 수 없으므로 새로운 객체를 생성하여 기존 data변수에 새로 생성된 String 객체를 생성한다. 아래는 그림으로 위 내용을 표현한 것이다.

![](https://i.imgur.com/UwKv9YE.png)

문자열을 결합하는 +연산자를 많이 사용할수록 String의 객체수가 늘어나기 때문에 프로그램 성능을 느리게하는 요인이 될 수 있다. 문자열에 많은 변화가 필요한경우에는 StringBuffer또는 Stringbuilder 클래스를 사용해야 한다.

### StringBuffer, Stringbuilder
StringBuffer 클래스는 변경이 가능한 mutable 클래스이다. 이 두 클래스는 내부적으로 Buffer에 문자열을 저장해두고 그안에서 추가, 수정, 삭제 작업을 할 수 있도록 설계되어 있다. String 처럼 새로운 객체를 만들지 않고도 문자열을 조작할 수 있는 것 이다.


#### StringBuffer, Stringbuilder 차이
그럼 StringBuffer와 Stringbuilder의 차이는 무엇일까.. StringBuffer는 각 메서드 별로 Synchronized 가 존재하여, 멀티스레드 환경에서도 동기화를 지원한다. 하지만, StringBuilder는 동기화를 보장하지 않는다. 그렇기 때문에 멀티스레드 환경이라면 값 동기화 보장을 위해 StringBuffer를 사용하고, 단일 스레드 환경이라면 StringBuilder를 사용하는 것이 좋다. 단일스레드환경에서 StringBuffer를 사용한다고 문제가 되는 것은 아니지만, 동기화 관련 처리로 인해 StringBuilder에 비해 성능이 좋지 않다.



참고
* [이것이자바다](http://www.hanbit.co.kr/store/books/look.php?p_code=B1460673937)
* [이러쿵저러쿵](http://ooz.co.kr/298)

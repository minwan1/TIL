## Generic이란
Generic을 사용하게되면 크게 2가지 장점이 있다. 안전하게 코딩을 할 수 있고 효율적이게 코딩할 수 있다.

먼저 안전성을 보면 Generic이 아닌 Object를 사용해서 개발하게 된다면 일단 가장 불안전한것은 Typesafe하지 않다는것이다. 이게 뭐 별거 아니겠지 생각할 수 도 있다. 하지만 Typesafe하다는것은 수많은 버그들을 방지해주는 정말 중요한 요소이다.컴파일 전단계에서 타입을 파라미터화해서 컴파일시 구체적인 타입이 결정됨으로 매우 안전하게 코딩을 할 수 있다.

그리고 Generic을 이용하면 형변환을 하지 않아도된다. 만약 List예제가 있고 이것을 String타입임에도 불구하고 Object로 받는다면 계속적인 형변환이 일어나야한다. 이것은 엄청난 자원손실이다.

간단하게 Object를 이용한 데이터 set을 만들었다.
```java
public class Box{
  priate Object object;
  public void set(Object object){ this.object = object;}
  public Object get(){return object}
}
```

위 Object를 generic으로 변경하는 예제를 만들었다.
```java
public class Box<T>{
  private T t;
  public T get(){ return t;}
  public void set(T t){this.t = t;}
}
```

E - Element (자바의 컬렉션에서 널리 사용되고 있다.)
K - Key
N - Number
T - Type
V - Value






* [이것이 자바다](http://book.naver.com/bookdb/book_detail.nhn?bid=8589375)

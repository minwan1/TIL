## TOC

![](https://i.imgur.com/BA8xwit.png)


## OOP
![](https://i.imgur.com/u5lW2A1.png)

### 1. Why Clean Code
* SW는 한번 작성되면 최소 10번 이상 읽힌다. 그래서 대충 돌아만 가게 작성하면 안되고 읽기 편하도록 작성해야한다.


### 2. Why OOP
![](https://i.imgur.com/ylOqI8r.png)


### 3. Object / Role/ Responsibility
![](https://i.imgur.com/b8P8afm.png)
보통 Spring 책을 보게되면 ArticleController,ArticleService,ArticleDao 같은게 있는데 이런건 잘못된 구조다. writeArticleService 가맞는 네이밍이다.
항상 서비스를 만들기위해서는 네이밍을 역할, 책임을 생각해야한다.  


### 4. 객체지향 설계 과정
![](https://i.imgur.com/Kc7XrNp.png)
1. 명사는 클래스로 뽑고, 동사는 메소드로 뽑는다.



### 5. Encapsulation (객체지향의 기본)
1. 내부의 변화는 외부에 영향을 주면 안된다. (코드에 따른 변경을 최소화시켜야한다.)

아래는 절차지향의 StopWatch이다
- 데이터의 변경은 모든 절차에 있어 코드 변경이 필요함
```java
public class ProceduralStopMatch{
  public long startTime; // milli seconds
  public long stopTime; // milli seconds
  public long sttartNanoTime; // nano seconds
  public long stopNanoTime; // nano seconds

  public long getElapsedTime(){
    return stopTime - startTime;
  }
  public long getElapsedNanotime(){
    return stopNanoTime - startNanoTime();
  }
}
```
아래는 객체지향의 StopWatch이다
- 해당 기능의 함수만 변경하면됨.
```java

public class StopMatch{
  public long startTime; /
  public long stopTime;


  public void start(){
    startTime = System.nanoTime();
  }

  public void start(){
    stopTime = System.nanoTime();
  }

  public Time getElapsedNanotime(){
    return new Time(stopTime - startTime);
  }
}
```
2. Tell, Don't Ask
데이터를 요청해서 변경하고 저장하라고 하지말고 기능을 실행해라.
데이터를잘알고 있는 객체에게 기능을 수행시켜야한다.
아래는 예제이다
```
if(member.getExpiredDate.getTime() < System.currentTimeMills)
if(member.isExpired())
```


3. Polymorphism(다형성)

![](https://i.imgur.com/0b8dXcY.png)


* 상위 클래스를 상속하여 상위클래스의 기능을 사용하는것이 재사용은 아니다. 그렇게 되면 그클래스를 상속하는 클래스들은 상위클래스에 의존적이게 되어 손쓸수없는 상태가 된다.(상위 인터페이스를 큰범위로 나두고 그 인터페이스를 구현체를 어떤것을 놓느냐에 따라 내부 비지니스로직이 다르게 구현하는것이 장점이다.(이것이 재사용))

* 어떤 변화가 있을지 모르니 항상 인터페이스를 만든다.
* 인터페이스를 사용하면 재사용하기 쉽다.(A라는 인터페이스에 B라는 구현체가있을때 B구현체말고 C라는 구현체를 넣어도 A인터페이스를 사용하는 클래스는 사용 가능하다. 이것이 재사용성이다.)

4. 타입 추상화
![](https://i.imgur.com/0b8dXcY.png)

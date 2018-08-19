# 이벤트

## 도메인 모델에 이벤트를 도입하려면 아래와같은 구성요소를 구현해야한다.


 이벤트 생성 주체 -> 이벤트 디스팻처 -> 이벤트 핸들러



 이벤트 생성 주체와 이벤트 핸들러를 연결해주는것이 이벤트 디스패처(dispatcher) 이다. 


 * 이벤트 생성 주체와 이벤트 해들러를 연결해주는것이 이벤트 디스패처이다.
 * 이벤트 생성 주체는 이벤트를 생성해서 디스패처에 이벤트를 전달한다.
 * 이벤트를 전달받은 디스패처는 해당 이벤트를 처리할 수 있는 핸들러에 이벤트 전파한다.
 * 이벤트는 디스패처의 구현방식에 따라 이벤트 생성과 처리를 동기나 비동기로 처리한다.



## 이벤트의 구성

이벤트는 발생한 이벤트에 대한 정보를 담는다. 이정보는 다음을 포함한다.
* 이벤트 종류 : 클래스 이름으로 이벤트 종류를 표현
* 이벤트 발생시간
* 추가 데이터 : 주문번호, 신규 배송지 정보등이 이벤트와 관련된 정보


배송지를 변경할 때 이벤트는 다음과 같을 수 있다.

* 이벤트 종류 : 클래스 이름으로 이벤트 종류를 표현
* 이벤트 발생 시간
* 추가 데이터 : 주문번호, 신규 배송지 정보등 이벤트와 관련된정보


이벤트를 위한 클래스 다음과 같을 수 있다.
```java
public class ShppingInfoChargedEvent {

    private String orderNumber;
    private long timestamp;
    private ShippingInfo newShippingInfo;

}
```


```java
//order객체
Events.raise(new ShippingInfo);
Events.raise(new ShiipingInfoChargedEvent(number, newShippingInfo));
```
위 코드에서 Event.raise()는 디스패처를 통해 이벤트를 전파하는 기능을 제공한다.

ShippingInfoChagedEvent를 처리하는 핸들러는 디스패처로부터 이벤트를 전달받아 필요한 작업을 수행한다. 



## 이벤트, 핸들러 디스패처 구현
실제 이벤트와 관련된 코드를 구현해보자. 이벤트와 관련된 코드는 다음과 같다.

* 이벤트 클래스
* EventHandler : 이벤트 핸들러위한 상위타입으로 모든 핸들러는 이인터페이스를 구현해야한다.
* Events : 이벤트 디스패처, 이벤트 발행, 이벤트핸들러 등록, 이벤트핸들러에 등록하는 등의 기능을 제공한다.

### 이벤트 클래스

이벤트 자차레르 위한 상위타입은 존재하지 않는다. 원하는 클래스를 이벤트로 사용할것이다. 이벤트는 벌어진 상태나와 변화를 의미하므로 과거형을 사용한다. 예를들어 OrderCanceledEvent라는 이름을 사용할 수 있다.

```java
public abstract class Event {

    private long timestamp;
    public Event(){
        this.timestamp = System.currentTimeMillis();
    }

    public long getTimestamp(){
        return timestamp;
    }
}
```

모든 이벤트가 공통으로 갖는 프로퍼티가 존재한다면 관련 상위 클래스를 만들 수 도 있다. 예를들어 다음과 같은 클래스를 상속하면된다.
```java
public abstract class Event {

    private long timestamp;
    public Event(){
        this.timestamp = System.currentTimeMillis();
    }

    public long getTimestamp(){
        return timestamp;
    }
}

```
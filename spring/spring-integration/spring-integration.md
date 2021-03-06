
# Spring Integration이란?
Spring integration은 스프링 기반 어플리케이션 내에 가벼운 메시징 기반서비스를 제공하고 선언적 어뎁터를 사용해 외부 시스템과 통합을 쉽게해준다. 이것은 Spring에서 기존사용하던 messaging, scheduling보다 더높은 추상화를 제공한다. Spring integration의 주된 목적은 좀더 테스트하기쉽고 유지보수하기 좋은 엔터프라이즈 Intergration 모델을 제공하는것이다.


흔히 스프링 프레임워크는 개발자에게 인터페이스를 제공하고, DI를 통해 디펜던시 의존성을 주입하여 준다. 스프링 인테그레이션은 이러한 개념에서 한걸음 더 나가, 애플리케이션 컴포넌트 레벨에서가 아닌 더 높은 상위 레벨에서 message paradigm 을 이용한 POJO 와이어링을 적용한다. 높은 레벨의 요구사항을 달성하기 위해,  재사용 가능한 세분화 된 컴포넌트를 조합하여 애플리케이션을 만들게 되는데, 디자인을 잘 고려한다면 이러한 flow가 상위레벨에서 재사용될 수 있도록 모듈화 될 수 있다.

또한 구체화된 컴포턴트를 와이어링하여, 스프링 인테으게이션은 외부 시스템과 커뮤니케이션 하기 위한 다양한 채널 어댑터와 게이트웨이를 제공한다. 채널 어댑터는 단방향(one-way) integration(보내기, 받기)으로써 사용되고, 게이트웨이는  요청/답변 시나리오(inbound, outbound)로써 사용된다. 


Spring Integration 은 스프링 프레임웍의 기본 기능에 **Messages, Message Channel, EndPoint** 라는 3가지 핵심 컴포넌트를 추가로 제공해 준다.
크게보면 어떤 메시지를 어떤 채널을 통해서 어떤 엔드포인트로 보낼것인가를 택하는것이다.


## Message

Spring Integration 에서 메시지란 메타데이터와 함께 결합되어 있는 일련의 자바 오브젝트를 위한 포괄적인 레퍼를 말한다.

아래 메시지 인터페이스가 보여주듯이 메시지는 페이로드와 여러 개의 헤더로 구성된다. 페이로드 프로퍼티는 자바 객체이고 메타데이터는 메시지 헤더들의 컬렉션이다.


## Message Channel
Spring Integration 의 메시지 채널은 pipes-and-filters 구조에서 pipe 의 역할을 한다. 메시지 발행자가 메시지를 채널로 보내면 메시지 소비자가 채널로부터 메시지를 수신하는 구조다. 이와 같이 메시지 채널은 메시징 컴포넌트들과 결합되어 있지 않기 때문에 메시지를 가로채거나 감시할 수 있는 좋은 지점이기도 하다.

![](https://i.imgur.com/VNYd2a1.jpg)
Spring Integration 의 메시지 채널은 Point-to-Point 또는 Publish/Subscribe 방식을 제공한다. Point-to-Point 채널의 경우, 채널로 보내진 메시지는 오직 하나의 메시지 소비자만이 수신할 수 있다. Publish/Subscribe 채널의 경우, 전체 메시지 구독자에게 메시지를 브로드케스트한다.



## Message Endpoint
Spring Integration 에서 Message Endpoint 는 pipes-and-filters 구조에서 filter 의 역할을 한다. 엔드포인트의 주요 역할은 어플리케이션 코드를 메시징 프레임웍에 연결해 주는 것이다. 이상적으로 어플리케이션 코드는 Message 나 Message Channel 객체에 대해서 알 필요가 없어야 한다. 엔드포인트의 역할은 MVC 패턴에서 컨트롤러의 역할과 비슷하다.  컨트롤러가 HTTP 요청을 처리하듯이 메시지 엔드포인트는 메시지를 처리한다. 컨트롤러를 URL 패턴에 매핑하듯이 메시지 엔드포인트는 메시지 채널에 매핑한다.


### Service Activator

Service Activator 는 서비스를 메시징 시스템에 연결하기 위한 엔드포인트이다. 입력 채널이 설정되어 있어야 하고 서비스가 값을 리턴하도록 구현했다면 출력 채널도 설정해야 한다.

Service Activator 는 메시지의 페이로드를 추출하거나 변환하는 식으로 메시지를 처리하기 위해서 서비스 객체의 메소드를 호출해 준다. 서비스 객체가 리턴한 값이 Message 타입이 아닌 경우라면 Message 타입으로 변환되기도 한다. 이렇게 최종 생성된 Message 타입의 응답은 출력 채널로 보내진다. 출력 채널이 설정되지 않은 경우라면, 메시지의 "return address" 에 명시된  채널로 보내지기도 한다.





![](https://i.imgur.com/H9nzIfP.jpg)

출처: https://springsource.tistory.com/47 [Rednics Blog]





## SFTP Outbound Channel Adapter
SFTP outbound channel adapter는 특별한 messageHandler이다. 이것은 원격 directory를 연결하고 수신 메시지를 받는 모든 파일을 전송하는 역할을 한다. outbound 채널 어댑터는 여러파일의 표현을 지원한다. 예를들어 ,
java.io.File: The actual file object
byte[]: A byte array that represents the file contents
java.lang.String: Text that represents the file contents
지원한다.



## 용어 설명
|이름|설명|
|-|-|
|gateway | 비즈니스 로직에서 메시지 송수신을 쉽게 도와주는 컴포넌트 |
|Service Activator | 메시지가 입력 채널에 도착했을 때 특정 빈의 메소드를 호출해주는 컴포넌트 |
|Channel Adapter | 외부 시스템과 메시 송수신 하기 위한 컴포넌트 |
|Transformer | 인풋 채널로 들어온 페이로드를 특정 목적에 맞게 변환하여 아웃풋 채널로 전달하는 엔드포인트 |
|Filter | 특정 채널로 어떤 메시지는 전달하지 말아야할지를 걸래는 컴포넌트 |
|Router | 메시지를 한개 이상의 채널로 보내는 컴포넌트 |
|Splitter | 채널로 들어온 메시지를 여러 조각으로 나눌 때 사용 |
|Aggregator | 여러 메시지를 조합하여 하나의 메시지로 통합하는 컴포넌트 |
|Message Bridge | 다른 종류의 메시징 채널이나 어댑터를 연결하는 컴포넌트 |
|Message Enricher | 수신 메시지에 추가적인 정보를 더하여 확장, 업데이트 된 객체를 하위 소비자에게 전송 |



```xml
<int: channel id="requestChannel" />

<int:gateway id="requestGateway"
            service-interface="integration.PointRequestGateway"
            default-request-channel="requestChannel" />

// 아래아웃바운드를 통해 http를 진행한다.
<int-http:outbound-gateway
        request-channel="requestChannel"
        url="http://example.com/point/>"
        http-method="GET"
        expected-response-type="java.lang.String" />

```

```java
public interface PointRequestGateway{
    Point getPoint(String id);
}
```

아래와같이 진행하게되면 주입된 interface를 통해 Spring integration을통해 http통신을 하게 된다.
```java
@Autowired
public PointRequestGateway gateway;
Point point = gateway.getPoint("41");
```


Spring integration은 messaging 기반으로 통신을 구현한다.

Messaging은 프로그램간에 빠르고 실뢰할 수 있는 통신을 비동기 방식으로 가능케하는 전송기술 



## Message
전송할 데이터가 담김 Wrapper Class
## Pipes 
Message 오브젝트를 발신 / 수신하기 위한 창구
## Filters
Message 오브젝트를 발신 / 수신하는 목적지


![](https://i.imgur.com/H9nzIfP.jpg)    

![](https://i.imgur.com/TXCNYfs.jpg)


```java
@Autowired
private DirectChannel helloChannel;

Message<String> message = MessageBuilder.withPayload("Hello world!").build();

helloChannel.send(message);
```
위와같이 보내면 채널로 메시지가 날라가게 된다.
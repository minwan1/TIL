# AMQP
Spring AMQP 프로젝트는 스프링의 컨셉을 AMQP기반의 메시징 솔루션 개발에 적용한것이다. 여기에서 제공하는 template은 높은 레벨로 추상호된 "template"을 제공한다.

# 도입
아래는 간단한 Spring AQMP를 경험해보는 간단한 예제이다.
먼저 XML로 의존성을 추가하자.
```xml
<dependency>
  <groupId>org.springframework.amqp</groupId>
  <artifactId>spring-rabbit</artifactId>
  <version>2.0.5.RELEASE</version>
</dependency>
```


 
그리고 다음과 같이 자바를 이용해서 간단하게 테스테 해볼 수 있다.
**Old Java Example**
```java
ConnectionFactory connectionFactory = new CachingConnectionFactory();
AmqpAdmin admin = new RabbitAdmin(connectionFactory);
admin.declareQueue(new Queue("myqueue"));
AmqpTemplate template = new RabbitTemplate(connectionFactory);
template.convertAndSend("myqueue", "foo");
String foo = (String) template.receiveAndConvert("myqueue");

```
여기에서 브로커에 이름이 아직없기때문에 기본 교환에 의존한다. 그리고 routingkey이름은 queue에 이름을 사용한다.


**Spring Bean Example**
```java
ApplicationContext context =
    new AnnotationConfigApplicationContext(RabbitConfiguration.class);
AmqpTemplate template = context.getBean(AmqpTemplate.class);
template.convertAndSend("myqueue", "foo");
String foo = (String) template.receiveAndConvert("myqueue");

........

@Configuration
public class RabbitConfiguration {

    @Bean
    public ConnectionFactory connectionFactory() {
        return new CachingConnectionFactory("localhost");
    }

    @Bean
    public AmqpAdmin amqpAdmin() {
        return new RabbitAdmin(connectionFactory());
    }

    @Bean
    public RabbitTemplate rabbitTemplate() {
        return new RabbitTemplate(connectionFactory());
    }

    @Bean
    public Queue myQueue() {
       return new Queue("myqueue");
    }
}
```

## 새로운 기능
2.0.2부터 Listner container로부터 사용되어지는 RabbitTemplate가 아닌 다른 커넥션으로 구성된 RabbitTemplate을 사용할 수 있다. 이것은 어떤 이유로 프로듀서가 블락되었을대 교착상태를 피하기 위한것이다.


# 참조


## AMQP 추상화
Spring AMQP는 jar로 구성되어 쉽게 사용할 수 있다. 이것은 org.springframework.amqp.core패키지를 가진다. 이것은 좀더 쉽게 AMQP방식을 지원하기 위한 패키지이다. 개발자들은 어떤 특정 Vendor에 의존하지 않고 AMQP방식의 프로토콜을 사용 수 있다. 예를들어 치 추상화되어진 package를 이용해 Spring-rabbit이라는 구현체를 사용해 rabbit-mq를 사용할 수 있다. 지금은 오로직 Rabbit-MQ구현만 있다. 그럼 본격적으로 이 AMQP패키지에 어떤것들이 있는지 알아보자. 먼저 Message 클래스를 봐보자.

### Message
Spring AMQP는 좀더 일반적인 방법으로 메시지의 도메인 모델을 정의한다. 이 메시지 클래스의 목적은 이 AMQP를 쉽게 사용할 수 있도록 캡슐화하여 제공하는것이다. 다음은 메시지클래스 본문이다.

```java
public class Message {

    private final MessageProperties messageProperties;

    private final byte[] body;

    public Message(byte[] body, MessageProperties messageProperties) {
        this.body = body;
        this.messageProperties = messageProperties;
    }

    public byte[] getBody() {
        return this.body;
    }

    public MessageProperties getMessageProperties() {
        return this.messageProperties;
    }
}
```
`MessageProperties` Interface는 messageId, timestampl, contentType 등 과 같이 몇몇의 변수들을 가진다. 이 변수들은 또한 확장되어질 수 있다. 

### Exchange
Exchange 인터페이스는 어느 메시지 생성자(Producer)가 보냈는지를 나타낸다. Virtual호스트 브로커 내에 각각의 Exchange는  몇몇의 다른 Properties뿐만 아니라 Unique 네임을 가진다.

```java
public interface Exchange {

    String getName();

    String getExchangeType();

    boolean isDurable();

    boolean isAutoDelete();

    Map<String, Object> getArguments();

}
```

보다시피 Exchange는 Direct, Topic, Fanout, header의 기본타입들을 가진다. 이것들은 상수로 정의되어져있다. 코어 패키지모듈에는 각각의 타입의 구현체들을 볼 수 있을것이다. 이 구현체들은 Queue에 어떤식으로 바인딩할지에따라 맞는 타입의 구현체를 사용한다. 
예를들면 아래와같다.
* Direct Exchange는 고정되어진 라우팅키로 큐에 배핑된다.(큐의 이름이 라우팅키와 같은경우가 있다. FanoutExchange는 모든 큐들에게 메시지를 보낸다.
* Fanout Exchange는 어떤 라우팅키에대한 생각없이 모든 큐에 메시지를 보낸다.





## 커넥션 그리고 관리
## CustomClientConnection properteis 추가
## AmqpTemplate
## 메시지 송신
## 메시지 수신
## 메시지 컨버터
## 메시지 요청/응답
## 브로커 구성

## 메시지지연
## RebbiMQ REST API
## ExceptionHandling
## 트랜잭샨
## MessageListner Container 구성
## Listner Concurrency

# 로깅시스템 AMQP Appenders

# Sample 예제
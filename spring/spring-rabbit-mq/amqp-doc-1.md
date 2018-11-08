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
* Direct Exchange는 고정되어진 라우팅키로 큐에 매핑된다.(큐의 이름이 라우팅키와 같은경우가 있다. FanoutExchange는 모든 큐들에게 메시지를 보낸다.
* Fanout Exchange는 어떤 라우팅키에대한 생각없이 모든 큐에 메시지를 보낸다.
* Topic exchange는 패턴을 기반으로 routing 키를 설정하여 보조한다


### Queue
이 큐는 메시지 호출자가 호출메시지를 받는 구성요소를 나타낸다.
```java
public class Queue  {

    private final String name; // 큐이름

    private volatile boolean durable;

    private volatile boolean exclusive;

    private volatile boolean autoDelete;

    private volatile Map<String, Object> arguments;

    /**
     * The queue is durable, non-exclusive and non auto-delete.
     *
     * @param name the name of the queue.
     */
    public Queue(String name) {
        this(name, true, false, false);
    }

    // Getters and Setters omitted for brevity

}
```

### Binding
큐를 연결하여 Producer가 Exchange로 메시지를 보내거나 Consumer가 Queue로부터 데이터를 받을 때 Exchange에 Queue들을 연결하는 Binding들은 메시징 시스템에 중요하다. 

You can bind a Queue to a DirectExchange with a fixed routing key.
```java
new Binding(someQueue, someDirectExchange, "foo.bar")
```
You can bind a Queue to a TopicExchange with a routing pattern.
```java
new Binding(someQueue, someTopicExchange, "foo.*")
```
You can bind a Queue to a FanoutExchange with no routing key.
```java
new Binding(someQueue, someFanoutExchange)
```
We also provide a BindingBuilder to facilitate a "fluent API" style.
```java
Binding b = BindingBuilder.bind(someQueue).to(someTopicExchange).with("foo.*");
```

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
## 트랜잭션
## MessageListner Container 구성
## Listner Concurrency

# 로깅시스템 AMQP Appenders

# Sample 예제




https://docs.spring.io/spring-amqp/reference/htmlsingle/




## 트랜잭션 논리

```java
RabiitTemplate.class
//기본적으로 컬백처리로 시작
private <T> T doExecute(ChannelCallback<T> action, ConnectionFactory connectionFactory) {
        Assert.notNull(action, "Callback object must not be null");
        RabbitResourceHolder resourceHolder = null;
        Connection connection = null;
        Channel channel;
        if (this.isChannelTransacted()) {
            resourceHolder = ConnectionFactoryUtils.getTransactionalResourceHolder(connectionFactory, true);
            channel = resourceHolder.getChannel();
            if (channel == null) {
                ConnectionFactoryUtils.releaseResources(resourceHolder);
                throw new IllegalStateException("Resource holder returned a null channel");
            }
        } else {
            connection = connectionFactory.createConnection();
            if (connection == null) {
                throw new IllegalStateException("Connection factory returned a null connection");
            }

            try {
                channel = connection.createChannel(false);
                if (channel == null) {
                    throw new IllegalStateException("Connection returned a null channel");
                }
            } catch (RuntimeException var11) {
                RabbitUtils.closeConnection(connection);
                throw var11;
            }
        }

        Object var6;
        try {
            if (this.confirmsOrReturnsCapable == null) {
                this.determineConfirmsReturnsCapability(connectionFactory);
            }

            if (this.confirmsOrReturnsCapable) {
                this.addListener(channel);
            }

            if (this.logger.isDebugEnabled()) {
                this.logger.debug("Executing callback on RabbitMQ Channel: " + channel);
            }

            var6 = action.doInRabbit(channel);
        } catch (Exception var12) {
            if (this.isChannelLocallyTransacted(channel)) {
                resourceHolder.rollbackAll(); //메시지 보내다가 Exception나면 롤백처리
            }

            throw this.convertRabbitAccessException(var12);
        } finally {
            if (resourceHolder != null) {
                ConnectionFactoryUtils.releaseResources(resourceHolder);
            } else {
                RabbitUtils.closeChannel(channel);
                RabbitUtils.closeConnection(connection);
            }

        }

        return var6;
    }
```


일단 이설정 때문에 지금 sqs가 트랜잭션을 지원을 안함. AUTO_ACKNOWLEDGE 이게뭔지 조사필요.


SessionAcknowledgeMode

* AUTO_ACKNOWLEDGE : 메세지 도착만하면 처리하기 전에 자동 acknowledge
* CLIENT_ACKNOWLEDGE : 메세지 처리까지 완료후 acknowledge

* DUPS_OK_ACKNOWLEDGE : 세션에 메세지 도착을 늦게 알려준다는(?) 따라서 JMS 실패로 중복메시지가 전달될 수 있다는...

* SESSION_TRANSACTED : 세션자체에 transactional이 적용되어 성공시에만 acknowledge가 가게 됨..


rabbitmq는 위와같이 지원함.


 질문을 잘 못잔달했나보네요 ㅠ. 그런데 동욱님 예제를 보면 아래와같이 컨트롤러로 오면 sqs로 포인트 접립 이벤트를 보내는것같더라고요.

 ```java
     @PostMapping("/sample2")
    public String save(@RequestBody PointDto requestDto){
        messagingTemplate.convertAndSend("sample2", requestDto);
        return "success";
    }
 ```

그렇다는것은 예를들어 음식 주문을 하면 포인트를 적립한다는 로직이 아래와같이 있을 때 트랜잭션을 어떻게 처리하는지 궁금합니다!

```java
//음식 주문 서비스

@Transactional
pulbic void orderFood(){

// 1. 음식 주문 비니지스로직수행
// 2. 위에 포인트 API(/sample2) 호출 (혹은 messagingTemplate.convertAndSend()를 이용한 포인트적립)
// 3. 음식주문 레파지토리를 통한 음식주문 정보 저장

}

```

만약 위와같이 포인트 적립 처리를 하신다면 음식주문 서비스에서 orderFood() 기능이 있을 때 API호출 완료후, 음식주문 레파지토리를 통한 음식 주문을 저장할 때 Exception이 난다고하면 이미 api는 포인트 적립 API를 호출했기 때문에 queue에 포인트 적립 정보가 들어갔을텐데요. 

이렇게 메시지큐에 이벤트가 전송된후 마지막에 Exception이 났을때 transaction처리를 어떻게 했는지 궁금합니다.!!!


I'm sorry. I added some more content. 
I tested two message queues(rabbitmq, amazon sqs) as shown below in spring.


Below is the logic to process the user email to the queue to send the signup completion email when the user completes the signup without exception.

```java
//rabbit mq configuration.class

@Bean
    public ConnectionFactory rabbitConnectionFactory() {
        CachingConnectionFactory connectionFactory =
                new CachingConnectionFactory("localhost",5672);
        connectionFactory.setUsername("guest");
        connectionFactory.setPassword("guest");
        return connectionFactory;
    }

    @Bean
    public SimpleMessageListenerContainer messageListenerContainer() {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer();
        container.setConnectionFactory(rabbitConnectionFactory());
        container.setQueueNames(queue);
       container.setMessageListener(exampleListener());
        container.setTransactionManager(platformTransactionManager);
        container.setChannelTransacted(true);
        return container;
    }


    @Bean
    public RabbitTemplate producerRabbitTemplate() {
        RabbitTemplate rabbitTemplate = new RabbitTemplate(rabbitConnectionFactory());
        rabbitTemplate.setQueue(queue);
        rabbitTemplate.setMandatory(true);
        rabbitTemplate.isChannelTransacted();
        rabbitTemplate.setChannelTransacted(true);
        return rabbitTemplate;
    }



//UserService.class

    @Autowired
    private final UserRepository userRepository;
    @Autowired
    private final RabbitTemplate rabbitTemplate;

    @Transactional
    public User createUser(final User user){
        rabbitTemplate.convertAndSend("spring-boot", user.getEmail()); // SignUp Completion email
        final User user = userRepository.save(user);
        if(true) throw new RuntimeException();
        return user;
    }
```


However, the logic above occurs a runtimeException.

Rabbit mq will not send an data to queue due to transactional annotation in Spring if an exception occurs.


```java

//amazon sqs configuration.class
	@Bean
	public QueueMessagingTemplate queueMessagingTemplate(AmazonSQSAsync amazonSqs) {
		
//UserService.class
    @Autowired
    private final UserRepository userRepository;
    @Autowired
    private QueueMessagingTemplate messagingTemplate;


    @Transactional
    public User createUser(final User user){
        messagingTemplate.convertAndSend("spring-boot", user.getEmail()); // SignUp Completion email
        final User user = userRepository.save(user);
        if(true) throw new RuntimeException();
        return user;
    }

```

However, sqs will send data to the queue even if an exception occurs.



## spring transaction

transaction을 프레임워크에 알리는 방법은 두개가 있다.


* RabbitTemplate and SimpleMessageListenerContainer 에 flag 를이용해 channelTransacted를 관리하는방법
* Spring의 플랫폼 트랜잭션 구현체 하나와 함께 외부 트랜잭션에게 진행중인 운영 문맥을 제공하는것이다.



```java



public class OrderService {

    private final ApplicationEventPublisher applicationEventPublisher;

    @Transactional
    public Food orderFood(){

        // 1. 음식 주문 비니지스로직 수행
        // 2. 음식주문 레파지토리를 통한 음식주문 정보 저장 
        applicationEventPublisher.publishEvent(new OrderFoodEvent(...));
        return Food;

    } 

    // 트랜잭션 커밋 완료 후 aop혹 위와 같이 ApplicationEventPublisher를 이용하여 아래와같이 리스너를 사용하여 비동기 포인트 적립처리

}

@Component
public class OrderFoodEventListener {


   //트랜잭션 완료되면 해당 메소드를 비동기 처리하여 포인트 적립 API호출
   @Async
   @TransactionalEventListener
    void handleEvent(OrderFoodEventEvent orderFoodEventEvent){
        // 비동기로 포인트 적립 API 호출
    }
}
```



## 신뢰성 가이드

## RabbitTransactionManager

자동으로 트랜잭션 쓰레드에 참여하게 된다.
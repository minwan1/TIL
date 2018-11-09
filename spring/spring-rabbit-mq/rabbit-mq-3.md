https://thepracticaldeveloper.com/2016/10/23/produce-and-consume-json-messages-with-spring-boot-amqp/




##

rabbitmq로 Spring smqp를 사용하는것은 정말 어렵지 않다. 
* RabbitTemplate 사용방법
* RabbitListener 사용방법


## 도입


* 1번째 클래스는 org.springframework.amqp.core.Message class 의 내용을 읽는것이다.
* 두번쨰는 유저가 생성한 구체적인 클래스의 JSON message를 읽을것이다.


![](https://i.imgur.com/Kc50xYg.png)



```java
@Service
public class CustomMessageListener {

    private static final Logger log = LoggerFactory.getLogger(CustomMessageListener.class);

    @RabbitListener(queues = MessagingApplication.QUEUE_GENERIC_NAME) // 큐네임을 주면 그냥 읽은다.
    public void receiveMessage(final Message message) {
        log.info("Received message as generic: {}", message.toString());
    }

    @RabbitListener(queues = MessagingApplication.QUEUE_SPECIFIC_NAME)
    public void receiveMessage(final CustomMessage customMessage) {
        log.info("Received message as specific class: {}", customMessage.toString());
    }
```

# 래빗  MQ설치하기
MAC기준

```
brew upgrade
brew install rabbitmq
```

```
# Open the file
nano ~/.bash_profile
 
# Add this additional line into it and save
export PATH=$PATH:/usr/local/sbin
```

모니터링 플러그인 추가(rabbitmq cmd )
```
rabbitmq-plugins enable rabbitmq-management
```


```
source 해당경로
$ rabbitmq-server
```


```
http://localhost:15672 (guest / guest )
```

![스크린샷 2018-08-02 오후 2.33.59](/assets/스크린샷%202018-08-02%20오후%202.33.59.png)




# Spring Boot Rabbit MQ - 2 : Rabbit MQ 실행


# 의존성 주입
```
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-amqp</artifactId>
		</dependency>
```

Spring-boot-starter-amqp에는 래빗 MQ중개기 접속에 필요한 모든 spring-amqp 및 rabbitmq-clinet 라이브러리가 있습니다.



```java
...
public class Producer {

    private RabbitTemplate rabbitTemplate;

    public void sendTo(final String routingKey,final String message){
        log.info("전송");
        rabbitTemplate.convertAndSend(routingKey, message);
    }
}
```

## RabbitTemplate
도우미 클래스 RabbitTemplate은 메시지를 주고 받기 위해 래빗MQ에 동기 접속하는 과정을 단순화한다.

## sendTo(rountingKey, message)
라우팅 키와 메시지를 파라미터로 받는 메소드입니다. 
* 라우팅 키가 곧 큐 이름이다. 
* 안에서는 역시 라우팅 키와 메시지를 받는 RabbitTemplate 인스턴스의 convertAndSend메소드를 호출합니다. 
* 메시지가 익스체인지(기본 익스체인지로) 전송되면 익스체인지는 메시지를 해당 큐로 실어 나른다.



```java
public class Consumer {

    @RabbitListener(queues = "${myqueue")
    public void handler(final String message){
        log.info("소비기 > " + message);
    }
    
}

```

## @RabbitListener
클래스 레벨레도 붙일 수 있는 어노테이션으로, 수신한 메시지를 처리할 핸들러 메소드를 지정한다. 다시 말해 래빗 MQ 큐를 감시할 리스너를 만들어 메시지가 들어오면 핸들러 메소드에 전달한다. 리스너는 내부적으로 메시지를 적잘한 타입으로 변한한다. (MessageConverter 인터페이스를 구현한 적절한 메시지 변환기로)



Produce, Consumer 두클래스는 코드가 아주 단순합니다. 하지만 막상 이런 코드를 래빗 MQ자바 클라이언트를 참고해서 구현혀라면 최소한 접속, 채널 메시지, 객체 생성및 메시지 전송 기능만 구현해도 코딩양이 상당하고, 접속을 열고, 채널을 생성하고, 기본적인 소비기를 생성하고, 들어오는 메시지마다 루푸를 순회하며 처리하는 등 여러가지 로직을 손수 개발해야하는 어려움이 있다. 그래서 스프링 AMQP개발팀이 코드 몇줄로 추상화한것이다.


```
myqueue = spring-boot
```

래빗 MQ에서 쓸 큐이름이다. 매인 애플리케이션 코드로 넘어가보자.


```java
@Configuration
public class RabbitMqConfig {
    
    @Value("${myqueue}")
    private String queue;
    
    @Bean
    Queue queue(){
        return new Queue(queue, false);
    }
    
    @Bean
    CommandLineRunner sender(final Producer producer){
        return args ->{
           producer.sendTo(queue, "안녕하세요. 여러분!"); 
        };
    }
}
```

## @Value String
application.properties에서 주어진프로티 값을 가져온다.

## @Bean Queue
queue 타입의 빈을 인스턴스화하고 queue 변수값(spring-boot)에 해당하는 이름의 큐를 만든다. Queue 클래스 생성자는 큐 이름과 보존여부를 파라미터로 받는데, 여기서는 보존 여부가 false여서 서버를 재시동하는 순간 큐는 사라집니다.


## @Bean CommandlineRunner
스프링 부트가 모든 자동구성을 마친후 실행할 메소드이다. 생산기(Produce)인스턴의 sendTo메소드를 호출하여 큐 이름과 메시지를 넘깁니다.(Produce 클래스에 @Componet를 붙여놔서 파라미터로 받을 때 이미 빈으로 인식합니다.)

#Rabbit MQ실행
AQMP 프로토콜에서는 큐에 바인딩된 익스체인지가 필요하므로 여기서는 spring-boot 라는 이름의 큐를 런타임에 생성해서 모든 큐를 기본 익스체인지에 바인딩했습니다. 그래서 익스체인지 정보를 하나도 주지 않았습니다. 생산기가 메시지를 보내면 일단 기본 익스체인지로 보내진뒤 큐(spring-boot)로 실려 가겠죠,


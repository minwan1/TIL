# JMS(자바 메시지 서비스)


22.2.5 Transaction management
스프링은 단일 JMS ConnectionFactory의 트랜잭션을 관리하는 JmsTransactionManager를 제공한다. JmsTransactionManager는 Chapter 11, 트랜잭션 관리에서 설명한 스프링이 관리하는 트랜잭션 기능을 JMS 어플리케이션이 사용하도록 한다. JmsTransactionManager는 로컬 리소스 트랜잭션을 수행하고 JMS 연결/세션 쌍을 지정한 ConnectionFactory에서 스레드로 바인딩한다. JmsTemplate는 자동으로 이러한 트랜잭션이 가능한 리소스를 탐지해서 적절하게 리소스상에서 작업을 실행한다.

Java EE 환경에서 ConnectionFactory는 연결과 세션의 풀을 사용하므로 이러한 리소스를 트랜잭션사이에서 효율적으로 재사용한다. 독립적인 환경에서 스프링의 SingleConnectionFactory를 사용하면 공유된 JMS Connection를 사용하게 되고 각 트랜잭션은 자신만의 독립적인 Session을 갖게 된다. 아니면 ActiveMQ의 PooledConnectionFactory 클래스처럼 프로바이더에 특화된 풀링(pooling) 어댑터를 사용하는 것을 고려해 봐라.

JmsTemplate를 JtaTransactionManager나 분산 트랜잭션을 위해서 XA 기능을 가진 JMS ConnectionFactory와 함께 사용할 수도 있다. 이 경우 XA로 잘 설정한 ConnectionFactory같은 JTA 트랜잭션 관리자를 사용해야 한다! (사용하는 Java EE 서버나 JMS 프로바이더의 문서를 참고해라.)

Connection에서 Session을 생성하려고 JMS API를 사용하는 경우 관리하는 트랜잭션 환경과 관리하지 않는 트랜잭션 환경사이에서 코드를 재사용하는 것이 혼란스러울 수 있다. 이는 JMS API가 Session을 생성하는 팩토리 메서드를 딱 하나만 가지고 있고 트랜잭션과 승낙(acknowledgement) 모드에 대한 값을 필요로 하기 때문이다. 관리되는 환경에서는 해당 환경의 트랜잭션 인프라가 이러한 값을 설정하므로 JMS 연결을 감싸는 벤더의 랩퍼는 이러한 값을 무시한다. 관리되지 않는 환경에서 JmsTemplate을 사용하는 경우 sessionTransacted와 sessionAcknowledgeMode 프로퍼티를 사용해서 이러한 값을 지정할 수 있다. PlatformTransactionManager와 JmsTemplate를 같이 사용하는 경우 템플릿은 항상 트랜잭션이 가능한 JMS Session을 가질 것이다.



출처: http://unabated.tistory.com/?page=79 [랄라라]
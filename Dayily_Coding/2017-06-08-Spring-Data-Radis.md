## Spring Radis
Spring Data Redis는 Spring Data의 부분으로 쉬운구성과 좀더 쉬운 접근성을 가진다. Spring data radis는 상호작용용으로 abstraction을 낮은레벨과 높은레벨을 제공한다.

레디스에 접속하기 위해서는 RedisClient가 필요로한다. 레디스클라이언트는 어플리케이션클라이언트와 레디스 서버사이의 연결 셋팅을 정의 하는데 사용된다. 먼저 메이븐 의존성 설정을한다.
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.data</groupId>
        <artifactId>spring-data-redis</artifactId>
        <version>1.8.4.RELEASE</version>
    </dependency>

    <dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.5.1</version>
  </dependency>
</dependencies>
```
### Java Configuration
```java
@Bean
JedisConnectionFactory jedisConnectionFactory() {
    return new JedisConnectionFactory();
}

@Bean
public RedisTemplate<String, Object> redisTemplate() {
    RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
    template.setConnectionFactory(jedisConnectionFactory());
    return template;
}
```
jedis client를 이용하여 Redis connectionFactory을 정의 할 수 잇다. jedisConnectionFactory을 사용하여 RedisTemplate를 정의한다음에 Custom repository를 쿼리하용으로 사용된다.
### 포트설정
```java
JedisConnectionFactory jedisConFactory = new JedisConnectionFactory();
    jedisConFactory.setHostName("localhost");
    jedisConFactory.setPort(6379);
    return jedisConFactory;

```

http://www.baeldung.com/spring-data-redis-tutorial

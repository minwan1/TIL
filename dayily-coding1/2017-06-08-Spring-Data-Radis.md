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

### Redis 저장할 수 있는 데이터타입
#### String
* Command Docs : http://redis.io/commands#string
* Text, 숫자 등 일반적인 Value
#### Set
* Command Docs : http://redis.io/commands#set
* Value가 Set 자료구조가 되므로, 하나의 Key 값에 여러 값을 Set 자료구조를 통해 저장 가능
* Set간의 집합 연산을 제공
#### Sorted Set
* Command Docs : http://www.redis.io/commands#sorted_set
* score라는 필드가 추가되어 정렬의 기준이 됨.
* score를 통해 질의 가능
* 그 외에는 위의 Set과 같음
#### Hashes
* Command Docs : https://redis.io/commands#hash
* Value가 Map 자료구조와 같은 Key/Value 형태가 됨
* 하나의 Key에 여러개의 필드를 갖는 구조가 됨
####  List
* Command Docs : https://redis.io/commands#list
* Value가 linkedList 자료구조가 됨
* Push/Pop 연산 가능
* index 활용 질의 가능

### Operation
Operation은 사용할 데이터 타입에 따라 다르게 사용해야 합니다. Operation들은 @Resources 어노테이션을 통해 선언해놓은 RedisTemplate로부터 주입 받을 수 있습니다.



http://www.baeldung.com/spring-data-redis-tutorial
출처: http://kingbbode.tistory.com/25 [개발노트 - kingbbode]

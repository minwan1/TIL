
# Logstash

- 데이터 수집 및 처리

- Input 기능을 이용해 다양한 곳에서 로그 데이터를 수집

- Filter를 이용해 수집한 로그 데이터를 변환

- Output 기능을 이용해 다양한 곳으로 변환된 로그 데이터를 전송

# ElasticSearch

- RESTful API 기반 분산 검색 엔진

- Logstash로부터 받은 로그 데이터를 저장하며, 검색과 분석 기능 제공

# Kibana

- ElasticSearch에 저장된 로그 데이터들을 시각화하며 검색 등 관리 기능 제공


# Spring

![](https://i.imgur.com/h1tbd4s.png)


* LogstashEncoder
* LoggingEventCompositeJsonEncoder

* <appender name="stash"
              class="net.logstash.logback.appender.LogstashTcpSocketAppender">
        <destination>127.0.0.1:5000</destination>

          <appender name="TCP" class="net.logstash.logback.appender.LogstashTcpSocketAppender">




```
최근의 로그 관리 기법의 추세는 ELK Stack 등을 이용한 중앙집중 방식이다. ELK Stack 스택을 통해 로그를 수집할 경우 전통적인 라인 단위의 텍스트 로그보다 JSON 형식의 로그를 사용하는 것이 효율적이다. logstash-logback-encoder 아티팩트를 추가함으로서 ELK Stack에 대응하는 로그를 손쉽게 생성할 수 있다.
```


```xml
          <dependency>
   <groupId>net.logstash.logback</groupId>
   <artifactId>logstash-logback-encoder</artifactId>
   <version>4.7</version>
</dependency>
```
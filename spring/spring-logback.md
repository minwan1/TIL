스프링에서는 기본적으로 commons.logging 라이브러리 ( Apache의 JCL, Jakarta Commons Logging )을 사용



## JCL 과 Log4j 사용
스프링 프레임워크는 JCL(Java Commons Logging) 을 이용해서 로그를 남긴다. 스프링이 JCL을 이용해서 생성하는 로그 메시지를 Log4j를 이용해서 남기고 싶다면, pom.xml이 파일에 다음과 같이 log4j 모듈을 추가해주면된다.


```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

![](https://i.imgur.com/bRZUiIx.png)

## logback과 slf4j

logback : log4j를 만든개발자가 log4j를 토대로 새롭게 만든 라이브러리다.

logback을 사용하기 위해선 slf4j(simple logging facade for java)를 함께 사용해야 한다. slf4j는 facade 패턴을 적용한 로깅 프레임워크다. facade 패턴은 인터페이스와 비슷한 역할이라 할 수 있다. slf4j와 함께 개발자는 원하는 로깅 라이브러리를 선택해 사용하면 된다. 따라서 slf4j를 사용한다면 개발하면서 로깅 라이브러리 변경이 필요한 경우 쉽게 교체가 가능하다.



## Spring boot log
스프링 부트는 기본적으로 JCL(Commons logging)을 기본적으로 사용한다. 

spring-boot에서는 Logback을 사용한다. 기존의 logback을 사용해본 사람은 알 것이다. Web Application을 가동하면 classpath 내에서 환경 설정 파일(logback.xml(logback-text.xml))을 검색해서 logback을 초기화시킨다. 이때는 아직 Spring이 구동되기 전의 시점이다. spring-boot에서는 logback-spring.xml을 사용해서 Spring이 logback을 구동할 수 있도록 지원해준다. 덕분에 profile이나 application.xml에 설정된 properties를 읽어올 수 있다.

여러가지 logging 라이브러리가 있는데 Spring Boot 2.0에서는 사진과 같이
Java Util Logging,
Log4j 2,
Logback을 기본적으로 사용할 수 있다.




```xml
<?xml version="1.0" encoding="UTF-8"?>
<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
        <charset>UTF-8</charset>
        <pattern>%d{yyyyMMdd HH:mm:ss.SSS} [%thread] %-3level %logger{5} - %msg %n</pattern>
    </encoder>
</appender>
```

```
%d{yyyyMMdd HH:mm:ss.SSS} - 날짜
%thread - 어떤 스레드에서 실행한 것인지
%-3level - 로깅 레벨
%logger{5} - 어떤 클래스의 로거가 실행된 건지
%msg - 메세지
%n - 줄바꿈
```



## 


### Log Level
        <!--FATAL-->
        <!--ERROR-->
        <!--WARN-->
        <!--INFO-->
        <!--DEBUG-->
        <!--TRACE-->


### Appender
로그를 출력 할 위치, 출력 형식 등을 설정할 수 있습니다.
Logback-Core 모듈을 통해 사용할 수 있는 기본적 Appender는 3가지가 있습니다.
1) ConsoleAppender
– 로그를 OutputStream에 write 하여, 최종적으로 콘솔에 출력되도록 합니다.
2) FileAppender
– 로그의 내용을 지정된 File에 기록합니다.
3) RollingFileAppender
– FileAppender로 부터 상속받은 Appender로 날짜, 최대 용량 등을 설정하여 지정한 파일명 패턴에 따라 로그가 다른 파일에 기록되도록 합니다. 이를 이용하여 대량의 로그를 효과적으로 기록할 수 있습니다.
Logback-Core의 기본 Appender 외에도 Logback-Classic 모듈의 다양한 Appender (SSLSocketAppender, SMTPAppender, DBAppender 등)을 사용하여 로그를 원격위치에 기록 할 수도 있습니다.
Appender들의 하위 항목으로 출력 형식(Layout Pattern)을 지정하여 각 Appender마다 원하는 내용을 출력시킬 수 있습니다.
ex) %logger(Logger 이름), %thread(현재 스레드명), %level(로그 레벨), %msg(로그메시지), %n(new line) 등

### Logger
실제 로그 기능을 수행하는 객체로 각 Logger마다 Name을 부여하여 사용합니다.
각 Logger 마다 원하는 출력 레벨값을 설정할 수 있으며, 0개 이상의 Appender를 지정할 수 있습니다. 각 소스로부터 입력받은 로깅 메시지는 로그 레벨에 따라 Appender로 전달 됩니다.기본적으로 최상위 로거인 Root Logger를 설정해 주어야하며, 추가로 필요한 로거에 대해 String 또는 클래스명 형식으로 Logger Name을 추가하여 사용할 수 있습니다. 또한 Logger의 Name은 .문자를 구분자로 사용하여 계층적으로 활용 할 수 있습니다.

# Srping 데이터베이스 Schema 및 Data 초기설정하기
Spring에서는 초기에 scheme 생성 및 데이터를 세팅하는 여러 가지 메커니즘을 제공합니다. 아래는 이러한 설정을 설명한 것입니다.

## Spring script-sql 설정

Spring 기본값으로 classpath 루트에 `schema.sql`, `data.sql` 파일이 있다면 서버 시작 시 스크립트를 실행합니다. 보통 schema.sql은 DDL 스크립트를 명시해두고, 데이터를 위한 DML 문은 data.sql 파일로 작성합니다. 만약 특정 환경에 맞는 sql 문을 실행하고 싶다면 아래와 같이 platform이라는 property를 설정하면 됩니다.

```yml
spring.datasource.platform=test
```

다음과 같이 설정하게 되면 schema-${platform}.sql에 test라는 변수명이 platform에 바인딩이 되어 schema-test.sql 이 실행됩니다. 하지만 memory db(h2 등)가 아닌 다른 물리적 데이터베이스(mysql 등)를 사용한다면 직접적으로 설정된 sql 문을 실행시키도록 해야 합니다. 기본적으로는 memonry db를 사용하지 않으면 schema sql 문을 실행시키지 않습니다. 그렇기 때문에 initialization-mode를 아래와 같이 always를 변경해야 schema.sql 문이 동작할 것입니다.

```java
spring.datasource.initialization-mode=always # Property for Spring boot 2.0 /
```

하지만 거의 이러한 설정은 최초 1회만 사용하여 테이블과 데이터를 셋팅하는 용도로 사용하고 그 이후에는 never 설정으로 사용하게 됩니다.


> 만약 JPA 기반에 애플리케이션을 만든다면 schema.sql을 사용할지 spring.jpa.hibernate.ddl-auto를 할지에 따라 하나를 disabled 처리를 해야 합니다.



## Spring JPA
그다음으로는 `spring.jpa.generate-ddl` properties를 사용하는 방법이 있습니다. 이 방법은 스프링이 실행될 때 자동으로 실행됩니다. `spring.jpa.generate-ddl` 설정에 boolean 값을 무엇을 넘기느냐에 따라 초기에 ddl을 실행시킬지 결정합니다.

## Spring Hibernate
그다음은 `spring.jpa.hibernate.ddl-auto` properties를 사용하는 방법이 있습니다.`spring.jpa.hibernate.ddl-auto`의 value는 enum 형식으로 관리 됩니다. spring.jpa.hibernate.ddl-auto에 enum 타입으로 `none`, `validate`, `update`, `create`, `create-drop`에 enum 형식들을 제공합니다. spring.jpa.hibernate.ddl-auto default 값은 spring에 현재 연결된 connection type에 따라 결정됩니다. 예를 들어 hsqldb, h2, derby 같은 connection이 연결되어있으면 default 값은 `create-drop`입니다. 나머지 데이터베이스 커넥션은 `none`설정이 Default가 됩니다.

```java
spring.jpa.hibernate.ddl-auto=none // 이것을 설정하면 hibernate에서 엔터티를 기반으로 객체를 생성하지 않는다.
```

또한 Hibernate는 classpath 루트의 `import.sql` 파일이 있다면 서버 시작 시 해당 스크립트를 자동 실행합니다. (이것은 ddl-auto 설정이 create or create-drop 즉 hibernate가 테이블을 만든다면 실행될 것입니다.)

만약 Spring이 인식하는 schema.sql 파일과 data.sql 파일이 있으면 동일 내용으로 import.sql 파일을 작성한다면 중복 입력으로 오류가 발생할 수 있습니다. (테이블 생성, PK 컬럼의 데이터 …) 추가로, Hibernate가 인식하는 import.sql 파일의 경우, 각 명령을 한 줄로 작성해야 문법 오류가 발생하지 않습니다.



## Srping batch database 초기화
만약 스프링 배치를 실행한다면 이것은 이미 package 안에 초기화 sql 문들이 들어있다. 기본적으로 메모리 DB들은 자동적으로 실행될 때 해당 스크립트들을 실행하고 다른 종류에 databse type들은 아라와 같은 설정으로 script sql을 실행시킬 수 있다.
```yml
spring.batch.initialize-schema=always
```
만약 초기화 설정을 끄고 싶다면 `spring.batch.initialize-schema=never`설정을 하면 됩니다.


**참고 사이트**
* [Database Initialization](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-database-initialization.html)
* [ID PRAVUS](https://pravusid.kr/java/2018/10/10/spring-database-initialization.html)

# Script-sql
Spring 기본값으로 classpath 루트에 schema.sql 파일이 있다면 서버 시작시 스크립트를 실행한다. 보통 schema.sql은 DDL 스크립트를 명시해두고, 데이터를 위한 DML문은 data.sql 파일로 작성해두면 역시 자동으로 실행한다.


# Hibernate sql 실행

Hibernate에도 기본 실행 스크립트가 있다는 점을 조심해야 한다.

Hibernate는 classpath 루트의 import.sql 파일이 있다면 서버 시작시 해당 스크립트를 자동 실행한다.

만약 Spring이 인식하는 schema.sql 파일과 data.sql 파일이 있거나, spring.datasource.data 옵션을 통해 추가로 적용할 스크립트 파일을 지정했음에도 불구하고, 동일 내용으로 import.sql 파일을 작성한다면 중복 입력으로 오류가 발생할 수 있다.(테이블 생성, PK 컬럼의 데이터 …)

추가로, Hibernate가 인식하는 import.sql 파일의 경우, 각 명령을 한 줄로 작성해야 문법오류가 발생하지 않는다

또한, JPA는 schema-${platform}.sql과 data-${platform}.sql 파일이 있다면 실행시켜 데이터베이스 플랫폼에 맞춘 스크립트 실행이 가능하다. 사용할 플랫폼 정의는 spring.datasource.platform값을 따른다



## spring boot script-sql 설정
Spring은 spring.datasource.initialization-mode값에 따라 embedded DataSource의 schema를 생성한다. 기본적으로는 emvedded datasource를 사용하지 않으면 데이터를 넣어지지 않은다. 그렇기 때문에 initialization-mode를 아래와같이 always를 변경해야한다.
```java
spring.datasource.initialization-mode=always # Property for Spring boot 2.0 /

````
그리고 아래 설정을 하게 되면 다음과같이 (schema-${platform}.sql) test라는 변수명이 platform에 바인딩이 되어 data-test.sql 이 실행된다. sql문에 이름을 넣을 수 있다.
```java
spring.datasource.platform=test
```




Spring 버전에 따라 설정에 조금 차이가 있다

```java
spring.jpa.hibernate.ddl-auto=none // 이것을 설정하면 hibernate에서 엔터티를 기반으로 객체를 생성하지 않는다.
```
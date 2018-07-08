## 1
Description	Resource	Path	Location	Type
java.lang.RuntimeException: java.lang.NoClassDefFoundError: com/querydsl/codegen/Keywords (com.mysema.maven:apt-maven-plugin:1.1.3:process:default:generate-sources)

org.apache.maven.plugin.MojoExecutionException: java.lang.RuntimeException: java.lang.NoClassDefFoundError: com/querydsl/codegen/Keywords
	at
### 해결방법: 메이븐 삭제하고 다시까니 잘되네 ㅡㅡ;


## 2

MySQLIntegrityConstraintViolationException


## 3 jpa 어디선가 exception이 나면 마지막순간까지 진행되다가 마지막순간에 트랜잭션이 끊기는순간에러exception 발생

- org.springframework.transaction.TransactionSystemException: Could not commit JPA transaction; nested exception is javax.persistence.RollbackException: Transaction marked as rollbackOnly
org.springframework.transaction.TransactionSystemException: Could not commit JPA transaction; nested exception is javax.persistence.RollbackException: Transaction marked as rollbackOnly
	at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:526)








## 4
exception 에러
- 생성자 추가안해서 에러 발생
- mysql과 자바 타입이 안맞아서 Could not deserialize - invalid stream header 에러발생
-  id to load is required for loading 프라이머리키 요청안했을대

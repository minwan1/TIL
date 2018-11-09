```java
public void methodNameA() {
    ...
    conn = ds.getConnection();
    conn.setAutoCommit(false);
 
    stmt = conn.createStatement();
 
    stmt.executeUpdate("INSERT INTO TABLE_NAME (COLUMN_NAME) VALUES ('VALUE')");
 
    conn.commit();
    ...
}
 
public void methodNameB() {
    ...
    conn = ds.getConnection();
    conn.setAutoCommit(false);
 
    stmt = conn.createStatement();
 
    stmt.executeUpdate("UPDATE TABLE_NAME SET COLUMN_NAME = 'NEW_VALUE'");
 
    conn.commit();
    ...
}


```


* 트랜잭션의 상태를 각 모듈에 직접 전파한다.
* 트랜잭션의 상태를 공용 저장공간을 이용하여 간접적으로 전파한다.

 언뜻 이해하기 힘들 수 있겠지만 간단하게 말해서 첫번째는 트랜잭션의 상태를 호출하는 메소드에 인자로 직접적으로 전달하는 것을 말하며 두번째는 메소드 내부에서 공용공간을 참조하여 간접적으로 상태를 확인하는 방법입니다.(일반적으로 ThreadLocal이 이용된다.) 



출처: http://reiphiel.tistory.com/tag/PlatformTransactionManager [레이피엘의 블로그]
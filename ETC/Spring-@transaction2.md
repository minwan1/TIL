어노테이션 트랜잭션처리시에 try catch로 잡게되면 이것은 롤백이 작업하지 않는다.

```java
class A{

    @Transactional
    public Result doStuff(){
        Result res = null;
        try {
          // do stuff
        } catch (Exception e) {

        }
        return res ;
    }
}
```

롤백하기위해서는 프로그래밍티적으로 아래와같이 선언할수있다.
```java
@Transactional(rollbackFor={MyException1.class, MyException2.class, ....})
public Result doStuff(){
   ...
}

```

아니면 아래와같이 TransactionAspectSupport을 선언할수 있다.
```java
public Result doStuff(){
  try {
    // business logic...
  } catch (Exception ex) {
    // trigger rollback programmatically
    TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
  }
}
```

참고
[stack overflow](https://stackoverflow.com/questions/25738883/spring-transactional-annotation-when-using-try-catch-block)

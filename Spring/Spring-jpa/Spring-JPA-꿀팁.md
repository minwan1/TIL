## Spirng-JPA-꿀팁


```java
@Transactional
public String completeTransaction(long id){
  Transaction transaction = transactionDao.findById(id);
  updateTransaction(id);
  transaction.setStatus("CANCELLED")
}

@Transactional
public TransferData updateTransaction(long id){
    Transaction transaction = transactionDao.findById(id);
    transaction.setStatus("WAITING");
}
```
위에 두개는 하나의 트랜잭션으로 연결된다. 결국 같은 주소를 바라보게된다. 결국 디비에는 CANCELLED가 업데이트된다

```java
@Transactional
public String completeTransaction(long id){
  Transaction transaction = transactionDao.findById(id);
  // 트랜잭션에 status가 CANCELLED 이다.
  updateTransaction(id);
}

@Transactional(propagation = Propagation.REQUIRES_NEW)
public TransferData updateTransaction(long id){
    Transaction transaction = transactionDao.findById(id);
    transaction.setStatus("WAITING");
}
```
위에처럼 가지가게되면 디비에 WAITING이 저장된다. 뭔가 다시 CANCELLED가 저장될것같았지만 WAITING이 저장된다. 그이유는 completeTransaction에서 영속성에 transaction status를 변경이 없으면 그내용을 커밋을 하지 않기때문에 updateTransaction메소드에 변경 내용만 디비에 반영된다.


```java
@Transactional
public String completeTransaction(long id){
  Transaction transaction = transactionDao.findById(id);
  // 트랜잭션에 status가 CANCELLED 이다.
  transaction.setStatus("FAILED")
  updateTransaction(id);
}

@Transactional(propagation = Propagation.REQUIRES_NEW)
public TransferData updateTransaction(long id){
    Transaction transaction = transactionDao.findById(id);
    transaction.setStatus("WAITING");
}
```
completeTransaction 에서 트랜잭션이 잠시 중지된다. 그리고 만약 completeTransaction에 transaction 객체가 변화가있으면 그것을 커밋한다



```java
@Transactional
public String completeTransaction(long id){
  Transaction transaction = transactionDao.findById(id);
  // 트랜잭션에 status가 CANCELLED 이다.

  updateTransaction(id);
    transaction.setStatus("FAILED")
}

@Transactional(propagation = Propagation.REQUIRES_NEW)
public TransferData updateTransaction(long id){
    Transaction transaction = transactionDao.findById(id);
    transaction.setStatus("WAITING");
}
```
completeTransaction 에서 트랜잭션이 잠시 중지된다. 그리고 만약 completeTransaction에 transaction 객체가 변화가있으면 그것을 커밋한다.



다른 트랜잭션을 물게되면 다른 영속성 컨텍스트를 가진다.



##

예를들어 id 1 게시판이 있다, 그러면 댓글을 저장할때 굳이 이 1을 조회해서 데이터를 저장할필요없는다. 그냥 게시판안에 new 게시판(1)을 넣어서 저장이 가능하다. 하지만 이렇게 저장한후 그트랜잭션안에서 1번 게시판을 조회해오려고하면
object references an unsaved transient instance - save the transient instance before flushing : com.test.board.댓글.게시글 -> com.test.board.comment.Comment 에러를 만날것이다


fee 테이블

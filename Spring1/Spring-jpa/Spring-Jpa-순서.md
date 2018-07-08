Spring - JPA - 클래스내에서 같은 아이디값의 오브젝트 다른 트랜잭션처리하기

```java
@Transactional
public void workRecipient(long id){
  Recipient recipient = recipientDAO.findId(id);// age:5 name:wan id=5
  updateRecipient(recipient, 13);
  recipient.setName = "test";

  // recipient의 값은 어떤값이 들어있을까 ?
}
/**
 * 이쪽에서 트랜잭션
 * @param recipient
 * @param age
 */
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void updateRecipient(Recipient recipient,int age){
  Recipient tempRecipient = recipientDAO.findId(recipient.getId);
  tempRecipient.setAge(age);
}
```

JPA에서 만약위에서 5라는 id값을 조회해와서 같은 오브젝트를 REQUIRES_NEW 속성으로 트랜잭션을 분리해서 작업하게되면 결과값은 아래와같이 된다.

// age:5 name:test id=5



```java
@Transactional
public void workRecipient(long id){
  Recipient recipient = recipientDAO.findId(id);// age:5 name:wan id=5
  updateRecipient(recipient, 13);
  recipient.setName = "test";
  throw new RuntimeException();

  // recipient의 값은 어떤값이 들어있을까 ?
}
/**
 * 이쪽에서 트랜잭션
 * @param recipient
 * @param age
 */
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void updateRecipient(Recipient recipient,int age){
  Recipient tempRecipient = recipientDAO.findId(recipient.getId);
  tempRecipient.setAge(age);
}
```

위와 같이 진행하게되면 결과값은 이렇다.
// age:13 name:test id=5

recipient 에 exception이 나든 안나든 내용이 적용되기를 원한다면 아래와같이 작성해야할것같다.


```java
@Transactional
public void workRecipient(long id){
  Recipient recipient = recipientDAO.findId(id);// age:5 name:wan id=5
  updateRecipient(recipient, 13);
  recipient.setName = "test";
  throw new RuntimeException();

  // recipient의 값은 어떤값이 들어있을까 ?
}
/**
 * 이쪽에서 트랜잭션
 * @param recipient
 * @param age
 */
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void updateRecipient(Recipient recipient,int age){
  Recipient tempRecipient = recipientDAO.findId(recipient.getId);
  recipient.setAge(age);
  tempRecipient.setAge(age);
}
```

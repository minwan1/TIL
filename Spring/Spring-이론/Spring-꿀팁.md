1. 영속성 객체
```
// REQUIRES_NEW 아예 트랜잭션 유지가 안된다.만약 hibernate에 업데이트 장점을 유지하려면 아예 새로운 영속성을 보내야한다.
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void updateRecipient(Recipient recipient,RecipientMapping recipientMapping){

}
영속성 붙은 recipient가 넘어왔어도 REQUIRES_NEW 속성떄문에 영속성이 유지안되는듯
```

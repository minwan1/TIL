자바에서 스프링 영속성 컨텍스트는 참조변수로 관리된다.


```java
@Transactional
public Transfer test1(Long id) {

    TransferData transfer1 = transferDAO.findById(id);
    tuitionInboundService.completeInbound(transfer.getId());
    transfer.setCurrentState(TransferStateEnum.PROCESSING);
    tuitionOutboundService.createOutbound(transfer.getId());
    return transfer;
}

```

```java

@Transactional(propagation = Propagation.REQUIRES_NEW)
public void completeInbound(Long id){
    Transfer transfer2 = transferDAO.findById(id);
    transfer2.getTransferDataInbound().setStatus(InboundState.COMPLETED);
}

```
transfer1은 fransfer2는 다른 영속성을 가지게된다. 아래와같다면 REQUIRES_NEW설정을 해도 의미없다 어차피 test1에 메소드의 트랜잭션을 물고있는것이다.





```java
@Transactional
public Transfer test1(Long id) {

    TransferData transfer1 = transferDAO.findById(id);
    tuitionInboundService.completeInbound(transfer);
    transfer1.setCurrentState(TransferStateEnum.PROCESSING);
    return transfer;
}

```

```java

@Transactional(propagation = Propagation.REQUIRES_NEW)
public void completeInbound(transfer){
    transfer.getTransferDataInbound().setStatus(InboundState.COMPLETED);
}

```



## 신뢰성

## Connection Failures
커넥션이 실패했을 떄 자바에서는 ShutdownListener 컬백을 제공할것이다.


## Acknowledgements and Confirms
커넥션이 실패했을 대 메시지는 와이 혹은 OS버퍼등에 존재할 수 있다. 이러한 메시지는 손실될것이다. 이메시지는 재전송이 필요할것이다. Acknowledgements는 서버와 클라이언트를 인지하게해준다.

Acknowledgements전송은 producer, consumer 둘다 rabbitmq서버에게 메시지를 생성, 소비를 했다고 해주는 신호이다. 프로듀서가 메시지를 생성하고 서버에게 보낸 후 Acknowledgements전송을 하는것을 `Confirm`이라한다.

물론 이러한 역할을 TCP가 하지만 이것은 그냥 네트워크 레벨에서 처리해주는것이다. Acknowledgements이것의 알림은 후에 메시지 손실은 이제 서버, 그리고 consumer에 책임이다. 그렇기 때문에 데이터베이스등에 저장될때 까지 Acknowledgements호출을 하면 안된다.


acknowledgements사용은 최소 한번의 배달을 보장한다.




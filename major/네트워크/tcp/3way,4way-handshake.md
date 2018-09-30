# TCP 3 Way-Handshake & 4 Way-Handshake
## TCP
TCP는 네트워크 계층 중 전송 계층에서 사용하는 프로토콜 로서, 장치들 사이에 논리적인 접속을 성립(establish)하기 위하여 연결을 설정하여 신뢰성을 보장하는 연결형 서비스 이다.


## 3 Way-Handshake
TCP 에서 서버와 클라이언트가 연결을 맺을때 3-way handshaking 을 하게 된다. TCP 통신을 이용하여 데이터를 전송하기 위해 **네트워크 연결을 설정(Connection Establish)** 하는 과정이다.


### TCP의 3-way Handshaking 역할


1. 양쪽 모두 데이타를 전송할 준비가 되었다는 것을 보장하고, 실제로 데이타 전달이 시작하기전에 한쪽이 다른 쪽이 준비되었다는 것을 알 수 있도록 합니다.

2. 양쪽 모두 상대편에 대한 초기 순차일련변호를 얻을 수 있도록 합니다.

양쪽 모두 데이터를 전송할 준비가 되었다는 것을 보장하고, 실제로 데이터 전달이 시작하기 전에 한 쪽이 다른 쪽이 준비되었다는 것을 알 수 있도록 한다.
즉, TCP/IP 프로토콜을 이용해서 통신을 하는 응용 프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정을 의미한다.



### 연결과정

![](https://i.imgur.com/Pa3rQRK.jpg)

여기서 SYN은 'synchronize sequence numbers', 그리고 ACK는'acknowledgment' 의 약자이다.


1. Client에서 Server에 연결 요청을 하기위해 SYN 데이터를 보낸다.



2. Server에서 해당 포트는 LISTEN 상태에서 SYN 데이터를 받고 SYN_RCV로 상태가 변경된다.

  그리고 요청을 정상적으로 받았다는 대답(ACK)와 Client도 포트를 열어달라는 SYN 을 같이 보낸다.



3. Client에서는 SYN+ACK 를 받고 ESTABLISHED로 상태를 변경하고 서버에 요청을 잘 받았다는 ACK 를 전송한다.

 ACK를 받은 서버는 상태가 ESTABLSHED로 변경된다.



위와 같이 3번의 통신이 정상적으로 이루어지면, 서로의 포트가 ESTABLISHED 되면서 연결이 되게 된다.



### Status

- Closed : 닫힌 상태
- LISTEN : 포트가 열린 상태로 연결 요청 대기 중
- SYN_RCV : SYNC 요청을 받고 상대방의 응답을 기다리는 중
- ESTABLISHED : 포트 연결 상태

출처: http://hyeonstorage.tistory.com/286?category=583737 [개발이 하고 싶어요]


## 4 Way-Handshake
TCP에서 서버와 클라이언트 서로 연결을 해제하려면 4-way handshake 를 하게 된다.

![](https://i.imgur.com/ggLmdqi.jpg)

### 정상 종료 상황

1. 통신을 종료하고자 하는 Client가 서버에게 FIN 패킷을 보내고 자신은 FIN_WAIT_1 상태로 대기한다.
2. FIN 패킷을 받은 서버는 해당 포트를 CLOSE_WAIT으로 바꾸고 잘 받았다는 ACK 를 Client에게 전하고 ACK를 받은 Client는 상태를 FIN_WAIT_2로 변경한다.

그와 동시에 Server에서는 해당 포트에 연결되어 있는 Application에게 Close()를 요청한다.

3. Close() 요청을 받은 Application은 종료 프로세스를 진행시켜 최종적으로 close()가 되고 server는 FIN 패킷을 Client에게 전송 후 자신은 LAST_ACK 로 상태를 바꾼다.



4. FIN_WAIT_2 에서 Server가 연결을 종료했다는 신호를 기다리다가 FIN 을 받으면 받았다는 ACK를 Server에 전송하고 자신은 TIME_WAIT 으로 상태를 바꾼다. (TIME_WAIT 에서 일정 시간이 지나면 CLOSED 되게 된다.)

최종 ACK를 받은 서버는 자신의 포트도 CLOSED로 닫게 된다.


### 비정상 종료 상황



따라서 다양한 상황에 따른 연결의 종료를 적절하게 처리하지 못하면, FIN_WAIT_1, FIN_WAIT_2, CLOSE_WAIT 상태로 남아 계속 기다리는 상황이 올 수 있다.



- CLOSE_WAIT 상태 : 어플리케이션에서 close()를 적절하게 처리해주지 못하면, TCP 포트는 CLOSE_WAIT 상태로 계속 기다리게 된다. 이렇게 CLOSE_WAIT 상태가 statement에 많아지게 되면, Hang 이 걸려 더이상 연결을 하지 못하는 경우가 생기기도 한다. 따라서 어플리케이션 개발시 여러 상황에 따라 close() 처리를 잘 해줘야 한다.



- FIN_WAIT_1 상태 : FIN_WAIT_1 상태라는 것은 상대방측에 커넥션 종료 요청을 했는데, ACK를 받지 못한 상태로 기다리고 있는 것이다. 이것은 아마 서버를 찾을 수 없는 것으로, 네트워크 및 방화벽의 문제일 수 있다.

(FIN_WAIT_1 의 상태는 일정 시간이 지나 Time Out이 되면 자동으로 닫는다.)



- FIN_WAIT_2 상태 : FIN_WAIT_2 상태는 클라이언트가 서버에 종료를 요청한 후 서버에서 요청을 접수했다고 ACK를 받았지만, 서버에서 종료를 완료했다는 FIN 을 받지 못하고 기다리고 있는 상태이다. 이상태는 양방의 두번의 통신이 이루어졌기 때문에 네트워크의 문제는 아닌 것으로 판단되며,(FIN 을 보내는 순간에 순단이 있어 못받은 것일 수도 있다.) 서버측에서 CLOSE를 처리하지 못하는 경우일 수도 있다. FIN_WAIT_2 역시 일정시간 후 Time Out이 되면 스스로 Closed 하게 된다.



- 어떠한 이유에서 FIN_WAIT_1과 FIN_WAIT_2 상태인 연결이 많이 남아있다면, 문제가 발생할 수 있다. 물론 일정 시간이 지나 Time Out이 되면 연결이 자동으로 종료되긴 하지만, 이 Time Out이 길어서 많은 수의 소켓이 늘어만 난다면, 메모리 부족으로 더 이상 소켓을 오픈하지 못하는 경우가 발생한다.

(이 경우는 네트워크나 방화벽 또는 어플리케이션에서 close() 처리 등에 대한 문제등으로 발생할 수 있으며, 원인을 찾기가 쉽지 않다.)

- 이러한 문제 해결을 위해서 FIN_WAIT_1과 FIN_WAIT_2 의 Time Out 시간을 적절히 조절할 필요가 있다.
- 각 상태의 Time Out 시간 확인 및 설정은 유닉스 환경에서 ndd 명령어를 통해 확인할 수 있다.


참고
* [Mind Net](http://mindnet.tistory.com/entry/네트워크-쉽게-이해하기-22편-TCP-3-WayHandshake-4-WayHandshake)
* [개발이 하고 싶어요](http://hyeonstorage.tistory.com/286?category=583737)


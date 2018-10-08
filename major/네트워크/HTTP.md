# HTTP는 Connection Less 방식으로 연결을 매번 끊고 새로 생성하는 구조이다.
+ Network 비용측면에서 많은 비용을 소비하는 구조이다.

# HTTP 1.1 부터는 Keep-Alive 라는 기능을 제공한다.

# Keep Alive란 
+ Socket에 IN/OUT Access가 마지막으로 종료된 시점부터 지정된 시간까지 Access 가 없더라도 
대기하는 구조. 정의된 시간내에 Access 가 이뤄진다면 연결상태를 유지.

# HTTP 에서 Keep Alive 
+ Connection Less 방식이라면 매번 Socket(port)를 열어야 하고 비용적인 측면에서 비효율적이다.
+ Keep Alive Time Out 이내에 Client 에서 Request를 재 요청하면 Socket을 새로 여는 것이아니라.
이미 열려 있는 Socket(port)에 전송하는 구조가 된다.
+ port Conenction에 소요되는 시간을 절약하게 된다.

# HTTP 는 기본적으로 Connection-Less 방식이다. 즉  맺어진 Socket 연결은 유지되지 않고
매번 끊어지며, 다시 생성되는 구조이다.


출처: https://weicomes.tistory.com/1 [25%]
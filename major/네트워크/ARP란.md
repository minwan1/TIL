# ARP란

## ARP Protocol (Address Resolution Protocol - 주소 결정 프로토콜)이란
목적지의 IP address는 알지만, 드웨어 주소(MAC주소)를 모를 때 Target MAC address 부분을 00:00:00:00:00:00으로 채우고 패킷의 목적지를 broadcast(ff:ff:ff:ff:ff:ff) 로 하여 네트워크 상의 모든 노드들한테 뿌려서Target MAC address를 얻어오는 것을 ARP protocol이 하는 일 입니다.즉 **논리적 주소를 물리적 주소로 변환시키는 일을 한다.**



## ARP 변환과정

![](https://i.imgur.com/JjuE6n9.jpg)
1. Host A 가 Host B 에게 IP packet 을 보내려고 한다.
2. Host A 는 Host B 의 주소를 알고 있다. 하지만 L2주소(Ethernet Addr 또는 MAC Addr) 는 모른다.
3. Host A 는 Host B 의 L2 주소를 알기 위해 ARP Request 를 Broadcast 한다.
이때 ARP Request 에는 Host B 의 IP Address 가 포함된다.
4. Host C 는 ARP Request 를 받았을때 ARP Request 에 포함된 IP Address 가 자신의 IP Address 가 아니기때문에 그냥 무시한다.
5. Host B 는 ARP Request 에 포함된 IP Address 가 자신의 IP 이므로 Host A 에서 Host B 의 L2주소 (Ethernet addr 또는 MAC addr) 를 알고 싶어한다는 것을 알고 자신의 L2 주소를 ARP Response 를 주어 응답한다.
6. 호스트 A는 호스트 B로부터 ARP Response 를 받고 호스트 B의 L2 주소(aa:bb:cc:dd:ee:ff) 를 알게 된다.
7. 호스트 A 는 이 정보를 가지고 IP Address 와 L2 주소를 매칭시킨 ARP Table 을 만들어서 캐시에 저장한다.
8. 호스트 A는 호스트 B로 L2주소를 써서 직접 IP 패킷을 전송한다.




## RARP protocol
반대로 목적지의 MAC 주소는 알지만, 목적지의 IP 주소를 모를때 네트워크 상에서 IP 주소를 요청하기 위해 사용하는 프로토콜은 RARP protocol 입니다.

참고
* [김칫찌개's 임시공간](https://m.blog.naver.com/PostView.nhn?blogId=lycox&logNo=220656038438&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)

* [개발자 지망생](http://blockdmask.tistory.com/189)
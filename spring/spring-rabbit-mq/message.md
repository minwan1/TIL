## Message Queue는 왜 사용해야 하는가?
일반적인 서버-클라이언트 구조에서는 사용자가 요청을 하면 서버는 그에 대한 처리를 한 후 사용자에게 응답을 한다. 간단한 서버 구조에서는 굳이 Message Queue(이하 MQ)를 사용할 필요가 없다. 우선 MQ를 적용하려면 RabbitMQ, Kafka, ActiveMQ등 다양한 MQ 중에서 시스템 목적에 맞는 MQ를 선정해야 하고 또 서버에 MQ를 설치해야 한다


## 애플리케이션/시스템 간의 통신
서버 간에 데이터를 주고 받거나 어떤 작업을 요청을 할 때는 항상 시스템 장애를 염두에 두어야 한다. 서버가 갑자기 죽거나 서버 점검 등으로 다운타임이 발생하는 동안에는 요청을 보낼 수가 없다. 요청하는 서버에서 failover 처리를 해놓고 연계 시스템이 다시 살아났을 때 요청을 보내는 방법도 있지만 MQ를 이용하면 더욱 간편하게 처리할 수 있다.
![](https://i.imgur.com/KbTxOMa.png)

P는 C에 직접 요청하는 것이 아닌 MQ에 전달한다. 그럼 C는 MQ로 부터 요청 데이터를 수신해서 처리한다. 만약 C가 요청을 받을 수 없을 수 없는 상황이라면 해당 요청은 C가 받을 때까지 MQ에 머무르게 된다.
물론 이런 상황에서 MQ에 다운타임이 발생하면 무용지물이 되어버리겠지만, 많은 MQ가 고가용성을 위해 클러스터링 등을 지원한다.



# Spring boot 메시징

메시징은 하나 또는 그이상의 엔터티간에 정보를 주고 받는 방법이다. 컴퓨터가 최초로 발명된 시점부터 어떤 형태로든 존재해왔고, 하드웨어 컴퍼넌트와 소프트웨어 컴포넌트가 서로 소통하는 수단이었다. 메시징은 하나의 송신자와 하나 또는 그이상의 수신자로 구성되며, 도익와 비동기, 펍섭과 피어투피어, RPC와 엔터프라이즈 기반 메시지 브로커인 ESB 등 다양한 기술이 있다.

무엇보다 분명한 사실은 메시징이 결합도를 느슨하게 만드는 분산 커뮤니케이션을 실현한다는 점이다. **즉 송신자가 어느 메시지를 어떻게 발생하건 수신자는 송신자에게 알리지 않고 메시지를 소비하는 구조이다.**



#메시징 큐 

# 메시지 큐 장점

메시지 큐의 장점:
비 동기(Asynchronous) : Queue에 넣기 때문에 나중에 처리 할 수 있다.
비 동조(Decoupling) : 애플리케이션과 분리 할 수 있다.
탄력성(Resilience) : 일부가 실패 시 전체에 영향을 받지 않는다.
과잉(Redundancy): 실패 할 경우 재실행 가능
보증(Guarantees): 작업이 처리된 걸 확인 할 수 있다.
확장성(Scalable): 다수의 프로세스들이 큐에 메시지를 보낼 수 있다.
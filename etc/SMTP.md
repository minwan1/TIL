## SMTP
SMTP는 SMTP (Simple Mail Transfer Protocol)의 약자입니다. 일반적으로 전자 메일 전송을위한 표준 프로토콜입니다. 이메일을 송수신하는 서버를 SMTP 서버라고합니다. 여기에서는 전자 메일을 보내는 과정과 SMTP 서버에서 수행되는 순서를 알아보겠습니다.

1. 사용자는 mail client (outlook 또는 eudora)와 같은 프로그램을 통해서 mail을 작성한 후, SMTP를 사용하여 mail deamon으로 메시지를 전송합니다.

2. 메일 데몬은 종단간 client의 주소를 분석하고 가장 가까운 mail server(송신자 소속의 메일서버)로 메시지와 정보를 보냅니다.

> ※ mail deamon이란 일종의 프로세스로서, 송신자의 메일과 정보를 메일 서버가 해석 가능하도록 재가공합니다. 반대의 경우 또한 같은 방법으로 수신자의 정확한 메시지 수신과 릴레이를 지원합니다.
참고적으로, UNIX 기반의 대표적인 SMTP mail deamon으로는 [send mail]이 있습니다.

3. 송신자가 보낸 편지가 일단 송신자 측의 전자우편을 관리하는 Mail Server에 전달되면,  Mail Server는 수신자의 전자우편 주소를 분석해서 최단 경로를 찾아 근접한 Mail Server에 편지를 전달하게 된다.

4. 최종 수신자측의 Mail Server에 도착하기까지 연속적으로 전달하는 중계작업이 계속된다.

서로 근접한 Mail Server들 간에 전자우편을 계속해서 중계해 나가는 방법을 통해 메일을 저장 후 전송 (Store-and-forward)하는 서비스를 하게 된다.

55 이러한 일련의 작업이 계속적으로 이루어 진후, 송수신자는 정확하게 메일 교환을 할 수 있게 된다.



참고
* [랄라라](http://unabated.tistory.com/entry/mail-전송의-원리)
# Daily Coding 2017-04-29일

## SSH (Secure Sell)
![](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/432/1210.gif)
Secure Sell의 약자로 원격지에 있는 컴퓨터를 안전하게 제어하기 위한 프로토콜 또는 이 프로토콜을 사용하는 프로그램들을 의미한다. SSH 클라이언트와 SSH 서버의 관계로 상호작용하면서 SSH 서버가 설치된 운영체제를 제어한다. 클라이언트와 서버 사이에는 강력한 암호화 방법을 통해서 연결되어 있기 때문에 데이터를 중간에서 가로채도 해석 할 수 없는 암호화된 문자만이 노출된다. 지금까지는 Telnet을 주로 사용했는데 이것을 대체하기 위한 통신 방법이다. 명령어로 원격서버를 제어하는것


### SSH Client
putty,터미널

### SSH ServletWebRequest
유닉스 계열의 운영체제에서는 OpenSSH


### SSH Key란?
서버에 접속 할 때 비밀번호 대신 key를 제출하는 방식이다.

chmod 600 : 소유자만 읽고 쓸수있음.
### ssh-keygen 이란



http://34.208.115.221:8080/jenkins/
a8c30be51965309125fbbe1b2eac1be7d69d1d20

## jenkins 설치하기
1. 젠킨스.war 다운
    * http://mirrors.jenkins.io/war-stable/latest/jenkins.war
2. 포트열어주기
    * aws의 경우 홈페이지에서 열어주고, 일반리눅스의경우 명령어로 포트열기
3. 톰켓 실행
4. jenkins관리에서 Configure global security의 회원관리 설정

5. Global tools Configuration
    * java(jdk)  /usr/java/jdk1.8.0_05/
    * git (version control tool)
    * Maven (build tool)
6. publish over ssh 플러그인추가
    * jenkins관리 - 시스템설정 - publish over SSH
    * ssh 서버설정



## NGINX
### 엔진엑스 설치
  * yum install nginx
  *  /etc/nginx/nginx.conf 여기서 기본셋팅 변경할 수 있음















참고
* [생활 코딩](https://opentutorials.org/module/432/3742)

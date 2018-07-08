## 리눅스 명령어
* ps -e | grep "mysql" // 프로그램 pid찾는것
* find / - name "mysql" //파일찾기
* mysql -u root -p //mysql 접속

## curl 명령어 헬스체크
curl -X GET --header 'Accept: application/json' 'http://localhost:8080/noticeBar?type=greeting'

## 맥 명령어
* cd /Applications //맥 응용프로그램 접속


## 로컬 배포방식
mvn -f pom_dev.xml clean package



scp -i ~/.ssh/dev.pem target/$WAR_FILE ubuntu@$TARGET_IP:/opt/tomcat/webapps/ROOT.war


## angular spring 리눅스 셋팅

## 노드 설치
sudo yum install nodejs npm --enablerepo=epel

sudo npm install -g bower -y
sudo npm install -g grunt-cli
sudo npm install grunt --save-dev
sudo npm install grunt-contrib-jshint --save-dev
sudo apt-get install ruby-compass -y

## chrome 불안전한 모드로 열때

sudo gem install compass
'/Applications/Google Chrome.app/Contents/MacOS/Google Chrome' --allow-running-insecure-content


## 포트확인명령어(뒤에 포트설정에따라 줄수있음 22는 ssh)
telnet ec2-13-124-128-232.ap-northeast-2.compute.amazonaws.com 22
telnet 13.124.132.88 22
## 리눅스 실시간 테일
tail -f  마지막 열줄 계속계속해서 출력
## 리눅스 검색
/  뒤에 찾을말

그 다음 줄에 있는 apple 이라는 단어를 또 검색하려면 키보드의 소문자 n 을 눌러주면 됩니다. next 라는 의미겠지요. 그리고 역방향으로 즉 거꾸로 거슬러 올라가며 다시 찾으려면 대문자 N 을 입력합니다.

## tail 명령어
컨트롤 b = 페이지 맨위로
컨트롤 f = 페이지 아래로
tail -f  마지막 열줄 계속계속해서 출력

## 로그파일 위치
/opt/tomcat/logs


## 톰켓위치

HOME : /usr/share/tomcat7
CONF :  /etc/tomcat7
LOG :  /var/log/tomcat7
ROOT : /var/lib/tomcat7  

## 톰켓관련 명령어
sudo service tomcat7 restart
sudo service tomcat7 status

## 리눅스 용량확인
```
df -k : 킬로바이트 단위로 현재 남은 용량을 확인

df -m : 메가바이트 단위로 남은 용량을 왁인

df -h : 보기 좋게 보여줌

df . : 현재 디렉토리가 포함된 파티션의 남은 용량을 확인
```

## 메이븐 빌드 명령어
mvn -f pom_dev.xml clean package


## 리눅스 명령어
ps -ef | grep java  //자바켜져있는지 확인
nslookup admin_dev.sodatransfer.com // ip보는 명령어
ls -altr  // 현재위치에서 파일업데이트된 시간확인하는것

출처: http://chago.tistory.com/4 [Mr,Cha GO!!!]

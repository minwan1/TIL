## 리눅스 명령어
* ps -e | grep "mysql" // 프로그램 pid찾는것
* find / - name "mysql" //파일찾기
* mysql -u root -p //mysql 접속


## 맥 명령어
* cd /Applications //맥 응용프로그램 접속


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
## 리눅스 실시간 테일
tail -f  마지막 열줄 계속계속해서 출력
## 리눅스 검색
/  뒤에 찾을말

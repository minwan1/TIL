# Docker


# 1. Docker 이미지
도커 이미지는 컨테이너 실행에 필요한 파일과 설정값등을 포함하고 있는 것으로 상태값을 가지지 않고 변하지 않습니다(Immutable).  컨테이너는 이미지를 실행한 상태라고 볼 수 있고 추가되거나 변하는 값은 컨테이너에 저장됩니다. 같은 이미지에서 여러개의 컨테이너를 생성할 수 있고 컨테이너의 상태가 바뀌거나 컨테이너가 삭제되더라도 이미지는 변하지 않고 그대로 남아있습니다

# 2. Docker 기본 명령어

**Docker 설치된 이미지 이미지 확인**
```
docker imangs // 설치된 이미지 확인
```

**현재 실행중인 Docker 이미지**
```
docker ps -a //docker ps 확인
```

**도커 이미지 제거**
```
docker rm [CONTAINER ID] // docker 컨테이너 삭제
docker rmi -f `docker images` //docker 모든 이미지 제거
```
**도커 컨테이너 제거**
```
docker rmi [IMAGE ID] // 이미지 삭제 

**도커 네트워크 리스트**
```
docker network ls
```
```
# 3. Docker 이미지 설치
Ø

**Docker MYSQL 설치**
```
docker pull mysql:5.6ql
```O
# 4. Dcoker 컨테이너 실행

**컨테이너 실행하기**
```
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
ex)docker run --name mysql -d -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql:5.6 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci

sudo docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 --restart=unless-stopped -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest rabbitmq:management
```
> -p 3306:3306 : 호스트의 3306포트와 컨테이너의 3306포트를 연결한다. 즉 호스트에 3306포트 접근이 발행하면 해당 컨테이너에 접속이 된다.

**명령어 옵션**
| 옵션    | 설명                                |
| ----- | --------------------------------- |
| -d    | detached mode 흔히 말하는 백그라운드 모드     |
| -p    | 호스트와 컨테이너의 포트를 연결 (포워딩)           |
| -v    | 호스트와 컨테이너의 디렉토리를 연결 (마운트)         |
| -e    | 컨테이너 내에서 사용할 환경변수 설정              |
| –name | 컨테이너 이름 설정                        |
| –rm   | 프로세스 종료시 컨테이너 자동 제거               |
| -it   | -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션 |
| –link | 컨테이너 연결 [컨테이너명:별칭]                |


**실행된 컨테이너 접속하기**
```
ex) docker exec -i -t mysql_test bash //도커 컨테이너에 접속
mysql -u root -p
```





참고
[도커](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)







# 추가 확인 필요


도커 리눅스 설치
```
sudo yum install -y docker-io #centos docker
curl -s https://get.docker.com/ | sudo sh
sudo usermod -aG docker $USER //docker 그룹을 sudo모드로 실행하는것(유저는 유저이름 )
sudo usermod -aG docker $USER # 현재 접속중인 사용자에게 권한주기
```
docker version

```
Client:
|Version:      17.03.1-ce|
 API version:  1.27
 Go version:   go1.7.5
 Git commit:   7392c3b/17.03.1-ce
|Built:        Tue May 30 17:59:44 2017|
|OS/Arch:      linux/amd64|

Server:
|Version:      17.03.1-ce|
 API version:  1.27 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   7392c3b/17.03.1-ce
|Built:        Tue May 30 17:59:44 2017|
|OS/Arch:      linux/amd64|
 Experimental: false
```

버전정보가 클라이언트와 서버로 나뉘어져 있습니다. 도커는 하나의 실행파일이지만 실제로 클라이언트와 서버역할을 각각 할 수 있습니다. 도커 커맨드를 입력하면 도커 클라이언트가 도커 서버로 명령을 전송하고 결과를 받아 터미널에 출력해 줍니다.
![](http://i.imgur.com/eQN0q8D.png)



docker compose
여러가지 컨테이너를 한번에 빌드할수 있게해주는 툴


docker 젠킨스 실행
```
docker run -p 8080:8080 -v /docker/jenkins:/var/jenkins_home -u root jenkins
docker run -d -p 8080:8080 -v /docker/jenkins:/var/jenkins_home -u root jenkins //background돌림
docker run -i -t jenkins /bin/bash //도커 접속하는것
``




![](http://i.imgur.com/WpUwCNI.png)
* 하이퍼바이저(버추얼박스,vmware)

Docker 명령어
docker start
docker stop
docker restart
docker pause
docker unpause
docker wait
docker kill
docker attack
docker exec

```
sudo usermod -aG docker ${USER} //docker 그룹을 sudo모드로 실행하는것(유저는 유저이름 )
sudo service docker restart
```

```
docker pull redis:latest //도커받는것
docker images // 다운받아진 도커이미지 확인하는것

docker run -i -t --name myredis -d redis
//-i -t는 인터렉티브하게 명령어창에서 실행해라. myredis<< 컨테이너 이름

docker exec -it myredis /bin/bash
redis-cli <<이명령어는 redis에 접속하게 해주는것
set mykey "hello"
get mykey


docker ps //현재실행중인 컨테이너 목록
docker start myredis// 도커를 스타트하는것
docker stop myredis// 도커를 멈추는것


```

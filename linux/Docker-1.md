## Docker

도커 이미지는 컨테이너 실행에 필요한 파일과 설정값등을 포함하고 있는 것으로 상태값을 가지지 않고 변하지 않습니다(Immutable).  컨테이너는 이미지를 실행한 상태라고 볼 수 있고 추가되거나 변하는 값은 컨테이너에 저장됩니다. 같은 이미지에서 여러개의 컨테이너를 생성할 수 있고 컨테이너의 상태가 바뀌거나 컨테이너가 삭제되더라도 이미지는 변하지 않고 그대로 남아있습니다.

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
 Version:      17.03.1-ce
 API version:  1.27
 Go version:   go1.7.5
 Git commit:   7392c3b/17.03.1-ce
 Built:        Tue May 30 17:59:44 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.03.1-ce
 API version:  1.27 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   7392c3b/17.03.1-ce
 Built:        Tue May 30 17:59:44 2017
 OS/Arch:      linux/amd64
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
```
















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

참고
[도커](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)

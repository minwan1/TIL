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
$ docker rmi $(docker images -q) // 모든 이미지 제거
$ docker rmi [IMAGE ID] // 이미지 삭제
$ docker rmi -f [IMAGE ID] // 컨테이너가 사용되고 있는 이미지도 삭제하는방법
```
**도커 컨테이너 제거**
```
$ docker rm $(docker ps -a -q) // 모든 컨테이너 제거
$ docker ps -a -f status=exited // exited 컨테이너 삭제
$ docker rm [CONTAINER ID] // 컨테이너 삭제 
```
# 3. Docker 이미지 설치 


**Docker MYSQL 설치**
```
$ docker pull mysql:5.6ql
```
# 4. Dcoker 컨테이너 실행

**컨테이너 실행하기**
```
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
ex)docker run --name mysql -d -p 13306:13306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql:5.6

sudo docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 --restart=unless-stopped -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest rabbitmq:management
```
> -p 3306:3306 : 호스트의 3306포트와 컨테이너의 3306포트를 연결한다. 즉 호스트에 3306포트 접근이 발행하면 해당 컨테이너에 접속이 된다.

**명령어 옵션**
| 옵션    | 설명                                |
| ----- | --------------------------------- |법

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




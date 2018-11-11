# Docker-file
DockerFile은 Docker 이미지 설정 파일이다. DcokerFile에 설정된 내용대로 이미지를 생성한다.

# Docker-file

```
FROM ubuntu:14.04
MAINTAINER Foo Bar <foo@bar.com>

RUN apt-get update
RUN apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx

VOLUME ["/data", "/etc/nginx/site-enabled", "/var/log/nginx"]

WORKDIR /etc/nginx

CMD ["nginx"]

EXPOSE 80
EXPOSE 443
```
* FROM: 어떤 이미지를 기반으로 할지 설정합니다. Docker 이미지는 기존에 만들어진 이미지를 기반으로 생성합니다. <이미지 이름>:<태그> 형식으로 설정합니다.

* MAINTAINER: 메인테이너 정보입니다.
    * RUN: 셸 스크립트 혹은 명령을 실행합니다.
    * 이미지 생성 중에는 사용자 입력을 받을 수 없으므로 apt-get install 명령에서 -y 옵션을 사용합니다(yum install도 동일).
    * 나머지는 nginx 설정입니다.

* VOLUME: 호스트와 공유할 디렉터리 목록입니다. docker run 명령에서 -v 옵션으로 설정할 수 있습니다. 예) -v /root/data:/data는 호스트의 /root/data 디렉터리를 Docker 컨테이너의 /data 디렉터리에 연결합니다.
* CMD: 컨테이너가 시작되었을 때 실행할 실행 파일 또는 셸 스크립트입니다.
* WORKDIR: CMD에서 설정한 실행 파일이 실행될 디렉터리입니다.
* EXPOSE: 호스트와 연결할 포트 번호입니다.

å
### ENTRYPOINT
ENTRYPOINT는 컨테이너가 시작되었을 때 스크립트 혹은 명령을 실행합니다. 즉 docker run 명령으로 컨테이너를 생성하거나, docker start 명령으로 정지된 컨테이너를 시작할 때 실행됩니다. ENTRYPOINT는 Dockerfile에서 단 한번만 사용할 수 있습니다.
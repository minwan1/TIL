# NGINX
Nginx는 트래픽이 많은 웹사이트를 위해 확장성을 위해 설계한 비동기 이벤트 기반 구조의 웹서버이다.

기존에는 거의 Apache를 웹서버로 사용했다. Apache는 다양한 기능과 서드파티 확장 기능 등 어떠한 웹 애플리케이션에도 적용할 수 있는 웹서버였지만 클라이언트 접속 당 CPU와 메모리 사용량이 증가함으로써 확장성이 떨어진다는 단점이 있었다.
그래서 대량의 클라 인터를 관리하기 위한 웹서버가 필요시 되었고, 그래서 나온 것이 nginx였다.
nginx는 event 기반으로 동작하기 때문에 apache와 같이 각각의 웹페이지 요청을 처리 가히 위해 새로운 process or thread를 생성하지 않는다고 한다. 그렇기 때문에 단일 서버에서도 수만 개의 동시 연결을 처리할 수 있다.
현재 nginx는 분산 메모리에 객체 캐시 시스템이 추가되었고, 로드밸런싱을 위한 reverse proxy 등을 지원한다.



## CGI
CGI는 Common Gateway Interface의 약자로 웹서버와 외부 프로그램을 연결해주는 표준화된 프로토콜이다. 웹이 처음 등장했을 때는 HTML과 이미지를 전달해주는 웹서버밖에 없었다. 하지만 웹에 대한 수요가 증가함에 따라서 정적인 HTML만을 가지고 정보를 제공하는 것에 한계가 생겼고, 이를 극복하기 위해서 등장한 기술이 CGI다. 웹서버로 요청이 들어왔을 때 그것이 웹서버가 처리할 수 없는 정보일 때 그 정보를 처리할 수 있는 외부 프로그램을 호출해서 외부 프로그램이 처리한 결과를 웹서버가 받아서 브라우저로 전송하는 것이다.

## Fast CGI
CGI는 하나의 요청(Request)에 하나의 프로세스를 생성한다. 이것은 프로세스를 생성하고 삭제하는 과정에서 많은 부하가 발생한다. 당연히 느리다. 이를 개선하기 위해서 등장한 것이 FastCGI이다. FastCGI는 요청이 있을 때마다 프로세스가 만들어지는 것이 아니라 만들어진 프로세스가 계속해서 새로운 요청들을 처리한다. 덕분에 프로세스를 생성하고 제거하는데 들어가는 부하가 줄어든다.

## Nginx 설정
NGINX의 설정은 NGINX가 어떻게 동작해야 하는가를 지정하는 기능으로 파일에 설정값을 기술한다. 아래의 명령을 이용하면 설정 파일의 위치를 쉽게 찾을 수 있다.
```
sudo find / -name nginx.conf
```

## 설정 파일의 역할
* nginx.conf : 메인 설정 파일. 
* fcgi.conf : FastCGI 환경설정 파일
* sites-enabled : 활성화된 사이트들의 설정 파일들이 위치. 아파치에서는 Virtual host의 설정에 해당한다. 기본적으로 존재하지 않을수도 있다. 이 디렉토리를 직접 만들어서 사용하는 방법은 가상 호스팅편에서 알아본다.
* sites-available : 비활성화된 사이트들의 설정 파일들이 위치


**기본설정**
```
worker_processes  1;
events {
    worker_connections  1024;
}
http { 
    include       mime.types;
    server {
        listen       80;
        location / {
            root   html;
            index  index.html index.htm;
        }
    }
}
```

### Core 모듈 설정
위의 예의 work_processes와 같은 지시자 설정 파일 최상단에 위치하면서 nginx의 기본적인 동작 방식을 정의한다. 여기서 사용되는 지시어 들은 다른 곳에서 사용되지 않는다.

### http 블록
http 블록은 이후에 소개할 server, location의 루트 블록이라고 할 수 있고, 여기서 설정된 값을 하위 블록들은 상속한다. http 블록은 여러 개를 사용할 수 있지만 관리상의 이슈로 한 번만 사용하는 것을 권장한다.

http, server, location 블록은 계층구조를 가지고 있다. 많은 지시 어가 각각의 블록에서 동시에 사용할 수 있는데, http의 내용은 server의 기본값이 되고, server의 지시어는 location의 기본값이 된다. 그리고 하위의 블록에서 선언된 지시어는 상위의 선언을 무시하고 적용된다.

### server 블록
server 블록은 하나의 웹사이트를 선언하는데 사용된다. 가상 호스팅(Virtual Host)의 개념이다. 예를들어 하나의 서버로 http://opentutorials.org 과 http://egoing.net 을 동시에 운영하고 싶은 경우 사용할 수 있는 방법이다. 가상 호스팅에 대한 자세한 내용은 가상 호스팅 수업을 참고하자. 

### location 블록
location 블록은 server 블록 안에 등장하면서 특정 URL을 처리하는 방법을 정의한다. 이를테면 http://opentutorials.org/course/1 과 http://opentutorials.org/module/1 로 접근하는 요청을 다르게 처리하고 싶을 때 사용한다. 

### events 블록
이벤트 블록은 주로 네트워크의 동작방법과 관련된 설정값을 가진다. 이벤트 블록의 지시어들은 이벤트 블록에서만 사용할 수 있고, http, server, location와는 상속관계를 갖지 않는다. 

## 설정 파일의 반영

설정 파일의 내용을 변경한 후에는 이를 NGINX에 반영해야 하는데 아래와 같이 reload 명령을 이용한다. restart를 이용해도 되지만 권장되지 않는다. 
```
sudo service nginx reload;
```




## NGINGX 변수처리
아래와 같은 URL로 접근했을 때 환경벼누는 아래와 같다.

http://opentutorials.org:80/production/module/index.php?type=module&id=12

* $host : opentutorials.org
* $uri : /production/module/index.php
* $args : type=module&id=12
* server_addr : 115.68.24.88
* server_name : localhost
* server_port : 80
* server_protocol : HTTP/1.1
* $arg_type : module
* $request_uri : /production/module/index.php?type=module&id=12
* $request_filename : /usr/local/nginx/html/production/module/index.php



## 가상 호스트
가상 호스트는 Virtual Host이다. Host는 네트워크에 연결된 하나의 컴퓨터를 의미하는데, Virtual Host는 한대의 컴퓨터로 마치 여러 대의 컴퓨터가 존재하는 것처럼 동작하도록 한다는 뜻이다. 이를테면 http://wan-blog.net, http://jin-blog.net으로 접속했을 때 둘 다 1.226.82.52 IP를 가리키고, 1.226.82.52 IP에 해당하는 Host가 각각의 도메인에 따라서 서로 다른 페이지를 서비스하게 할 수 있다.

### 가상호스트 사용법
가상호스트를 지정하기 위해서는 nginx.conf 파일에 server 블록을 사용하면 된다. 하나의 호스트에서 하나의 웹서비스만을 운영한다면 이런 방식도 좋지만, 만약 하나의 호스트에서 복수의 서비스를 운영한다면 본 토픽의 말미에서 설명하는 include 방식을 이용할 것을 권장한다. 서버블록에 올 수 있는 주요 지시자는 아래와 같다.


**include**

include는 별도의 파일에 설정을 기록해서 설정의 그룹핑, 재활용성을 높이는 방법을 제공한다. 예를들어 아래의 설정은 sites-enabled 디렉토리에 있는 모든 파일을 자동으로 가져오게 하는 예인데, sites-enabled 디렉토리에는 가상호스트에 따라서 파일을 만들어서 위치시키면 nginx가 이 디렉토리의 파일들 모두를 가져와서 사용하게 된다. nginx.conf 파일 중 http 블록 안에 아래와 같이 기술한 후에 sites-enabled 디렉토리에 가상호스트 설정들을 파일별로 위치시키면 된다. 


```
http {
    includes sites-enabled/*;
}
```
위와 같이 상대경로를 사용할 때 sites-enabled 디렉토리는 conf 디렉토리 하위에 위치해야 한다. 




## rewrite

재작성(Rewrite)이란 요청을 통해서 주어진 URL의 규칙을 변경해서 웹서비스를 보다 유연하게 만드는 방법이다. 아파치 웹서버에서는 mod_rewrite라는 모듈을 통해서 할 수 있었던 작업이다. NGINX 에서는 rewrite 모듈을 통해서 URL을 재작성 할 수 있다. 





## 간단한 구성 예제
```
server {
    root /www/data;

    location / {
    }

    location /images/ {
    }

    location ~ \.(mp3|mp4) {
        root /www/media;
    }
}
```
여기에서 엔진엑스는 /www/data/images/ 폴더에서 img를 찾게 된다. 그러나 이 URI 끝에 mp3, mp4가 들어가면 location 블록이 들어가기 때문에 /www/media에서 찾게 된다. 만약 /로 끝난다면 /www/data에서 index.html을 찾을 것이다. 만약 여기에서 요청 URI /images/some/path/ 이렇다면 /www/data/images/some/path/index.html을 찾을 것이다. 만약 존재하지 않는다면 404코드를 보낼 것이다.

```
location / {
    index index.$geo.html index.htm index.html;
}
```
만약 위와 같이 설정할 경우에 index.html 파일 이외에도 추가적으로 설정할 수 있게 된다. NGINX는 지정된 순서대로 파일을 검색하고 보여준다.

```
location / {
    root /data;
    index index.html index.php;
}

location ~ \.php {
    fastcgi_pass localhost:8000;
    ...
}
```


참고
* [생활코딩](https://opentutorials.org/module/384/4328)
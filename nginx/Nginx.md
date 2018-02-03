Upstream이란
Proxy_pass 지시자를 통해 nginx가 받은 리퀘스트를 넘겨 줄 서버들을 정의하는 지시자가 upstream이다.




sudo nginx -s stop && sudo nginx



## mac
/usr/local/etc/nginx/nginx.conf 맥위치
도메인 네임 변경 로컬 sudo vi /etc/hosts

nginx: [emerg] open() "/usr/localå/Cellar/nginx/1.12.0/logs/access.log" failed (13: Permission denied) [로그]
##linux
killall nginx  << nginx 죽이기

/var/www.html
/etc/nginx/conf.d/default.conf
/etc/nginx/sites-available/default

/var/log/nginx/logs




```
/usr/local/etc/nginx/nginx.conf
/usr/local/var/www
```






[4:13]
sudo su 로 접근하세요






### NGINX 설정
설정파일은 아래의 위치 중의 하나에 있다.




jsession을 유지하기위해서는


일단 엔진엑스가 내부적으로 localhost:8080으로 톰켓호출한다
유저가 만약 엔진엑스를 호출한다면 그것은 다시 localhost:8080으로 들어갈것이다.(근데 만약 여기에서 webserver도 거치지않고 바로 localhost:8080을 호출한다면 jsession떄문에 session이 유지가안된다.)

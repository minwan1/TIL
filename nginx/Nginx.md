Upstream이란
Proxy_pass 지시자를 통해 nginx가 받은 리퀘스트를 넘겨 줄 서버들을 정의하는 지시자가 upstream이다.




sudo nginx -s stop && sudo nginx














도메인 네임 변경 로컬 sudo vi /etc/hosts
/usr/local/etc/nginx/nginx.conf 맥위치


### NGINX 설정
설정파일은 아래의 위치 중의 하나에 있다.
/etc/nginx/conf.d/default.conf
/etc/nginx/sites-available/default



jsession을 유지하기위해서는


일단 엔진엑스가 내부적으로 localhost:8080으로 톰켓호출한다
유저가 만약 엔진엑스를 호출한다면 그것은 다시 localhost:8080으로 들어갈것이다.(근데 만약 여기에서 webserver도 거치지않고 바로 localhost:8080을 호출한다면 jsession떄문에 session이 유지가안된다.)

도커 사용 예제 1
버전 별 Mysql 사용하기


```
//-d 대몬 버전
// 
$ docker run -d -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql:5.6 –character-set-server=utf8 –collation-server=utf8_unicode_ci



// --name << 이름 붙인거
$ docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password mysql:5.7 –character-set-server=utf8 –collation-server=utf8_unicode_ci
```




## docker compose
여러가지 부품을 모아놓는것


## Dockerfile

도커 이미지를 만드는것



##

#      MYSQL_ROOT_PASSWORD: "root"
      MYSQL_DATABASE: "app"
#      MYSQL_USER: "app"
#      MYSQL_PASSWORD: "app"


```
  mysql:
     container_name: study.docker-test.mysql
     image: mysql/mysql-server:5.7
     environment:https://recruit.navercorp.com/naver/job/detail/developer?annoId=20002317&classId=&jobId=&entTypeCd=&searchTxt=
      MYSQL_DATABASE: study
      MYSQL_ROOT_HOST: '%'
      MYSQL_ROOT_PASSWORD: password
     ports:
     - "3306:3306"
     restart: always
```



     volumes:
     - /opt/mysql_data:/var/lib/mysql
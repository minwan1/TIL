도커 사용 예제 1
버전 별 Mysql 사용하기


```
//-d 대몬 버전
// 
$ docker run -d -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql:5.6

// --name << 이름 붙인거
$ docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password mysql:5.7
```




## docker compose
여러가지 부품을 모아놓는것


## Dockerfile

도커 이미지를 만드는것
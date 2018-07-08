l## mac 환경 mysql 실행 및 중지
mysql.server start
mysql.server stop
## mysql 접속
mysql -u root

##mysql 비밀번호 변경
mysql 접속 후 아래 명령어 실행
```
mysql> use mysql;
mysql> set password = password('원하는 비밀번호');
```


mysql.server start 실행
sudo mysql -u root -p 로그인

root 1234 local










참고

1. [백발의개발자](http://m.blog.naver.com/jjoommnn/130181901609) - angular 이론에 대해 아주 쉽게 설명해준다.
출처: http://windowx.tistory.com/entry/mysql-scheduler-사용하기 [mulder's it blog]

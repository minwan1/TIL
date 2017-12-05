## 좋은 블로그들 추천


select * from coupon where user_id="cfeb5db5-3474-439f-86d4-9bdf050a101a" order by expire_date desc;
mysql 시간순으로 불러오기





## 스케줄러
스케줄러 시작
```sql
SET GLOBAL event_scheduler = ON;
 SET @@global.event_scheduler = ON;
 SET GLOBAL event_scheduler = 1;
 SET @@global.event_scheduler = 1;

```
스케줄러 종료
```sql
SET GLOBAL event_scheduler = OFF;
SET @@global.event_scheduler = OFF;
SET GLOBAL event_scheduler = 0;
SET @@global.event_scheduler = 0;
```

```sql
#스케줄러 삭제
 drop event 이벤트명;
```


```sql
create event IF NOT EXISTS e1
    ON SCHEDULE
        EVERY 60 SECOND
        STARTS CURRENT_TIMESTAMP
    DO update transfer_data set current_state = "PROCESSING" where current_state ="PENDING" and dst_currency = "KRW" and UNIX_TIMESTAMP(created_at) < UNIX_TIMESTAMP(now()) - (60*3);
```

```sql
select * from transfer_data where current_state ="PENDING" and dst_currency = "KRW" and UNIX_TIMESTAMP(created_at) < UNIX_TIMESTAMP(now()) - (60*3);

update transfer_data set current_state = "PROCESSING" where current_state ="PENDING" and dst_currency = "KRW" and UNIX_TIMESTAMP(created_at) < UNIX_TIMESTAMP(now()) - (1000*60*30);
```


show events;













출처: http://windowx.tistory.com/entry/mysql-scheduler-사용하기 [mulder's it blog]

```










1. [백발의개발자](http://m.blog.naver.com/jjoommnn/130181901609) - angular 이론에 대해 아주 쉽게 설명해준다.



mysql.server start 실행
sudo mysql -u root -p 로그인

root 1234 local

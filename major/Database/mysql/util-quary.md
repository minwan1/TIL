일일통계에서 모든 날짜를 구하고싶다면 아래와같이 테이블을 만들어야한다.
```sql
select * from (
select * from 
(select adddate('1970-01-01',t4.i*10000 + t3.i*1000 + t2.i*100 + t1.i*10 + t0.i) selected_date from
 (select 0 i union select 1 union select 2 union select 3 union select 4 union select 5 union select 6 union select 7 union select 8 union select 9) t0,
 (select 0 i union select 1 union select 2 union select 3 union select 4 union select 5 union select 6 union select 7 union select 8 union select 9) t1,
 (select 0 i union select 1 union select 2 union select 3 union select 4 union select 5 union select 6 union select 7 union select 8 union select 9) t2,
 (select 0 i union select 1 union select 2 union select 3 union select 4 union select 5 union select 6 union select 7 union select 8 union select 9) t3,
 (select 0 i union select 1 union select 2 union select 3 union select 4 union select 5 union select 6 union select 7 union select 8 union select 9) t4) v
where selected_date between '2018-05-30' and '2018-06-15'
) sd;

```



```sql
# 일반
select DATE_FORMAT(created_at, '%y-%m-%d') m, count(*) count
from applications 
where pre_qr_serial_number is NULL
and application_type = "REGISTERED"
and created_at between '2018-04-03 00:00:00' and '2019-04-23 23:59:59'
group by m;



## 사전제작
select DATE_FORMAT(created_at, '%y-%m-%d') m, count(*) count
from applications 
where pre_qr_serial_number is not NULL
and application_type = "REGISTERED"
and created_at between '2018-04-03 00:00:00' and '2019-04-23 23:59:59'
group by m;


```
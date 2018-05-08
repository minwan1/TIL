## MYSQL Schduler
### 현재 등록 이벤트 확인
```sql
show events;
```
### 해당 이벤트 내용확인(비지니스로직확인)
```sql
 show create event e1; //e1이라는 event 확인
```

### 이벤트 등록 예제
1분마다 transfer가 DST_CURRENCY가 KRW이면서 상태가 PENDING인 row를 PROCESSING으로 변경 예제
```sql
create event IF NOT EXISTS e1
    ON SCHEDULE
        EVERY 60 SECOND
        STARTS CURRENT_TIMESTAMP
    DO update transfer_data set current_state = "PROCESSING" where current_state ="PENDING" and src_currency = "USD" and UNIX_TIMESTAMP(created_at) < UNIX_TIMESTAMP(now()) - (60*3*10);

```

### 이벤트 스케줄러 시작
```sql
SET GLOBAL event_scheduler = ON;
 SET @@global.event_scheduler = ON;
 SET GLOBAL event_scheduler = 1;
 SET @@global.event_scheduler = 1;

```
### 이벤트 스케줄러 종료
```sql
SET GLOBAL event_scheduler = OFF;
SET @@global.event_scheduler = OFF;
SET GLOBAL event_scheduler = 0;
SET @@global.event_scheduler = 0;
```

###  등록된 이벤트 스케줄러 삭제
```sql
#스케줄러 삭제
 drop event 이벤트명;
```
### 현재 이벤트 실행 여부 확인
```sql
show processlist
```

### 이벤트 수정하기
```sql
ALTER EVENT `e1`
   ON SCHEDULE
       EVERY 60 SECOND
       STARTS CURRENT_TIMESTAMP
   DO update transfer_data set current_state = "PROCESSING" where current_state ="PENDING" and dst_currency = "KRW" and UNIX_TIMESTAMP(created_at) < UNIX_TIMESTAMP(now()) - (60*3*10);
```

참고
* [프로그래머의 블로그](http://genesis8.tistory.com/298)

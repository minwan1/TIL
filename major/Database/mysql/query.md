
## select insert

```sql

INSERT INTO table_a
( title, name, regdate, register, memberid, categoryid )
SELECT title, name, regdate, register, memberid, categoryid
FROM table_b
WHERE categoryid=10
```


## select update
```sql
update users u
inner join (
  select user_id, MAX(id) as max_id
  from process_histories
  group by user_id
    ) b on u.id = b.user_id
set u.current_process_history_id = b.max_id
where u.id in (2,3);
```
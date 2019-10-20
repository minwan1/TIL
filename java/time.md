

시스템 타임존 보기
```java
    TimeZone tz = Calendar.getInstance().getTimeZone();
            System.out.println("현재 TimeZone 구역 의 해당 ID ::: "  + tz.getID());

```



```java
System.out.println(instant.now()); // utc 기준으로 나옴
```



```java
         Instant instant = DateTimeUtils.fromKoreanDateTime(LocalDateTime.of(2019, 8, 01, 00
                    , 00, 00));

            System.out.println(instant); //2019-07-31T15:00:00Z
```
위에는 한국시간 기준으로 -9 시간을 하여 utc기준으로 나온다. instant는 무조건 utc를 기준으로 보여준다.
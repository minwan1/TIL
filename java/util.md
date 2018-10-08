
# Local

## LocalDate
로컬 날짜 클래스로 날짜 정보만 필요할 때 사용하면 된다.
```java
LocalDate targetDate = LocalDate.of(int year, int month, int dayOfMonth);   // 파라미터로 주어진 날짜 정보를 저장한 LocalDate 객체를 리턴한다. 결과 : 1986-11-22
```
## LocalTime
로컬 시간 클래스로 시간 정보만 필요할 때 사용하면 된다.

```java
LocalTime targetTime = LocalTime.of(int hour, int minute, int second, int nanoOfSecond); // 파라미터로 주어진 시간 정보를 저장한 LocalTime 객체를 리턴한다.
```
## LocalDateTime
날짜와 시간 정보 모두가 필요할 때 사용하면 된다.
```java
LocalDateTime targetDateTime = LocalDateTime.of(int year, int month, int dayOfMonth, int hour, int minute, int second, int nanoOfSecond);
```


## 포멧팅
```java
LocalDateTime now = LocalDateTime.now();
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy년 M월 d일 a h시 m분");
String nowString = now.format(dateTimeFormatter);   // 결과 : 2016년 4월 2일 오전 1시 4분
```

## String to localdate
```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("EEEE, MMM d, yyyy HH:mm:ss a");
 String date = "Tuesday, Aug 16, 2016 12:10:56 PM";

        LocalDateTime localDateTime = LocalDateTime.parse(date, formatter);

        System.out.println(localDateTime);


 LocalTime isoTime = LocalTime.parse("10:15:30",
            DateTimeFormatter.ISO_LOCAL_TIME);
      System.out.println(isoTime);

      // hour-of-day (0-23)
      LocalTime localTime = LocalTime.parse("22:45:30",
            DateTimeFormatter.ofPattern("HH:mm:ss"));
      System.out.println(localTime);
```
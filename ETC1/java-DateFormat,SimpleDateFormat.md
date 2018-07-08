##SimpleDateFormat and DateFormat
이것은 text를 날짜형식으로 parse하거나 날짜를 text로 parse하는것의 예제들이다. SimpleDateFormat은 DateFormat을 상속한다. DateFormat은 date/time의


```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Locale;

public class SimpleDateFormatExample {
    public static void main(String[] args) {
        Date curDate = new Date();
        SimpleDateFormat format = new SimpleDateFormat("yyyy/MM/dd");

        String DateToStr = format.format(curDate);
        System.out.println(DateToStr);

        format = new SimpleDateFormat("dd-M-yyyy hh:mm:ss");
        DateToStr = format.format(curDate);
        System.out.println(DateToStr);

        format = new SimpleDateFormat("dd MMMM yyyy zzzz", Locale.ENGLISH);
        DateToStr = format.format(curDate);
        System.out.println(DateToStr);

        format = new SimpleDateFormat("E, dd MMM yyyy HH:mm:ss z");
        DateToStr = format.format(curDate);
        System.out.println(DateToStr);
        try {
            Date strToDate = format.parse(DateToStr);
            System.out.println(strToDate);
        } catch (ParseException e) {
            e.printStackTrace();
        }
    }
}

```
출력
```
2014/05/11
11-5-2014 11:11:51
11 May 2014 Eastern European Summer Time
Sun, 11 May 2014 23:11:51 EEST
Sun May 11 23:11:51 EEST 2014
```

SimpleDateFormat의 특징은 패턴으로 date형식을 만든다.

DateFormat 예제

```java
import java.text.DateFormat;
import java.text.ParseException;
import java.util.Date;

public class DateFormatExample {
   public static void main(String[] args) {

       Date curDate = new Date();

       System.out.println(curDate.toString());

       String DateToStr = DateFormat.getInstance().format(curDate);
       System.out.println(DateToStr);

       DateToStr = DateFormat.getTimeInstance().format(curDate);
       System.out.println(DateToStr);

       DateToStr = DateFormat.getDateInstance().format(curDate);
       System.out.println(DateToStr);

       DateToStr = DateFormat.getDateTimeInstance().format(curDate);
       System.out.println(DateToStr);

       DateToStr = DateFormat.getTimeInstance(DateFormat.SHORT)
               .format(curDate);
       System.out.println(DateToStr);

       DateToStr = DateFormat.getTimeInstance(DateFormat.MEDIUM).format(
               curDate);
       System.out.println(DateToStr);

       DateToStr = DateFormat.getTimeInstance(DateFormat.LONG).format(curDate);
       System.out.println(DateToStr);

       DateToStr = DateFormat.getDateTimeInstance(DateFormat.MEDIUM,
               DateFormat.SHORT).format(curDate);
       System.out.println(DateToStr);

       try {
           Date strToDate = DateFormat.getDateInstance()
                   .parse("July 17, 1989");
           System.out.println(strToDate.toString());
       } catch (ParseException e) {
           e.printStackTrace();
       }

   }
}

```

출력
```
Sun May 11 23:37:54 EEST 2014
5/11/14 11:37 PM
11:37:54 PM
May 11, 2014
May 11, 2014 11:37:54 PM
11:37 PM
11:37:54 PM
11:37:54 PM EEST
May 11, 2014 11:37 PM
Mon Jul 17 00:00:00 EEST 1989
```

특징
long,midiue 등의 타입으로 fomat형식 지정

## locale 예제
```java
localeFormarttedDate = DateFormat.getDateTimeInstance(DateFormat.MEDIUM,
					                DateFormat.SHORT,LocaleContextHolder.getLocale()).format(date);
                          //Nov 20, 2017 11:21 PM    
                          //2017. 11. 20 오후 11:21
```



```java
if (calendar.get(Calendar.DAY_OF_WEEK) == 1 || calendar.get(Calendar.DAY_OF_WEEK) == 7) { // 주말이라면 플러스플러스 ㄲ
  dayCount++;
}
```


##
long time : 만료일 체크할 때 사용하면좋음
Datetime : 유저들한테 시간보열질 때
timestamp : 그냥 자동체크될때



참고
* [Java Code Geeks](https://examples.javacodegeeks.com/core-java/text/java-dateformat-example/)

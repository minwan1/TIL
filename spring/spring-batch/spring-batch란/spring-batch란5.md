## FlatFileItemWriter
서비스를 운영하다보면 파트너사에 데이터를 공유해줘야하거나 내부적으로 데이터를 파일로 관리해야할 필요가 있을 떄 csv파일을 많이쓴다. 쉽게 데이터베이스, 액세파일에 import할 수 있고 데이터를 파싱하기 쉽기 떄문에 csv파일을 많이 쓴다. 이러한 데이터를 파일로 바꾸는 일은 배치작업으로 많이 일어난다. Spring batch에서는 데이터를 쉽게 csv로 변경해주는 클래스가 FlatFileItemWriter이다.



## FlatFileItemWirter
FlatFileItemWriter는 스트림또는 파일에 데이터를 쓰게해주는 클래스이다. 파일이 기록되는 위치는 Resource class를 통해서 정의해줘야한다. FlatFileItemWriter를 사용하기위해서는 아래와같이 builder를 생성해서 해당 class를 생성할 수 있다.


```java
    public ItemWriter<Member> completeUserWriter() {
        return new FlatFileItemWriterBuilder<Member>()
                .name("orderTransactionWriter")
                .resource(new FileSystemResource("./test.csv"))
                .delimited()
                .delimiter(",") //구분
                .names(MEMBER_FIELDS)
                .headerCallback(writer -> writer.write(
                        String.join(",", MEMBER_FIELDS)))
                .build();
    }
```

1. 먼저 떨어질 파일 위치와 형식을 지정해준다. 
2. 그리고 구분단위를 ,로 지정해준다. 
3. names는 이것을 키값기반으로 dto에서 값을 순서대로 밸류값을 얻어온다.
4. 그리고 최종적으로 csv최상단 header바인딩되는 필드명을 지정해준다.
5. build()메소드를 통해 파일을 생성한다.




##

LineSeparator : \n


lineAggregator: 
 delimiter : ","
 fieldExtractor : 키값들



 DelimitedLineAggregator (이것 sertter)



```java
	@Override
	public String doWrite(List<? extends T> items) {
		StringBuilder lines = new StringBuilder();
		for (T item : items) {
			lines.append(this.lineAggregator.aggregate(item)).append(this.lineSeparator);
		}
		return lines.toString();
	}
```

결과값
```
1,test1,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		2,test2,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		3,test3,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		4,test4,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		5,test5,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		6,test6,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		7,test7,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		8,test8,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		9,test9,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
		10,test10,WAITING,2018-05-19T00:23:25,2018-05-19T00:23:25
```
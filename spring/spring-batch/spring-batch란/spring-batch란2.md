# 간단한 잡으로 메타테이블 간단하게 알아보기
그럼 실제로 스프링 배치를 돌렸을 때 어떻게 아래의 메타테이블에 데이터들이 저장되는지 알아보겠습니다. 
![](https://i.imgur.com/IBxzbj5.png)

먼저 
```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-jdbc'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    runtimeOnly 'com.h2database:h2'
    runtimeOnly 'mysql:mysql-connector-java'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

```

# BATCH_JOB_INSTANCE
다음 테이블을 조회하면 다음과 같이 조회될것이다.


JOB_INSTANCE_ID | VERSION | JOB_NAME | JOB_KEY|
------- | ------- | ------- | -------|
1 | 0 | simpleJob | d41d8cd98f00b204e9800998ecf8427e|

* JOB_INSTANCE_ID
    * BATCH_JOB_INSTANCE 테이블의 PK
* JOB_NAME
    * 수행한 Batch Job Name

BATCH_JOB_INSTANCE 테이블은 Job Parameter에 따라 생성되는 테이블이다. 이것은 Spring Batch가 실행될때 외부에서 받을 수 있는 파라미터를 의미한다. 

예를 들어, 특정 날짜 JobParameter로 넘기면 Spring Batch에서는 해당 날짜 데이터로 조회/ 가공/ 입력등의 작업을 할 수 있다.

같은 Batch Job이라도 Job Parameter가 다르면 BATCH_JOB_INSTANCE에는 기록되며, Job parameter가 같다면 기록되지 않습니다.


기존에는 잡 인스턴스를 업그레이드에해서 잡인스턴스에 파라미터를 아래와같이 넘겼다.
```java

@Slf4j // log 사용을 위한 lombok 어노테이션
@RequiredArgsConstructor // 생성자 DI를 위한 lombok 어노테이션
@Configuration
public class SimpleJobConfiguration {
    private final JobBuilderFactory jobBuilderFactory;
    private final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job simpleJob() {
        return jobBuilderFactory.get("simpleJob")
                .start(simpleStep1(null))
                .build();
    }

    @Bean
    @JobScope
    public Step simpleStep1(@Value("#{jobParameters[requestDate]}") String requestDate) {
        return stepBuilderFactory.get("simpleStep1")
                .tasklet((contribution, chunkContext) -> {
                    log.info(">>>>> This is Step1");
                    log.info(">>>>> requestDate = {}", requestDate);
                    return RepeatStatus.FINISHED;
                })
                .build();
    }
}

```

![](https://i.imgur.com/NjUHzyK.png)


이렇게 실행하면 아래와같이 BATCH_JOB_INSTANCE에는 새로운 잡 인스턴스가 추가되어진다.

JOB_INSTANCE_ID | VERSION | JOB_NAME | JOB_KEY|
------- | ------- | ------- | -------|
1 | 0 | simpleJob | d41d8cd98f00b204e9800998ecf8427e
2 | 0 | simpleJob | 83eda6e86aa43ff7becb7968d1ebb945

그런데 그대로 파라미터를 같은값으로 실행하면 아래와같이 에러메시지를 만날것이다.

```java
Caused by: org.springframework.batch.core.repository.JobInstanceAlreadyCompleteException: A job instance already exists and is complete for parameters={requestDate=20180805}.  If you want to run this job again, change the parameters.
```

그럼이번에 기존에 넘긴 파라미터에서 아래와같이 값을 변경해서 앱을 실행해보자.

```java
{requestDate=20180806}
```

이렇게 배치를 실행하면 아래와 같이 새로운 잡인스턴스가 잘 추가되는것을 볼 수 있다.

JOB_INSTANCE_ID | VERSION | JOB_NAME | JOB_KEY
----------------|---------|----------|--------
1 | 0 | simpleJob | d41d8cd98f00b204e9800998ecf8427e
2 | 0 | simpleJob | 83eda6e86aa43ff7becb7968d1ebb945
3 | 0 | simpleJob | 7d58a461dae8e2a4524ad8ff10a313c5


즉, 동일한 Job이 Job Parameter가 달라지면 그때마다 BATCH_JOB_INSTANCE에 생성되며, 동일한 Job Parameter는 여러개 존재할 수 없습니다 .


# BATCH_JOB_EXECUTION

다음으로 볼것은 BATCH_JOB_EXECUTION 테이블입니다.

JOB_EXECUTION_ID | VERSION | JOB_INSTANCE_ID | CREATE_TIME | START_TIME | END_TIME | STATUS | EXIT_CODE | EXIT_MESSAGE | LAST_UPDATED | JOB_CONFIGURATION_LOCATION
------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | --------
1 | 2 | 1 | 2019-01-11 16:21:59 | 2019-01-11 16:21:59 | 2019-01-11 16:21:59 | COMPLETED | COMPLETED |  | 2019-01-11 16:21:59 | NULL|
2 | 2 | 2 | 2019-01-11 17:12:49 | 2019-01-11 17:12:49 | 2019-01-11 17:12:49 | COMPLETED | COMPLETED |  | 2019-01-11 17:12:49 | NULL|
3 | 2 | 3 | 2019-01-11 17:21:13 | 2019-01-11 17:21:13 | 2019-01-11 17:21:13 | COMPLETED | COMPLETED |  | 2019-01-11 17:21:13 | NULL|

JOB_EXECUTION 테이블을 보시면 3개의 ROW가 있는데요. 
저희가 실행했던 파라미터가 없는 simpleJob, requestDate=20180805 파라미터로 실행했던 simpleJob, requestDate=20180806 파라미터로 실행했던 simpleJob 까지 이렇게 3개의 실행 데이터입니다.

JOB_EXECUTION와 JOB_INSTANCE는 부모-자식 관계이다. JOB_EXECUTION은 자신의 부모 JOB_INSTANCE가 성공.실패했던 모든 내역을 갖고 있다. 실제 그런지 예제를 돌려보자.


아래와같이 실행을 해도 Exception을 던지기 때문에 BatchJob인스턴스는 실패할것이다.
```java
 @Bean
    public Job simpleJob() {
        return jobBuilderFactory.get("simpleJob")
                .start(simpleStep1(null))
                .next(simpleStep2(null))
                .build();
    }

    @Bean
    @JobScope
    public Step simpleStep1(@Value("#{jobParameters[requestDate]}") String requestDate) {
        return stepBuilderFactory.get("simpleStep1")
                .tasklet((contribution, chunkContext) -> {
                    throw new IllegalArgumentException("step1에서 실패합니다.");
                })
                .build();
    }

    @Bean
    @JobScope
    public Step simpleStep2(@Value("#{jobParameters[requestDate]}") String requestDate) {
        return stepBuilderFactory.get("simpleStep1")
                .tasklet((contribution, chunkContext) -> {
                    log.info(">>>>> This is Step1");
                    log.info(">>>>> requestDate = {}", requestDate);
                    return RepeatStatus.FINISHED;
                })
                .build();
    }
```

실패를 하면 아래와 같이 상태에 실패와 함께 왜실패했는지 로그등이 남겨진다.


JOB_EXECUTION_ID | VERSION | JOB_INSTANCE_ID | CREATE_TIME | START_TIME | END_TIME | STATUS | EXIT_CODE | EXIT_MESSAGE | LAST_UPDATED | JOB_CONFIGURATION_LOCATION|
------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | --------
1 | 2 | 1 | 2019-01-11 16:21:59 | 2019-01-11 16:21:59 | 2019-01-11 16:21:59 | COMPLETED | COMPLETED |  | 2019-01-11 16:21:59 | NULL
2 | 2 | 2 | 2019-01-11 17:12:49 | 2019-01-11 17:12:49 | 2019-01-11 17:12:49 | COMPLETED | COMPLETED |  | 2019-01-11 17:12:49 | NULL
3 | 2 | 3 | 2019-01-11 17:21:13 | 2019-01-11 17:21:13 | 2019-01-11 17:21:13 | COMPLETED | COMPLETED |  | 2019-01-11 17:21:13 | NULL
4 | 2 | 4 | 2019-01-12 09:47:24 | 2019-01-12 09:47:24 | 2019-01-12 09:47:24 | FAILED | FAILED | java.lang.IllegalArgumentException: step1?? ?????.↵⇥at com.example.batch.job.SimpleJobConfiguration.lambda$simpleStep1$0(SimpleJobConfiguration.java:35)↵⇥at org.springframework.batch.core.step.tasklet.TaskletStep$ChunkTransactionCallback.doInTransaction<br>... | 2019-01-12 09:47:24 | NULL|


그럼 이번에는 이 simpleStep1을 Exception이 아닌 정상적으로 다시 실행해보자.

```java
    @Bean
    @JobScope
    public Step simpleStep1(@Value("#{jobParameters[requestDate]}") String requestDate) {
        return stepBuilderFactory.get("simpleStep1")
                .tasklet((contribution, chunkContext) -> {
                    log.info(">>>>> This is Step1");
                    log.info(">>>>> requestDate = {}", requestDate);
                    return RepeatStatus.FINISHED;
                })
                .build();
    }

```

그럼 BATCH_JOB_EXECUTION테이블은 아래와같이 정상적으로 처리가 될것이다.

JOB_EXECUTION_ID | VERSION | JOB_INSTANCE_ID | CREATE_TIME | START_TIME | END_TIME | STATUS | EXIT_CODE | EXIT_MESSAGE | LAST_UPDATED | JOB_CONFIGURATION_LOCATION
------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | --------
1 | 2 | 1 | 2019-01-11 16:21:59 | 2019-01-11 16:21:59 | 2019-01-11 16:21:59 | COMPLETED | COMPLETED |  | 2019-01-11 16:21:59 | NULL
2 | 2 | 2 | 2019-01-11 17:12:49 | 2019-01-11 17:12:49 | 2019-01-11 17:12:49 | COMPLETED | COMPLETED |  | 2019-01-11 17:12:49 | NULL
3 | 2 | 3 | 2019-01-11 17:21:13 | 2019-01-11 17:21:13 | 2019-01-11 17:21:13 | COMPLETED | COMPLETED |  | 2019-01-11 17:21:13 | NULL
4 | 2 | 4 | 2019-01-12 09:47:24 | 2019-01-12 09:47:24 | 2019-01-12 09:47:24 | FAILED | FAILED | java.lang.IllegalArgumentException: step1?? ?????.↵⇥at com.example.batch.job.SimpleJobConfiguration.<br>...| 2019-01-12 09:47:24 | NULL
5 | 2 | 4 | 2019-01-12 09:59:41 | 2019-01-12 09:59:41 | 2019-01-12 09:59:41 | COMPLETED | COMPLETED |  | 2019-01-12 09:59:41 | NULL

여기에서 보면 JOB_EXECUTION_ID 4,5를 봐보면 JOB_INTANCE_ID가 4인것을 볼 수 있다. 그리고 의도한대로 실패와 성공의 정보가 담겨있는것을 알 수 있다.

여기에서 보다시피 동일한 JobParameter실행했음에도 두번째 JOB_INSTANCE가 실행됐는데 처음에 만약에 실패한 인스터라고하면 두번째에 다시 해당 인스턴스를 실행할 수 있다.


# JOB, JOB_INSTANCE, JOB_EXECUTION

Job : simpleJob
Job : Job parameter를 2018.08.01로 실행한 simpleJob
Job Execution : Job parameter를 2018.08.01로 실행한 simpleJob의 1번째 시도 혹은 다음번 시도

# BATCH_JOB_EXECUTION_PARAMS
BATCH_JOB_EXECUTION_PARAM테이블은 BATCH_JOB_EXECUTION 테이블이 생성될 당시에 입력받은 Job Parameter를 담고 있다.

|JOB_EXECUTION_ID | TYPE_CD | KEY_NAME | STRING_VAL | DATE_VAL | LONG_VAL | DOUBLE_VAL | IDENTIFYING|
------- | ------- | ------- | ------- | ------- | ------- | ------- | -------
2 | STRING | requestDate | 20180805 | 1970-01-01 00:00:00 | 0 | 0 | Y
3 | STRING | requestDate | 20180806 | 1970-01-01 00:00:00 | 0 | 0 | Y
4 | STRING | requestDate | 20180807 | 1970-01-01 00:00:00 | 0 | 0 | Y
5 | STRING | requestDate | 20180807 | 1970-01-01 00:00:00 | 0 | 0 | Y

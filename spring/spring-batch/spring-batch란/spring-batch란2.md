# 간단한 잡으로 메타테이블 간단하게 알아보기
그럼 실제로 스프링 배치를 돌렸을 때 어떻게 아래의 메타테이블에 데이터들이 저장되는지 알아보겠습니다. 
![](https://i.imgur.com/IBxzbj5.png)

먼저 
```java
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


먼저 위에 스프링 배치에서 정의 해준 테이블들을 생성하기위해서는 데이터베이스 구성이 필요합니다. docker-compose를 통해 데이터베이스를 구성해보겠습니다. 프로젝트 Root폴더에 다음과 같이 docker-compose.yaml파일 구성해야합니다. (물론 docker가 설치 되있으셔야합니다.)

```yml
version: '3'

services:
  mysql:
     container_name: batch.mysql
     image: mysql/mysql-server:5.7
     environment:
      MYSQL_ROOT_HOST: '%'
      MYSQL_DATABASE: "batch"
      MYSQL_ALLOW_EMPTY_PASSWORD: "yes"
     ports:
     - "3306:3306"
     volumes:
     - ./volumes/mysql:/var/lib/mysql
```
* [도커 윈도우 설치](https://docs.docker.com/docker-for-windows/install/)
* [도커 맥 설치](https://docs.docker.com/docker-for-mac/install/)

docker-compose를 구성하셨다면 docker-compose를 아래와 같은 명령어로 실행시키시면 local환경에서 mysql구성이 됩니다.

```
$ docker-compose up -d
```


가장 먼저 잡을 하나 등록해서 실험해보겠습니다. 잡은 하나이상에 스텝을 가지기 때문에 스텝을 빈으로 만들어서 실행할 job에 등록하셔야합니다.
```java
@Slf4j
@Configuration
@RequiredArgsConstructor
public class jobConfiguration {

    private final JobBuilderFactory jobBuilderFactory;
    private final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job job(){
        return jobBuilderFactory.get("job")
                .start(step1())
                .build();
    }

    @Bean
    public Step step1(){
        return stepBuilderFactory.get("step1")
                .tasklet((contribution, chunkContext) -> {
                    log.info(">>> This is Step1");
                    return RepeatStatus.FINISHED;
                }).build();
    }

}
```

## Job Instance
JobInstance는 배치에서 Job이 실행될 때 하나의 실행단위 입니다. 만약 오늘과 내일 testJob이라는 job을 실행한다면 2개의 testJob기록이 생길것입니다. 그리고 이 JobInstantce는 1:N의 관계롤 JobExecution을 가집니다. 

## Job Execution
JobExecution은 JobInstance에 대한 실행 단위를 나타냅니다. Job exectuion은 Job Instance를 실행했던 성공 실패여부 왜실패했는지에대한 정보를 갖고 있는 클래스라고 보시면 됩니다.

```java
public class JobExecution extends Entity {
	private final JobParameters jobParameters;
	private JobInstance jobInstance;
	private volatile Collection<StepExecution> stepExecutions = Collections.synchronizedSet(new LinkedHashSet<>());
	private volatile BatchStatus status = BatchStatus.STARTING;
	private volatile Date startTime = null;
	private volatile Date createTime = new Date(System.currentTimeMillis());
	private volatile Date endTime = null;
	private volatile Date lastUpdated = null;
	private volatile ExitStatus exitStatus = ExitStatus.UNKNOWN;
	private volatile ExecutionContext executionContext = new ExecutionContext();
	private transient volatile List<Throwable> failureExceptions = new CopyOnWriteArrayList<>();
	private final String jobConfigurationName;
```
위에는 JobExecution class입니다. JobExecution은 다음과 같은 정보를 가지고 있습니다.
작성필요

## Job parameteres
JobParameteres는 Job이 실행될 때 필요한 파라미터들을 Map타입으로 저장하는 객체입니다. JobParameteres는 JobInstance를 구분하는 기준이 됩니다. 예를들어 하나의 Job을 실행할때 JobParameter로 date정보를 넘기면 하나의 JobInstance가 생성이됩니다. 하지만 동일한 JobParameter로 date를 넘긴다면 JobInstance는 생성되지 않을것이고 실행되지 않을것입니다. 즉 JobInstance와 JobParametes는 1:1 관계입니다.

## Step



## BATCH_JOB_INSTANCE
다음 테이블을 조회하면 다음과 같이 조회될것이다.


JOB_INSTANCE_ID | VERSION | JOB_NAME | JOB_KEY|
------- | ------- | ------- | -------|
1 | 0 | job | d41d8cd98f00b204e9800998ecf8427e|

* JOB_INSTANCE_ID
    * BATCH_JOB_INSTANCE 테이블의 PK
* JOB_NAME
    * 수행한 Batch Job Name



BATCH_JOB_INSTANCE 테이블은 Job Parameter에 따라 생성되는 테이블이다. 이것은 Spring Batch가 실행될때 외부에서 받을 수 있는 파라미터를 의미한다.  예를 들어, 특정 날짜 JobParameter로 넘기면 Spring Batch에서는 해당 날짜 데이터로 조회/ 가공/ 입력등의 작업을 할 수 있다. 같은 Batch Job이라도 Job Parameter가 다르면 BATCH_JOB_INSTANCE에는 기록되며, Job parameter가 없거나 같다면 기록되지 않습니다. 기존에는 잡 인스턴스를 업그레이드에해서 잡인스턴스에 파라미터를 아래와같이 넘겼다.

* Step에 jobScope 어노테이션을 가져가지 않는다면 exception이 발생한다.

```java

@Slf4j // log 사용을 위한 lombok 어노테이션
@RequiredArgsConstructor // 생성자 DI를 위한 lombok 어노테이션
@Configuration
public class jobConfiguration {
    private final JobBuilderFactory jobBuilderFactory;
    private final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job job() {
        return jobBuilderFactory.get("job")
                .start(step1(null))
                .build();
    }

    @Bean
    @JobScope
    public Step step1(@Value("#{jobParameters[date]}") String date) {
        return stepBuilderFactory.get("step1")
                .tasklet((contribution, chunkContext) -> {
                    log.info(">>>>> This is Step1");
                    log.info(">>>>> date = {}", date);
                    return RepeatStatus.FINISHED;
                })
                .build();
    }
}

```

* JobScope는 하나의 Job동안에 빈의 scope를 갖는다.
* StepScope는 하나의 Job동안 호출되어지는 만큼의 빈의 scope를 가지게 된다. (바로 step scope를 실행하게되면 No context holder available for step scope이라는 에러를 만나는데 아마 job에 빈을 먼저 등록하지 않아서 나는것같다.)

위에 job을 실행하기위해서 아래와같이 job parameter를 넘길 수 있습니다.
![](https://i.imgur.com/PvQI2pl.png)


이렇게 실행하면 아래와같이 BATCH_JOB_INSTANCE에는 기존과 같은 Job에 이름이지만 새로운 Job 인스턴스가 추가되어진것을 볼 수 있습니다.


JOB_INSTANCE_ID | VERSION | JOB_NAME | JOB_KEY|
------- | ------- | ------- | -------|
1 | 0 | job | d41d8cd98f00b204e9800998ecf8427e
2 | 0 | job | 83eda6e86aa43ff7becb7968d1ebb945

그런데 그대로 파라미터를 같은값으로 실행하면 아래와같이 에러메시지를 만날것이다.

```java
Caused by: org.springframework.batch.core.repository.JobInstanceAlreadyCompleteException: A job instance already exists and is complete for parameters={date=20190421}.  If you want to run this job again, change the parameters.
```

그럼이번에 기존에 넘긴 파라미터에서 아래와같이 값을 변경해서 앱을 실행해보자.

```java
{date=20190422}
```

이렇게 배치를 실행하면 아래와 같이 새로운 잡인스턴스가 잘 추가되는것을 볼 수 있다.

JOB_INSTANCE_ID | VERSION | JOB_NAME | JOB_KEY
----------------|---------|----------|--------
1 | 0 | job | d41d8cd98f00b204e9800998ecf8427e
2 | 0 | job | 83eda6e86aa43ff7becb7968d1ebb945
3 | 0 | job | 7d58a461dae8e2a4524ad8ff10a313c5


즉, 동일한 Job이 Job Parameter가 달라지면 그때마다 BATCH_JOB_INSTANCE에 생성되며, 동일한 Job Parameter는 여러개 존재할 수 없습니다 .


# BATCH_JOB_EXECUTION

다음으로 볼것은 BATCH_JOB_EXECUTION 테이블입니다.

JOB_EXECUTION_ID | VERSION | JOB_INSTANCE_ID | CREATE_TIME | START_TIME | END_TIME | STATUS | EXIT_CODE | EXIT_MESSAGE | LAST_UPDATED | JOB_CONFIGURATION_LOCATION
------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | --------
1 | 2 | 1 | 2019-04-21 16:21:59 | 2019-04-21 16:21:59 | 2019-04-21 16:21:59 | COMPLETED | COMPLETED |  | 2019-04-21 16:21:59 | NULL|
2 | 2 | 2 | 2019-04-21 17:12:49 | 2019-04-21 17:12:49 | 2019-04-21 17:12:49 | COMPLETED | COMPLETED |  | 2019-04-21 17:12:49 | NULL|
3 | 2 | 3 | 2019-04-21 17:21:13 | 2019-04-21 17:21:13 | 2019-04-21 17:21:13 | COMPLETED | COMPLETED |  | 2019-04-21 17:21:13 | NULL|

JOB_EXECUTION 테이블을 보시면 3개의 ROW가 존재하는것을 확인할 수 있습니다. 앞서 실행했던 파라미터가 없는 job, date=20190421, date=20190422 파라미터로 실행했던 job 까지 이렇게 3개의 실행 데이터입니다.

JOB_EXECUTION와 JOB_INSTANCE는 부모-자식 관계이다. JOB_EXECUTION은 자신의 부모 JOB_INSTANCE가 성공.실패했던 모든 내역을 갖고 있다. 실제 그런지 예제를 돌려보겠습니다.


아래와같이 실행을 해도 Exception을 던지기 때문에 BatchJob인스턴스는 실패할것이다.
```java
 @Bean
    public Job job() {
        return jobBuilderFactory.get("job")
                .start(step1(null))
                .next(step2(null))
                .build();
    }

    @Bean
    @JobScope
    public Step step1(@Value("#{jobParameters[date]}") String date) {
        return stepBuilderFactory.get("step1")
                .tasklet((contribution, chunkContext) -> {
                    throw new IllegalArgumentException("step1에서 실패합니다.");
                })
                .build();
    }

    @Bean
    @JobScope
    public Step step2(@Value("#{jobParameters[date]}") String date) {
        return stepBuilderFactory.get("step2")
                .tasklet((contribution, chunkContext) -> {
                    log.info(">>>>> This is step2");
                    log.info(">>>>> date = {}", date);
                    return RepeatStatus.FINISHED;
                })
                .build();
    }
```

실패를 하면 아래와 같이 상태에 실패와 함께 왜실패했는지 로그등이 남겨진다.


JOB_EXECUTION_ID | VERSION | JOB_INSTANCE_ID | CREATE_TIME | START_TIME | END_TIME | STATUS | EXIT_CODE | EXIT_MESSAGE | LAST_UPDATED | JOB_CONFIGURATION_LOCATION|
------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | --------
1 | 2 | 1 | 2019-04-21 16:21:59 | 2019-04-21 16:21:59 | 2019-04-21 16:21:59 | COMPLETED | COMPLETED |  | 2019-04-21 16:21:59 | NULL
2 | 2 | 2 | 2019-04-21 17:12:49 | 2019-04-21 17:12:49 | 2019-04-21 17:12:49 | COMPLETED | COMPLETED |  | 2019-04-21 17:12:49 | NULL
3 | 2 | 3 | 2019-04-21 17:21:13 | 2019-04-21 17:21:13 | 2019-04-21 17:21:13 | COMPLETED | COMPLETED |  | 2019-04-21 17:21:13 | NULL
4 | 2 | 4 | 2019-01-12 09:47:24 | 2019-01-12 09:47:24 | 2019-01-12 09:47:24 | FAILED | FAILED | java.lang.IllegalArgumentException: step1?? ?????.↵⇥at com.example.batch.job.jobConfiguration.lambda$step1$0(jobConfiguration.java:35)↵⇥at org.springframework.batch.core.step.tasklet.TaskletStep$ChunkTransactionCallback.doInTransaction<br>... | 2019-01-12 09:47:24 | NULL|


그럼 이번에는 이 step1을 Exception이 아닌 정상적으로 다시 실행해보자.

```java
    @Bean
    @JobScope
    public Step step1(@Value("#{jobParameters[date]}") String date) {
        return stepBuilderFactory.get("step1")
                .tasklet((contribution, chunkContext) -> {
                    log.info(">>>>> This is Step1");
                    log.info(">>>>> date = {}", date);
                    return RepeatStatus.FINISHED;
                })
                .build();
    }

```

그럼 BATCH_JOB_EXECUTION테이블은 아래와같이 정상적으로 처리가 될것이다.

JOB_EXECUTION_ID | VERSION | JOB_INSTANCE_ID | CREATE_TIME | START_TIME | END_TIME | STATUS | EXIT_CODE | EXIT_MESSAGE | LAST_UPDATED | JOB_CONFIGURATION_LOCATION
------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | --------
1 | 2 | 1 | 2019-04-21 16:21:59 | 2019-04-21 16:21:59 | 2019-04-21 16:21:59 | COMPLETED | COMPLETED |  | 2019-04-21 16:21:59 | NULL
2 | 2 | 2 | 2019-04-21 17:12:49 | 2019-04-21 17:12:49 | 2019-04-21 17:12:49 | COMPLETED | COMPLETED |  | 2019-04-21 17:12:49 | NULL
3 | 2 | 3 | 2019-04-21 17:21:13 | 2019-04-21 17:21:13 | 2019-04-21 17:21:13 | COMPLETED | COMPLETED |  | 2019-04-21 17:21:13 | NULL
4 | 2 | 4 | 2019-01-12 09:47:24 | 2019-01-12 09:47:24 | 2019-01-12 09:47:24 | FAILED | FAILED | java.lang.IllegalArgumentException: step1?? ?????.↵⇥at com.example.batch.job.jobConfiguration.<br>...| 2019-01-12 09:47:24 | NULL
5 | 2 | 4 | 2019-01-12 09:59:41 | 2019-01-12 09:59:41 | 2019-01-12 09:59:41 | COMPLETED | COMPLETED |  | 2019-01-12 09:59:41 | NULL

여기에서 보면 JOB_EXECUTION_ID 4, 5를 봐보면 JOB_INTANCE_ID가 4인것을 볼 수 있다. 그리고 의도한대로 실패와 성공의 정보가 담겨있는것을 알 수 있다. 여기에서 실패한 Job instance를 동일한 JobParameter실행했음에도 job이 실행되어 성공한것을 볼 수 있습니다. 이렇듯 spring 배치에서 실패한 job에 한에서는 같은 job parameter를 넘기더라도 실행을 할 수 있습니다.


# JOB, JOB_INSTANCE, JOB_EXECUTION

Job : job
Job : Job parameter를 2018.08.01로 실행한 job
Job Execution : Job parameter를 2018.08.01로 실행한 job의 1번째 시도 혹은 다음번 시도

# BATCH_JOB_EXECUTION_PARAMS
BATCH_JOB_EXECUTION_PARAM테이블은 BATCH_JOB_EXECUTION 테이블이 생성될 당시에 입력받은 Job Parameter를 담고 있다.

|JOB_EXECUTION_ID | TYPE_CD | KEY_NAME | STRING_VAL | DATE_VAL | LONG_VAL | DOUBLE_VAL | IDENTIFYING|
------- | ------- | ------- | ------- | ------- | ------- | ------- | -------
2 | STRING | date | 20190421 | 1970-01-01 00:00:00 | 0 | 0 | Y
3 | STRING | date | 20190422 | 1970-01-01 00:00:00 | 0 | 0 | Y
4 | STRING | date | 20180807 | 1970-01-01 00:00:00 | 0 | 0 | Y
5 | STRING | date | 20180807 | 1970-01-01 00:00:00 | 0 | 0 | Y



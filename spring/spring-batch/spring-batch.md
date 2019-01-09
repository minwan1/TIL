

# 스프링 부트 배치 주의사항

1. 가능하면 단순화해서 복잡한 구조와 로직을 피애햐한다.
2. 데이터를 직접 사용한 작업이 빈번하게 일어나므로 데이터 무결성을 유지하는 유효성 검사등의 방어책이 있어야한다.
3. 배치 처리 시 시스템 I/O 사용을 최소화해야한다. 잦은 I/O로 데이터베이스 커넥션과 네트워크 비용이 커지면 성능에 영향을 줄 수 있기 때문이다. 따라서 가능하면 한번에 데이터를 조회하여 메모리에 저장해두고 처리를 한 다음, 그결과를 한번에 데이터베이스에 저장하는것이 좋다.
4. 일반적으로 같은 서비스에 사용되는 웹, API 배치, 기타 프로젝트들은 서로 영향을 줍니다. 따라서 배치처리가 진행되는 동안 다른 프로젝트 요소에 영향을 주는 경우가 없는지 주의를 기울여야한다.

5. 스프링 부트 배치는 스케줄러를 제공하지 않습니다. 배치 처리 기능만 제공하며 스케줄링 기능은 스프링에서 제공하는 쿼츠 프레임워크, IBM 티볼리(Tivoli) 스케줄러, BMC 컨트롤-M(Control-M)등을 이용해야 합니다. 

리눅스 crontab 명령은 가장 간단히 사용할 수 있지만 이는 추천하지 않습니다. crontab의 경우 각서버마다 따로 스케줄링 관리해야 하며 무엇보다 클러스터링 기능이 제공되지 않습니다. 반면 쿼츠와 같은 스케줄링 프레임워크를 사용한다면 클러스터링 뿐만 아니라 다양한 스케줄링기능. 실행 이력관리등 여러 이점을 얻을 수 있습니다.





# 스프링 부트 배치 이해하기

## 배치 시나리오

1. 읽기(read) : 데이터 저장소(일반적으로 데이터베이스) 에서 특정 데이터 레코드를 읽습니다.
2. 처리(processing) : 원하는 방식으로 데이터를 가공/처리합니다.
3. 쓰기(write) : 수정된 데이터를 다시 저장소(데이터베이스)에 저장합니다.


배치처리는 단순하게 읽기 -> 처리 -> 쓰기 흐름을 갖습니다. 이것을 도식화하면 다음과 같을 수 있습니다.

![](https://i.imgur.com/xZDN6mh.jpg)

Job과 Step은 1 : M, Step과 ItemReader, ItemProcessor, ItemWriter는 1:1의 관계를 가집니다. **즉 Job이라는 하나의 큰 일감에 여러 단계를 두고, 각 단계를 배치의 기본 흐름대로 구현합니다.**

그럼 각 구성 요소의 역할을봐보겠습니다.


## Job

Job은 배치 처리 과정을 하나의 단위로 만들어 표현한 객체입니다. 또한 전체 배치 처리에 있어 항상 최상단 계층에 있습니다. 위에서 하나의 Job(일감) 안에는 여러 Step(단계)이 있다고 설명했던 바와 같이 스프링 배치에서 Job 객체는 여러 Step 인스터스를 포함하는 컨테이너입니다.

```java
public class JobBuilderFactory {

	private JobRepository jobRepository;

	public JobBuilderFactory(JobRepository jobRepository) {
		this.jobRepository = jobRepository;
	}

	/**
	 * Creates a job builder and initializes its job repository. Note that if the builder is used to create a &#64;Bean
	 * definition then the name of the job and the bean name might be different.
	 * 
	 * @param name the name of the job
	 * @return a job builder
	 */
	public JobBuilder get(String name) {
		JobBuilder builder = new JobBuilder(name).repository(jobRepository);
		return builder;
	}

}
```
JobBuilderFactory로 jobBuilder를 생성한다. 다음은 JobBuilder를 의미한다. 그럼 JobBuilder Class가 무엇을 하는지 알아보자.


```java

JobBuilder Class

    public SimpleJobBuilder start(Step step) {
		return new SimpleJobBuilder(this).start(step);
	}

	/**
	 * Create a new job builder that will execute a flow.
	 *
	 * @param flow a flow to execute
	 * @return a {@link SimpleJobBuilder}
	 */
	public JobFlowBuilder start(Flow flow) {
		return new FlowJobBuilder(this).start(flow);
	}

	/**
	 * Create a new job builder that will execute a step or sequence of steps.
	 *
	 * @param step a step to execute
	 * @return a {@link SimpleJobBuilder}
	 */
	public JobFlowBuilder flow(Step step) {
		return new FlowJobBuilder(this).start(step);
	}
```

1. Step을 추가해서 가장 기본이 되는 SimpleJobBuilder를 생성한다.
2. Flow를 실행할 JobFlowBuilder를 생성한다.
3. Step을 실행할 JobFlowBuilder를 생성한다.

결국 JobBuilder를 통해 job을 생성하는게아닌 또 다른 JobBuilder들을 생성한다. 이러한 이유는 Job생성방법이 다르기 때문에 별도의 JobBuilder를 생성하는것이다.


그럼 JobBuilder로 가장 간단하게 만들 수 있는 빌더인 SimpleJobBuilder를 이용해 Job을 생성해보자.
```java
@Autowired
private JobBuilderFactory jobBuilderFactory;

@Bean
public Job simpleJob(){
    return JobBuilderFactory.get("simpleJob")
    .start(simpleStep())
    .build();
}
```

JobBuilderFactory의 get() 메서드에 "simpleJob" 문자열을 파라미터로 넘기면 "simpleJob" 이라는 이름을 가진 Job을 생성할 수 있는 JobBuilder 객체 인스턴스가 반환 된다. 이 빌더를 통하여 job을 생성하면 "simpleJob"이라는 이름을 가진 Job이 생성되어진다.

### JobInstance
JobInstance는 배치에서 Job이 실행될 때 하나의 Job실행 단위이다.


### JobExecution
JobExeCution은 JobInstance에 대한 한번의 실행을 나타내는 객체입니다. 


### JobParameters
JobParameters는 Job이 실행될 때 필요한 파라미터들을 Map타입으로 저장하는 객체이다. 


## Step
Step은 실질적인 배치 처리를 정의하고 제어하는데 필요한 모든 정보가 들어있는 도메인 객체입니다. Job을 처리하는 실질적인 단위로 쓰입니다. 모든 Job에서는 1개 이상의 Step이 있어야 합니다.

### StepExcution
Job에 JobExecution이라는 Job 실행 정보가 있다면 Step에는 StepExecution이라는 Step 실행정보를 담는 객체가 이씃ㅂ니다. 각각의 Step이 실행될 때마다 StepExecution이 생성됩니다. 


```java

public class StepExecution extends Entity {
    private final JobExecution jobExecution;
    private final String stepName;
    private volatile BatchStatus status;
    private volatile int readCount;
    private volatile int writeCount;
    private volatile int commitCount;
    private volatile int rollbackCount;
    private volatile int readSkipCount;
    private volatile int processSkipCount;
    private volatile int writeSkipCount;
    private volatile Date startTime;
    private volatile Date endTime;
    private volatile Date lastUpdated;
    private volatile ExecutionContext executionContext;
    private volatile ExitStatus exitStatus;
    private volatile boolean terminateOnly;
    private volatile int filterCount;
    private transient volatile List<Throwable> failureExceptions;

```


## JobRepository

JobRepository는 배치 처리 정보를 담고 있는 메커니즘이다. 어떤 Job이 실행되었으며 몇번 실행되었고 언제 끝났는지 등 배치 처리에 대한 메타데이털르 저장한다. 예를 들어 Job하나가 실행되면 JobRepository에서는 배치 실행에 관련된 정보를 담고 있는 도메인인인 JobExecution을 생성합니다.

JobRepository는 Step의 실행 정보를 담고 있는 StepExecution도 저장소에 저장하면 전체 메타데이터를 저장/관리하는 역할을 수행합니다.


## JobLancher

JobLauncher는 Job, JobParameters와 함께 배치를 실행하는 인터페이스입니다. 인터페이스의 메서드는 run() 하나이다.

```java
public interface JobLauncher{
    public JobExecution run(Job job, JobParameters jobParameters) throws ...
}
```


## ItemReader
ItemReader는 Step의 대상이 되는 배치 데이터를 읽어오는 인터페이스입니다. FILE, XML, DB등 여러타입의 데이터를 읽어올 수 있습니다.

```java
public interface ItemReader<T> {
    T read() throws Exception;
}
```
ItemReader에서 read() 메서드의 반환타입을 제넥으로 구현했기 때문에 직접 타입을 지정 할 수 있습니다.


## ItemProcessor

ItemProcessor는 ItemReader로 읽어온 배치 데이터를 변환하는 역할을 수행한다. 굳이 ItemWriter에 변화로직을 넣을 수 도 있는데 왜 ItemProcessor를 넣는 이유는 크게 두가지이다.
1. ItemWriter는 저장만 수행하고, ItemProcessor는 로직 처리만 수행해 역할을 명확하게 분리한다.
2. 읽어온 배치 데이터와 쓰여질 데이터의 타입이 다를 경우에 대응하기 위해서이다. 명확한 인풋과 아웃풋을 ItemProcessor로 구현해놓는다면 더 직관적인 코드가 될 것이다.

다음은 ItemProcessor인터페이스 코이다. 제니릭을 사용해 인풋, 아웃풋 타입을 정의하고 비지니스 로직을 구현한다.


```java
public interface ItemProcessor<I, O> {
	O process(I item) throws Exception;
}

```


## ItemWriter
ItemWriter는 배치 데이터를 저장합니다. 일반적으로 DB나 파일에 저장한다.

```java
public interface ItemWriter<T> {
    void write(List<? extends T> items)throws Exception
}
```
ItemWriter도 ItemReader와 비슷한 방식으로 구현하면 됩니다. 제네릭으로 원하는 타입을 받습니다. write() 메서드는 List 자료구조를 사용해 지정한 타입의 리스트를 매개변수로 받습니다. write() 메서드는 List자료구조를 사용해 지정한 타입의 리스트를 매개변수로 받고 리스트의 데이터 수는 설정한 청크의 단위로 불러온다. write() 메서드의 반환값은 따로 없고 매개변수 받은 데이터를 저장하는 로직을 구현하면 된다.

* 청크란 아이템이 트랜잭션에서 커밋되는 수를 말합니다.

# 스프링 부트 휴면 회원 배치 설계하기

https://github.com/young891221/Spring-Boot-Community-Batch/tree/step1



# 스프링 부트 휴면 회원 배치 구현하기
배치는 위에서 장황하게 설명했지만 결국 읽고, 처리하고, 쓰는 기본적인 프로세스만 알면 쉽게 배처 로직을 구현할 수 있다. 구현 순서느 다음과 같다.
1. 휴면회원 배치 테스트 코드 생성
2. 휴면회원 배치정보 설정
3. SQL로 테스트 데이터 주입하기
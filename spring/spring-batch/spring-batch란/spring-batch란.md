# Spring Batch Example - Spring Batch란?
많은 엔터프라이즈 에플리케이션에서 대량의 작업들을 한번에 일과적으로 처리를 할필요가 있다. 배치 작업들은 유저에 처리 없이 자동화되어 많은 양의 작업들이 한번에 처리되는것을 의미한다. 이러한 배치성 작업들은 대부분 시간 기반 이벤트(주기적인 시간다, 알림처리 등)들이다. 배치 어플리케이션으로 주기적으로 외부또는 내부로부터 받은 데이터를 형식화하고 검증하여 여러 기록 시스템에서 그 형식에 맞는 트랜잭션 방법으로 작업을 처리한다. 이렇듯 배치처리는 하루에 수백만건의 작업들을 처리하기위해 사용되어진다. 

스프링 배치는 가볍고 종홥적고 일상적인 엔터프라이즈 시스템들에서 튼튼한 배치를 처리하기위해 설계되어졌다. 스프링배치는 스케줄링 프레임워크는 아니다. 말그대로 배치를 위해 생성되어진 프레임워크다. 이러한 배치처리를 돕는 여러 production 레벨에 스케줄러들이 존재한다. 예를들어 오픈서스 기반에 쿼츠, Tivoli, Control-M 등이 있다. 배치는 스케줄러를 대체하는게 아닌 결합하여 사용하는것이다.



# 스프링 부트 배치 설계시 주의사항

1. 가능하면 단순화해서 복잡한 구조와 로직을 피해햐한다.
2. 데이터를 직접 사용한 작업이 빈번하게 일어나므로 데이터 무결성을 유지하는 유효성 검사등의 방어책이 있어야한다.
3. 배치 처리 시 시스템 I/O 사용을 최소화해야한다. 잦은 I/O로 데이터베이스 커넥션과 네트워크 비용이 커지면 성능에 영향을 줄 수 있기 때문이다. 따라서 가능하면 한번에 데이터를 조회하여 메모리에 저장해두고 처리를 한 다음, 그결과를 한번에 데이터베이스에 저장하는것이 좋다.
4. 일반적으로 같은 서비스에 사용되는 웹, API 배치, 기타 프로젝트들은 서로 영향을 줍니다. 따라서 배치처리가 진행되는 동안 다른 프로젝트 요소에 영향을 주는 경우가 없는지 주의를 기울여야한다.
5. 스프링 부트 배치는 스케줄러를 제공하지 않습니다. 배치 처리 기능만 제공하며 스케줄링 기능은 스프링에서 제공하는 쿼츠 프레임워크, IBM 티볼리(Tivoli) 스케줄러, BMC 컨트롤-M(Control-M)등을 이용해야 합니다. 



# 스프링 부트 배치 Flow
그럼 이제 실제 스프링 배치로 배치처리를 어떻게 하는지 알아보겠습니다. 스프링 배치 처리 흐름은 크게 청크 지향 프로세승과 TaskLet 방식이 있습니다. 먼저 청크지향 프로세싱을 설명하고 그다음 Tasklet 방식을 설명하겠습니다. 아래는 스프링 배치의 청크 지향 프로세싱 흐름을 도시확한 그림입니다. 아래의 그림을 보시면서 내용 읽으시면 좀 더 쉽게 이해하실 수 있습니다.

* 스프링 배치는 아래와 같이 **JobLaucher**를 실행시켜 등록된 **Job**을 실행합니다. 
* 하나의 **Job**은 여러개의 **Step**을 가질 수 있습니다. 
* **Step**은 **ItemReader, ItemProcessor, ItemWriter**로 만들어집니다. 
* **Step**은 차례대로 **ItemReader, ItemProcessor, ItemWriter** 처리합니다.
* **Job**, **Step**이 실행됐는지 또 이러한 작업의 단위가 중복 실행됐는지등은 미리 스프링 배치에서 생성한 **JobRepository**를 통해 확인하게 됩니다. 

![](https://i.imgur.com/xZDN6mh.jpg)

각 용어에 대해서는 아래에서 좀 더 세부적으로 알아보겠습니다.



## JobRepository
위에서 배치에 실행된 정보들을 Spring batch에서 정의해놓은 Repository를 통해 Batch정보를 저장한다고했습니다. 이역할을 하는것이 JobRepository입니다. JobRepository는 배치 처리 정보를 담고 있는 매커니즘입니다. 어떤 Job이 실행되었으며 몇번 실행되었고 언제 끝나는지 배치에대한 메타 데이터를 저장하고있습니다. JobResitory는 Step의 실행정보를 담고 있는 StepExecution도 저장소에 저장하며 전체 메타데이터를 저장/관리하는 역할을 수행합니다.

### Batch 메타 테이블 구조
![](https://i.imgur.com/IBxzbj5.png)


## JobLancher

JobLauncher는 Job, JobParameters와 함께 배치를 실행하는 인터페이스입니다. run 메소드를 호출하게되면 JobExcution을 받습니다.

```java
public interface JobLauncher{
    public JobExecution run(Job job, JobParameters jobParameters) throws ...
}
```
만약 같은 파라미터로 그전에 실행하다가 중단되거나 실행한 Job이 있다면 그전과 동일한JobExcution을 받게 됩니다.


## Job

Job은 배치 처리 과정을 하나의 작업 단위로 표현한 객체입니다. 위에서 하나의 Job(일감) 안에는 여러 Step(단계)이 있다고 설명했던 바와 같이 스프링 배치에서 Job 객체는 여러 Step 인스터스를 포함하고 있습니다.

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

## Step
Step은 실질적인 배치 처리를 정의하고 제어하는데 필요한 모든 정보가 들어있는 도메인 객체입니다. Job을 처리하는 실질적인 단위로 쓰입니다. 모든 Job에서는 1개 이상의 Step이 있어야 합니다.


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



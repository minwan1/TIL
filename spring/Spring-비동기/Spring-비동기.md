# @Async with ThreadPoolTaskExecutor
Thread pool을 이용해서 thread를 관리가능한 방식입니다.
아래의 SpringAsyncConfig 클래스를 추가해주시기 바랍니다.

## 3. The @Async Annotation
1. public만 사용 가능합니다.
2. 같은 클래스내에서는 비동기가 적용되지 않습니다.


```java
@Configuration
@EnableAsync
public class SpringAsyncConfig {

    @Bean(name = "threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(3);
        taskExecutor.setMaxPoolSize(30);
        taskExecutor.setQueueCapacity(10);
        taskExecutor.setThreadNamePrefix("Executor-");
        taskExecutor.initialize();
        return taskExecutor;
    }

}
```


위 예는 쓰레드 풀 생성시 최소 가용 쓰레드로 3개를 생성한다. 만약 3개 쓰레드가 모두 사용 중이라면 큐에 30개까지 대기할 수 있다. 대기 중인 큐마저 모두 차버리면 가용 쓰레드를 최대 20개까지 생성한다. 최대 가용 쓰레드까지 차버리면 TaskRejectedException 예외가 발생한다.


아래와같이 사용가능함.

```java
public class GreetingService {
	
    @Async("threadPoolTaskExecutor")
    public void method1() throws Exception {
        // do something
        System.out.println(Thread.currentThread());
    }

}
```


|속성|설명|
|-|-|
|id|생성할 빈의 식별 값을 지정한다. |
|pool-size | 쓰레드 풀의 갯수를 지정한다. |
|queue-capacity | 풀의 쓰레드가 모두 작업을 실행중인 경우 큐에서 대기할 수 있는 작업의 개수를 지정한다. |
|keep-alive | 풀에 있는 시간을 지정한다. 이시간이지나면 쓰레드는 풀에서 제거된다.|
|rejection-policy | 큐가 다차서 더이상 작업을 받을 수 없을 때 작업을 어떻게 처리할지를 결정한다. 설쩡할 수 있는 값은 다음과 같다. ABORT : 작업을 거부하고 익셉션을 발생시킨다. CALLER_RUNS :  호출한 쓰레드를 이용해서 실행한다. DISCARD : 작업을 싱행하지 않고 무시한다. DISCARD_OLDEST : 큐의 헤드에서 하나를 제거하고 작업을 추가한다. |


## Java ThreadPool
병렬 작업 처리가 많아지면 스레드 개수가 증가되고 그에 따른 스레드 생성과 스케줄링으로 인해 CPU가 바빠져 메모리 사용량이 늘어납니다. 따라서 어플리케이션의 성능이 저하됩니다. 갑작스런 병렬작업의 극대화로 인한 스레드 증폭을 막으려면 스레드 풀(Thread Pool)을 사용해야 합니다. 스레드 풀은 작업 처리에 사용되는 스레드를 제한된 개수만큼 정해 놓고 작업 큐(Queue)에 들어오는 작업들을 하나씩 스레드가 맡아 처리합니다.




출처: http://palpit.tistory.com/732 [palpit's log-b]




Spring threadpool예제
https://www.mkyong.com/spring/spring-and-java-thread-example/




Spring ThreadPoolTaskScheduler vs ThreadPoolTaskExecutor



ThreadPoolTaskExecutor is a specialized class for executing tasks.
ThreadPoolTaskScheduler is a specialized class for scheduling tasks.
ThreadPoolTaskExecutor 지금 바로 실행이 필요할경우 사용.
ThreadPoolTaskScheduler 예정되어진 일정이 있다면 스케줄러사용.







http://heowc.tistory.com/68 (쓰레드풀 테스트)



https://stackoverflow.com/questions/43874079/how-to-decide-on-the-threadpooltaskexecutor-pools-and-queue-sizes(쓰레드 수용범위 계산)

## Synchronzied

 이전 Part 1에서 executor service를 통해 어떻게 코드를 병렬적으로 실행시키는지 알아보았다. 멀티 스레드로 프로그램을 작성하게 될 때는 여러 스레드에 의해 공용으로 접근되는 변수에 접근할 때 각별한 주의를 기울여야 한다. 
 아래 코드에서 count 변수를 하나 설정하였고 메서드 increment()를 정의했다.

```java
int count = 0;

void increment() {
    count = count + 1;
}
```

굉장히 간단한 코드이다. increment() 메서드는 변수 count의 값을 1 증가시킨다. 자 이 메서드를 아래와 같이 호출해보자.

```java
ExecutorService executor = Executors.newFixedThreadPool(2);

IntStream.range(0, 10000)
    .forEach(i -> executor.submit(this::increment));

stop(executor);

System.out.println(count);  // 9965
```

 설명도 할 것 없이, 0부터 10000까지 즉, executor에 increment 함수를 넘겨서 10000회 호출시킨다. 결과를 보자면 count의 값은 10000이 되어야 마땅하다. 그러나 실제 결과는 매번 다르다. 그리고 절대 10000이 되는 경우를 보기는 쉽지 않다. 그 이유는 동기화 없이 무분별하게 다수의 스레드에 의해 변수에 접근하게 되면 흔히들 Race condition 이라는 현상을 만나기 때문이다.

 **변수의 값을 증가시키는 과정은 다음과 같이 나뉜다 첫째로 현재 변수의 값을 읽어내고, 두번째로 1 증가된 값을 알아낸 다음, 세번째로 그 값을 해당 변수의 값으로 대입시킨다. 이것이 한번에 순차대로 모두 일어나면 문제가 없겠지만 두개 이상의 스레드에 의해 진행되면 문제가 생길 수 있다. 초기에 count 변수의 값은 0이다. 스레드는 cpu 시간을 사용하여 움직인다. 스레드 1이 cpu에 의해 구동되어 첫번째, 두번째 단계까지 마무리 짓고 차례가 스레드 2로 넘어갔다고 하자. 스레드 1은 0이란 값을 읽었고 다음 값을 계산하여 1이란 결론을 얻은 상태이다. 스레드 2는 운이 좋게도 첫번째, 두번째 그리고 세번째 단계를 모두 진행했다. 그리고 다시 스레드 1의 차례가 되었다. count 의 현재 값은 스레드 2에 의해 1이 되었을 것이다. 그러나 스레드 1은 세번째 단계를 수행하여 count 변수에 자신이 계산한 값 1을 대입하게 되고 결과적으로 count는 2가 아닌 1의 값을 갖는다.**

 대강 그렇고, 아마 운영체제 시간에 배울 것이다. 

 다행스럽게도 이를 위해 자바는 일찍부터 synchronized라는 키워드를 통해 스레드 동기화를 지원하고 있었다. 우리는 synchronized 키워드를 이용해서 위 race condition 을 정상적으로 실행되도록 고칠 수 있다.

```java
synchronized void incrementSync() {
    count = count + 1;
}
```

즉, 동기화 메서드로 incrementSync 메서드를 배타적으로 접근하게 만든 것이다. 위 코드에서 executor에 incrementSync를 넘겨주면 예상한 결과값인 10000이 count의 값으로 할당되었다는 사실을 알 수 있다.

```java
ExecutorService executor = Executors.newFixedThreadPool(2);

IntStream.range(0, 10000)
    .forEach(i -> executor.submit(this::incrementSync));

stop(executor);

System.out.println(count);  // 10000
 synchronized 키워드는 블록 구문으로도 사용 가능하다.
void incrementSync() {
    synchronized (this) {
        count = count + 1;
    }
}

```
 내부적으로 자바는 소위 monitor라 불리는 개념을 동기화를 위해 사용한다. 역시 운영체제에서 알 수 있을 내용이며 monitor lock or intrinsic lock을 보면 나오고 지금 당장 몰라도 된다고 치자. 이 모니터는 한 객체에 바인딩 되는데 메서드에 동기화를 걸게 되면 각 메서드는 해당 상응하는 객체에 대해 동일한 모니터를 공유한다.

 모든 암시적인 모니터는 재진입 가능한(reentrant) 특징을 가진다. 이게 무슨 뜻이냐 하면 한 스레드는 같은 락을 여러번 얻어낼 수 있다는 뜻이다. 한 동기화 메서드에 진입하여 특정 객체에 대해 락을 얻은 스레드는 같은 객체를 락으로 사용하는 다른 동기화 메서드를 호출할 수 있다.

 Locks
 synchronzied 키워드를 이용한 암시적인 락킹을 이용하는 대신에 Lock 인터페이스로 정의된 명시적인 락을 Concurrency API를 통해 사용할 수 있다. Locks은 매우 다양한 메서드를 제공하는데 무척이나 훌륭한 특징을 갖는 락 제어를 가능케 한다. 따라서 암시적인 모니터보다 좀더 강렬하다.

 다수의 락 구현체들이 자바 표준 JDK에 있으며 다음 섹션에서 소개한다.

 ReentrantLock
  ReentrantLock 클래스다. 상호 배타적 락인데 기본적으로 synchronized 키워드를 이용한 암시적인 모니터와 같이 동작한다. 차이가 있다면 더 많은 기능을 제공한달까. 이름에서 나타나듯 이 락은 재진입 가능한 특징을 같는다.

 어떻게 이용하는지 살펴보자.

```java
ReentrantLock lock = new ReentrantLock();
int count = 0;

void increment() {
    lock.lock();
    try {
        count++;
    } finally {
        lock.unlock();
    }
}
```
 lock() 메서드를 통해 락을 획득한다. 그리고 unlock() 메서드를 통해 락을 해제한다. try / finally 블록으로 동기화 처리될 부분의 코드를 감싸는 것이 중요하다. 그래야만 예외상황에서도 락을 해제하지 않는 일을 방지할 수 있기 때문이다. 위 메서드는 이제 스레드-안전하다. 동기화 메서드와 동일하다. 만일 다른 스레드가 이미 락을 갖고 있다면 이후 lock()이 호출되면 현재 스레드는 락이 풀릴 때까지 정지된다. 오직 단 하나의 스레드만이 락을 특정 시간동안 획득할 수 있다.

 락은 훌륭한 동기화 제어를 위해 다음과 같이 다양한 메서드를 제공한다.

 ```java
ExecutorService executor = Executors.newFixedThreadPool(2);
ReentrantLock lock = new ReentrantLock();

executor.submit(() -> {
    lock.lock();
    try {
        sleep(1);
    } finally {
        lock.unlock();
    }
});

executor.submit(() -> {
    System.out.println("Locked: " + lock.isLocked());
    System.out.println("Held by me: " + lock.isHeldByCurrentThread());
    boolean locked = lock.tryLock();
    System.out.println("Lock acquired: " + locked);
});

stop(executor);

```

  첫번째 작업은 락을 1초동안 가지고 있을 것이며 두번째 작업은 락에 대한 상태를 출력한다.
Locked: true
Held by me: false
Lock acquired: false
 tryLock() 메서드는 lock()을 대신할 수 있는 메서드이며 스레드가 정지되는 상황에 들어가지 않으면서 락을 획득하려는 시도를 하게 해준다. 결과로 리턴되는 진위값은 공용 변수에 접근하기 전에 해당 스레드가 락을 획득했는지 그렇지 못했는지를 판단하는 데 사용된다.

 ReadWriteLock

 인터페이스 ReadWriteLock은 읽기와 쓰기 접근에 대한 대해 락을 운영할 수 있는 새로운 타입의 락이다. 이 아이디어는 변하지 않는 변수를 여럿이 동시에 읽기만 할 때는 대개 문제가 발생하지 않고 쓸때만 문제가 발생한다는 점에 착안해 나왔다. 그러므로 읽기-락은 동시에 여러 스레드에 의해 특정 스레드가 쓰기 락을 요청하지 않는 한 계속 획득될 수 있다. 이런 방식으로 쓰기보다 읽기가 더 빈번히 일어나는 상황에서는 성능을 높일 수 있다.

 ````java
ExecutorService executor = Executors.newFixedThreadPool(2);
Map<String, String> map = new HashMap<>();
ReadWriteLock lock = new ReentrantReadWriteLock();

executor.submit(() -> {
    lock.writeLock().lock();
    try {
        sleep(1);
        map.put("foo", "bar");
    } finally {
        lock.writeLock().unlock();
    }
});

````
 위 예제는 처음에 쓰기-락을 요구한다. 제일 처음에는 map에 데이터를 넣어야 하기 때문이다. 그리고 1초간 잔다. 이 약 1초간의 작업이 끝나기 이전에 두개의 다른 작업은 executor에 등록되는데 이 등록되는 작업들은 map에서 값을 읽고 출력한 뒤 1초간 잔다.
```java
 Runnable readTask = () -> {
    lock.readLock().lock();
    try {
        System.out.println(map.get("foo"));
        sleep(1);
    } finally {
        lock.readLock().unlock();
    }
};

executor.submit(readTask);
executor.submit(readTask);

stop(executor);
```
 위 코드들을 실행해보면 알겠지만 두 읽기를 수해하는 작업은 쓰기가 완료되는 전체 시간동안 반드시 지연된다. 쓰기 작업이 끝나서 쓰기-락이 해제된 시점에 두 읽기 작업은 비로소 구동이 시작되고 둘 중 어느 하나라도 막히는 바 없이 동시에 작업을 수행하게 된다. 읽기 작업 간에는 서로가 서로를 기다려야 할 필요가 없기 때문이다.

 StampedLock
 자바 8은 새로운 종류의 락을 하나 또 가져왔는데, 그 이름은 StampedLock이다. 이 또한 마찬가지로 읽기, 쓰기 락을 바로 위에서 살펴본 바와 같이 지원한다. 그러나 ReadWriteLock과는 대조적으로 락을 구하는 메서드는 Long 타입의 stamp를 리턴한다(Timestamp의 stamp...와 유사). 이 stamp 값을 이용해서 락을 해제하거나 락이 아직 타당한지를 확인해 볼 수 있다. 게다가 stamped locks은 optimistic locking이라 불리는 또다른 락 모드를 지원한다.

 마지막으로 봤었던 예제를 ReadWriteLock 대신에 StampedLock을 이용하여 수정해보자.

```java 
ExecutorService executor = Executors.newFixedThreadPool(2);
Map<String, String> map = new HashMap<>();
StampedLock lock = new StampedLock();

executor.submit(() -> {
    long stamp = lock.writeLock();
    try {
        sleep(1);
        map.put("foo", "bar");
    } finally {
        lock.unlockWrite(stamp);
    }
});

Runnable readTask = () -> {
    long stamp = lock.readLock();
    try {
        System.out.println(map.get("foo"));
        sleep(1);
    } finally {
        lock.unlockRead(stamp);
    }
};

executor.submit(readTask);
executor.submit(readTask);

stop(executor);

```
 readLock() 혹은 writeLock()을 호출하여 읽기와 쓰기 락을 얻을 수 있다. 그리고 두 메서드는 stamp 값을 리턴하고 나중에 finally 블록에서 락을 해제할 때 사용된다. 여기서 알고 넘어가야 점이 있는데 stamped lock은 재진입 가능한 특징이 없다는 것이다. 각각의 락을 요구하는 메서드의 호출은 새로운 stamp를 리턴하고 락을 얻을 수 없으면 블럭된다. 이전에 락을 얻었던 동일한 스레드가 락을 요구해도 블럭된다. 그러니 데드락에 빠지지 않게 무척이나 주의해야 한다.

 이전의 ReadWriteLock 예제와 같이 두 읽기 작업은 쓰기 작업이 완료되어 쓰기-락이 풀릴 때까지 반드시 기다려야 한다. 그리고 읽기 작업이 수행되면 동시에 코드에 접근하여 값을 콘솔에 출력할 것이다. 쓰기-락이 새로 요구되지 않는 한 읽기 작업은 서로를 블럭시키지 않기 때문이다.

 다음은 optimistic locking이다.

```java
ExecutorService executor = Executors.newFixedThreadPool(2);
StampedLock lock = new StampedLock();

executor.submit(() -> {
    long stamp = lock.tryOptimisticRead();
    try {
        System.out.println("Optimistic Lock Valid: " + lock.validate(stamp));
        sleep(1);
        System.out.println("Optimistic Lock Valid: " + lock.validate(stamp));
        sleep(2);
        System.out.println("Optimistic Lock Valid: " + lock.validate(stamp));
    } finally {
        lock.unlock(stamp);
    }
});

executor.submit(() -> {
    long stamp = lock.writeLock();
    try {
        System.out.println("Write Lock acquired");
        sleep(2);
    } finally {
        lock.unlock(stamp);
        System.out.println("Write done");
    }
});

stop(executor);
```
한 최적 읽기 락은 tryOptimisticRead() 메서드를 이용하여 획득가능하며 이 메서드는 항상 현재 스레드를 블럭시키는 것 없이 무조건 stamp 값을 리턴한다. 즉 현재 락을 획득할 수 있건 없건 상관않고 리턴한다. 락을 얻었는지 못 얻었는지는 stamp의 값이 0이냐 아니냐를 가지고 판단해야 한다. 0이면 못 얻은거다. 또한 해당 stamp 값이 현재 유효한지 아닌지를 lock.validate(stamp) 방식으로 알아낼 수 있다.
Optimistic Lock Valid: true
Write Lock acquired
Optimistic Lock Valid: false
Write done
Optimistic Lock Valid: false
결과는 위와 같은데 잘 생각하면 이해가 된다. 초기에 읽기 락이 획득되었다. 따라서 첫 호출에선 true 가 리턴된다. 그러나 1초를 쉬게 되고 이때 두번째 작업에서 쓰기 락을 획득한다. 이 경우, 읽기능 수행이 불가능해지며 따라서 다음 호출문에서는 false가 리턴된다. 쓰기는 2초 후에 끝나게 되고 동시에 쓰기-락도 해제된다. 그러면 이제 읽기-락은 다시 활성화 되어 true가 리턴된다. 그러나 잘 보면 마지막 출력은 true가 아니라 false가 리턴되었다. 이 말은 다음과 같다. 최적-락에서 읽기-락의 경우 한번 비활성화되면 끝까지 안살아난다는 거다. 따라서 매번 공용 변수를 접근하고 나서는 읽기가 타당함을 보증하기 위해 락을 활성화시켜줘야 한다.

 가끔은 읽기-락을 해제하지 않고 쓰기 락을 얻는 것이 유용할 때까 있다. 그럴 때 StampedLock은 tryConvertToWirteLock() 을 딱 그 목적을 위해 제공한다.

```java
ExecutorService executor = Executors.newFixedThreadPool(2);
StampedLock lock = new StampedLock();

executor.submit(() -> {
    long stamp = lock.readLock();
    try {
        if (count == 0) {
            stamp = lock.tryConvertToWriteLock(stamp);
            if (stamp == 0L) {
                System.out.println("Could not convert to write lock");
                stamp = lock.writeLock();
            }
            count = 23;
        }
        System.out.println(count);
    } finally {
        lock.unlock(stamp);
    }
});
stop(executor);
```
위 작업을 보면 첫번째로 읽기-락을 획득하여 count 필트의 값을 출력한다. 그러나 현재 값이 0이라면 그 값을 23으로 바꾸고 싶었기에(그러고 싶었다고 하자), 읽기-락을 쓰기 락으로 곧바로 전환해야 했다. 끊고 새로 쓰기-락을 요청하려면 그 사이에 다른 스레드가 들어와 무슨 일을 저지를지 모를 일이기 때문이다. tryConvertToWriteLock() 메서드를 호출하면 블럭되는 일이란 없다. 단지 당장 쓰기-락을 획득할 수 없는 경우 0 값의 stamp를 리턴하게 된다. 이 경우 어쩔 수 없이 writeLock()을 호출한다. 그리고 사용가능한 쓰기-락이 생길 때까지 블럭된다.

Semaphores

 세마포어는 암세포어다. Concurrency API는 락 이외에도 counting semaphores를 지원한다. 락은 단순히 특정 변수나 자원에 대한 배타적인 접근권을 부여하는 반면에, 세마포어는 허가권들의 집합을 운영하는 형태라 생각할 수 있다. 이 개념은 다음과 같은 시나리오에서 굉장히 유용하다. 한 자원에 대해 제한된 수만의 스레드만 접근하도록 설정해야 하는 경우다.
```java
ExecutorService executor = Executors.newFixedThreadPool(10);

Semaphore semaphore = new Semaphore(5);

Runnable longRunningTask = () -> {
    boolean permit = false;
    try {
        permit = semaphore.tryAcquire(1, TimeUnit.SECONDS);
        if (permit) {
            System.out.println("Semaphore acquired");
            sleep(5);
        } else {
            System.out.println("Could not acquire semaphore");
        }
    } catch (InterruptedException e) {
        throw new IllegalStateException(e);
    } finally {
        if (permit) {
            semaphore.release();
        }
    }
}

IntStream.range(0, 10)
    .forEach(i -> executor.submit(longRunningTask));

stop(executor);
```

 위에서 만든 executor는 잠재적으로 10개의 스레드를 돌릴 수 있다. 그러나 우리는 5 크기의 세마포어만을 사용했다. 따라서 위 코드에는 최대 5개의 스레드만이 동시에 접근할 수 있다. 여기서도 역시 try/finally 블럭을 이용하여 어떤 예외에서도 세마포어를 해제해 줄 수 있도록 설정했다.

결과는 이렇다.
Semaphore acquired
Semaphore acquired
Semaphore acquired
Semaphore acquired
Semaphore acquired
Could not acquire semaphore
Could not acquire semaphore
Could not acquire semaphore
Could not acquire semaphore
Could not acquire semaphore
 tryAcquire()가 실행될 때마다 초기 5개에 대해서는 세마포어를 할당해 접근을 허가하지만 그 이후부터는 얻을때까지 최대 1초까지 기다리라고 인자에 넣은 것처럼 1초 기다리고 리턴하게 된다. 더 이상의 세마포어는 없기 때문이다.
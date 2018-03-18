## Useful Heap 구현
### 제법 쓸만한 힙 구현 : 힙의 변경
앞서 구현한 힙은 우선순위를 알고 그 우선순위를 던져야하기때문에 사용하기에는 조금은 적합한 힙이 아닐 수 있다. 이번에는 그보안점을 개선하는 힘을 만들것이다. 우선순위의 판단 기준을 힙에 설정할 수 있는 힙을만들것이다.

앞서 힙을 구현하기위해 아래와같은 2개의 구조체를 사용했다.
```c
typedef struct _heapElem{
    Priority pr; // 값이 작을수록 높은 우선순위
    HData data;
} HeapElem;

typedef struct _heap{
    int numOfData;
    HeapElem heapArr[HEAP_LEN];
}Heap;
```
우선순위의 판단 기준을 힙에 설정할 수 있어야 한다는 요구사항을 만족하기위해서 이둘은 다음과 같이 하나의 구조체로 대신하고자한다.

```c
typedef struct _heap{
    PriorityComp * comp;
    int numOfData;
    HData heapArr[HEAP_LEN]
}Heap;
```
가장 큰변화는 heapElem 을 제거했다. heapElem는 우선순위를 저장하기위한 구조체였는데 그것을 없애고 PriorityComp * comp가 추가 되었다. 이는 함수 포인터다. 두 개의 데이터를 대상으로 우선순우의 높고 낮음을 판단하는 함수를 등록하기 위한 포인터 변수이다. 물론 이함수 작성은 프로그래머가 직접 정의해야한다.
다음 PriorityComp * comp 을 작성하기 위한 가이드라인
- PriorityComp의 typedef 선언은 다음과 같다.(typedef int PriorityComp(HData d1, HData d2));
- 첫 번째 인자의 우선순위가 높다면, 0 보다 큰 값이 반환되도록 정의한다.
- 두 번째 인자의 우선순위가 높다면 0보다 작은값이 반환도되록 정의한다
- 첫 번째, 두 번째 인자의 우선순위가 동일하면 0을 반환한다.

위의 typedef 선언은 함수의 반환형과 매개 변수의 선언을 어떻게 해야 하는지 알려준다. 먼저 데이터간 우선순위의 비교에 사용될 함수를 정의해서 힙에 등록해야한다. 따라서 힙의 초기화 함수는 다음과 같이 수정한다.

```c
void HeapInit(Heap * ph, PriorityComp pc){
  ph -> numOfData = 0;
  ph->comp = pc; // 우선순위에 비교에 사용되는 함수
}

```
기존에 Hinsert함수도 우선순위 정보를 직접 전달하지 않기 때문에 이함수의 원형도 다음과 같이 변경되어야한다
```c
void HInsert(Heap * ph, HData data);
```

#### 제법 쓸만한 수준의 힙 구현
힙을 완성하였으니 이를 기반으로 우선순위 큐를 구현할 차례이다. 그런데 우리가 힙을 구현한 이유는 앞서 운선순위 큐를 구현하기위해 힙을 구현한 것이였다. 실제로 힙의 HInsert,HDelete 함수의 호출결과로는 우선순위 큐의 enqueque,dequeue연산결고와 일치한다. 먼저 우선순위 큐 자료구조를 정의하겠다.

void PQueueInit(PQueue * ppq, PriorityComp pc);
- 우선순위 큐의 초기화를 진행한다.
- 우선순위 큐 생성후 제일먼저 호출되어야하는 함수이다.

int PQIsEmpty(PQueue ppq);
- 우선순위 큐가 빈 경우 TRUE(1)을, 그렇지 않은 경우 FALSE(0)을 반환한다.

void PEnqueue(PQueue * ppq, PQData data);
- 우선순위 큐에 데이터를 저장한다. 매개변수 data로 전달된 값을 저장한다

PQData PDequeue(PQueue * ppq);
- 우선순위 가장 높은 데이터를 삭제한다.
- 삭제된 데이터는 반환된다.
- 본 함수의 호출을 위해서는  데이터가하나 이상 존재함이 보장되어야한다.




## 등차 수열
앞의 항에 항상 일정한 수를 더하여 만들어가는 수열이다. 각 항에 더해지는 일정한 수를 '공차'라 한다. 첫째항이 a, 공차가 d인 등차수열은 다음과 같이 전개된다. 더해진 공차의 개수는 수열의 항수보다 하나씩 작으므로, 등차수열의 일반항은 an=a+(n-1)d가 된다.
![](https://i.imgur.com/HyuuPFu.jpg)
다음 수열은 첫째항이 1, 공차가 1인 등차수열이다. 일반항은 an=1+(n-1)=n이다.


 http://terms.naver.com/entry.nhn?docId=1085836&cid=40942&categoryId=32209

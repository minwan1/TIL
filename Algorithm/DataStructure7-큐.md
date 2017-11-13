## 큐의 이해와 ADT 정의
스택은 먼저 들어간 데이터가 나중에 나오는 구조인 반면, 큐는 먼저들어간 데이터가 먼저나오는 구조이다 큐는 유명한 FIFO(FIRST IN FIRST OUT)의 자료구조이다.

### 큐의 ADT 정의
enqueue 큐에 데이터를 넣는 연산
dequeue 큐에서 데이터를 꺼내는 연산


### 큐 자료구조의 ADT
void QueueInit(Queue * pq);
- 큐의 초기화를 진행한다.
- 큐 생성후 제일 먼저 호출되어야 하는 함수이다

int QIsEmpty(Queue * pq);
- 큐가 빈 경우 TRUE(1)을, 그렇지 않은 경우 FALSE(0)을 반환한다.

void Enqueque(Queue * pq, Data data);
- 큐에 데이터를 저장한다. 매개변수 data로 전달된 값을 저장한다.

Data Dequeque(Queue * pq);
- 저장순서가 가장 앞선 데이터를 삭제한다
- 삭제된 데이터는 반환된다.
- 본 함수의 호출을 위해서는 데이터가 하나 이상 존재함이 보장되어야한다.

Data QPeek(Queue * pq);
- 저장순서가 가장 앞선 데이터를 반환하되 삭제 하지 않는다.
- 본 함수의 호출을 위해서는 데이터가 하나 이상 존재함이 보장되어야한다.


### 큐의 배열 기반 구현
![](https://i.imgur.com/vj955o5.png)
Q의 머리는 F를 의미하고 꼬리는 R을 의미한다. 따라서 dequeue연산시 F를 삭제하고 R을 중심으로 enqueue연산을 한다.
첫번째 그림은 dequeue연산시 반환할 데이터를 배열의 맨앞부분에 위치키는 방식으로 가장 보편적으로 인식하는 배열의 삭제 방법을 적용한것이다. 사실 이방법은 dequeue 연산의 대상이 맨 앞부분에 위치하므로, 그림에서 보인 것과 달리 F가 불필요하다. 이 방식은 dequeue 연산 시마다 저장된 데이터를 한 칸씩 이동시켜야 하는 단점이 있다.

위그림에서 두번 째 그림에서 dequeque 연산 시 F를 이동시키고 있다. 이방식을 취하게되면 dequeue의 과정에서 데이터 이동은 필요치 않다. 그저 F가 가리키는 위치만 오른쪽으로 옮기면 된다. 하지만 이방에도 이슈가 있다.

![](https://i.imgur.com/NkTJnd0.png)
만약 위의 그림처럼 문자 D가 배열의 끝에 저장된 상황이다라고 가장하자. 때문에 더이상 R을 오른쪽으로 옮길 수 없다. 그런데 dequeue연산도 몇 차례 진행이 되어 배열의 앞부분이 비어있다. 이럴 경우 R을 회전시켜한다. R이 배열의 끝에 도달하면, 다시 맨앞으로 이동시켜서 R이 회전하게 만드는 것이다. 물론 R을 뒤 쫓아 가는 F도 배열의 끝에 도달하면 회전해야한다. 이러한 방식으로 동작하는 배열 기반의 큐를 가리켜 원형 큐라 한다.

### 원형 큐(Circular Queue)의 소개
![](https://i.imgur.com/oea3qeI.png)
R과 F를 회전시킨다는것은 배열을 다음의 형태로 바라본다는 뜻이 된다. 큐에서 첫번째 데이터가 추가되는 순간 F와 R이 동시에 그 데이터를 가리킨다. 그 녀석이 큐의 머리이자 꼬리이다. R이 끝에 도달으면 R~F를 처첨으 배열로 옮긴다.

위의 그림은 아래의 enqueue를 거쳐서 위의 그림의 결과값에 도다른다.
![](https://i.imgur.com/uyvRwOO.png)
그리고 위그림을 대상으로 2회의 dequeue연산을 진행하면, 다음에 상태에 이르게 된다.
![](https://i.imgur.com/J6BrXB7.png)
아래의 그림은 Q에 내용이 완전히 찼을때와 비어있을때의 그림이다.
![](https://i.imgur.com/M7YvOTa.png)
![](https://i.imgur.com/RgWoJ5Q.png)
위그림을 보면 알듯이 F와 R의 위치만 가지고는 꽉 찬 경우와 텅빈경우를 구분할 수가 없다. 이러한 문제를 해결하는 방법은 배열의 길이를 N-1로 채워졌을 때, 이를 꽉 찬것으로 간주한다. 이렇게하면 저장 공간 하나를 낭비하게 된다. 하지만 이로 인해 문제 하나가 해결이 되는 셈이니 잃는 것보다 얻는것이 더많다.

### 공간 하나를 비운 원형 큐를 구현하자
![](https://i.imgur.com/Wsqbt3N.png)
1. 먼저 원형에서 F,R이 같은곳을 가리키면 그곳은 텅빈 상황을 가리킨다. 왜냐하면 공간 하나를 비우기로 했으니 F,R이 같은 위치에 있다는것은 텅빈 상황을 가리킨다.
2. enqueue 연산 시, R이 가리키는 위치를 한칸 이동시킨 다음에, R이 가리키는 위치에 데이터를 저장
3. dequeque 연산 시, F가 가리키는 위치를 한칸이동시키고 다음에 F가 가리키는 위치에 데이터를 반환 및 소멸한다.

* 원형큐가 텅빈상태 : F와 R이 동일한 위치를 가리킨다.
* 원형 큐가 꽉찬 상태 : R이 가리키는 위치의 앞을 F가 가리킨다.

원형 큐의 핵심 소스 원형으로 이어주는 부분
```c
int NextPosIdx(int pos){
    if(pos == QUE_LEN-1){ //배열의 마지막 요소의 인덱스 값이라면
        return 0; // 0을 돌려주게되면 0 부터 이제 다시시작하게되는것이다
    }else{
        return pos+1; // 아니라면 넣어준 위치에 + 1을 해서 준다.
    }
}
```
큐가 꽉찼다는것은 꼬리 다음에 머리가 다찼을시에 꽉찬것이다.
```C
void Enqueue(Queue * pq, Data data){
    if(NextPosIdx(pq->rear) == pq->front){ // 큐가 꽉 찼다면, 꼬리다음에 머리가있으면 꽉찬것이다.
        printf("Queue Memory Error!");
        exit(-1);
    }
    pq->rear = NextPosIdx(pq->rear);
    pq->queArr[pq->rear] = data;
}
```

[배열기반의 원형 큐 소스](https://github.com/minwan1/Algorithm/tree/master/CircularQueue/CircularQueue)

## 큐의 연결리스트 기반 구현
스택과 큐의 유일한 차이는 앞에서 꺼내느냐 뒤에서 꺼내느냐에 따라 달라질것같다는 말은 연결리스트 기반의 큐라면 위의 판단이 어느정도 옮다, 하지만 연결리스트 기반의 큐에서도 여전한 차이점은 push와 pop이 이뤄지는 위치가 같ㅇㄴ 반면, 큐는 enqueue와 dequeue가 이뤄지는 위치가 다르다.

### 연결리스트 기반 큐의 구현
F->null
R->null

처음 생성을하게되면 F,R은 null로 초기화해준다.

```c
void Enqueue(Queue * pq, Data data){
    Node * newNode = (Node *)malloc(sizeof(Node));
    newNode->next = NULL;
    newNode->data = data;

    if(QIsEmpty(pq)){
        pq->front = newNode;
        pq->rear = newNode;
    }else{
        pq->rear->next = newNode; //마지막노드 다음 노드에 새로운 노드삽입
        pq->rear = newNode; // 꼬리에 삽임
    }

}
```
위에처럼 새로운 노드가 추가시 R도 새로운노드를 봐야하고 기존의 꼬리노드도 새로운 꼬리노드를 바라보게해야한다.
```c
Data Dequeue(Queue * pq){
    Node * delNode;
    Data retData;

    if(QIsEmpty(pq)){
        printf("Queue Memory Error");
        exit(-1);
    }

    delNode = pq->front; // 삭제할노드 주소저장
    retData = delNode->data; //리턴할 데이터 저장
    pq->front = pq->front->next; // 다음 노드로 이동 후 데이터삭제

    free(delNode);
    return retData;
}
```

## 덱의 이해와 구현
큐 : 뒤로 넣고 앞으로 빼는 자료구조
덱 : 앞으로도 뒤로도 넣을수 있고 앞으로도 뒤로도 뱰 수 있는 자료구조

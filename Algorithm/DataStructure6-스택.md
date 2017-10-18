## 스택 ADT 정의
스택은 크게 꺼내고, 넣고, 들여다보는 push, pop, peak가 존재한다. 스택 같은경우에는 프로그램에서 사용되는 예는 프로그램에서 인터럽트가 났을때 계속 계속해서 최종 인터럽트가 발생한곳을 찾아 들어가야하기 때문에 돌아올 주소를 저장할 공간으로 스택을 사용한다. 먼저 스택에 대한 ADT를 정의하자면 아래와같은 메소드들이 필요한다.
```c
void StackInit(Stack * pstack);
- 스택의 초기화를 진행한다.
- 스택 생성 후 제일 먼저 호출되어야 하는 함수이다.
int SisEmpty(Stack * pstack);
- 스택이 빈 경우TRUE(1)을 그렇지 않은 경우 FALSE(0)을 반환한다.
void Spush(Stack * pstack, Data);
- 스택에 데이터를 저장한다. 매개변수 data로 전달된 값을 저장한다.
Data SPop(Stack * pstack);
- 마지막에 저장된 요소를 반환하되 삭제하지 않는다.
- 본 함수의 호출을 위해서는 데이터가 하나 이상 존재함이 보장되어야 한다.
```

먼저 스택을 구현하기위해서는 배열기반의 방법과 연결리스트 기반의 구현방법이 존재한다. 먼저 배열기반의 구현방법으로 스택을 구현할 것 이다.

## 스택의 배열 기반 구현
* 먼저 배열의 인덱스 0 이 스택에 바닥이다
* 마지막에 저장된 데이터의 위치를 기억해야한다.

#### push
top을 위로 한칸올리고, top이 가리키는 위치에 데이터장
#### pop
top이 가리키는 데이터를 반환하고, top아래로 한카 내림

구조체 정의는 topIndex의 현재 배열의 index를 확인하는 변수 배열의 구조체를 정의해주면 된다.
```c
typedef struct _arrayStack
{
    Data stackArr[STACK_LEN];
    int topIndex;
} ArrayStack;
```
스택은 쉽다보니 밑에 주석으로 간단하게 함수들의 기능을 설명하겠다.
```c
void StackInit(Stack * pstack){ // stack 초기화 0 값을 넣을떄마다 1씩 증가하기때문에 -1로시작
    pstack -> topIndex = -1;
}


int SIsEmpty(Stack * pstack){ // 배열이 비어있는지 확인해주는 함수
    if(pstack->topIndex == -1){
        return TRUE;
    }else{
        return FALSE;
    }
}

void SPush(Stack * pstack, Data data){ // 데이터를 삽입하는 함수
    pstack -> topIndex += 1;
    pstack -> stackArr[pstack -> topIndex] = data;
}

Data SPop(Stack * pstack){ // 데이터를 빼는함수
    int rIdx;

    if(SIsEmpty(pstack)){
        printf("Stack Memory Error!");
        exit(-1);
    }

    rIdx = pstack -> topIndex;
    pstack->topIndex -=1;

    return pstack->stackArr[rIdx];
}


Data SPeek(Stack * pstack){ // 현제 스택에 인덱스값을 확인해주는 함수
    if(SIsEmpty(pstack)){
        printf("Stack Memory Error!");
        exit(-1);
    }

    return pstack -> stackArr[pstack -> topIndex];
}
```
[깃허브 소스](https://github.com/minwan1/Algorithm/tree/master/ArrayBaseStack)
## 스택의 연결리스트 기반 구현
푸쉬의 구현은 함수의 주석들만 봐도 이해가 될 수 있다. 너무 간단하다.
```c
void StackInit(Stack * pstack){
    pstack -> head = NULL;  //포인터 변수 head는 NULL로 초기화한다.
}

int SIsEmpty(Stack * pstack){
    if(pstack -> head == NULL) //스택이 비어있으면 head는 NULL이다.
        return TRUE;
    else //아니면 FALSE리턴
        return FALSE;
}
void SPush(Stack * pstack,Data data){
    Node * newNode = (Node*) malloc(sizeof(Node)); // 새노드 생성

    newNode -> data = data; // 새노드에 데이터저장
    newNode -> next = pstack -> head; //새로운 노드가 기존 노드를 가리킨다

    pstack -> head = newNode; // 포인터 변수가 head가 새노드를 가키게된다.
}

Data SPop(Stack * pstack){

    Data rdata;
    Node * rnode;

    if(SIsEmpty(pstack)){
        printf("Stack Memory Error");
        exit(-1);
    }

    rdata = pstack->head->data; // 삭제할 데이터를 임시로 저장
    rnode = pstack->head; // 삭제할 노드의 주소값을 임시로 저장

    pstack->head = pstack->head->next; // 삭제할노드 다음 노드를 head가 가리킴
    free(rnode); // 노드 삭제
    return rdata; // 데이터 반환

}

Data SPeek(Stack * pstack){ // 데이터 조회
    if(SIsEmpty(pstack)){
        printf("Stack Memory Error!");
        exit(-1);
    }
    return pstack->head->data;
}
```
[깃허브 소스](https://github.com/minwan1/Algorithm/tree/master/ListBaseStack)

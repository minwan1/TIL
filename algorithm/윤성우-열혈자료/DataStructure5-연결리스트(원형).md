## 원형 연결 리스트

![](https://i.imgur.com/zsn5CtN.png)
단순 연결리스트의 꼬리(테일)가 머리(헤드)를 가리키게되면 원형 연결리스트가 된다.

꼬리를 가리키는 포인터 변수는 ***tail*** 이다
머리를 가리키는 포인터 변수는 ***tail->next*** 이다


그전장에서 구현한 단순연결리스트를 원형리스트로 수정하기위해서는 아래와같이 수정이 필요하다.
* 구현목록
* 조회관련 LFirst(수정필요)[순환하는형태로 변경해야함]
* 삭제관련 LNext
* 삭제관련 LRemove
* 삽입관련 (수정필요) [앞과 뒤에 삽입이 가능하도록 두 개의 함수 정의]
* 정련관련 (이부분은 원형리스트에서는 제거하고구현함.)
* 이외의 부분

구조체 정의 및 함수정의 부분이다
```c
typedef int Data

typedef struct _node{
  Data data;
  struct _node * next;
}Node;

typedef struct _CLL{
  Node * tail;  // 원형리스트를 구현하기떄문에 이름을 head 대신에 tail로 선언
  Node * cur;
  Node * before;
  int numOfData;
}CList;

typedef CList List;

void ListInit(List * plist); // list처음에 초기화
void LInsert(List * plist, Data data); // list에 값 입력 (노드를 꼬리에 추가함 기존방식)
void LInsertFront(List * plist, Data data); //list에 (노드를 머리에 추가함 원형리스트방식)
int LFirst(List * plist, Data * pdata); // 리스트 첫번째 데이터 출력
int LNext(List * plist, Data * pdata);  // 리스트
Data LRemove(List * plist); // list에서 입력되어진 벨류값 제거
int LCount(List * plist); //벨류 데이터 총개수

```

원형리스트의 함수정의부분이다.
```c
void ListInit(List * plist){ //리스트를 선언하고 삽입하면 리스트 초기화
  plist -> tail = NULL;
  plist -> cur = NULL;
  plist -> before = NULL;
  plist -> numOfData = 0;
}

// LInsert & LInsertFront의 공통 부분
void LInser~(List * plist, Data data){
  Node * newNode = (Node*)malloc(sizeof(Node));
  newNode -> data = data;

  if(plist->tail == null){
    plist -> tail = newNode;//첫번째 데이터가 삽입될때 테일이 테일을 바로보게해주는부분
    newNode -> data = data;

  }else{
    newNode -> next = plist -> tail -> next;//새로운노드가 기존에 데이터 앞에 넣게해주는부분 밑에 그림처럼 7이 4를 바로볼 수 있게하는부분
    plist -> tail -> next = newNode; // 꼬리가 새로 삽입된 노드를 헤드로 바라보게 해주는부분
    //plist -> tail = newNode;  이부분을 넣으면 꼬리로 넣게해주는부분
  }

}

```
밑에그림은 테일로 계속계속해서 테일로 다음 데이터를 바라보고있는것을 보여주는것이다.
![](https://i.imgur.com/NAWb6TR.png)


첫번째 데이터를 조회할때 before에 tail을 넣고 cur에 head를 넣으면된다.
```c
int LFirst(List * plist, Data pdata){
  if(plist->tail == NULL)
    return FALSE;

  plist -> before = plist -> tail; // 첫번째 전에는 taill이니까 tail을 바라본다.
  plist -> cur = plist -> tail -> next; // 첫번재 조회니까 테일다음 조회

  *pdata = plist->tail->next;

  return TRUE;  
}

```

원형 연결리스트에서 2번째 노드부터 조회할 때 cur 현재 위치를 before로 넘기고 현재 위치를 plist -> cur -> next;로 위치를 받으면 현재위치가 되고 그 데이터를 조회하면 현재 위치의 데이터가된다.
```c
int LNext(List * plist, Data * pdata){
  if(plist -> tail == NULL){
    return FALSE;
  }

  plist -> before = plist -> cur; // 현재 위치가 before로 변경됨
  plist -> cur = plist -> cur -> next; // 현재위치의 다음위치가 다음위치가 됨

  *pdata = plist -> cur -> data;  // 현재위치의 다음위치가 현재위치니까 그데이터를 삽입
  return TRUE;
}
```
그다음부터는 연결리스트랑 똑같은 함수를 사용해도된다.

원형 리스트의 경우 노드를 삭제 할 노드를 tail을 가리키거나 마지막남은 노드일경우가 있다. 이럴 경우를 위해서 아래와같이 예외처리를 해줘야한다.
```c
LData LRemove(List * plist){
  Node * rpos = plist->cur;
  LData rdata = rpos -> data;

  if(rpos == plist -> tail){ // 삭제할 노드를 tail이 가리킨다면
    if(plist->tail == plist->tail->next){// 그리고 마지막 노드라면
      plist->tail = NULL;
    }else{ //마지막 노드가 아니라면 마지막노드를 삭제하고 삭제한노드 뒤에 주소를 tail에 넣는다.
      plist -> tail = plist -> before;
    }

  }


  plist->before->next = plist->cur->next;
  plist->cur = plist->before;

  free(rpos);
  (plist->numOfData)--;
  return rdata;

}
```

## 양방향 연결리스트
노드를 양방향으로 연결시켜준다.
![](https://i.imgur.com/KRbDqOB.png)


```c
typedef struct _node{
  Data data;
  struct _node * next;
  struct _node * prev;
}
```
위에 노드에서 next,prev가 추가됬기때문에 그전소스에서 포인터before변수를 지웠다.
```c
int LNext(List * plist, Data * pdata){
  if(plist->cur->next == NULL){
    return FALSE;
  }

  plist->cur = plist->cur->next;
  *pdata = plist->cur->data;

  return TRUE;
}

```

양방향연결리스트 2 볼차례

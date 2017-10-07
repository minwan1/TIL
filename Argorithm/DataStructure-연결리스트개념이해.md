## Abstract Data Type(추상 자료형 ADT의 이해)
```c
int c = 5;
int *a = 5;
print("%d",*a); //5가 출력됨.
```

구체적인 기능의 완성과정을 언급하지 않고, 순수하게 기능이 무엇인지를 나열한것

예)지갑
- 카드사입
- 카드의 추출
- 동전의 삽입
- 동전의 추출
- 지폐의 삽입
- 지폐의 추출


## 순차(배열) 리스트의 이해
순차리스트 - 배열을 기반으로 구현된 리스트
연결 리스트 - 메모리의 동적 할당을 기반으로 구현된 리스트

```c
typedef struct __ArrayList{
  int arr[100]; //배열은 선언할떄 숫자를 제한해야함.
  int numOfData;
  int curPosition;
}ArrayList;

typedef ArrayList list;



void void ListInit(List * plist);
void LInsert(List * plist, LData data);
int LFirst(List * plist, LData * pdata);
int LNext(List * plist,LData * pdata);
LData Lremove(List * plist);
int LCount(List * plist);
```

연결리스트 참조일련의 과정
LFirst -> LNext -> LNext



## malloc , free함수용도
프로그램을 실행시키면 운영체제는 우리가 실행시킨 프로그램을 위해 메모리 공간을 할당해준다.
스택(Stack), 힙(Heap), 데이터(Data),
데이터 : 전역변수가 할당됨
힙영역 : 동적메모리가 할당되는영역
스택 : 지역변수들이 할당된다.


## 연결리스트 개념이해
```c
typedef struct _node{  //typedef를 붙이면 구조체 선언할때 typedef를 안붙여도됨.
  int data;
  struct _node * next; //node를 선언한것을 바로 사용가능
}Node;

Node * head = NULL; //헤더
Node * tail = NULL; //꼬리
Node * cur = NULL; //현재위치

Node * newNode = NULL;
int readData;

while(1){
  printf("자연수입력:");
  scanf("%d", &readData);
  if(readData < 1)
    break;

  newNode = (Node*)malloc(sizeof(Node)); //사이즈만큼 메모리에할당후 시작 주소값 리턴
  newNode->data = readData;
  newNode->next = NULL;

  if(head == NULL){
    head = newNode;
  }else{
    tail->next = newNode;
  }

  tail = newNode;
}

```
조회
```c

if(head == null){
  print("값이 존재하지않음")
}else{
  cur = head;
  while(cur - > next == null){
    cur = cur -> next;
    print("%d", cur->data);
  }
}

```

노드 전체삭제
```
if(head == null){
  print("삭제할 값이 존재하지않음");
}else{
  Node * delNode;
  Node * delNextNode;
  delNode = head;
  delNextNode = delNode -> next;
  printf("%d데이터 삭제",delNode;
  free(delNode);
  while(delNextNode->next == null){
    delNode = delNextNode;
    delNextNode = delNode -> next;
    printf("%d데이터 삭제",delNode -> data);
    free(delNode);
  }

}
```


참고
- [기초 탄탄 영상털이's Stroy](http://dsnight.tistory.com/50)

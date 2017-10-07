## 단순연결리스트의 ADT구현
void ListInit(List * plist);
- 초기화할 리스트의 주소값을 인자로 전달한다.
- 리스트의 생성 후 제일먼저 호출되어야하는 함수이다.

void LInsert(List * plist, LData data);
- 리스트에 데이터를 저장한다. 매개변수 data에 전달된 값을 저장한다.

int LFirst(List * plist, LData * pdata);
- 첫 번째 데이터가 pdata가 가리키는 메모리에 저장된다.
- 데이터의 참조를 위한 초기화가 진행된다.
- 참조 성공 시 TRUE(1), 실패 시 FALSE(O) 반환

int LNext(List * plist,LData * pdata);
- 참조된 데이터의 다음 데이터가 pdata가 가르키는 메모리에 저장된다.
- 순차적인 참조를 위해서반복 호출이 가능하다.
- 참조를 새로 시작하려면 먼저 LFirst 함수를 호출해야한다.
- 참조 성공시 TRUE(1), 실패 시 FALSE(0)반환

LData Lremove(List * plist);
- LFirst 또는 LNext함수의 마지막 반환 데이터를 삭제한다.
- 삭제된 데이터는 반환된다.
- 마지막 반환 데이터를 삭제하므로 연이은 반복 호출을 허용하지 않는다.

int LCount(List * plist);
- 리스트에 저장되어 있는 데이터의 수를 반환한다.

void SetSortRule(List * plist,int(* comp)(LData d1,LData d2))
- 반환형이 int이고, 2개의 인자를 받고 함수의 주소값을 전달

### 함수 포인터란
```c

#include <stdio.h>

// 함수포인터 타입 정의
typedef int (* calcFuncPtr)(int, int);


// 덧셈 함수
int plus (int first, int second)
{
    return first + second;
}
// 뺄셈 함수
int minus (int first, int second)
{
    return first - second;
}
// 곱셈 함수
int multiple (int first, int second)
{
    return first * second;
}
// 나눗셈 함수
int division (int first, int second)
{
    return first / second;
}

// 매개변수로 함수포인터를 갖는 calculator 함수
int calculator (int first, int second, calcFuncPtr func)
{
    return func (first, second);     // 함수포인터의 사용
}

int main(int argc, char** argv)
{
    calcFuncPtr calc = NULL;
    int a = 0, b = 0;
    char op = 0;
    int result = 0;

    scanf ("%d %c %d", &a, &op, &b);

    switch (op)    // 함수포인터 calc에 op에 맞는 함수들의 주소를 담음
    {
        case '+' :
            calc = plus;
            break;

        case '-':
            calc = minus;
            break;

        case '* ':
            calc = multiple;
            break;

        case '/':
            calc = division;
            break;
    }

    result = calculator (a, b, calc);

    printf ("result : %d", result);

    return 0;
}

```
연결리스트의 이해에서 사용한 리스트를 업그레이드하여 링크드 리스트 제작
```
typedef struct _node{  //typedef를 붙이면 구조체 선언할때 typedef를 안붙여도됨.
  int data;
  struct _node * next; //node를 선언한것을 바로 사용가능
}Node;

Node * head = NULL; //헤더
Node * tail = NULL; //꼬리
Node * cur = NULL; //현재위치

Node * newNode = NULL;
int readData;

head = (Node*)malloc(sizof(Node)); //추가된 내용
tail = head;

while(1){
  printf("자연수입력:");
  scanf("%d", &readData);
  if(readData < 1)
    break;

  newNode = (Node *)malloc(sizeof(Node)); //사이즈만큼 메모리에할당후 시작 주소값 리턴
  newNode->data = readData;
  newNode->next = NULL;

  //if(head == NULL){
  //  head = newNode;
  //}else{
  //  tail->next = newNode;
  //}

  tail->next = newNode;
  tail = newNode;

}
```

정렬 기능 추가된 연결리스트의 구조체
```c
typedef struct _linkedList{
  Node * head;
  Node * cur;
  Node * before;
  int numOfData;
  int (*comp)(LData d1,LData d2);
}
```
연결리스트의 핵심.
```c
void FInsert(List * plist, LData data)
{
    Node * newNode = (Node*)malloc(sizeof(Node));
    newNode->data = data;

    newNode->next = plist->head->next;
    plist->head->next = newNode;

    (plist->numOfData)++;
}

```
![](https://i.imgur.com/DIGU2PC.png)

newNode->next = plist->head->next; 노드를 계속계속해서 연결시키는부분


연결리스트의 정렬과 삽입 구현2 볼차례

참고
[http://norux.me/8](http://norux.me/8)
윤성우의 열혈자료구조

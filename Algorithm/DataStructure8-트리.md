## 트리의 개요
트리는 계층적 관계를 표현하는 자료구조이다. 자료구조하면 보통은 무엇인가를 저장하고 꺼내는 것이 전부인 것으로 이해하는 경우가 많다. 앞서 보인 선형 자료구조들은 이에 초점이 맞춰져 있었으니 무리도 아니다. 하지만 자료구조는 근본적으로 무엇인가를 표현하는 도구이다. 이번에 보개될 트리는 데이터의 저장과 삭제가 아닌 표현에 초점이 맞춰져 있다.
![](https://i.imgur.com/PaO7dcR.png)

#### 트리관련 용어의 소개
![](https://i.imgur.com/UOrxWEE.png)

```
- 노드(Node)
트리 구성요소에 해당하는 A,B,C,D,E,F와 같은 요소
- 간선(edge)
노드와 노드를 연결하는 연결선
-루트 노드(root node)
트리 구조에서 최상위에 존재하는 A와 같은 노드
-단말 노드(terminal node)
아래로 또 다른 노드가 연결되어 있지 않은 E,F,C,D같은 노드
-내부 노드(internal node)
단말 노드를 제외한 모든 노드로 A,B와 같은 노드
````
* A노드는 B,C,D의 부모노드이다
* B,C,D는 노드 A의 자식 노드이다
* B,C,D는 부모 노드가 같으므로, 서로가 서로에게 형제노드라고한다.
노드 B는 노드 A의 자식노드이지만, 동시에 노드 E,F의 부모 노드도 된다. 그리고 특정 노드의 위에 위치한 모든 노드를 가리켜 조상노드라한다. 특정 노드의 아래에 위치한 모든 노드를 가리켜 후손 노드라고한다. 즉 노드 A와 B는 노드 E의 조상 노드이다. 반면 노드 B,C,D,E,F는 노드 A의 후손 노드이다,

#### 이진트리와 서브 트리
![](https://i.imgur.com/rgzWglz.png)
큰 트리에 속하는 작은 트리를 가리켜 서브트리라 한다.
![](https://i.imgur.com/zhHqpoN.png)
위에 그림도 엄밀히 따지면 D,E,F,G도 이진트리가 아니라고 생각할 수 있다. 하지만 D,E,F,G에서 그아래에 공백노드라는것이 존재하기떄문에 이진트리가 될 수 있다. 이진 트리에 관한 정의중에 노드가 위치할 수 있는 곳에 노드가 존재하지않는다면 공집합 노드라는것이 존재한다고 간다한다.
![](https://i.imgur.com/G0y27uv.png)

그래서 위와같은 그림들도 이진트리가 될 수 있다. (단 3개이상의 자식노드를 가지게되면 이진트리가 아니다.)
다음은 완전이진트리와 높이,레벨에 대한 설명의 그림이다.
![](https://i.imgur.com/hR7q0HI.png)
모든 레벨에 노드가 꽉찬 노드를 포화이진트리라고하고, 포화 이진 트리를 완전 이진 트리라 할 수 있다. 하지만 완전 이진트리를 포화이진트리라 단정 지을 수 는 없다.


## 이진트리 구현
이진트리는 재귀적인 특성을 가지고 구현 하게 된다. 이진트리에 구현방법에는 배열기반의 트리와 연결리스트 기반의 트리가 있다. 그러나 트리를 표현하기에는 연결리스트가 더 유연하기 때문에 우리가 구현할 대부분의 트리는 연결리스트 기반으로 구현할 것이다. 하지만 트리가 완성 된 이후부터는 그트리를 대상으로 매우 빈번한 탐색이 이루어지는 완전한 이진트리 라면 배열로 구현하는것을 고려해볼만 하다. 배열은 분명 연결리스트에 비해서 탐색이 빠르고 매우 용이하고 또 빠르기 때문이다.
![](https://i.imgur.com/BF46jX3.png)
연결리스트 기반은 그림같은경우에는 구현 방식이 바로 이해되었을것이다. 이렇게 이해가 쉬운것은 연결리스트를 기반으로 트리를 구현할 경우 연결리스트의 구성형태가 트리와 일치하기때문이다. 그렇다고해서 배열 기반의 트리들이 의미가 없다는것은 아니다. 완전이진트리의 힙이라는 자료구조는 배열을 기반으로 구현한다.힙이 요구하는 바를 만족시키기위해서는 배열이 훨씬 용이하다.

```c
typedef struct _bTreeNode{
    BTData data;
    struct _bTreeNode * left;
    struct _bTreeNode * right;
} BTreeNode;
```

스택이나 큐등등과같이 이진트리는 따로 이진트리의 구조체를 정의해줄 필요가 없다. 노드가 위치할 수 있는 곳에 노드가 존재하지 않는다면, 공집합 노드가 존재하는것으로 간주한다. 따라서 자식 노드가 하나도없는 노드는 그자체로도 이진트리이다. 두개의 공집합 노드를 두고 있기 떄문이다.

```c
BTreeNode * MakeBTreeNode(void); // 노드 생성
BTData GetData(BTreeNode * bt); // 노드에 저장된 데이터를 반환
void SetData(BTreeNode * bt, BTData data); // 노드에 데이터를 저장
```
MakeBTreeNode 이함수가 호출되면 노드를 동적 할당및 초기화하여 그주소값을 반환한다. 그럼 아래의 모양으로 노드를 초기화한다.
null-???(data)-null
위에서 ???는 쓰레기값을 의미한다. 즉 데이터가 저장되는 멤버 data를 대상으로는 별도의 초기화를 진행하지 않는다.

BTreeNode * GetLeftSubTree(BTreeNode * bt);
BTreeNode * GetRightSubTree(BTreeNode * bt);
위쪽 두함수는 각각 인자로 절달된 이진 트리의 왼쪽 서브트리, 그리고 오른쪽 서브트리의 루트노드의 주소값을 반환하는 함수들이다.

void MakeLeftSubTree(BTreeNode * main, BTreeNode * sub);
void MakeRightSubTree(BTreeNode * main, BTreeNode * sub);
위의 두함수는 서브트리의 연결을 담당한다. 첫 번째 함수는 매개변수 sub로 전달된 트리또는 노드를, 매개변수 main으로 전달된 노드의 왼쪽 서브 트리로 연결한다.

간단하게 makeSubTree들을 이용해서 노드를 연결 해보겠다

```c
BTreeNode * bt1 = MakeBTreeNode(); // 노드 bt1 생성
BTreeNode * bt2 = MakeBTreeNode(); // 노드 bt2 생성
BTreeNode * bt3 = MakeBTreeNode(); // 노드 bt3 생성

MakeLeftSubTree(bt1, bt2); // 노드 A의 왼쪽 자식 노드로 노드 B연결
MakeRightSubTree(bt1, bt3); // 노드 A의 오른쪽 자식 노드로 노드 C연결

  A
B  C   << 아래와같은형태의 노드 생성
```
[이진트리 예제소스](https://github.com/minwan1/Algorithm/tree/master/BinaryTree)

## 이진 트리의 순회(Traversal)
### 순회의 세가지 방법
1. 전위 순회(루트 노드를 먼저!)
2. 중위 순회(루트노드를 중간에!)
3. 후위 순회(루트 노드를 마지막에!)

이진트리를 순회하는 대표적인 방법은 다음 내용을 기준으로 세가지를 나뉜다.
![](https://i.imgur.com/10OLUka.png)

이렇듯 순회 방법은 간단하다. 하지만 높이가 2 이상인 트리구조는 재귀적인 구현이 필요하다.
![](https://i.imgur.com/YB2bDBT.png)
위 그림의 이진 트리를 대상으로 중위 순회를 진행 한 이진트리이고 순회의 순서는 다음과 같다.
1. 왼쪽 서브 트리의 순회
2. 루트 노드의 방문
3. 오른쪽 서브 트리의 순회

아래 소스는 완전하지 않지만 이진트리를 순회하는 함수이다.
```c
void InorderTraverse(BTreeNode * bt){ // 이진 트리 전체를 중위 순회하는 함수
  InorderTraverse(bt->left);  //1 단계 왼쪽 서브 트리의 순회
  printf("%d \n", bt->data);  //2 단계 루트 노드의 방문
  InorderTraverse(bt->right); //3 단계 오른쪽 서브 트리의 순회
}

```
하지만 위함수는 재귀의 탈출 조건이 정의되어 있지 않다, 노드의 방문이 그저 데이터의 출력이다. 노드에 저장된 데이터의 출력으로, 노드의 방문이 이뤄진것으로 가정한이유는, 순회의 방법에 초점을 두기 위한것이니 우선은 재귀의 탈출 조건 구성에 집중하자.

![](https://i.imgur.com/lvqNwy3.png)

위 그림에서 루트 노드가 L인 왼쪽 서브트리를 보면 다음 과정을 거쳐서 순회하게된다.
1. 왼쪽 서브트리의 순회 (노드 N을 대상으로)
2. 루트 노드의 방문 (노드 L을 대상으로)
3. 오른쪽 서브 트리의 순회 (공집합 노드를 대상으로)

위의 단계를 보면 쉽게 재귀의 탈출 조건을 발견할 수 있을것이다. 노드 L의 오른쪽 서브트리가 NULL이므로 함수에 NULL이 전달되기 때문이다. 따라서 함수는 다음과 같이 정의 되어야한다

```c

void InorderTraverse(BTreeNode * bt){ // 이진 트리 전체를 중위 순회하는 함수

  if( bt == NULL){
    return ;
  }
  InorderTraverse(bt->left);  //1 단계 왼쪽 서브 트리의 순회
  printf("%d \n", bt->data);  //2 단계 루트 노드의 방문
  InorderTraverse(bt->right); //3 단계 오른쪽 서브 트리의 순회
}
```
[깃허브 중위순회 소스](https://github.com/minwan1/Algorithm/tree/master/BinaryTreeTraverseMain/BinaryTreeTraverseMain)
이로써 중위 순회를 끝냈다, 전위 순회와 후위 순회도 끝낸것이나 다름없다. 노드의 방문순서가 이들의 유일한 차이기 때문이다.
```c
void PreorderTraverse(BTreeNode * bt){ // 전위 순회함수
  if(bt == NULL){
    return ;
  }

  printf("%d \n", bt->data); // 전위 순회이므로 루트 노드 먼저 방문
  PreorderTraverse(bt->left);
  PreorderTraverse(bt->right);
}

```
```c
void PreorderTraverse(BTreeNode * bt){ // 후위 순회함수
  if(bt == NULL){
    return ;
  }

  printf("%d \n", bt->data);
  PreorderTraverse(bt->left);
  PreorderTraverse(bt->right); // 후위 순회이므로 루트 노드 먼저 방문
}

```
### 노드의방문을 자유롭게 구성하기
위에서는 단순히 노드의 방문했을경우에는 Printf로 값을출력해준다. 하지만 방문의 상황에따라 목적이 달라진다. 이때 함수 포인터를 이용해서 이것을 제어할 수 있다.

```c
typedef void VisitFuncPtr(BTData data);
void InorderTraverse(BTreeNode * bt, VisitFuncPtr action);{
  if(bt == NULL){
    return ;
  }

  InorderTraverse(bt->left, action);
  action(bt -> data);
  InorderTraverse(bt->right,action);

}

void showData(int data){
  printf("%d ",data)
}
```
showData함수를 던지게되면 안에 함수내용에따라 기능을 다르게 사용할 수 있다.

## 탐색의 이해와 보간 탐색
탐색은 어떻게 찾을가보다는 휴얼적인 탐색을 위한 저장법이 무엇일까를 고민해야한다. 효율적인 탐색이 가능한 대표적인 저장방법은 트리이다.

### 보간 탐색(Interpolation Search)
이진탐색은 정렬된 데이터들을 대상으로 중앙에 위치한 데이터를 탐색한후 이를 기준으로 탐색 대상을 반씩 줄여 나가면서 탐색을 진행하는 알고리즘이다. 때문에 찾는 대상의 위치에 따라서 탐색의 효율에 차이가 존재한다. 그러나 보간 탐색은 이러한 이진 탐색의 비효율성을 개선시킨 알고리즘이다. 개선의 원리는 이진 탐색처럼 그냥 중앙에서 탐색을 시작하지않고, 탐색 대상이 앞쪽에 위치해있으면 앞쪽에서 탐색을 시작한다. 예르들어 아래와같이 오름차순의 데이터가 존재할 때, 앞쪽에 위치한 데이터를 찾고자할때, 이진탐색과 보간탐색의 첫번째 탐색위치는 다음과 같이 차이가난다

* 둘다 정렬이 완료된 상태의 데이터를 대상으로 탐색을 진행하는 알고리즘이다.
![](https://i.imgur.com/4SAnzEY.png)

위 그림에서는 정수 12를 찾을 때의 첫 번째 탐색 위치를 보여준다. 이진 탐색은 값에 상관없이 탐색위치를 결정하지만, 보간 탐색은 그값이 상대적으로 앞에 위치한다고 판단을 하면 앞쪽에서 탐색을 진행한다. 따라서 데이터와 데이터가 저장된 위치의 인덱스 값이 직선의 형태로 비례하면, 보간 탐색의 경우 단번에 데이터를 찾기도한다. 위 그림과 같이 말이다. 단번에 찾지 못하더라도 탐색의 위치가, 찾는 데이터와 가깝기 때문에 탐색 대상을 줄이는 속도가 이진 탐색보다 뛰어나다.

이러한 보간탐색의 특성은 전화번호부나 사전에 비유된다. 예를들어서 김정수라는 사람의 전화번호를 찾을 때 이진탐색의 경우처럼 전화번호부의 중심부를 펼치는 사람은 없다. 이때는 전화번호부의 인덱스를 보고 'ㄱ'에 해당하는 앞쪽에서 찾는다.
이제 고민해야하는것은 보간 탐색의 위치를 결정하는 방법이다.
![](https://i.imgur.com/4SAnzEY.png)
보간 탐색 : 비례식 구성
![](https://i.imgur.com/eug6N71.png)
위 그림에서 low와 high는 탐색 대상의 시작과 끝에 해당하는 인덱스 값이고, s는 찾는 데이터가 저장된 위치의 인덱스 값이다. 그런데 보간 탐색은 데이터의 값과 그 데이터가 저장된 위치의 인덱스값이 비례한다고 가정하기 때문에, 위 그림을 근거로 다음의 비례식이 구성된다.

A:Q = (high-low) : (s-low)
여기에서 마지막 그림에 A는 (high-low)와 곱하고 (s-low)는 Q와 곱하고 최종적으로s를 구한다.(내항은 내항끼리 외항은외항끼리/)
이식에서 s는 찾고자하는 데이터의 인덱스 값이므로 위의비례식을 s에 대한 식으로 위 배열 다음 그림 순으로 공식이 진행되어 탐색 위치의 인덱스값 계산식이 나올것이다.


## 이진 탐색 트리
트리는 탐색에 효율적이다. 이진 트리에 저장된 데이터의 수가 10억개 수준에 이른다해도 트리의 높이는 30을 넘지 않기 떄문이다. 대신 이진트리는 단말 노드에 이르는 길의 갈래가 매우 많다. 따라서 찾는 데이터가 존재하는 제대로 된 길을 선택할 수 있어야 한다.
그래서 이제 만들려고하는 이진탐색트리는 데이터를 저장하는 규칙이 있다. 그리고 그 규칙은 특정 데이터의 위치를 찾는데 사용 할 수 있다. 쉽게 말해 이진트리에 데이터의 저장규칙을 더해놓은것이 이진탐색 트리이다. 아래는 이진탐색트리가 되기 위한 조건이다. 참고로 탐색키는 정수라 가정하였으며, 이러한 탐색키를 간단히 키로 표현하였다.
* 이진 탐색 트리의 노드에 저장된 키는 유일하다.
* 루트 노드의 키가 왼쪽 서브 트리를 구성하는 어떠한 노드의 키보다 크다.
* 루트 노드의 키가 오른쪽 서브 트리를 구성하는 어떠한 노드의 키보다 작다.
* 왼쪽과 오른쪽 서브 트리도 이진 탐색 트리이다.

![](https://i.imgur.com/3dbk6nX.png)

### 이진 탐색 트리의 구현 방안
* 구현 방법
이전에 구현 이진트리를 참조하여 처음부터 완전히 다 구현을 한다.
* 구현 방법 2
이진 탐색 트리도 이진트리이니, 이전에 구현한 이진트리를 활용하여 구현한다.


#### 이진 탐색 트리 삽입 원리
![](https://i.imgur.com/viZhGa3.png)
위 원리는 간단하다. 맨위 루트노드부터 값이 작으면 왼쪽 자식 노드로, 값이 크면 오른쪽 자식 노드로 이동한다. 그리고 비교대상이 없을 때까지 내려간다.  

```c
void BSTInsert(BTreeNode ** pRoot, BSTData data){
    BTreeNode * pNode = NULL; // parent node
    BTreeNode * cNode = * pRoot; // current node
    BTreeNode * nNode = NULL; // new node

    // 새로운 노드가(새 데이터가 담긴 노드가) 추가될 위치를 찾는다.
    while(cNode != NULL){
        if(data == GetData(cNode))
            return; // 데이터의(키의) 중복을 허용하지 않음

        pNode = cNode;

        if(GetData(cNode) > data)
            cNode = GetLeftSubTree(cNode);
        else
            cNode = GetRightSubTree(cNode);
    }

    //pNode의 자식 노드로 추가할 새노드의 생성
    nNode = MakeBTreeNode(); // 새노드의 생성
    SetData(nNode, data); // 새노드에 데이터 저장

    // pNode의 자식 노드로 새 노드를 추가
    if(pNode != NULL){ // 새 노드가 루트 노드가 아니라면
        if(data < GetData(pNode))
            MakeLeftSubTree(pNode, nNode);
        else
            MakeRightSubTree(pNode, nNode);
    }else{ // 새 노드가 루트 노드라면,
        * pRoot = nNode;
    }
}


```

#### 이진 탐색 탐색 원리
그냥 소스를 보면 더 쉽게 이해할 수 있을것이다.
```c

BTreeNode * BSTSearch(BTreeNode * bst, BSTData target){
    BTreeNode * cNode = bst;  // current node
    BSTData cd;               // current data

    while(cNode != NULL){
        cd = GetData(cNode);

        if(target == cd)
            return cNode;
        else if(target < cd)
            cNode = GetLeftSubTree(cNode);
        else
            cNode = GetRightSubTree(cNode);

    }

    return NULL; // 탐색 대상이 저장되어 있지 않음
}
```

#### 이진 탐색 삭제 원리
![](https://i.imgur.com/fVyRBlV.png)
먼저 이진 탐색 트리의 삭제대한 경우의 수를 생각해보자
* 삭제할 노드가 단말 노드인 경우
* 삭제할 노드가 하나의 자식 노드를(하나의 서브트리를)갖는 경우
* 삭제할 노드가 두개이 자식노드를(두개의 서브트리를)갖는 경우

##### 먼저 삭제할 노드가 단말 노드인 경우를 삭제해보자
![](https://i.imgur.com/TFaG6Cj.png
위 그림에서 보이듯이 상황 1에서는 ㅏㄱ제 대상인 단말 노드를 삭제하는것으로 삭제과정이 완료된다.
```c
if(삭제할 노드가 단말 노드이다!){
  if(GetLeftSubTree(pNode) == dNode){ // 삭제할 노드가 왼쪽 자식 노드라면
    RemoveLeftSubTree(pNode); // 왼쪽 자식 노드 트리에서 제거
  }else{ // 삭제할 노드가 오른쪽 자식 노드라면
    RemoveRightSubTree(pNode); // 오른쪽 자식 노드 트리에서 제거
  }
}
```


##### 삭제할 노드가 하나의 자식 노드를(하나의 서브트리를)갖는 경우
![](https://i.imgur.com/mPd5nJu.png)
위그림 왼쪽 트리에서, 10이 저장된 노드가 왼쪽 자식 노드이건 오른쪽 자식 노드이건, 이에 상관없이 10이 저장된 노드는 8이 저장된 노드의 오른쪽 자식 노드가 되어야한다.

```c
if(삭제할 노드가 하나의 자식 노드를 지닌다!){
  BTreeNode * dcNode;

  if(GetLeftSubTree(dNode) != NULL){ // 자식 노드가 왼쪽에 있다면,
    dcNode = GetLeftSubTree(dNode);
  }else{ // 자식 노드가 오른쪽에 있다면
    dcNode = GetRightSubTree(dNode);
  }

  if(GetLeftSubTree(pNode) == dNode) // 삭제 대상이 왼쪽 자식 노드이면
    ChangeLeftSubTree(pNode, dbNode); // 왼쪽으로 연결
  else // 삭제 대상이 오른쪽 자식 노드이면
    ChangeRightSubTree(pNode, dcNode); // 오른쪽으로 연결

}
```
##### 먼저 삭제할 노드가 단말 노드인 경우를 삭제해보자
![](https://i.imgur.com/zYWkRcq.png)
위의 첫번째 그림에서 8을 삭제할경우 이를 대체할 후보로 다음 두개의 노드를 꼽을 수 있다.
* 8이 저장된 노드의 왼쪽 서브 트리에서 가장 큰 값인 7을 저장한 노드
* 8이 저장된 노드의 오른쪽 서브트리에서 가장 작은 값인 8를 저장한 노드

위 2번째 그림은 8의 노드를 지우고 7또는 9를 저장한 노드로 각각 대체했을 때의 결과의 그림이다. 이그림에서 대체 후에도 이진 탐색 트리가 유지됨에 주목하자. 위그림에서 보듯이 삭제할 노드의 왼쪽 서브트리에서 가장 큰 값이나, 살제할 노드의 오른쪽 서브트리에서 가장 작은값을 저장한 노드로 대체하면 된다. 물론 서브트리에서 가장 큰 값이나 가장 작은 값을 저장한 노드를 찾는것은 어렵지 않은데, 다음 그림을 통해서 그 방법을 보이겠다.

위 3번째 그림에서 보듯이 NULL을 만날때 까지 계속해서 오른쪽 자식 노드로 이동하면되고, 가장 작은 값을 찾을 때는 NULL을 만날 때까지 왼쪽으로 이동하면된다. 어느것을 택해도 이진 탐색 트리의 유지에는 지장이 없으므로 삭제할 ***노드의 오른쪽 서브트리에서 가장 작은 값을 지니는 노드를 찾아서 이것으로 삭제할 노드를 대체한다.***

다음은 앞서 보인 이진 탐색 트리의 삭제 전과 후의 모습의 그림이다.
![](https://i.imgur.com/yV8W2I6.png)
위그림에서 보이는 삭제 결과를 이루기 위해 해야할일은 다음과 같다. 삭제가 되는 8이 저장된 노드를 대체한다. 그리고 이로 인해서 생기는 빈자리는 9가 저장된 노드의 자식노드로 대체한다.

그리고 위와같이 적용되는 방식을 아래의 그림과 같이 적용하려한다.
![](https://i.imgur.com/WlXpS1Q.png)
위 그림에서 8이 저장된 노드의 위치에 9가 저장된 노드를 가져다 놓지 않고, 값의 대입을 통해 노드의 교체를 대신하고 있다. ***우선 이방법은 여러모로 편한다*** 이방법을 사용할 경우 삭제 대상인 8의 부모노드,자식노드의 연결을 유지하기위해서 별도의 코드를 삽입할필요가 없기 때문이다. 그리고 10의 노드는 9의 노드와 연결해주면 된다



```c
if(삭제할 노드가 두 개의 자식 노드를 지닌다.){
  BTreeNode * mNode = GetRightSubTree(dNode); // mNode는 대체 노드 가리킴
  BTreeNode * mpNode = dNode; // mpNode는 대체 노드의 부모 노드 가리킴
  ***

  // 단계 1. 삭제 대상의 대체 노드를 찾는다.
  while(GetLeftSubTree(mNode != NULL)){
    mpNode = mNode;
    mNode = GetLeftSubTree(mNode);
  }

  // 단계 2. 대체할 노드에 저장된 값을 삭제할 노드에 대입한다.
  SetData(dNode, GetData(mNode));

  //단계 3. 대체할 노드의 부모노드와 자식 노드를 연결한다.
  if(GetLeftSubTree(mpNode) == mNode){ // 대체할 노드가 왼쪽 자식 노드라면
    //대체할 노드의 자식 노드를 부모 노드의 왼쪽에 연결
    ChangedLeftSubTree(mpNode, GetRightSubTree(mNode));  
  }else{ // 대체할 노드가 오른쪽 자식 노드라면
    // 대체할 노드의 자식 노드를 부모 노드의 오른쪽에 연결
    ChangedRightSubTree(mpNode, GetRightSubTree(mNode));
  }  

}

```

#### 이진 탐색 트리의 삭제구현 : 삭제의 구현을 위한 이진 트리의 확장
삭제를 포함한 이진 탐색 트리의 완성을 위해서 우선 BinaryTree2,BinaryTree2.c에 다음 네 개의 함수를 추가로 선언 및 정의하고자 한다.

```c
//왼쪽 자식 노드를 트리에서 제거, 제거된 노드의 주소 값이 반환 된다.
BTreeNode * RemoveLeftSubTree(BTreeNode * bt);
//오른쪽 자식 노드를 트리에서 제거, 제거된 노드의 주소값이 반횐된다.
BTreeNode * RemoveRightSubTree(BTreeNode * bt);
//메모리 소멸을 수반하지 않고 main의 왼쪽 자식 노드를 변경한다.
void ChangedLeftSubTree(BTreeNode * main, BTreeNode * sub);
//메모리 소멸을 수반하지않고, main의 오른쪽 자식 노드를 변경한다.
void ChangedRightSubTree(BTreeNode * main, BTreeNode * sub);
```

앞서 우리는 이진트리의 구성과 관련된 기본적인 기능을 BinaryTree2.c 사용했다. 그리고 이를 이용해 수식트리를 구현했다. 하지만 이진 탐색 트리의 구현을 위한 충분한 도구가 되지는 못한다. 그이유는 다음 두가지이다.
* 노드의 제거에 대한 기능이 정의되지 않았다.
* MakeLeftSubTree, MakeRightSubTree함수는 교체되는 노드의 소멸까지 진행한다.

위의 내용에 대한 부연 설명을 위한 MakeLeftSubTree 소스이다
```c
void MakeLeftSubTree(BTreeNode * main, BTreeNode * sub){
  if(main -> left != NULL)
    free(main -> left); // 메모리의 소멸까지 진행한다!

  main->left = sub;
}
```

위함수 문제는 main이 가리키는 노드의 왼쪽 자식 노드가 존재하는 상황이라면 이에대한 메모리 해제를 진행한다. 따라서 왼쪽 자식 노드의 교체를 목적으로 어울리지 않는다. 그래서 자식 노드의 단순 교체를 목적으로 다음 두함수를 추가로 정의하였다.


```c
// 메모리의 소멸을 수반하지 않고 main의 왼쪽 자식 노드를 변경한다.
void ChangedLeftSubTree(BTreeNode * main,BTreeNode * sub){
  main->left = sub;
}

//메모리의 소멸을 수반하지 않고 main의 오른쪽 자식 노드를 변경한다.
void ChangedLeftSubTree(BTreeNode * main,BTreeNode * sub){
  main->right = sub;
}

```

그전에 BinaryTree2,BinaryTree2.c에서 이진트리 구성의 초점을 맞췄기 때문에 삭제에 대한 기능을 정의하지 않았다. 이진 탐색 트리에서는 삭제에 대한 논의가 빠질 수 없기 때문에 삭제와 관련된 다음 두 함수를 추가로 정의 하였다.

```c
BTreeNode * RemoveLeftSubTree(BTreeNode * bt){
  BTreeNode * delNode;

  if(bt != NULL){
    delNode = bt->left;
    bt->left = NULL;
  }

  return delNode;
}


BTreeNode * RemoveRightSubTree(BTreeNode * bt){
  BTreeNode * delNode;

  if(bt != NULL){
    delNode = bt -> right;
    bt -> right = NULL;
  }

  return delNode;
}
```

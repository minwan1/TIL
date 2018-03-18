## 균현 잡힌 이진 탐색 트리 AVL트리의 이해

이진 탐색 트리의 탐색 연산은 O(log2n)의 시간 복잡도를 가진다.
아래는 이진트리의 예제들이다.
![](https://i.imgur.com/fPjdZRa.png)
위에 두개의 이진트리가 존재하는데 여기에서 5를찾기 위해서는 어떻게 저장되느냐에 따라 성능차이가 확연하다. 이것이 바로 이진트리의 단점이다. 이러한 이진탐색트리의 단점을 해결한 트리를 가리켜 균형잡힌이진트리라 하며 그종류는 대략 다음과 같다
* AVL 트리
* 2-3 트리
* 2-3-4 트리
* Red-Black 트리
* B트리

이중에서 AVL트리를 구현해볼것이다.


### 자동으로 균현을 잡는 AVL 트리와 균형 인수(Balance Factor)
AVL트리는 노드가 추가될 때, 그리고 삭제될 때 트리의 군형상태를 파악해서 스스로 그 구조를 변경하여 균형을 잡는 멋진 트리이다.
아래는 균형인수의 정의이다.
![](https://i.imgur.com/gPBeoC8.png)
위에 첫번째 그림에서 보듯이 왼쪽은 + 2 오른쪽은0이다. 그러므로 균형인수는 2이다. 두번째그림은 반대로 균형인수는 -2이다. 이러한 균형인수들을 제대로 잡히지 않은 균형인수라 할 수 있다.
이것에대한 균형을 제대로 잡으려면 AVL 트리의 리밸런싱을 해야한다. 균형을 잡기 위한 트리 구조의 재종을 가리켜 리밸런싱이라한다. 균형 인수의 절대 값이 크면 클수록 그만큼 트리의 균형이 무너진 상태이다. 따라서 AVL트리는 균형 인수의 절대값이 2 이상인 경우에 균형을 잡기 위한 트리의 재종을 진행한다.

### 리밸런싱이 필요한 첫 번째 상태와 회전 LL회전
AVL트리의 균형이 무너지는 상태는 4가지로 정리가 된다. 그리고 각 상태 별 리밸런싱 방법에도 차이가 있다. 그중 첫번째 상태는 다음과 같다.
![](https://i.imgur.com/HSYlZxG.png)
위에 그림과 같은 상태 왼쪽에 두개의 자식노드가 존재하는 상태를 가르켜 LL상태라고하고 반대로 오른쪽에 두개가 있으면 RR상태라고하 부른다.

![](https://i.imgur.com/1UTYCnO.png)
위 두번째 그림은 리밸런싱을 통한 균형 잡힌 트리구조로 변경하는것이다. 5가 들어있는 트리를 T3자리로 옮기고 5의 오른쪽 자식노드로 T3을 넣는것이다. 위그림에서 보이듯이 cNode가 가리키는 노드의 오른족 서브 트리를 pNode가 가리키는 노드의 왼쪽 서브트리로 옮기기 위해서는 다음 문장을 실행해야한다.

ChangeLeftSubTree(pNode, GetRightSubTree(cNode));

정리하면, LL회전을 위해서는 다음 두 문장을 순서대로 실행해야한다.

1. ChangeLeftSubTree(pNode, GetRightSubTree(cNode));
2. ChangedRightSubTree(cNode,pNode);

LL회전과 관련하여 위의 두문장이 필요한 이유를 각각 이해해야한다. 위그림으로 설명하자면 1번은 T3을 5에왼쪽노드에 붙이는 것이다 2번은 그 값5(pNode)를 3(cNode)노드의 오른쪽노드에 붙이는것이다.


### 리밸런싱이 필요한 두 번쟤 상태와 RR회전
아래는 RR상태의 경우의 리밸런싱이 되는 결과이다. LL과 는 그냥 반대로생각하면될것같다.
![](https://i.imgur.com/LfxQ7Pb.png)

### 리밸런싱이 필요한 세 번째 상태와 LR회전
아래의 그림은 LR상태의 그림이다. 5자식 노드에 왼쪽아래 오른쪽 아래이기 떄문에 LR이라고한다.
![](https://i.imgur.com/9fAuV6w.png)
먼저 LR상태를 리밸런싱하기 위해서는 LR상태인 상태를 LL상태로 변경해야한다. 값 1,3,5를 LL형태로 아래와같이 변경해야한다.
![](https://i.imgur.com/D4Sq1x1.png)

## 균현 잡힌 이진 탐색 트리 : AVL 트리의 구현
AVL트리의 구현을 위해서는 기존의 이진탐색트리에 노드의 추가, 삭제 작업에 리밸런싱 기능만 넣으면 AVL트리를 만들 수 있다. 그래서 기존에 만들었던, BinaryTree3,BinarySearchTree2 c,h들을 이용해서 AVL트리를 만들것이다. 그리고 여기에다가 리밸런싱을 도와주는 함수를 정의하기위해 AVLRebalance c,h를 만들것이다.

### AVL 트리의 구현을 위한 BinarySearchTree2.c의 확장 포인트
루트 노드를 기준으로 왼쪽과 오른쪽 균형이 잘 잡혀있는 이진 탐색 트리가 있다. 이 트리의 군형이 깨지는, 다시 말해서 루트 노드의 균형 인수의 절대값이 1을 넘어가는 상황은 노드의 삽입과 삭제의 과정에서 발생한다.

* BSTInsert 함수 트리에 노드를 추가
* BSTRemove 함수 트리에서 노드를 제거

따라서 트리의 균형을 재조정하는 함수의 이름을 Rebalance라 했을 때, 위에서 언급한 두 함수는 대략 다음가 같은 방식으로 화정 되어야 한다.

```c
void BSTInsert(BTrreNode ** pRoot, BSTData data){
  ....
  Rebalance(pRoot); // 노드 추가 후 리밸런싱!
}

BTreeNode * BSTRemove(BTreeNode ** pRoot, BSTdata tartget){
  ...
  Rebalance(pRoot); // 노드 제거 후 리밸런싱!
  return dNode;
}

```
위의 Rebalance 함수 호출문을 보면, 인자로 루트 노드의 정보를 전달함을 알 수 있다. 이는 트리의 불균형 여부를 루트 노드를 기준으로 확인해야 하기 떄문이다.

### 리밸런싱에 필요한 도구들의 정의 : 균형을 이루고 있는가 ?
먼저 AVL트리의 상태로 만들기위해서는 현재의 이진트리가 불균형한상태인지 알아야한다. 그러기위해서는 루트노드를 기준으로부터 왼쪽 서브트리의 길이와 오른쪽 서브트리의 길이를 알아야한다. 따라서 리밸런싱에 필요한 첫번째 도구로 다음 함수를 정의하였다.

```c
//트리의 높이를 계산하여 반환
int GetHeight(BTreeNode * bst){
  int leftH;  // left height
  int rightH; // right height

  if(bst == NULL)
    return 0;

  leftH = GetHeight(GetLeftSubTree(bst)); // 왼쪽 서브 트리 높이 계산
  rightH = GetHeight(GetRightSubTree(bst)); // 오른쪽 서브 트리 높이 계산

  // 큰 값의 높이를 반환한다.
  if(leftH > rightH)
    return leftH + 1; // 주의할점
  else
   return rightH + 1; // 주의할점  가장 높은값만 리턴하는 구조
}
```
트리는 단말 노드의 수 만큼 경로가 나뉘기 때문에, 그리고 트리의 높이는 그 중에서 가장 깊이 벋은 경로를 기준으로 결정되기 때문에 위와같이 재귀적인 형태로 정의해야한다. 위 함수는 GetHeight 함수가 호출될 때 마다 높이를 1씩 더해가는 구조로 정의되어있다.최종적으로 왼쪽에서 가장긴노드와 오른쪽에서 가장긴노드를 뺄셈하여 리턴해준다. 여기에서 중요한점은 큰값만 리턴되고 낮은값은 리턴되기때문에!!!!!! 왼쪽이드 오른쪽이드 가장높은 노드의 높이만 구해올 수 있다.

최종적으로 아래의 함수를 통해 최종 균형 인수의값을 구한다.
```c
// 두 서브 트리의 '높이의 차(균형 인수)'를 반환
int GetHeightDiff(BTreeNode * bst){
  int lsh;
  int rsh;

  if(bst == NULL){
    return 0;
  }

  lsh = GetHeight(GetLeftSubTree(bst)); // 왼쪽 서브 트리의 높이
  rsh = GetHeight(GetRightSubTree(bst)); // 오른쪽 서브 트리의 높이
  return lsh - rsh ; // 균형 인수 계산결과 반환

}

```

### 리밸런싱에 필요한 도구들의 정의: LL회전, RR회전

다음은 LL회전을 담당하는 함수이다.
```c
BTreeNode * RotateLL(BTreeNode * bst){ // LL회전을 담당하는 함수
  BTreeNode * pNode; // parent node
  BTreeNode * cNode; // child node

  //pNode 와 cNode가 LL회전을 위해 적절한 위치를 가리키게 한다.

  pNode = bst;
  cNode = GetLeftSubTree(pNode);

  //실제 LL회전을 담당하는 두 개의 문장
  // 먼저 현재노드의 왼쪽노드들을 왼쪽 서브트리로 붙인다.
  ChangeLeftSubTree(pNode, GetRightSubTree(cNode));
  // 그리고 현재 노드의 오른쪽으로 부모노드를 붙인다.
  ChangeRightSubTree(cNode, pNode);

// LL회전으로 인해서 변경된 루트 노드의 주소 값 반환
  return cNode;

}

```

다음은 RR회전을 담당하는 함수이다.

```c
BTreeNode * RotateRR(BTreeNode * bst){
  BTreeNode * pNode;
  BTreeNode * cNode;

  // pNode와 cNode가 RR회전을 위해 적절한 위치를 가리키게 한다.
  pNode = bst;
  cNode = GetRightSubTree(pNode);

  ChangeRightSubTree(pNode, GetLeftSubTree(cNode));
  ChangeLeftSubTree(cNode, pNode);

  // RR회전으로 인해서 변경되
  return cNode;


}
```

### 리밸런싱에 필요한 도구들의 정의 : LR회,RL회전
* LR회전 : 부분적 RR회전에 이어서 ll회전을 진행한다.
* RL회전 : 부분적 LL회전에 이어서 RR회전을 진행한다.

다음은 RL회전을 담당하는 함수이다.
```C
BTreeNode * RotateLR(BTreeNode * bst){
  BTreeNode * pNode; // parent node
  BTreeNode * cNode; // child node

  //pNode와 cNode가 LR회전을 위해 적절한 위치를 가리키게 한다.

  pNode = bst;
  cNode = GetLeftSubTree(pNode);

  //실제 LR회전을 담당하는 두 개의 문장
  ChangeLeftSubTree(pNode, RotateRR(cNode)); // 부분적 RR회전
  return RotateLL(pNode); // LL회전

}
```
![](https://i.imgur.com/jJPZKRK.png)
먼저 위에서 말한대로 LL회전인 상태에서 트리를 균형있게 만들기 쉽기때문에 RL을 LL로 바꿔줘야한다. 그러기위해서는  먼저 부분적 RR회전을 해줘야한다. 위에 파란 그림처럼 그러면 반대로 빨간그림의 부분적 rr회전의 결과를 볼수 있을것이다. 그리고 이제 3의 저장된 노드의 주소값이 리턴될것인데 이것을 다시 5와 합쳐주면된다. 그림에서는 3,5를 여결할때 ChangeLeftSubTree함수를 호출하면서 RotateRR함수의 반환값을 두번째 인자로 전달하는것을 의미한다. 그리고 이제 마지막으로 다시 LL회전을 해주면된다. 이 LL회전을 담하는 줄이 return RotateLL(pNode); 이다.

마찬가지로 LR회전도 기능만 반대인 함수를 구한하면된다

```c
BTreeNode * RotateRL(BTreeNode * bst){
  BTreeNode * pNode; // parent node
  BTreeNode * cNode;

  // pNode와 cNode가 RL회전을 위해 적절한 위치를 가리키게한다.
  pNode = bst;
  cNode = GetRightSubTree(pNode);

  //실제 RL회전을 담당하는 두개 의 문장
  ChangeRightSubTree(pNode, RotateLL(cNode)); // 부분적 LL회전
  return RotateRR(pNode); // RR회전
}
```


### 리밸런싱에 필요한 도구들의 정의 : Rebalance 함수
균형이 맞혀져있는지 확인하는 함수이다.
```c
BTreeNode * Rebalance(BTreeNode ** pRoot){

  int hDiff == GetHeightDiff(*pRoot); // 균형 인수 결과

  //균형 인수가 +2 이상이면 LL상태 또는 LR상태이다
  if(hDiff > 1){

    // 이안에서 다시  왼쪽 자식 노드에서 LL노드인지 LR노드인지 체크한다.
    // 만약 0보다 크면 LL노드 일테고 아니면 LR노드 구조일테다.
    if(GetHeightDiff(GetLeftSubTree(*pRoot)) > 0){
      * pRoot = RotateLL(*pRoot);
    else
     * pRoot = RotateLR(*pRoot);

  }


  // 균형인수가 -2이하이면 RR상태 또는 RL상태이다.

  if(hDiff < -1){
    // 위와 마찬가지로 RR인지 RL인체 크를 한다.
    if(GetHeightDiff(GetRightSubTree(*pRott)) < 0){
      * pRoot = RotateRR(*pRoot);
    }else{
      * pRoot = RotateRL(*pRoot);
    }
  }

  return * pRoot;

}
```

위 코딩을 아래의 그림과 같이 표현했다.
![](https://i.imgur.com/wlbNrRt.png)


### 리밸런싱의 도구 AVLRebalance.h, AVLRebalance.c
지금까지 구현한기능을 AVLRebalance.c와 헤더에 추가해주고 기능을 테스트 하면된다.

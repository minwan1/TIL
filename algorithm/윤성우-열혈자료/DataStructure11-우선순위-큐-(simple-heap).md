## 힙의 소개
힙은 이진트리이되 완전 이진트리이다. 그리고 모든 노드에 저장된 값은 자식 노드에 저장된 값보다 크거나 같아야한다. 즉 루트 노드에 저장된 값이 가장 커야한다. 여기에서 말하는 값은 말그대로 값이 될수 도 있고 우선순위 큐에서 말하는 큐일 수 도 있다. 여기에서 구현하려는것은 힙이기 떄문에 값은 우선순위가 된다.

![](https://i.imgur.com/4wKtPuw.png)
위로 올라갈수록 저장된 값이 커지는 완전 이진트리를 가리켜 최대 힙이라고한다 반면 저장된 값이 작아지는 완전이진트리를 최소 힙이라고한다.

## 힙의 구현과 우선순위 큐의 완성
힙의 구현은 곧 우선순위 큐의 완성으로 이어진다. 힙을 구현하고 이를 기반으로 우선순위 큐를 구현하고자한다. 힙의 구현을 위해서는 데이터의 저장과 삭제의 방법을 먼저 알아야한다. 따라서 먼저 데이터의 저장과정을 최소 힙을 기준으로 설명하겠다.

### 힙에서 데이터의 저장과정
![](https://i.imgur.com/grk07Vd.png)
그럼 먼저 첫번쨰 그림에서 3이라는 숫자를 넣어보자. 먼저 새로운 데이터(3)는 우선순위가 제일 낮다는 가정하에 마지막 위치에 저장합니다. 그리고는 부모 노드와 우선순위를 비교해서 위치가 바뀌어야 한다면 바꿔줍니다. 바뀐 다음에도 계속계속해서 부모 노드와 비교합니다. 계속 계속 부모노드와 비교를하고 마지막그림 처럼 부모노드와의 비교를 통해 자신의 위치를 찾아가는 매우 단순한 방식이다.

### 힙에서의 데이터 삭제과정
![](https://i.imgur.com/pknbh5C.png)
먼저 수선 순위에서 큐의 삭제는 가장 높은 우선순위의 데이터 삭제를 의미하므로 먼저 루트노드를 삭제해야한다. 위 그림만 봐도 대충 어떻게 삭제되는 지 알 수 있을것이다. 완전 이진트리에서 마지막 레벨의 가장 오른쪽에 위치한 노드를 루트 노드로 옮긴다.
이어서 노드 삽입할때와 유사하게 비교의 과정을 통해서 8이 제 위치를 찾게끔 한다. 즉 위 그림에서 보이듯이 두 개의 자식 노드중 우선순위가 높은 3이 저장된 왼쪽 자식노드와 8이 저장된 노드를 교환한다. 무론 이과정에서 오른쪽 노드의 우선순위가 높다면, 오른쪽 자식 노드와 교환해야한다.

### 삽입과 삭제의 과정에서 보인 성능 평가
데이터의 우선순위가 높을수록 데이터를 배열의 앞쪽에 위치시키는 방식으로 구현한 우선순위 큐의 성능은 다음과 같았다.
- 배열 기반 데이터 저장의 시간 복잡도 O(n)
- 배열 기반 데이터 삭제의 시간 복잡도 O(1)

배열의 경우 저장된 모든 데이터와의 우선순위 비교과정을 거쳐야 하므로 데이터 저장의 시간 복잡도는 O(n)이고 삭제는 맨앞에서 저장된 데이터를 삭제되기 때문에 O(n)이다

- 연결리스트 기반 데이터 저장의 시간 복잡도 O(n)
- 연결리스트 기반 데이터 삭제의 시간 복잡도 O(1)

연결리스트 또한 다를게없다.

- 힙 기반 데이터 저장의 시간 복잡도 O(log2n)
- 힙 기반 데이터 삭제의 시간 복잡도 O(log2n)

힙은 완전 이진 트리이므로, 힙에 저장할 수 있는 데이터의 수는 트리의 높이를 하나 늘 때마다 두배씩 증가한다. 때문에 데이터의 수가 두배늘 때 마다, 비교 연산의 횟수는 1회 증가한다. 바로 이 사실을 근거로 위의 결론은 내릴 수 있다.


## Simple-Heap 구현
### 힙의 구현에 어울리는 것은 연결리스트 아니면 배열?
우선순위 큐의 구현에는 어울리는것은 힙으로 결론 났다. 힙은 트리이다. 앞서 트리를 구현하는 방법에는 배열,연결리스트를 이용하는 방법이 있음을 설명했다. 일반적으로 힙을 구현할 때는 배열방식을 이용한다. 연결리스트를 기반으로 힙을 구현하면, 새로운 노드를 힙의 마지막 위치에 추가하는것이 쉽지 않다. 그래서 힙과 같이, 새로운 노드를 추가한 이후에도 완전 이진 트리를 유지해야하는 경우에는 연길리스트가 아닌 배열을 기반으로 트리르 구현한다.


### 배열을 기반으로 힙을 구현하는데 필요한 지식들
배열을 기반으로 힙을 구현하는 방법을 간단히 설명하자면 노드에 고유의 번호를 부여한다. 그리고 그 번호가 각 노드의 데이터가 저장 될 배열의 인덱스 값이 된다.
![](https://i.imgur.com/UfH0arF.png)
위 그림에서 보듯이 구현의 편의를 위해서 인덱스가 0인 위치의 배열 요소는 사용하지 않는다. 배열 기반으로 힙을 구현하기 위해서는 왼쪽 그리고 오른쪽 자식 노드의 인덱스값을 얻는 방법, 그리고 부모 노드의 인덱스 값을 얻는 방법을 알아야한다. 자식 노드의 인덱스 값을 얻는 방법은 데이터의 삭제를 위해서, 부모 노드의 인덱스 값을 얻는 방법은 데이터의 추가를 위해서 필요한다.
아래는 부모,자식노드들의 인덱스를 얻는법이다.
* 왼쪽 자식 노드의 인덱스 값    부모 노드의 인덱스 값 * 2
* 오른쪽 자식 노드의 인덱스 값  부모 노드의 인덱스 값 * 2 + 1
* 부모 노도의 인덱스 값       자식의 인덱스값 / 2

이진 트리는 레벨이 증가함에 따라서 추가할 수 있는 자식 노드의 수가 두 배씩 증가하는 구조다 보니, 2를 나누고 곱하는 방식으로 부모 노드와 자식노드의 인덱스 값을 구 할 수 있다.

먼저 header파일을 정의 하자면 아래와같을 수 있다.

```c
#define TRUE    1
#define FALSE   0

#define HEAP_LEN 100

typedef char HData;
typedef int Priority;

typedef struct _heapElem{
    Priority pr; // 값이 작을수록 높은 우선순위
    HData data;
} HeapElem;

typedef struct _heap{
    int numOfData;
    HeapElem heapArr[HEAP_LEN];
}Heap;


void HeapInit(Heap * ph);
void HIsEmpty(Heap * ph);
void HInsert(Heap * ph, HData data, Priority pr);

HData HDelete(Heap * ph);
```
위 header파일에 HeapElem 구조체를 보면 순수한 힙의 구현을 위한 헤더파일이 아닌, 우선순위 큐의 구현을 염두에 두고 정의한 헤더파일이다. 그리고 이구조체는 우선순위 정보를 별도로 담을 수 있도록 정의되어 있다. 이는 우선순위 큐의 구현을 고려 했다는 뜻이다. 따라서 위의 헤더파일에 선언된 HDelete함수는 우선순위 큐와 마찬가지로, 데이터의 삽입 순선에 상관없이 우선순위에 근거하여 삭제가 이뤄지도록 정의할것이다.

### 원리 이해 중심의 힙 구현 : HDelete 함수에 대한 설명 중심으로
다음으로 헤더파일에 정의되어 있는 함수들을 구현하겠다. 그런데 먼저 이를 구현하기에 앞서 다음 사실들을 정리하고 기억할 필요가 있다.
* 힙은 완전 이진 트리이다.
* 힙의 구현은 배열을 기반으로 하며, 인덱스가 0인 요소는 비워둔다.
* 따라서 힙에 저장된 노드의 개수와 마지막 노드의 고유번호는 일치한다.
* 노드의 고유번호가 노드가 저장되는 배열의 인덱스 값이 된다.
* 우선순위를 나타내는 정수 값이 작을수록 높은 우선순위를 나타낸다고 가정한다.



```c


void HeapInit(Heap * ph){ // heap 의 초기화
    ph->numOfData = 0;
}

int HIsEmpty(Heap * ph){  // 힙이 비어있는지 확인
    if(ph->numOfData == 0){
        return TRUE;
    }else{
        return FALSE;
    }
}

int GetParentIDX(int idx){ // 부모 노드의 인덱스 값 반환
    return idx/2;
}

int GetLChildIDX(int idx){ // 왼쪽 자식 노드의 인덱스 값 반환
    return idx*2;
}

int GetRChildIDX(int idx){ // 오른쪽 자식 노드의 인덱스 값 반환
    return GetLChildIDX(idx)+1;
}

// 두 개의 자식 노드 중 높은 우선순위의 자식 노드 인덱스 값 변환
int GetHiPriChildIDX(Heap * ph, int idx){
    if(GetLChildIDX(idx) > ph->numOfData)
        return 0;
    else if(GetLChildIDX(idx) == ph->numOfData)
        return GetLChildIDX(idx);
    else {
        if(ph->heapArr[GetLChildIDX(idx)].pr > ph->heapArr[GetRChildIDX(idx)].pr)
            return GetRChildIDX(idx);
        else
            return GetLChildIDX(idx);
    }
}

//힙에 데이터 저장
void HInsert(Heap * ph, HData data, Priority pr){
    int idx = ph->numOfData+1;
    HeapElem nelem =  {pr,data};

    while(idx != 1){
        if(pr < (ph->heapArr[GetParentIDX(idx)].pr)){
            ph->heapArr[idx] = ph->heapArr[GetParentIDX(idx)];
            idx = GetParentIDX(idx);
        }else{
            break;
        }
    }

    ph->heapArr[idx] = nelem;
    ph->numOfData += 1;
}


//힙에서 데이터 삭제
HData HDelete(Heap * ph){
    HData retData = (ph->heapArr[1]).data;
    HeapElem lastElem = ph->heapArr[ph->numOfData];

    int parentIdx = 1;
    int childIdx;

    while(childIdx = GetHiPriChildIDX(ph, parentIdx)){
        if(lastElem.pr <= ph->heapArr[childIdx].pr){
            break;
        }

        ph->heapArr[parentIdx] = ph->heapArr[childIdx];
        parentIdx = childIdx;
    }

    return retData;

}
```
먼저 GetHiPriChildIDX 임하수에대 알아보면 이함수의 기능은 노드의 인덱스값을 전달하면, 이노드의 두자식 노드중에서 우선순위가 높은 것의 인덱스값을 반환한다. 만약 자식노드가 없으면 0을 반환한다. 자식 노드가 한개일경우에는 그해당노드를 반환한다.
```c
if(GetLChildIDX(idx) > ph->numOfData)
    return 0;
```
GetHiPriChildIDX 함수에서 윗 부분이 이해가 가지 않을 수 있다. 힙은 완전 이진 트리이므로 오른쪽 자식 노드만 존재하는 상황은 발생하지 않는다. 따라서 왼쪽 자식 노드가 없다면 자식 노드가 존재하지 않는것으로 판단할 수 있다.  그리고 힙은 완전 이진 트리이므로 자식노드가 하나도 존재하지 않는 노드는 단말 노드이다.
그런데 GetLChildIDX함수에 의해 단말 노드의 왼쪽 자식 노드 인덱스값이 힙에 저장된 노드의 수를 넘어선다것은 힙의 저장된 노드의 수보다 많기 때문에 존재하지 않는다는게 맞다.

```c
else if(GetLChildIDX(idx) == ph->numOfData)
        return GetLChildIDX(idx);
    else
```
그다음도 마찬가지로 하나뿐인 자식 노드는 왼쪽 자식 노드이다. 그리고 힙의 마지막 노드이다. 그러므로 GetLChildIDX 에의해 사용하고있는 힙과 같은 노드의 갯수를 가진다면 그것이 우선순위가 높은 노드이기때문에 그 인덱스를 리턴해준다.


다음 HDelete함수를 설명하겠다.

```c

//힙에서 데이터 삭제
HData HDelete(Heap * ph){
    HData retData = (ph->heapArr[1]).data; // 반환을 위해서 삭제할 데이터 저장
    HeapElem lastElem = ph->heapArr[ph->numOfData]; // 힙의 마지막 노드 저장

    // 아래의 변수 parentIdx에는 마지막 노드가 저장될 위치 정보가 담긴다.
    int parentIdx = 1; // 루트 노드가 위치해야 할 인덱 값 저장
    int childIdx;


    // 루트 노드의 우선순위가 높은 자식 노드를 시작으로 반복문 시작
    while(childIdx = GetHiPriChildIDX(ph, parentIdx)){

        //마지막 노드와 우선순위 비교
        if(lastElem.pr <= ph->heapArr[childIdx].pr){
            // 마지막 노드의 우선순위가 높으면 반복문 탈출
            break;
        }
        // 마지막 노드보다 우선순위 높으니, 비교대상 노드의 위치를 한 레벨 올림
        ph->heapArr[parentIdx] = ph->heapArr[childIdx];
        // 마지막 노드가 저장될 위치정보를 한 레벨 내림
        parentIdx = childIdx;

    } // 반복문 탈출하면 parentIdx에는 마지막 노드의 위치정보가 시작됨


    // 마지막 노드 최종 저장
    ph->heapArr[parentIdx] = lastElem;
    ph->numOfData -= 1;
    return retData;

}
```
HDelete 함수가 호출되면서 변수 parentIdx는 1로 초기화된다. 그런데 1은 루트노드의 인덱스 값이므로 변수 parentIdx가 1로 초기화된 이상황을 마지막 노드를 루트 노드로 옮긴 상황으로 간주할 수 있다. 그리고 이 루트노드를 바탕으로 우선순위 더 높은 노드를 찾아 while문을 빠져 나온뒤에 위치를 교환하면된다.


### 원리 이해 중심의 힙 구현 : HInsert 함수에 대한 설명 중심으로
HInsert또한 HDelete 원리와 비슷하다. 삽입 과정을 설명하자면 새로운 데이터는 우선순위가 제일 낮다는 가정하에서 마지막 위치에 저장한다. 그리고 우선순위의 비교를 통해서 자신의 위치를 찾을 때까지 위로 올린다.

```c
//힙에 데이터 저장
void HInsert(Heap * ph, HData data, Priority pr){
    int idx = ph->numOfData+1; // 새노드가 저장딜 인덱스 값을 idx에 저장
    HeapElem nelem =  {pr,data}; // 새 노드의 생성 및 초기화

    // 새노드가 저장될 위치가 루트 노드의 위치가 아니라면 while문 반복
    while(idx != 1){

        // 새노드와 부모 노드의 우선순위 비교
        if(pr < (ph->heapArr[GetParentIDX(idx)].pr)){ // 새노드의 우선순위가 높다면
            // 부모노드를 한 레벨 내림, 실제로 내림
            ph->heapArr[idx] = ph->heapArr[GetParentIDX(idx)];
            // 새 노드를 한 레벨 올림, 실제로 올리지는 않고 인덱스 값만 갱신
            idx = GetParentIDX(idx);
        }else{
            break; // 새 노드의 우선순위가 높지 않다면
        }
    }

    ph->heapArr[idx] = nelem; // 새 노드를 배열에 저장
    ph->numOfData += 1;
}
```
위이 함수에서 idx = GetParentIDX(idx);를 계속 갱신하면서 저장되어야할 위치를 갱신하고 마지막에 급부분에 노드를 새로 입력되어진 노드로 입력하면된다.

위에 소스를 main함수를 작성후 돌려보면 잘돌아갈것이다. 하지만 위의 힙은 부족한부분이있다
```c
typedef struct _heapElem{
  Priority pr;
  HData data;
}HeapElem;
```

위의 구조체는 힙을 이루는 노드를 표현한것인데 이구조체의 멤버로 우선순위 정보를 담는 변수가 선언 되어 있다.void HInsert(Heap * ph, HData data, Priority pr) 또 값을 입력하려고하면 우리는 우선순위를 알아야한다. 하지만 힙의 데이터를 넣기전에 우리가 미리 데이터의 우선순위를 알고 데이터를 넣어야한다는것은 매우 비효율적이다. 그래서 다음편에서는 이러한 문제를 해결할것이다.





[깃허브소스](https://github.com/minwan1/Algorithm/tree/master/SimpleHeap)

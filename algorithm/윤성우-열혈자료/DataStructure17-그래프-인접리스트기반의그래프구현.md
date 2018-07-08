## 인접 리스트 기반의 그래프 구현
인저리스트 기반의 그래프는 정점의 수만큼 리스트를 만들어서 그래프를 구현하는것,
**헤더파일의 정의**
그래프의 구현 관점에서 무방향 그래프와 방향 그래프의 유일한 차이점은 연결리스트에 추가하는 노드의 수에 있기 때문에, 이둘의 구현방법에는 차이가 없다고 볼 수 있다. 그러나 굳이 따지자면 무방향 그래프의 구현이 조금 더 복잡하다 할 수 있다. 연결리스트에 추가해야 하는 노드의 수가 방향 그래프에 비해 두배 더많기 때문이다. 다음은 무방향 그래프의 구현을 보이고자 한다.


```c
#ifndef ALGraph_h
#define ALGraph_h

#include "DLinkedList.h"
#include <stdio.h>


enum {A, B, C, D, E, F, G, H, I, J}; // 정점의 이름을 상수화!

typedef struct -ual
{
    int numV; // 정점의 수
    int numE; // 간선의 수
    List * adjList; // 간선의 정보
} ALGraph;

//그래의 초기화
void GraphInit(ALGraph * pg, int nv);

//그래프의 리소스 해제
void GraphDestroy(ALGraph * pg);

//간선의 추가
void AddEdge(ALGraph * pg, int fromV, int toV);

//간선의 정보 출력
void ShowGraphEdgeInfo(ALGraph * pg);

#endif /* ALGraph_h */

```

인접 리스트의 핵심은 연결리스트이다. enum {A, B, C, D, E, F, G, H, I, J};  필요한 정점의 수가 열 개를 넘으면 이름을 더 추가하면 되고, 또한 프로그램의 성격에 따라서 이름도 바꿀 수 있다.

그럼 위에 선언된 함수들을 기반으로 그래프를 구성하는 main 함수와 그 실행결과를 보이겠으니 이를 통해서 위에 선언된 함수들이 어떻게 사용되는지 해보자.

```c
ALGraph graph;
GraphInit(&graph, 5);

AddEdge(&graph, A, B);
AddEdge(&graph, A, D);
AddEdge(&graph, B, C);
AddEdge(&graph, C, D);
AddEdge(&graph, D, E);
AddEdge(&graph, E, A);

ShowGraphEdgeInfo(&graph);

GraphDestroy(&graph);
```


위의 main 함수에서 보이듯이 그래프의 생성 및 초기화는 다음 두문장에 의해서 이뤄진다.
ALGraph graph;
GraphInit(&graph, 5);

특히 GraphInit 함수의 호출문에서 두 번째 인자로 5를 전달했는데, 이는 정점의 수를 의미한다.즉 위의 두 문장으로 인해서 5개의 정점으로 이뤄진 그래프가 형성되는 것이다. 그리고 이때 생성되는 정점의 이름은 각각 A,B,C,D,E가 되도록 그래프를 구현할것이다.

**그래프의 구현**
먼저 GraphInit함수를 보자. 이함수를 보면 구현의 방식이 전체적으로 머릿속에서 그려질것이다. 참고로 아래의 함수에서 생성하는 연결리스트는 DLinkedList.h와 DLinkedList.c에 선언 및 정의된 연결리스트이다.

```c
void GraphInit(ALGraph * pg, int nv)
{
    int i;

    //정점의 수에 해당하는 길이의 리스트 배열을 생성한다.
    pg->adjList = (List*)malloc(sizeof(List) * nv); // 간선정보를 저장할 리스트 생성
    // 정점의 수만큼 리스트를 생성해줘야함.
    pg->numV = nv; // 정점의 수는 nv에 저장된 값으로 결정
    pg->numE = 0; // 초기의 간선 수는 0 개

    // 정점의 수만큼 생성된 리스트들을 초기화한다.
    for(i=0; i<nv; i++)
    {
        ListInit(&(pg->adjList[i]));
        SetSortRule(&(pg->adjList[i]), WhoIsPrecede);// 리스트의 정렬 기준을 설정
    }
}
```

생성된 연결리스트에 정렬기준을 설정하고 있는데, 이것은 알파벳순으로 출력을 유도하기 위해서 절열기준을 설정했다.

위의 함수를 보았으니 GraphDestroy함수가 필요한 이유가 무엇인지, 이함수내에서 어떠한 일을 해야 하는지 판단할 수 있을 것이다.

```c
void GraphDestroy(ALGraph * pg){
  if(pg->adjList != NULL)
    free(pg->adjList); // 동적으로 할당된 연결리스트의 소멸
}
```

이제 간선의 추가를 담당하는 AddEde함수를 보자, 이함수의 정의를 통해서 간선의 이름이 지니는 상수 값의 으미도 함께 파악하자.

```c
// 간선의 추가
void AddEdge(ALGraph * pg, int fromV, int toV)
{
    //정점 fromV의 연결리스트에 정점 toV의 정보 추가
    LInsert(&(pg->adjList[fromV]), toV);

    //정점 toV의 연결 리스트에 정점 fromV의 정보 추가
    LInsert(&(pg->adjList[toV]), fromV);
    pg->numE += 1;
}
```

무방향 그래프의 간선을 추가하는 것이므로 위의 함수에서는 LInsert함수를 두 번 호출하였다. 만약에 구현하는 것이 방향 그래프였다면, LInsert함수의 호출은 한번이면 충분했을것이다. 그리고 연결리스트를 지정하는 인덱스 값으로 fromV와 toV가 사용되었음에 주목하자! 예를 들어서 fromV와 toV로 각각 A와 B가 전달되면, LInsert 함수의 호출형태는 다음과 같아진다.

LInsert(&(pg->adjList[A]),B);
LInsert(&(pg->adjList[B]),A);

이렇듯 정점의 이름이 바로 사용될 수 있는 이유는, 정점의 이름이 의미하는 바가 상수이고, 그값이 0에서부터 시작해서 1씩 증가히기 때문이다.
![](https://i.imgur.com/4sjayn7.png)
쉽게 말해 위 첫번째 그림을 그래프로 구현한것이다. 위에 정점과 간선만 다를뿐 원리는 같다. 강정점들은 연결리스트로 구성되어있고, 그 정점들은 리스트(배열)로 구성되어있다.

[깃허브소스](https://github.com/minwan1/Algorithm/tree/master/ALGraph/ALGraph)

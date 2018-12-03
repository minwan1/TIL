## 그래프
그래프는 트리와 비슷하게 노드와 엣지로 구성되어 있습니다. 그래프에서는 노드를 버텍스, 엣지(간선)를 아크라고 부릅니다. 사실 그냥 노드랑 엣지로 불러도 상관 없지만, 있어보이려면 버텍스와 아크로 부르도록 합시다.


## 그래프 종류

* 무향그래프
* 유향그래프


## 그래프를 구현하는 두 가지 방법
그래프를 구현하는 방법에도 배열을 이용하는 방법과 연결리스트를 이용하는 방법으로 나뉜다. 하지만 그래프에서는 이들 각각을 다음과 같이 표현한다.
* 인접 행렬 기반 그래프 정방 행렬을 활용[가로셀이가 같은 행렬을 의미]
* 인접 리스트 기반 그래프  연결 리스트를 활용

### 행렬
정방 행렬은 가로세로의 길이가 같은 행렬을 의미하는데, 이러한 행렬은 2차워 배열로 표현한다. 즉 위에서 말하는 정방 행렬은 2차원 배열을 뜻하는것이다.

![](https://i.imgur.com/pd6PD1P.png)

위그림에서 보이듯이 정점이 2개이면 가로세로의 길이가 4인 2차원 배열을 선언한다. 그리고 두정점이 연결되어 있으면 1로, 연결되어 있지 않으면 0으로 표시한다. 단, 간선의 방향성이 없기 때문에 하나의 간선에 대해서 두 개의 지점을 1로 표시해야한다. 예를 들어서[m][n]인 지점이 1로 표시되면, [n][m]인 지점도 1로 표시되어야 한다. 때문에 행렬은 대각선을 기준으로 대칭을 이룬다.

위에서 2번 째그림은 방향그래프의 인접행렬이다. 위 그림에서 보이듯이, A에서 B로 향하는 간선의 표시를 위해서 [0][1]인 위치를 1로 표시하였다. 그리고 이것이다 저눕이다. 때문에 무방향 그래프와 달리 대칭을 이루지 않는다.


### 인접 리스트
이번에는 인접 리스트 기반의 그래프 표현방법을 살펴보자,
먼저 아래는 무바방향 그래프의 표현방법이다.
![](https://i.imgur.com/4sjayn7.png)
위에 첫번째 그림은 각각의 정점은 자신과 연결된 정점의 정보를 담기 위해서 하나의 연결리스트르를 갖는다. 그리고 각각의 정점에 연결된 간선의 정보는 가각의 연결리스트에 담아야한다.

위에서 두 번째 그림은 인접 리스트를 기반으의 방향 그래프의 표현 방법을 보인것이다. 방향 그래프에서는 각 정점별로 가리키는 정점의 정보만을 연결리스트에 담는다. 때문에 무방향 그래프에 비해서 추가된 노드의 수가 반으로 준다. 위의 2번쨰 그림 그래프에서 정점 A가 B,C,D를 가리킨다. 때문에 점점 A의 연결리스트에서만 노드의 정보가 추가되었다.


그래프 탐색에는 2가지 방법이 존재함. BFS, DFS가 존재한다.


## BFS란

BFS는 현재 위치에 인접한 모든 위치의 노드를 방문하고, 그 이웃 노드들의 또 다른 이웃 노드들을 방문하는 것은 그 다음에 진행하는 것을 의미합니다. BFS를 가장 효과적으로 구현하는 방법은 큐를 이용해서 순환적 형태로 구현하는 것이 가장 깔끔합니다.

![bfs](/assets/bfs_qlzcs7ezv.jpg)

![](https://i.imgur.com/U4UmF9I.jpg)


```java
class Graph{
    class Node{
        int data;
        boolean marked;
        LinkedList<Node> adjacent;

        public Node(int data) {
            this.data = data;
            this.marked = false;
            this.adjacent = new LinkedList<>();
        }
    }

    Node[] nodes;

    Graph(int size){
        nodes = new Node[size];
        for(int i = 0; i < size; i++){
            nodes[i] = new Node(i);
        }
    }

    void addEdge(int i1, int i2){
        Node n1 = nodes[i1];
        Node n2 = nodes[i2];

        if(!n1.adjacent.contains(n2)){
            n1.adjacent.add(n2);
        }

        if(!n2.adjacent.contains(n1)){
            n2.adjacent.add(n1);
        }
    }

    void bfs(){
        bfs(0);
    }

    void bfs(int index){
        Node root = nodes[index];
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        root.marked = true;

        while(!queue.isEmpty()){
            Node r = queue.poll();
            for(Node n : r.adjacent){
                if(!n.marked){
                    n.marked = true;
                    queue.offer(n);
                }
            }
            visit(r);
        }
    }

    void visit(Node n){
        System.out.printf(n.data + " ");
    }

}

public class Bfs {
    public static void main(String[] args){

        Graph g = new Graph(9);
        g.addEdge(0, 1);
        g.addEdge(1, 2);
        g.addEdge(1, 3);
        g.addEdge(2, 4);
        g.addEdge(2, 3);
        g.addEdge(3, 4);
        g.addEdge(3, 5);
        g.addEdge(5, 6);
        g.addEdge(5, 7);
        g.addEdge(6, 8);
        g.bfs(0);

    }
}
```
다익스트라 알고리즘
* http://bumbums.tistory.com/4




# 그래프란
그래프 탐색이란
하나의 정점으로부터 시작하여 차례대로 모든 정점들을 한 번씩 방문하는 것
Ex) 특정 도시에서 다른 도시로 갈 수 있는지 없는지, 전자 회로에서 특정 단자와 단자가 서로 연결되어 있는지


# BFS(너비우선 탐색) : Stack 
시작 정점에서 가장 가까운 노드부터 차례대로 탐색해나가 마지막 노드까지 검색해나가는 기법을 말한다. 아래는 너미우선을 그림으로 표현한것이다.


최단거리

DFS란




![bfs](/assets/bfs_qlzcs7ezv.jpg)


1 에서 2, 3, 4를 더해서 572가 나오는 같의 전체 숫자를 구해라.

# DFS(깊이우선 탐색) : Queue
시작 정점의 한 방향으로 갈 수 있는 경로가 있는 곳까지 깊이 탐색해 가다가 더 이상 갈 곳이 없게 되면, 가장 마지막에 만났던 갈림길 간선이 있는 정점으로 되돌아와서 다른 방향의 간선으로 탐색을 계속 반복하여 결국 모든 정점을 방문하는 순회방법.

DFS는 백트래킹(back-tracking)에서 쓰이는 기법 중에 하나로, 다음과 같이 동작합니다.



![dfs](/assets/dfs_tft8e70gu.jpg)

계산기같은 뭔가 점수처리하는것에서 사용


일단 아래참조 
http://blog.naver.com/PostView.nhn?blogId=heojh93&logNo=220019573185&parentCategoryNo=&categoryNo=18&viewDate=&isShowPopularPosts=true&from=search




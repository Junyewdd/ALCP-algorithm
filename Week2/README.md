# ✅ 최단 경로

최단 경로를 구하는 문제가 나왔을 때, BFS / 다익스트라 / 플로이드-와샬 중 어느 것을 써야 할 지 헷갈리곤 했다. 적절한 최단 경로 알고리즘을 선택하는 방법을 알아보자.

## 1️⃣ BFS

- 그래프의 모든 노드를 방문하는 너비 우선 탐색 알고리즘이다.
- DFS는 깊이 우선 탐색 알고리즘으로, BFS와 DFS의 차이를 말할 때 **너비 우선과 깊이 우선을 이야기하는데, 이는 단순히 개념적인 차이이다.** 
- **동시성이 있는 경우 BFS로 풀어야 한다!**
- 대표적으로 [[백준/G5] 토마토](https://www.acmicpc.net/problem/7576) 문제의 경우, 토마토 여러 개가 있고 상하좌우 토마토들을 익게 만든다면, 여러 토마토가 동시에 하루가 지날 때마다 주변의 토마토에 영향을 끼치므로 동시성을 가진다.
- DFS로 풀 수 있는 모든 문제는 BFS로 풀 수 있지만, 역은 성립하지 않는다.
- **답이 되는 경로가 여러 개인 경우에도 최단 경로임을 보장한다.**
- **가중치가 없는 그래프의 최단 경로를 찾는 경우에 이용한다.**

### 슈도 코드
```
create a queue Q
mark v as visited and put v into Q
while Q is non-empty
	remove the head u of Q
	mark and enqueue all (unvisited) neighbours of u
```

### 문제

[[백준/골드4] 숨바꼭질 2](https://www.acmicpc.net/problem/12851) 

수빈이의 위치를 아래와 같이 그래프 형태로 표현할 수 있다. 동생의 위치에 도달하는 가장 빠른 위치를 찾기 위해 BFS 알고리즘을 이용할 수 있고, 기본적으로 모든 노드를 방문하는 알고리즘이기 때문에 빠른 시간으로 찾는 방법의 수도 알 수 있다.

`queue`에 넣는 위치를 조건으로 판단할 수 있다. 단순히 모든 노드를 방문한다면 시간 복잡도가 O(N^2)이기 때문에 시간 제한에 걸리므로, `visited` 배열을 이용해 중복 탐색을 막아야 한다.

<img width="434" alt="image" src="https://github.com/RumosZin/ALCP-algorithm/assets/81238093/b324c4b7-f235-4e09-9df6-760efefb6639">

## 2️⃣ 다익스트라

- 한 정점에서 다른 정점까지의 최단 경로를 구하는 알고리즘이다.
- **도착 정점뿐만 아니라 모든 다른 정점까지의 최단 경로를 방문하면서 각 정점까지의 최단 경로를 모두 찾는다.**

### 동작

1. 출발 노드, 도착 노드를 설정한다.
2. **최단거리 테이블**을 초기화 한다.
3. 현재 위치한 노드의 인접 노드 중 **노드 방문 여부 확인 배열**을 확인해 방문하지 않은 노드 중 거리가 가장 짧은 노드를 선택하고 방문 처리한다.
4. 해당 노드를 거쳐 다른 노드로 넘어가는 간선 가중치를 계산해 최단거리 테이블을 초기화 한다.
5. 3번-4번을 반복한다.

최단 거리 테이블

- 1차원 배열로, N개 노드까지 오는데 필요한 최단 거리를 기록한다. N 크기의 배열을 선언하고 큰 값을 넣어서 초기화 한다.

노드 방문 여부 확인 배열

- 방문하지 않은 노드를 판별하는 1차원 배열로, 크기는 최단 거리 배열과 같다. `false`로 초기화 하여 방문하지 않음을 명시한다.

### 특징

- 방문하지 않은 노드 중 최단 거리인 노드를 선택하는 과정을 반복한다.
- **가중치가 양수일 때만 가능하다.**

### 예시 코드

```cpp
int Find_Shortest_Node()
{
    int Min_Dist, Min_Idx;
    Min_Dist = INF;
    Min_Idx = -1;
 
    for (int i = 1; i <= V; i++)
    {
        if (Select[i] == true) continue;
        if (Dist[i] < Min_Dist)
        {
            Min_Dist = Dist[i];
            Min_Idx = i;
        }
    }
    return Min_Idx;
}
 
void Update_Dist(int NewNode)
{
    for (int i = 1; i <= V; i++)
    {
        if (Select[i] == true) continue;
        if (Dist[i] > Dist[NewNode] + MAP[NewNode][i])
        {
            Dist[i] = Dist[NewNode] + MAP[NewNode][i];
        }
    }
}
 
void Dijkstra()
{
    for (int i = 1; i <= V; i++) Dist[i] = MAP[Start][i];
    Dist[Start] = 0;
    Select[Start] = true;
    
    for (int i = 0; i < V - 1; i++)
    {
        int NewNode = Find_Shortest_Node();
 
        Select[NewNode] = true;
        Update_Dist(NewNode);
    }
}
```

### 문제

[[백준/골드5] 숨바꼭질 3](https://www.acmicpc.net/problem/13549)

앞선 숨바꼭질 2 문제와 비슷하다. 조건이 다른 것이 있다면 수빈이가 위치 X에서 걷는다면 1초 후에 X-1 또는 X+1로 이동하지만, 순간 이동을 하는 경우에는 0초 후에 2*X의 위치로 이동한다. 

앞선 문제와 다르게, **다른 노드로 이동할 때 가중치에 차이가 생긴 것이다!**

이런 경우, **우선순위 큐를 이용한 BFS** 또는 **다익스트라 알고리즘**을 선택할 수 있다.

<img width="358" alt="image" src="https://github.com/RumosZin/ALCP-algorithm/assets/81238093/e214359c-1f29-476b-8f85-d892d97ab0a8">

## 3️⃣ 플로이드-와샬

- 다익스트라의 경우 한 지점에서 다른 지점까지의 최단 경로를 구하는 알고리즘이다. **플로이드-와샬은 모든 지점에서 다른 모든 지점까지의 최단 경로를 구하는 알고리즘이다.**
- 최단 거리 테이블에서 자기 자신의 비용은 0, 직접적으로 연결되지 않은 경로는 큰 값으로 설정한다.

### 동작

1. 그래프의 노드와 간선에 따라 최단거리 테이블을 갱신한다.
2. 1번 노드를 거쳐 가는 경우를 고려하여 테이블을 갱신한다.
3. 모든 노드에 대해서 2번을 반복한다.

### 예시 코드
```cpp
void initAdj() {
    int i, j;
    for (i = 0; i < V; i++)
        for (j = 0; j < V; j++) {
            adj[i][j] = INF;
            if (i == j)adj[i][j] = 0;
        }
}

// 시작 지점 : i
// 끝 지점 : j
// 경유 지점 : k
void floyd() {
    int k,j,i;
     
    for (k = 0; k < V; k++)
        for (i = 0; i < V; i++)
            for (j = 0; j < V; j++)
                adj[i][j] = MIN(adj[i][j], adj[i][k] + adj[k][j]);
}
```

### 문제

[[백준/골드2] 회의준비](https://www.acmicpc.net/problem/2610)

모든 참석자들의 의사전달시간 중 최댓값이 최소가 되도록 해야 한다. 이때 최댓값은 어떤 위원회의 위원이 다른 위원들에게 의견을 전달하기 위한 각 의사 전달 시간 중 가장 큰 값이다. 그 중에서 최소는, 각 위원들의 의사전달 시간 중 가장 작은 값이다.

플로이드-와샬 알고리즘을 이용해 모든 위원들에 대한 의사 전달 시간을 구하고, 위원들을 확인하면서 위원회를 생성한다. 플로 이드-와샬 알고리즘의 특성 상, 초기화 값이 INF이므로 만약 도달할 수 없는 INF 값이라면 서로 같은 위원회에 속하지 않음을 알 수 있다.

각 위원들의 의사 전달 시간 최댓값을 구하고, 그 중 최솟값을 구한다.

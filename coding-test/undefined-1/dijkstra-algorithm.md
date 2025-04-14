# Dijkstra Algorithm

### 1. 다익스트라 알고리즘이란?

> 그래프에서 하나의 시작 정점으로부터 다른 모든 정점까지의 **최단 경로(Shortest Path)** 를 구하는 알고리즘.
>
> 단, **모든 간선의 가중치가 0 또는 양수** 여야함. (음수 가중치 X)

#### 핵심 개념:

* 매번 **가장 가까운 노드** 를 선택해서, 그 노드를 통해 다른 노드로 가는 **더 짧은 경로가 있는지 갱신** 함.
* **그리디 알고리즘** 의 일종(한 번 선택한 노드는 다시 처리하지 않음)

***

### 2. 동작 방식(우선순위 큐 사용 버전)&#x20;

1. 출발 노드를 기준으로 **거리 배열** `dist[]` **를 초기화** (`dist[start] = 0` , 나머지는 `INF` )
2. `PriorityQueue` 를 이용해 **가장 가까운 노드를 계속 선택**
3. 해당 노드를 통해 다른 노드로 가는 **거리를 갱신**
4. **모든 노드를 처리할 때까지 반복**&#x20;

***

### 3. 자바 구현 (우선순위 큐 기반)&#x20;

```java
Class Edge implements Comparable<Edge> {
    int to, weight;
    Edge(int to, int weight) {
        this.to = to;
        this.weight = weight;
    }
    public int compareTo(Edge other) {
        return this.weight - other.weight;
    }
}

public void dijkstra(int start, List<List<Edge>> graph, int[] dist) {
    int n = graph.size();
    boolean[] visited = new boolean[n];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[start] = 0;
    
    PriorityQueue<Edge> pq = new PriorityQueue<>();
    pq.offer(new Edge(start, 0));
    
    while(!pq.isEmpty()) {
        Edge current = pq.poll();
        int curNode = current.to;
        
        if(visited[curNode]) continue;
        visited[curNode] = true;
        
        for(Edge e : graph.get(curNode)) {
            int next = e.to;
            int cost = dist[curNode] + e.weight;
            if(cost < dist[next]) {
                dist[next] = cost;
                pq.offer(new Edge(next, cost));
            }
        }
    }
}
```

#### 그래프 준비(인접 리스트)&#x20;

```java
List<List<Edge>> graph = new ArrayList<>();
for(int i = 0; i <= N; i++) {
    graph.add(new ArrayList<>());
}

graph.get(1).add(new Edge(2, 4));
graph.get(1).add(new Edge(3, 2));
graph.get(2).add(new Edge(3, 5));
// 등등 ...
```

***

### 4. 시간 복잡도

| 버전         | 시간복잡도              | 설명                     |
| ---------- | ------------------ | ---------------------- |
| 배열 사용      | `O(V²)`            | 작은 정점 수에서 사용 (V ≤ 500) |
| 우선순위 큐     | `O((V + E) log V)` | 보통의 Dijkstra 구현        |
| 힙 + 인접 리스트 | `O(E log V)`       | 희소 그래프에서 효율적           |

***

### 5. 사용 조건 정리

| 조건         | 가능 여부               |
| ---------- | ------------------- |
| 가중치 있는 그래프 | ✅                   |
| 양의 가중치     | ✅                   |
| 음의 가중치     | ❌ (Bellman-Ford 사용) |
| 무방향 그래프    | ✅                   |
| 방향 그래프     | ✅                   |
| 최단 경로 역추적  | ✅ (이전 노드 배열 활용)     |

***

### 6. 예제 - 간단한 입력 예

#### 문제

* 정점 수: 6개
* 간선 :&#x20;

```scss
1 → 2 (2)
1 → 3 (5)
2 → 3 (1)
2 → 4 (2)
3 → 4 (3)
```

* 시작 노드 : 1

#### 출력(1에서 각 정점까지 최단 거리)

```makefile
1번: 0
2번: 2
3번: 3
4번: 4
```

***

### 7. Dijkstra 로 자주 나오는 문제 유형&#x20;

| 유형          | 설명                              |
| ----------- | ------------------------------- |
| 기본 최단 경로    | 하나의 시작점 → 모든 노드 최단 거리           |
| 도착점 제한      | 시작 → 도착점 하나만                    |
| 경로 역추적      | 최단 거리뿐 아니라, "어떻게" 이동했는지도 출력     |
| 경유지 포함      | 특정 정점을 반드시 들러야 함 (경로 A → B → C) |
| 거리 제한       | 최단 경로가 특정 거리 이하인 경우만 유효         |
| 2차원 맵 최단 경로 | BFS보다 정밀한 거리 계산 필요할 때           |



***

### 8. 관련 알고리즘&#x20;

| 알고리즘               | 차이점                                             |
| ------------------ | ----------------------------------------------- |
| **BFS**            | 모든 간선 가중치가 동일할 때 Dijkstra 대신 사용 가능 (`O(V + E)`) |
| **Bellman-Ford**   | 음수 가중치 허용, 느림                                   |
| **Floyd-Warshall** | 모든 정점 간 최단 거리 (`O(N³)`), 정점 수 적을 때              |
| **A\***            | 휴리스틱을 사용한 최적화된 최단 경로 탐색 (게임, 네비게이션 등)           |



***

### 요약

```csharp
[Start]
   ↓
 우선순위 큐에서 가장 작은 거리 노드 꺼냄
   ↓
  인접 노드 확인
   ↓
  거리 갱신 시, 다시 큐에 넣음
   ↓
  모든 노드 처리될 때까지 반복
```


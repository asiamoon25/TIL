# DFS, BFS

### 1. DFS & BFS 란?

#### DFS(Depth-First Search, 깊이 우선 탐색)

* 한 노드를 방문한 후, **다음 노드로 "끝까지" 들어갔다가** , 더 이상 갈 곳이 없으면 **되돌아오는 방식의 탐색**
* 주로 **Stack** 자료구조 사용(혹은 재귀)

#### BFS (Breadth-First Search, 너비 우선 탐색)

* 한 노드를 방문한 후, **인접한 노드를 모두 큐에 넣고**, 큐에서 하나씩 꺼내며 탐색&#x20;
* **최단거리 탐색 문제** 에 유리
* **Queue** 자료구조 사용

***

### 2. 공통 개념

* **그래프 탐색 알고리즘** ( 트리도 그래프의 일종 )
* **방문 처리** 가 중요 (무한 루프 방지)
* 인접 리스트(`List<List<Integer>>` ) 나 인접 행렬 (`int[][]` ) 로 그래프 표현 가능

***

### 3. 코드 예제(Java)

#### 예제 그래프(무방향, 연결)

```
1 - 2
|   |
3 - 4
```

**인접 리스트 표현**

```java
List<List<Integer>> graph = new ArrayList<>();

for(int i = 0; i <= 4; i++) {
    graph.add(new ArrayList<>());
}

graph.get(1).addAll(List.of(2,3));
graph.get(2).addAll(List.of(1,4));
graph.get(3).addAll(List.of(1,4));
graph.get(4).addAll(List.of(2,3));

```

#### DFS (재귀)

```java
boolean[] visited = new boolean[5];

void dfs(int node) {
    visited[node] = true;
    System.out.println(node + " ");
    
    for(int next : graph.get(node)) {
        if(!visited[next]) {
            dfs(next);
        }
    }
}
```

* 호출 : `dfs(1)` &#x20;
* 출력 : `1 2 4 3` 또는 `1 3 4 2`  (탐색 순서에 따라 다름)

#### BFS

```java
boolean[] visited = new boolean[5];

void bfs(int start) {
    Queue<Integer> queue = new LinkedList<>();
    queue.offer(start);
    visited[start] = true;
    
    while(!queue.isEmptry()) {
        int node = queue.poll();
        System.out.println(node + " ");
        
        for(int next : graph.get(node)) {
            if(!visited[next]) {
                visited[next] = true;
                queue.offer(next);
            }
        }
    }
}
```

* 호출 : `bfs(1)` &#x20;
* 출력 : `1 2 3 4`&#x20;

***

### 4. DFS vs BFS 비교

| 항목     | DFS                                    | BFS                           |
| ------ | -------------------------------------- | ----------------------------- |
| 전략     | 한 방향으로 끝까지 파고듦                         | 가까운 노드부터 탐색                   |
| 자료구조   | 스택 / 재귀                                | 큐                             |
| 구현 난이도 | 재귀면 간단, 반복은 복잡                         | 반복문만으로 가능                     |
| 사용 예시  | 모든 경우의 수 탐색 (백트래킹, 미로, 그래프 연결 요소 탐색 등) | 최단 거리, 레벨 탐색 (최단 경로, 퍼지는 것 등) |
| 속도     | 깊이 → 느릴 수 있음                           | 가까운 노드부터 탐색 → 빠름              |
| 메모리 사용 | 깊이가 깊으면 스택 많음                          | 큐에 많은 노드가 들어감                 |

***

### 5. 실제 활용 예시

| 문제 유형                  | DFS | BFS                      |
| ---------------------- | --- | ------------------------ |
| 미로 찾기                  | ✅   | ✅ (최단 거리 찾을 때 BFS가 더 적합) |
| 연결 요소 개수 구하기           | ✅   | ✅                        |
| 사이클 탐지                 | ✅   | ✅                        |
| 섬의 개수 구하기 (Flood Fill) | ✅   | ✅                        |
| 퍼지는 문제 (불, 바이러스 등)     | ❌   | ✅                        |
| 최단 거리 구하기              | ❌   | ✅                        |
| 백트래킹 (n-Queen, 조합)     | ✅   | ❌                        |

***

### 6. 그래프 표현 방법

#### 인접 리스트(Sparse Graph 에 적합)

```java
List<List<Integer>> graph = new ArrayList<>();
```

#### 인접 행렬(Dense Graph 에 적합)

```java
int[][] graph = new int[N+1][N+1];
```

***

### 7. 응용 예시

#### 미로 최단 경로(BFS)

```java
int[][] maze = {
    {1,1,0,1},
    {0,1,0,1},
    {0,1,1,1},
    {1,0,0,1}
};
int[][] dist = new int[4][4];

Queue<int[]> q = new LinkedList<>();
q.offer(new int[]{0, 0});
dist[0][0] = 1;

int[] dx = {-1,1,0,0}, dy = {0,0,-1,1};

while (!q.isEmpty()) {
    int[] cur = q.poll();
    int x = cur[0], y = cur[1];

    for (int i = 0; i < 4; i++) {
        int nx = x + dx[i], ny = y + dy[i];
        if (nx < 0 || ny < 0 || nx >= 4 || ny >= 4) continue;
        if (maze[nx][ny] == 0 || dist[nx][ny] != 0) continue;

        dist[nx][ny] = dist[x][y] + 1;
        q.offer(new int[]{nx, ny});
    }
}
System.out.println(dist[3][3]);  // 도착지까지 최단 거리
```

***

### 8. 정리

| 포인트    | DFS        | BFS          |
| ------ | ---------- | ------------ |
| 구현법    | 재귀 / Stack | Queue        |
| 전형적 패턴 | 백트래킹, 완전탐색 | 최단거리, 레벨순 탐색 |
| 방문순서   | 깊이 우선      | 너비 우선        |
| 트리 구조  | 후위 순회와 유사  | 레벨 순회        |


# 답

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {

        int n = graph.length;
        int[] visited = new int[n]; // 0: not visited, 1: visiting, 2: safe
        List<Integer> result = new ArrayList<>();

        // DFS to check if a node is safe
        for(int i = 0; i < n; i++){
            if (dfs(i, graph, visited)){
                result.add(i);
            }
        }
        return result;
    }

    private boolean dfs(int node, int[][] graph, int[] visited) {
        if(visited[node] > 0) {
            return visited[node] == 2; // Return true if alreay marked as safe
        }

        visited[node] = 1; // Mark as visited
        for(int neighbor : graph[node]) {
            if (!dfs(neighbor, graph, visited)) {
                return false; // If any neighbor is unsafe, current node is unsafe
            }
        }
        visited[node] = 2; // Mark as safe
        return true;
    }
}
```


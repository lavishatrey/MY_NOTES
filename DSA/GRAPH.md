# Graph Data Structures & Algorithms — Complete Notes (Markdown)

*All code in C++ (LeetCode-style `class Solution`) — explanations, intuition, comments, and time/space complexity included.*

> **How to use:** Each section contains (1) concept + intuition, (2) key variants, (3) a clean C++ implementation in a `Solution` class/function format similar to LeetCode, (4) comments inside code, and (5) time & space complexity.


---

# Table of Contents

1. Graph basics & representations
2. Traversals: BFS and DFS
3. Topological sort (Kahn + DFS)
4. Shortest paths: Dijkstra, Bellman-Ford, 0-1 BFS, Shortest path on DAG
5. All-pairs shortest paths: Floyd–Warshall
6. Minimum Spanning Tree: Kruskal (DSU) and Prim (heap)
7. Strongly Connected Components: Kosaraju & Tarjan
8. Bridges & Articulation Points (cut edges/vertices)
9. Union-Find (Disjoint Set Union) — utility & patterns
10. Bipartite check (BFS/DFS & coloring)
11. Eulerian Path/Circuit (Hierholzer)
12. A\* (brief) & heuristics (optional)
13. Patterns, tips, and complexity summary table

---

# 1. Graph basics & representations

**Concept:** Graph $G = (V, E)$; directed/undirected, weighted/unweighted.

**Representations:**

* Adjacency list: `vector<vector<int>> adj;` or `vector<vector<pair<int,int>>> adj` for weights. (Preferred for sparse graphs.)
* Adjacency matrix: `vector<vector<int>> mat;` (useful for dense graphs, small n or when you need O(1) edge checks.)
* Edge list: `vector<tuple<int,int,int>> edges;` (useful for MST and Bellman-Ford).

**Space/time tradeoffs:**

* Adjacency list: `O(V + E)` memory; iterate neighbors in `O(deg(v))`.
* Matrix: `O(V^2)` memory; `O(1)` edge check.

---

# 2. Traversals: BFS and DFS

## BFS (Breadth-First Search)

**Use cases:** shortest path in unweighted graphs, level-order, bipartite check.
**Intuition:** Expand by layers — queue stores frontier.

```cpp
// BFS shortest distance from source in an unweighted graph
class Solution {
public:
    vector<int> bfsShortestPath(int n, vector<vector<int>>& adj, int src) {
        const int INF = 1e9;
        vector<int> dist(n, INF);
        queue<int> q;
        dist[src] = 0;
        q.push(src);
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : adj[u]) {
                if (dist[v] == INF) {
                    dist[v] = dist[u] + 1; // 1 per edge (unweighted)
                    q.push(v);
                }
            }
        }
        return dist; // INF means unreachable
    }
};
```

**Complexity:** Time `O(V + E)`, Space `O(V)` for queue & dist.

## DFS (Depth-First Search)

**Use cases:** topological sort, components, cycle detection, bridges/articulation, Euler paths (partially), recursion-based logic.
**Intuition:** go deep first; recursion/stack.

```cpp
// Recursive DFS to build a list of visited nodes (component)
class Solution {
public:
    void dfs(int u, vector<vector<int>>& adj, vector<int>& vis, vector<int>& comp) {
        vis[u] = 1;
        comp.push_back(u);
        for (int v : adj[u]) {
            if (!vis[v]) dfs(v, adj, vis, comp);
        }
    }

    vector<vector<int>> getComponents(int n, vector<vector<int>>& adj) {
        vector<int> vis(n, 0);
        vector<vector<int>> comps;
        for (int i = 0; i < n; ++i) {
            if (!vis[i]) {
                vector<int> comp;
                dfs(i, adj, vis, comp);
                comps.push_back(comp);
            }
        }
        return comps;
    }
};
```

**Complexity:** Time `O(V + E)`, Space `O(V)` recursion stack + visited.

---

# 3. Topological Sort

**Use cases:** DAG tasks scheduling, DP on DAG.

## Kahn’s Algorithm (BFS)

**Intuition:** Repeatedly remove nodes with in-degree 0.

```cpp
// Return topo order or empty if cycle
class Solution {
public:
    vector<int> topoKahn(int n, vector<vector<int>>& adj) {
        vector<int> indeg(n, 0);
        for (int u = 0; u < n; ++u)
            for (int v : adj[u]) indeg[v]++;
        queue<int> q;
        for (int i = 0; i < n; ++i)
            if (indeg[i] == 0) q.push(i);
        vector<int> order;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            order.push_back(u);
            for (int v : adj[u]) {
                if (--indeg[v] == 0) q.push(v);
            }
        }
        if ((int)order.size() != n) return {}; // cycle exists
        return order;
    }
};
```

**Complexity:** `O(V + E)` time, `O(V)` space.

## DFS-based Topo (reverse postorder)

**Intuition:** Do DFS and push nodes after visiting all neighbors; reverse result.

```cpp
class Solution {
public:
    void dfsTopo(int u, vector<vector<int>>& adj, vector<int>& vis, vector<int>& order) {
        vis[u] = 1;
        for (int v : adj[u]) if (!vis[v]) dfsTopo(v, adj, vis, order);
        order.push_back(u); // postorder
    }

    vector<int> topoDFS(int n, vector<vector<int>>& adj) {
        vector<int> vis(n, 0), order;
        for (int i = 0; i < n; ++i) if (!vis[i]) dfsTopo(i, adj, vis, order);
        reverse(order.begin(), order.end());
        // Does not detect cycles easily — add color array if needed.
        return order;
    }
};
```

**Complexity:** `O(V + E)` time.

---

# 4. Shortest Paths

## Dijkstra (non-negative weights)

**Intuition:** Greedy: always finalize the node with smallest tentative distance using a min-heap.

```cpp
#include <queue>
#include <vector>
using namespace std;

class Solution {
public:
    // adj: vector of {neighbor, weight}
    vector<long long> dijkstra(int n, vector<vector<pair<int,int>>>& adj, int src) {
        const long long INF = (1LL<<60);
        vector<long long> dist(n, INF);
        // min-heap of (distance, node)
        priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<>> pq;
        dist[src] = 0;
        pq.push({0, src});
        while (!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();
            if (d != dist[u]) continue; // stale
            for (auto &e : adj[u]) {
                int v = e.first; int w = e.second;
                if (dist[v] > d + w) {
                    dist[v] = d + w;
                    pq.push({dist[v], v});
                }
            }
        }
        return dist;
    }
};
```

**Complexity:** `O((V + E) log V)` or more commonly `O(E log V)` with binary heap. Space `O(V)`.

## Bellman–Ford (handles negative weights, detects negative cycle)

**Intuition:** Relax edges V-1 times; if we can relax further, there's a negative cycle.

```cpp
class Solution {
public:
    // edges: vector of {u, v, w}
    pair<vector<long long>, bool> bellmanFord(int n, vector<tuple<int,int,int>>& edges, int src) {
        const long long INF = (1LL<<60);
        vector<long long> dist(n, INF);
        dist[src] = 0;
        // Relax V-1 times
        for (int i = 0; i < n-1; ++i) {
            bool changed = false;
            for (auto &e : edges) {
                int u, v, w; tie(u,v,w) = e;
                if (dist[u] != INF && dist[v] > dist[u] + w) {
                    dist[v] = dist[u] + w;
                    changed = true;
                }
            }
            if (!changed) break;
        }
        // check negative cycle
        bool negCycle = false;
        for (auto &e : edges) {
            int u, v, w; tie(u,v,w) = e;
            if (dist[u] != INF && dist[v] > dist[u] + w) {
                negCycle = true;
                break;
            }
        }
        return {dist, negCycle};
    }
};
```

**Complexity:** `O(V * E)` time, `O(V)` space.

## 0-1 BFS (edges weights only 0 or 1)

**Intuition:** Use deque; push\_front for 0 edges and push\_back for 1 edges.

```cpp
#include <deque>
class Solution {
public:
    vector<int> zeroOneBFS(int n, vector<vector<pair<int,int>>>& adj, int src) {
        const int INF = 1e9;
        vector<int> dist(n, INF);
        deque<int> dq;
        dist[src] = 0; dq.push_back(src);
        while (!dq.empty()) {
            int u = dq.front(); dq.pop_front();
            for (auto &pr : adj[u]) {
                int v = pr.first, w = pr.second; // w in {0,1}
                if (dist[v] > dist[u] + w) {
                    dist[v] = dist[u] + w;
                    if (w == 0) dq.push_front(v);
                    else dq.push_back(v);
                }
            }
        }
        return dist;
    }
};
```

**Complexity:** `O(V + E)`, Space `O(V)`.

## Shortest path in DAG

**Intuition:** Topologically sort then relax edges in topo order — linear time.

```cpp
class Solution {
public:
    vector<long long> shortestPathDAG(int n, vector<vector<pair<int,int>>>& adj, int src) {
        // build indegree for topo
        vector<int> indeg(n, 0);
        vector<vector<int>> adjSimple(n);
        for (int u = 0; u < n; ++u)
            for (auto &pr : adj[u]) indeg[pr.first]++; // assume no multigraph, directed
        // get topo order using Kahn
        queue<int> q;
        for (int i = 0; i < n; ++i) if (indeg[i] == 0) q.push(i);
        vector<int> topo;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            topo.push_back(u);
            for (auto &pr : adj[u]) {
                int v = pr.first;
                if (--indeg[v] == 0) q.push(v);
            }
        }
        const long long INF = (1LL<<60);
        vector<long long> dist(n, INF);
        dist[src] = 0;
        for (int u : topo) {
            if (dist[u] == INF) continue;
            for (auto &pr : adj[u]) {
                int v = pr.first; int w = pr.second;
                if (dist[v] > dist[u] + w) dist[v] = dist[u] + w;
            }
        }
        return dist;
    }
};
```

**Complexity:** `O(V + E)` time.

---

# 5. All-Pairs Shortest Paths — Floyd–Warshall

**Intuition:** Dynamic programming over intermediate vertices. Works for moderate `n` (e.g., `n <= 400`).

```cpp
class Solution {
public:
    // dist matrix: n x n, INF if no edge; dist[i][i] = 0
    void floydWarshall(vector<vector<long long>>& dist) {
        int n = dist.size();
        for (int k = 0; k < n; ++k)
            for (int i = 0; i < n; ++i)
                for (int j = 0; j < n; ++j)
                    if (dist[i][k] < (1LL<<60) && dist[k][j] < (1LL<<60))
                        dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
    }
};
```

**Complexity:** `O(n^3)` time, `O(n^2)` space.

---

# 6. Minimum Spanning Tree (MST)

## Kruskal + DSU

**Intuition:** Sort edges by weight, union components greedily.

```cpp
#include <algorithm>
class DSU {
public:
    vector<int> p, r;
    DSU(int n): p(n), r(n,0) { for(int i=0;i<n;++i)p[i]=i; }
    int find(int x){ return p[x]==x?x:p[x]=find(p[x]); }
    bool unite(int a,int b){
        a=find(a); b=find(b);
        if(a==b) return false;
        if(r[a]<r[b]) swap(a,b);
        p[b]=a;
        if(r[a]==r[b]) r[a]++;
        return true;
    }
};

class Solution {
public:
    // edges: vector of {w, u, v}
    long long kruskal(int n, vector<tuple<int,int,int>>& edges) {
        sort(edges.begin(), edges.end()); // sorts by weight
        DSU dsu(n);
        long long mst = 0;
        for (auto &t : edges) {
            int w,u,v; tie(w,u,v) = t;
            if (dsu.unite(u,v)) mst += w;
        }
        return mst;
    }
};
```

**Complexity:** `O(E log E)` (sorting), space `O(V)`.

## Prim (using heap)

**Intuition:** Grow a single tree, always pick smallest crossing edge.

```cpp
#include <queue>
class Solution {
public:
    // adj: vector of {neighbor, weight}
    long long prim(int n, vector<vector<pair<int,int>>>& adj) {
        vector<int> vis(n, 0);
        long long total = 0;
        // min-heap of (weight, to)
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
        pq.push({0, 0}); // start from node 0
        while (!pq.empty()) {
            auto [w, u] = pq.top(); pq.pop();
            if (vis[u]) continue;
            vis[u] = 1;
            total += w;
            for (auto &e : adj[u]) {
                int v = e.first, wt = e.second;
                if (!vis[v]) pq.push({wt, v});
            }
        }
        // if not all nodes visited, graph disconnected
        return total;
    }
};
```

**Complexity:** `O(E log V)` time, `O(V)` space.

---

# 7. Strongly Connected Components (SCC)

## Kosaraju’s Algorithm

**Intuition:** 1) do DFS ordering (finish times), 2) reverse graph, 3) DFS in order to collect components.

```cpp
class Solution {
public:
    void dfs1(int u, vector<vector<int>>& adj, vector<int>& vis, vector<int>& order) {
        vis[u] = 1;
        for (int v: adj[u]) if (!vis[v]) dfs1(v, adj, vis, order);
        order.push_back(u);
    }
    void dfs2(int u, vector<vector<int>>& rev, vector<int>& vis, vector<int>& comp) {
        vis[u] = 1;
        comp.push_back(u);
        for (int v: rev[u]) if (!vis[v]) dfs2(v, rev, vis, comp);
    }

    vector<vector<int>> kosaraju(int n, vector<vector<int>>& adj) {
        vector<int> vis(n, 0);
        vector<int> order;
        for (int i = 0; i < n; ++i) if (!vis[i]) dfs1(i, adj, vis, order);
        vector<vector<int>> rev(n);
        for (int u = 0; u < n; ++u)
            for (int v: adj[u]) rev[v].push_back(u);
        fill(vis.begin(), vis.end(), 0);
        vector<vector<int>> sccs;
        for (int i = n-1; i >= 0; --i) {
            int u = order[i];
            if (!vis[u]) {
                vector<int> comp;
                dfs2(u, rev, vis, comp);
                sccs.push_back(comp);
            }
        }
        return sccs;
    }
};
```

**Complexity:** `O(V + E)` time.

## Tarjan’s Algorithm (single DFS)

**Intuition:** Use discovery time and low-link values to detect root of SCC.

```cpp
class Solution {
public:
    int timeCounter = 0;
    vector<int> disc, low, stStack;
    vector<char> onStack;
    vector<vector<int>> sccs;

    void tarjanDFS(int u, vector<vector<int>>& adj) {
        disc[u] = low[u] = ++timeCounter;
        stStack.push_back(u);
        onStack[u] = 1;
        for (int v : adj[u]) {
            if (disc[v] == 0) {
                tarjanDFS(v, adj);
                low[u] = min(low[u], low[v]);
            } else if (onStack[v]) {
                low[u] = min(low[u], disc[v]);
            }
        }
        if (low[u] == disc[u]) { // head of SCC
            vector<int> comp;
            while (true) {
                int v = stStack.back(); stStack.pop_back();
                onStack[v] = 0;
                comp.push_back(v);
                if (v == u) break;
            }
            sccs.push_back(comp);
        }
    }

    vector<vector<int>> tarjan(int n, vector<vector<int>>& adj) {
        disc.assign(n, 0); low.assign(n, 0); onStack.assign(n, 0);
        timeCounter = 0;
        for (int i = 0; i < n; ++i) if (disc[i] == 0) tarjanDFS(i, adj);
        return sccs;
    }
};
```

**Complexity:** `O(V + E)`.

---

# 8. Bridges & Articulation Points

**Intuition:** Use DFS timestamps and `low` values:

* An edge `(u,v)` is a bridge if `low[v] > disc[u]`.
* Vertex `u` is articulation if certain child conditions met (root child count >1 or `low[child] >= disc[u]`).

```cpp
class Solution {
public:
    int timeCounter;
    vector<int> disc, low, parent;
    vector<pair<int,int>> bridges;
    vector<int> articulation; // boolean 0/1 stored as int

    void dfs(int u, vector<vector<int>>& adj) {
        disc[u] = low[u] = ++timeCounter;
        int children = 0;
        for (int v : adj[u]) {
            if (disc[v] == 0) {
                children++;
                parent[v] = u;
                dfs(v, adj);
                low[u] = min(low[u], low[v]);
                // bridge
                if (low[v] > disc[u]) bridges.push_back({u,v});
                // articulation
                if (parent[u] == -1 && children > 1) articulation[u] = 1;
                if (parent[u] != -1 && low[v] >= disc[u]) articulation[u] = 1;
            } else if (v != parent[u]) {
                low[u] = min(low[u], disc[v]);
            }
        }
    }

    void findBridgesArticulation(int n, vector<vector<int>>& adj) {
        disc.assign(n, 0); low.assign(n, 0); parent.assign(n, -1);
        articulation.assign(n, 0); timeCounter = 0;
        for (int i = 0; i < n; ++i) if (disc[i] == 0) dfs(i, adj);
        // bridges & articulation now filled
    }
};
```

**Complexity:** `O(V + E)` time, `O(V)` space.

---

# 9. Union-Find (Disjoint Set Union — DSU)

**Use cases:** Kruskal, connectivity, offline queries, cycle detection in undirected graph.

```cpp
class DSU {
public:
    vector<int> parent, rank;
    DSU(int n) {
        parent.resize(n);
        rank.assign(n, 0);
        for (int i = 0; i < n; ++i) parent[i] = i;
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]); // path compression
        return parent[x];
    }
    bool unite(int a, int b) {
        a = find(a); b = find(b);
        if (a == b) return false;
        if (rank[a] < rank[b]) swap(a, b);
        parent[b] = a;
        if (rank[a] == rank[b]) rank[a]++;
        return true;
    }
};
```

**Complexity:** near `O(α(n))` amortized per op, nearly constant. Space `O(n)`.

---

# 10. Bipartite Check

**Intuition:** Two-coloring using BFS/DFS. If any edge connects same color -> not bipartite.

```cpp
class Solution {
public:
    bool isBipartite(int n, vector<vector<int>>& adj) {
        vector<int> color(n, -1);
        queue<int> q;
        for (int start = 0; start < n; ++start) {
            if (color[start] != -1) continue;
            color[start] = 0;
            q.push(start);
            while (!q.empty()) {
                int u = q.front(); q.pop();
                for (int v : adj[u]) {
                    if (color[v] == -1) {
                        color[v] = color[u]^1;
                        q.push(v);
                    } else if (color[v] == color[u]) return false;
                }
            }
        }
        return true;
    }
};
```

**Complexity:** `O(V + E)` time, `O(V)` space.

---

# 11. Eulerian Path / Circuit (Hierholzer’s algorithm)

**Definitions:**

* Undirected Eulerian circuit: connected & every vertex has even degree.
* Undirected Eulerian path (not circuit): exactly 0 or 2 vertices of odd degree.
* Directed variants: indegree/outdegree conditions.

**Intuition:** Repeatedly follow unused edges until stuck, then stitch cycles.

```cpp
#include <stack>
class Solution {
public:
    // For undirected graphs using adjacency list of multiset-like (index-based edges)
    vector<int> eulerianPathUndirected(int n, vector<vector<pair<int,int>>>& adj /* {neighbor, edge_id} */) {
        vector<int> used; // will be sized to number of edges
        int m = 0; // number of edges
        for (auto &v : adj) m += v.size();
        m /= 2; // undirected edges counted twice
        used.assign(m, 0);

        // find start: vertex with odd degree or 0
        int start = 0;
        for (int i = 0; i < n; ++i) if (adj[i].size() % 2 == 1) { start = i; break; }

        vector<int> res;
        stack<int> st;
        vector<int> ptr(n, 0);
        st.push(start);
        while (!st.empty()) {
            int u = st.top();
            while (ptr[u] < (int)adj[u].size() && used[adj[u][ptr[u]].second]) ptr[u]++;
            if (ptr[u] == (int)adj[u].size()) {
                // no more edges
                res.push_back(u);
                st.pop();
            } else {
                auto [v, id] = adj[u][ptr[u]++];
                used[id] = 1;
                st.push(v);
            }
        }
        reverse(res.begin(), res.end());
        // res holds eulerian trail/circuit vertices (size m+1)
        return res;
    }
};
```

**Complexity:** `O(V + E)`.

---

# 12. A\* Search (brief)

**Intuition:** Like Dijkstra but uses `f = g + h` where `h` is heuristic estimate to target (admissible & consistent for correctness). Useful in pathfinding (grids, maps).

```cpp
// Outline: requires problem-specific heuristic h(u)
class Solution {
public:
    // g: actual distance so far, h: heuristic
    // priority queue by f = g + h
};
```

**Complexity:** Depends on heuristic quality; worst-case similar to Dijkstra. Use when you have a good heuristic.

---

# 13. Patterns, Tips & Common LeetCode-style Templates

* **Graph representation:** For most LeetCode problems, parse into `vector<vector<int>>` or `vector<vector<pair<int,int>>>`.
* **Visited vs. distance:** Use `vector<int> vis` for visited, `vector<int> dist` when distances required.
* **Cycle detection:**

  * Directed: DFS color (0 = unvisited, 1 = visiting, 2 = done) or use indegree & Kahn detect.
  * Undirected: use parent pointer in DFS to avoid trivial back-edge.
* **Edge cases:** disconnected graphs, single node, self-loops, multiple edges.
* **When to choose BFS vs Dijkstra:** BFS if unweighted; Dijkstra if non-negative weights.
* **Negative edges:** Use Bellman-Ford — but avoid if `V * E` too big.

---

# Complexity Summary Table

| Algorithm              |         Time |    Space | Use-case notes                          |
| ---------------------- | -----------: | -------: | --------------------------------------- |
| BFS / DFS              |     `O(V+E)` |   `O(V)` | Traversals, shortest unweighted         |
| Dijkstra (binary heap) | `O(E log V)` |   `O(V)` | Non-negative weights                    |
| Bellman-Ford           |      `O(VE)` |   `O(V)` | Negative weights, detect negative cycle |
| 0-1 BFS                |     `O(V+E)` |   `O(V)` | Edge weights 0/1                        |
| Floyd–Warshall         |     `O(V^3)` | `O(V^2)` | All pairs, small n                      |
| Kruskal + DSU          | `O(E log E)` |   `O(V)` | MST, sort edges                         |
| Prim (heap)            | `O(E log V)` |   `O(V)` | MST, single-tree growth                 |
| Kosaraju / Tarjan      |     `O(V+E)` |   `O(V)` | SCCs                                    |
| Bridges/Articulation   |     `O(V+E)` |   `O(V)` | Cut edges/vertices                      |
| Eulerian Path          |     `O(V+E)` | `O(V+E)` | Build path/circuit                      |

---

# Appendix: Common LeetCode-style Class Templates

Below are a few condensed, ready-to-submit templates you can paste into LeetCode or your judge:

### BFS template (shortest unweighted distance)

```cpp
class Solution {
public:
    vector<int> shortestPathUnweighted(int n, vector<vector<int>>& adj, int src) {
        const int INF = 1e9;
        vector<int> dist(n, INF);
        queue<int> q;
        dist[src] = 0; q.push(src);
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : adj[u]) {
                if (dist[v] == INF) {
                    dist[v] = dist[u] + 1;
                    q.push(v);
                }
            }
        }
        return dist;
    }
};
```

### Dijkstra template

```cpp
class Solution {
public:
    vector<long long> dijkstra(int n, vector<vector<pair<int,int>>>& adj, int src) {
        const long long INF = (1LL<<60);
        vector<long long> dist(n, INF);
        priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<>> pq;
        dist[src] = 0; pq.push({0, src});
        while (!pq.empty()) {
            auto [d,u] = pq.top(); pq.pop();
            if (d != dist[u]) continue;
            for (auto &e : adj[u]) {
                int v = e.first; int w = e.second;
                if (dist[v] > d + w) {
                    dist[v] = d + w;
                    pq.push({dist[v], v});
                }
            }
        }
        return dist;
    }
};
```

---



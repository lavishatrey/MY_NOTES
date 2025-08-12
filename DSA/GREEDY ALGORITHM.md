 **Greedy Algorithms** 


---

## 1. What is Greedy?

Greedy algorithms build a solution step-by-step, **always choosing the locally optimal choice** with the hope it leads to a globally optimal solution.

**When to use:**

* Problem has **greedy choice property** (local optimum leads to global optimum)
* Problem has **optimal substructure** (optimal solution to subproblem helps solve bigger problem)

**Common pattern:** Sort data → pick best available choice → repeat.

---

## 2. Classic Problems from Striver’s Sheet

---

### 2.1. **Activity Selection Problem** (Interval Scheduling)

**Problem:** Given start & end times, choose max number of non-overlapping activities.

**Intuition:**
Pick activity with earliest finishing time → leaves most room for future activities.

**Algorithm:**

1. Sort activities by end time.
2. Pick the first activity, then always pick the next activity whose start ≥ last chosen’s end.

**Code:**

```cpp
class Solution {
public:
    int maxMeetings(vector<int>& start, vector<int>& end) {
        int n = start.size();
        vector<pair<int,int>> activities;
        for(int i=0; i<n; i++) activities.push_back({end[i], start[i]});
        sort(activities.begin(), activities.end()); // sort by end time
        
        int count = 1;
        int lastEnd = activities[0].first;
        for(int i=1; i<n; i++){
            if(activities[i].second > lastEnd){
                count++;
                lastEnd = activities[i].first;
            }
        }
        return count;
    }
};
```

**Time Complexity:** `O(n log n)` (sorting)
**Space:** `O(n)`

**Diagram:**

```
Sorted by end: (start,end)
(1,2), (3,4), (0,6), (5,7)...
Pick → (1,2) → (3,4) → (5,7)...
```

---

### 2.2. **Fractional Knapsack**

**Problem:** Maximize value you can carry with capacity W, but you can take fraction of items.

**Intuition:**
Take highest `value/weight` ratio first.

**Algorithm:**

1. Sort items by `value/weight` descending.
2. Take full items until can’t; take fraction of next.

**Code:**

```cpp
struct Item {
    int value, weight;
};

class Solution {
public:
    double fractionalKnapsack(int W, Item arr[], int n) {
        vector<pair<double, pair<int,int>>> items;
        for(int i=0; i<n; i++){
            double ratio = (double)arr[i].value / arr[i].weight;
            items.push_back({ratio, {arr[i].value, arr[i].weight}});
        }
        sort(items.rbegin(), items.rend()); // sort by ratio desc
        
        double totalValue = 0.0;
        for(auto &it : items){
            if(W >= it.second.second){
                totalValue += it.second.first;
                W -= it.second.second;
            } else {
                totalValue += it.first * W;
                break;
            }
        }
        return totalValue;
    }
};
```

**Time:** `O(n log n)`
**Space:** `O(n)`

---

### 2.3. **Minimum Coins to Make Value** (Greedy coin change)

**Works only when coin system is canonical** (like Indian currency, US coins).

**Algorithm:**

1. Sort coins descending.
2. Take as many of largest as possible, then next largest, etc.

**Code:**

```cpp
class Solution {
public:
    int minCoins(vector<int>& coins, int V) {
        sort(coins.rbegin(), coins.rend());
        int count = 0;
        for(int c : coins){
            if(V == 0) break;
            count += V / c;
            V %= c;
        }
        return count;
    }
};
```

**Time:** `O(n log n)`
**Space:** `O(1)`

---

### 2.4. **Huffman Coding** (Optimal prefix codes)

**Problem:** Minimize weighted path length in binary tree.

**Intuition:**
Merge two smallest frequencies repeatedly.

**Algorithm:**

* Use min-heap → repeatedly pop two smallest → push their sum.

**Code:**

```cpp
class Solution {
public:
    int huffmanCost(vector<int>& freq) {
        priority_queue<int, vector<int>, greater<int>> pq(freq.begin(), freq.end());
        int cost = 0;
        while(pq.size() > 1){
            int a = pq.top(); pq.pop();
            int b = pq.top(); pq.pop();
            cost += a + b;
            pq.push(a + b);
        }
        return cost;
    }
};
```

**Time:** `O(n log n)`
**Space:** `O(n)`

---

### 2.5. **Job Sequencing Problem**

**Problem:** Each job has a deadline & profit, takes 1 unit time; maximize profit.

**Intuition:**
Pick jobs with highest profit first; schedule them in latest possible free slot before deadline.

**Algorithm:**

1. Sort jobs by profit desc.
2. For each job, put it in last available slot before deadline.

**Code:**

```cpp
struct Job {
    int id, dead, profit;
};

class Solution {
public:
    vector<int> JobScheduling(Job arr[], int n) {
        sort(arr, arr+n, [](Job a, Job b){ return a.profit > b.profit; });
        int maxDeadline = 0;
        for(int i=0; i<n; i++) maxDeadline = max(maxDeadline, arr[i].dead);
        vector<int> slot(maxDeadline+1, -1);
        
        int count = 0, totalProfit = 0;
        for(int i=0; i<n; i++){
            for(int d = arr[i].dead; d > 0; d--){
                if(slot[d] == -1){
                    slot[d] = arr[i].id;
                    count++;
                    totalProfit += arr[i].profit;
                    break;
                }
            }
        }
        return {count, totalProfit};
    }
};
```

**Time:** `O(n log n + n*maxDeadline)`
**Space:** `O(maxDeadline)`

---

### 2.6. **Minimum Spanning Tree (MST)**

Greedy algorithms: **Kruskal’s** & **Prim’s**.

**Kruskal’s:**

* Sort edges by weight, union-find to avoid cycles.

**Code (Kruskal’s):**

```cpp
struct Edge {
    int u, v, w;
};
struct DSU {
    vector<int> parent, size;
    DSU(int n) : parent(n), size(n,1) { iota(parent.begin(), parent.end(), 0); }
    int find(int x){ return parent[x]==x ? x : parent[x]=find(parent[x]); }
    bool unite(int a, int b){
        a = find(a); b = find(b);
        if(a==b) return false;
        if(size[a] < size[b]) swap(a,b);
        parent[b] = a;
        size[a] += size[b];
        return true;
    }
};
class Solution {
public:
    int kruskalMST(int n, vector<Edge>& edges) {
        sort(edges.begin(), edges.end(), [](Edge a, Edge b){ return a.w < b.w; });
        DSU dsu(n);
        int mstWeight = 0;
        for(auto &e: edges){
            if(dsu.unite(e.u, e.v)){
                mstWeight += e.w;
            }
        }
        return mstWeight;
    }
};
```

---

### 2.7. **Dijkstra’s Algorithm** (Shortest path, non-negative weights)

**Greedy:** Pick node with smallest current distance, relax edges.

**Code:**

```cpp
class Solution {
public:
    vector<int> dijkstra(int n, vector<vector<pair<int,int>>>& adj, int src) {
        vector<int> dist(n, INT_MAX);
        dist[src] = 0;
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
        pq.push({0, src});
        while(!pq.empty()){
            auto [d, u] = pq.top(); pq.pop();
            if(d > dist[u]) continue;
            for(auto &[v, w] : adj[u]){
                if(dist[v] > d + w){
                    dist[v] = d + w;
                    pq.push({dist[v], v});
                }
            }
        }
        return dist;
    }
};
```

**Time:** `O((V+E) log V)`
**Space:** `O(V+E)`

---

## 3. Greedy Pitfalls

* Greedy doesn’t always work — e.g., Coin Change (non-canonical coins), Knapsack (0/1 variant).
* Always prove greedy choice property OR test with counterexamples.

---

## 4. Quick Striver Sheet Greedy Checklist

* ✅ Activity Selection
* ✅ Fractional Knapsack
* ✅ Min Coins
* ✅ Huffman Coding
* ✅ Job Sequencing
* ✅ MST (Kruskal & Prim)
* ✅ Dijkstra
* ✅ Gas Station Problem
* ✅ N Meetings in One Room (same as Activity Selection)
* ✅ Minimum Platforms Problem
* ✅ Candy Distribution (2-pass)

---


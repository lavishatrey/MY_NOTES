 **Dynamic Programming (DP)** cheat-sheet / notes :

* what the problem pattern is,
* how to define states,
* recurrence,
* intuition,
* an ASCII diagram where helpful,
* one or two canonical LeetCode-style C++ implementations (memoization + tabulation or only the usual approach),
* time & space complexity,
* common optimizations (rolling array, prefix sums, binary search, etc.).


# 1. DP fundamentals — What is DP? When to use it

Short definition: DP solves problems that can be broken into overlapping subproblems with optimal substructure. Use it when naive recursion repeats work.

Two principal approaches:

* Top-down memoization (recursion + cache)
* Bottom-up tabulation (fill table iteratively)

Key recipe:

1. Define the state(s) — a concise set of parameters that uniquely identify a subproblem.
2. Write the recurrence.
3. Decide base case(s).
4. Choose order to compute (for tabulation).
5. Optionally optimize space/time (rolling array, prefix sums, monotonic queue, bitmask, etc).

Example toy pattern: Fibonacci

* State: `f(n)` = n-th Fibonacci
* Recurrence: `f(n)=f(n-1)+f(n-2)`
* Base: `f(0)=0, f(1)=1`

C++ top-down and bottom-up:

```cpp
// Fibonacci - Leetcode style
class Solution {
public:
    // Top-down memo
    int fib_memo(int n, vector<int>& memo){
        if(n<=1) return n;
        if(memo[n]!=-1) return memo[n];
        return memo[n] = fib_memo(n-1,memo) + fib_memo(n-2,memo);
    }
    int fib(int n) {
        vector<int> memo(n+1, -1);
        return fib_memo(n, memo);
    }
    // Bottom-up (space optimized)
    int fib_iter(int n) {
        if(n<=1) return n;
        int a=0,b=1;
        for(int i=2;i<=n;i++){
            int c=a+b;
            a=b; b=c;
        }
        return b;
    }
};
```

Time: `O(n)`. Space: memo `O(n)` or optimized `O(1)`.

---

# 2. Pattern: 0/1 Knapsack (and general bounded DP)

**Problem:** choose items (each once) with weights & values to maximize value under capacity W.

State:

* `dp[i][w]` = max value using first `i` items with capacity `w`.

Recurrence:

* `dp[i][w] = max(dp[i-1][w], value[i] + dp[i-1][w-weight[i]] )` if `w >= weight[i]`

Base:

* `dp[0][*] = 0`, `dp[*][0] = 0`

Tabulation (space optimized to 1D loop from high→low to avoid reuse of item):

```cpp
// 0/1 Knapsack - LeetCode style
class Solution {
public:
    int knapSack(int W, vector<int>& wt, vector<int>& val) {
        int n = wt.size();
        vector<int> dp(W+1, 0);
        // iterate items
        for(int i=0;i<n;i++){
            // go backwards to prevent reusing the same item
            for(int w=W; w>=wt[i]; w--){
                dp[w] = max(dp[w], val[i] + dp[w - wt[i]]);
            }
        }
        return dp[W];
    }
};
```

Complexity: Time `O(nW)`, Space `O(W)` (tabulation 2D would be `O(nW)`).

Diagram (conceptual):

```
items -> i
capacity -> w
dp table: rows = items, cols = capacity
```

Notes: For large `W`, knapsack might be infeasible; consider value-based DP or meet-in-the-middle.

---

# 3. Unbounded Knapsack / Coin Change (combinations / permutations)

**Problem1 (coin change count):** number of ways to make amount with unlimited coins.

State:

* `dp[a]` = number of ways to make amount `a`.

Recurrence:

* For combinations (order not important): iterate coins outer, amount inner:
  `dp[a] += dp[a - coin]` for `a >= coin`.

Example (count combinations):

```cpp
// Coin Change 2 (ways) - LeetCode style
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        vector<long long> dp(amount+1,0);
        dp[0] = 1;
        for(int coin : coins){
            for(int a = coin; a <= amount; ++a){
                dp[a] += dp[a - coin];
            }
        }
        return (int)dp[amount];
    }
};
```

Time: `O(amount * number_of_coins)`. Space: `O(amount)`.

Permutation variant (order matters) switches loops (amount outer, coins inner), and watch for overflow.

---

# 4. Subset Sum & Partition problems (boolean knapsack)

**Subset Sum:** can we pick subset sum equal to target?

State:

* `dp[w]` = true if subset sum `w` is possible.

Recurrence:

* For item weight `wt`: iterate `w` from target→wt: `dp[w] = dp[w] || dp[w-wt]`

C++:

```cpp
// Partition Equal Subset Sum - LeetCode style
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if(sum % 2) return false;
        int target = sum / 2;
        vector<char> dp(target+1, 0);
        dp[0] = 1;
        for(int x : nums){
            for(int w=target; w>=x; --w){
                dp[w] = dp[w] | dp[w-x];
            }
        }
        return dp[target];
    }
};
```

Time: `O(n * target)`. Space: `O(target)`.

---

# 5. Longest Increasing Subsequence (LIS)

Two common approaches:

A) DP `O(n^2)`: `dp[i]` = LIS ending at `i`.
Recurrence: `dp[i]=1+max(dp[j] for j<i and a[j]<a[i])`.

B) Patience sorting / binary-search `O(n log n)` — maintain `tails[]`.

Provide both solutions:

```cpp
// LIS - LeetCode style (O(n log n) preferred)
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> tails; // tails[len-1] = smallest tail for increasing subseq of length len
        for(int x : nums){
            auto it = lower_bound(tails.begin(), tails.end(), x);
            if(it == tails.end()) tails.push_back(x);
            else *it = x;
        }
        return tails.size();
    }
    // O(n^2) version
    int lengthOfLIS_n2(vector<int>& nums) {
        int n=nums.size();
        if(n==0) return 0;
        vector<int> dp(n,1);
        int ans=1;
        for(int i=0;i<n;i++){
            for(int j=0;j<i;j++){
                if(nums[j] < nums[i]) dp[i] = max(dp[i], dp[j]+1);
            }
            ans = max(ans, dp[i]);
        }
        return ans;
    }
};
```

Complexities: `O(n log n)` time, `O(n)` space.

Diagram (tails idea):

```
numbers:  3 5 7 1 2 8
tails progression:
[3] -> [3,5] -> [3,5,7] -> [1,5,7] -> [1,2,7] -> [1,2,7,8] => length 4
```

---

# 6. Longest Common Subsequence (LCS) & Longest Common Substring

**LCS (Classic DP)**

State:

* `dp[i][j]` = length of LCS for `A[0..i-1]` and `B[0..j-1]`.

Recurrence:

* if `A[i-1]==B[j-1]`: `dp[i][j]=dp[i-1][j-1]+1`
* else `dp[i][j]=max(dp[i-1][j], dp[i][j-1])`

Space optimization: keep only two rows.

```cpp
// LCS length - LeetCode style
class Solution {
public:
    int longestCommonSubsequence(string a, string b) {
        int n=a.size(), m=b.size();
        vector<int> prev(m+1,0), cur(m+1,0);
        for(int i=1;i<=n;i++){
            for(int j=1;j<=m;j++){
                if(a[i-1]==b[j-1]) cur[j] = prev[j-1] + 1;
                else cur[j] = max(prev[j], cur[j-1]);
            }
            swap(prev, cur);
            fill(cur.begin(), cur.end(), 0);
        }
        return prev[m];
    }
};
```

Time: `O(nm)`. Space: `O(min(n,m))` with proper transposition.

---

# 7. Edit Distance (Levenshtein distance)

State:

* `dp[i][j]` = min ops to convert `A[0..i-1]` → `B[0..j-1]`.

Ops: replace, insert, delete.

Recurrence:

* if equal: `dp[i][j]=dp[i-1][j-1]`
* else: `dp[i][j]=1 + min(dp[i-1][j] (delete), dp[i][j-1] (insert), dp[i-1][j-1] (replace))`

```cpp
// Edit Distance - LeetCode style
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n = word1.size(), m = word2.size();
        vector<int> prev(m+1), cur(m+1);
        for(int j=0;j<=m;j++) prev[j] = j; // insert all
        for(int i=1;i<=n;i++){
            cur[0] = i; // delete all from word1
            for(int j=1;j<=m;j++){
                if(word1[i-1]==word2[j-1]) cur[j] = prev[j-1];
                else cur[j] = 1 + min({prev[j], cur[j-1], prev[j-1]});
            }
            swap(prev, cur);
        }
        return prev[m];
    }
};
```

Time: `O(nm)`. Space: `O(m)`.

---

# 8. DP on grids (paths, unique paths, min path sum)

Classic patterns:

* `dp[i][j]` ways to reach cell `(i,j)` or minimum cost to `(i,j)`.

Unique paths (only right/down):

* `dp[i][j] = dp[i-1][j] + dp[i][j-1]`

Min path sum:

* `dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])`

Space optimize by single row.

```cpp
// Minimum Path Sum - LeetCode style
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        vector<int> dp(m, 0);
        for(int i=0;i<n;i++){
            for(int j=0;j<m;j++){
                if(i==0 && j==0) dp[j] = grid[i][j];
                else {
                    int up = (i>0 ? dp[j] : INT_MAX);
                    int left = (j>0 ? dp[j-1] : INT_MAX);
                    dp[j] = grid[i][j] + min(up, left);
                }
            }
        }
        return dp[m-1];
    }
};
```

Time: `O(nm)`. Space: `O(m)`.

---

# 9. Matrix Chain Multiplication / Optimal parenthesization

Goal: order of multiplication to minimize scalar multiplications.

State:

* `dp[i][j]` = min cost to multiply matrices `i..j`.

Recurrence:

* `dp[i][j] = min_{k=i..j-1} (dp[i][k] + dp[k+1][j] + dims[i-1]*dims[k]*dims[j])`

Complexity: naive `O(n^3)` time, `O(n^2)` space.

```cpp
// Matrix Chain Multiplication (cost only)
class Solution {
public:
    long long matrixChainOrder(vector<int>& dims) {
        int n = dims.size() - 1;
        vector<vector<long long>> dp(n+1, vector<long long>(n+1, 0));
        for(int len=2; len<=n; ++len){
            for(int i=1; i+len-1<=n; ++i){
                int j = i+len-1;
                dp[i][j] = LLONG_MAX;
                for(int k=i; k<j; ++k){
                    long long cost = dp[i][k] + dp[k+1][j] + 1LL*dims[i-1]*dims[k]*dims[j];
                    if(cost < dp[i][j]) dp[i][j] = cost;
                }
            }
        }
        return dp[1][n];
    }
};
```

---

# 10. Bitmask DP (subset DP)

Used when `n ≤ 20` or so; state is a bitmask representing chosen elements.

Example: Traveling Salesman Problem (TSP) DP:

* `dp[mask][i]` = min cost to visit set `mask` and end at node `i`.

Recurrence:

* `dp[mask | (1<<j)][j] = min(dp[mask | (1<<j)][j], dp[mask][i] + cost[i][j])`

Complexity: `O(n^2 * 2^n)` time, `O(n * 2^n)` space.

Sketch (not full code due to length) — pattern same as classic TSP.

---

# 11. DP on Trees

Typical: tree DP where states at node depend on children. Two common patterns:

* Rooted tree DP: `dp[u][0/1]` meaning not take / take node (e.g., tree vertex cover, maximum independent set).
* Rerooting DP: compute dp with different roots using two-pass.

Example: Maximum independent set on tree (no two adjacent nodes choose):

```cpp
// Tree DP (Max Independent Set)
class Solution {
public:
    vector<vector<int>> adj;
    vector<vector<int>> dp; // dp[u][0]=max if u not taken, dp[u][1]=max if taken

    void dfs(int u, int p){
        dp[u][0] = 0;
        dp[u][1] = 1; // take u
        for(int v: adj[u]) if(v!=p){
            dfs(v,u);
            dp[u][0] += max(dp[v][0], dp[v][1]);
            dp[u][1] += dp[v][0];
        }
    }

    int maxIndependentSet(int n, vector<pair<int,int>>& edges){
        adj.assign(n, {});
        for(auto &e: edges){
            adj[e.first].push_back(e.second);
            adj[e.second].push_back(e.first);
        }
        dp.assign(n, vector<int>(2,0));
        dfs(0,-1);
        return max(dp[0][0], dp[0][1]);
    }
};
```

Time: `O(n)`. Space: `O(n)`.

---

# 12. Digit DP (count numbers with property)

Pattern: DP over digits with tight/leading-zero states.

State:

* `dp[pos][tight][leadingZero][otherParams]` memoized top-down.

Example pattern: count numbers ≤ N with certain property. Implementation requires careful handling of tight.

(Due to space, I give the pattern — say if you want a full `count numbers without digit 5` code.)

---

# 13. Monotonic Queue / Sliding Window Optimization of DP

When recurrence needs `dp[i] = max(dp[j] + f(i,j))` with monotonic property, use deque to maintain candidates.

Classic: `dp[i] = max(dp[j] + (i-j)*A[j])` where `A[j]` decreases/increases.

Use when naive `O(n^2)` is too slow. Also used for sliding window maximum (deque).

---

# 14. DP Optimization Techniques — common tricks

* **Prefix sums:** convert `O(n^2)` summation to `O(1)` queries.
* **Suffix sums / difference arrays**
* **Space optimization:** only keep required previous rows (rolling arrays).
* **Binary search in DP:** e.g., patience sorting for LIS → `O(n log n)`.
* **Convex hull trick / Li Chao tree:** optimize linear DP transitions `O(n log n)`.
* **Divide & Conquer DP optimization:** if quadrangle inequality holds; reduces `O(n^2)` to `O(n log n)`.

Example: prefix sums to optimize partition cost:

If recurrence uses `sum(i..j)`, precompute `pref[]` so `sum(i..j)=pref[j+1]-pref[i]`.

---

# 15. Common problem templates (quick lookup)

* 0/1 knapsack, subset sum, partition.
* Unbounded knapsack / coin change.
* LIS / LNDS.
* LCS / edit distance.
* DP on grids (unique paths, min path).
* DP on trees (rooted DP, rerooting).
* Bitmask DP (TSP, assignment).
* Digit DP.
* DP with monotonic queue.
* DP + binary search (LIS length using tails).
* DP optimizations: prefix sums, rolling arrays, convex hull trick.

---

# 16. Worked example — classic LeetCode problem with full explanation

**Problem:** LeetCode 152 — Maximum Product Subarray (DP-ish)

* Intuition: Because product of negatives flips sign, need both max and min product ending at `i`.

State:

* `maxProd[i]`, `minProd[i]` — max/min product subarray ending at i

Recurrence:

* `maxProd[i] = max(nums[i], nums[i] * maxProd[i-1], nums[i] * minProd[i-1])`
* `minProd[i] = min(nums[i], nums[i] * maxProd[i-1], nums[i] * minProd[i-1])`

Code:

```cpp
// Maximum Product Subarray - LeetCode style
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int n = nums.size();
        int maxProd = nums[0], minProd = nums[0], ans = nums[0];
        for(int i=1;i<n;i++){
            int x = nums[i];
            int cand1 = x;
            int cand2 = x * maxProd;
            int cand3 = x * minProd;
            int newMax = max({cand1, cand2, cand3});
            int newMin = min({cand1, cand2, cand3});
            maxProd = newMax;
            minProd = newMin;
            ans = max(ans, maxProd);
        }
        return ans;
    }
};
```

Time: `O(n)`, Space: `O(1)`.

---

# 17. How to practice & interview tips

* When given a problem, start with a recursive formulation: define `f(params)`.
* Identify overlapping subproblems → memoize.
* Convert to iterative DP if asked for better performance or simpler constant factors.
* Always state state size and complexity.
* Sketch small examples and base cases; if indices shift, be explicit.
* For knapsack-like problems, remember reverse iteration for 1D `0/1` behavior.
* For string DP, use indexing `i`/`j` as lengths to avoid off-by-one.

---

# 18. Short checklist for writing DP solutions in interviews

1. Explain what state (`i,j,...`) means.
2. Give recurrence.
3. Show base cases.
4. Choose memoization or tabulation (and show why order is valid).
5. Give complexity (`O(...)` time, `O(...)` space).
6. Mention possible optimizations.
7. Provide boundary/edge-case handling.

---

# ASCII Diagrams — quick references

1. DP table for LCS (i across rows, j across cols):

```
    ""  a  b  c
""   0  0  0  0
a    0  1  1  1
b    0  1  2  2
d    0  1  2  2
```

2. Knapsack dp table shape:

```
items -> rows (i)
weight -> cols (w)
dp[i][w] = best value with first i items and capacity w
```

3. Bitmask visualization for subset of 4 elements:

```
mask bits: 3 2 1 0  (4 elements)
mask 0101 => items 0 and 2 selected
```

---


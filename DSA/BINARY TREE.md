**Binary Tree notes Striver-Sheet style** 
---

# **1. Binary Tree Basics**

**Definition:**
A **binary tree** is a tree where each node has at most two children: `left` and `right`.

**LeetCode TreeNode definition (C++):**

```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
```

---

## **2. Traversals**

### 2.1 Recursive Traversals (DFS)

```cpp
class Solution {
public:
    void inorder(TreeNode* root, vector<int>& ans){
        if(!root) return;
        inorder(root->left, ans);
        ans.push_back(root->val);
        inorder(root->right, ans);
    }
    void preorder(TreeNode* root, vector<int>& ans){
        if(!root) return;
        ans.push_back(root->val);
        preorder(root->left, ans);
        preorder(root->right, ans);
    }
    void postorder(TreeNode* root, vector<int>& ans){
        if(!root) return;
        postorder(root->left, ans);
        postorder(root->right, ans);
        ans.push_back(root->val);
    }
};
```

**Time:** `O(n)`
**Space:** `O(h)` recursion stack

---

### 2.2 Iterative Traversals

* Inorder → stack
* Preorder → stack
* Postorder → 2 stacks or modified preorder

---

### 2.3 Level Order (BFS)

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        if(!root) return ans;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            int sz = q.size();
            vector<int> level;
            for(int i=0; i<sz; i++){
                auto node = q.front(); q.pop();
                level.push_back(node->val);
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
            }
            ans.push_back(level);
        }
        return ans;
    }
};
```

---

## **3. Height / Depth of Tree**

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root) return 0;
        return 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
};
```

**Time:** `O(n)`
**Space:** `O(h)`

---

## **4. Diameter of Binary Tree**

**Idea:** Diameter = max of:

1. Left diameter
2. Right diameter
3. Path through root = left height + right height

```cpp
class Solution {
public:
    int diameter;
    int height(TreeNode* root){
        if(!root) return 0;
        int lh = height(root->left);
        int rh = height(root->right);
        diameter = max(diameter, lh + rh);
        return 1 + max(lh, rh);
    }
    int diameterOfBinaryTree(TreeNode* root) {
        diameter = 0;
        height(root);
        return diameter;
    }
};
```

---

## **5. Balanced Binary Tree**

**Balanced:** height difference ≤ 1 for every node.

```cpp
class Solution {
public:
    int check(TreeNode* root){
        if(!root) return 0;
        int lh = check(root->left);
        if(lh == -1) return -1;
        int rh = check(root->right);
        if(rh == -1) return -1;
        if(abs(lh - rh) > 1) return -1;
        return 1 + max(lh, rh);
    }
    bool isBalanced(TreeNode* root) {
        return check(root) != -1;
    }
};
```

---

## **6. Lowest Common Ancestor (LCA)**

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root || root == p || root == q) return root;
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        if(left && right) return root;
        return left ? left : right;
    }
};
```

---

## **7. Maximum Path Sum**

**Path can start and end anywhere.**

```cpp
class Solution {
public:
    int ans;
    int gain(TreeNode* root){
        if(!root) return 0;
        int left = max(0, gain(root->left));
        int right = max(0, gain(root->right));
        ans = max(ans, left + right + root->val);
        return root->val + max(left, right);
    }
    int maxPathSum(TreeNode* root) {
        ans = INT_MIN;
        gain(root);
        return ans;
    }
};
```

---

## **8. Zigzag Level Order Traversal**

```cpp
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        if(!root) return ans;
        queue<TreeNode*> q;
        q.push(root);
        bool leftToRight = true;
        while(!q.empty()){
            int sz = q.size();
            vector<int> level(sz);
            for(int i=0; i<sz; i++){
                TreeNode* node = q.front(); q.pop();
                int idx = leftToRight ? i : sz - 1 - i;
                level[idx] = node->val;
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
            }
            leftToRight = !leftToRight;
            ans.push_back(level);
        }
        return ans;
    }
};
```

---

## **9. Boundary Traversal**

Order: root → left boundary → leaves → right boundary (reverse).

---

## **10. Vertical Order Traversal**

Use BFS with column indexing.

---

## **11. Top View / Bottom View**

* **Top View:** first node seen in each column from top.
* **Bottom View:** last node seen in each column from top.

---

## **12. Symmetric Tree**

```cpp
class Solution {
public:
    bool isMirror(TreeNode* a, TreeNode* b){
        if(!a && !b) return true;
        if(!a || !b) return false;
        return a->val == b->val &&
               isMirror(a->left, b->right) &&
               isMirror(a->right, b->left);
    }
    bool isSymmetric(TreeNode* root) {
        return isMirror(root->left, root->right);
    }
};
```

---

## **13. Flatten Binary Tree to Linked List**

```cpp
class Solution {
public:
    void flatten(TreeNode* root) {
        if(!root) return;
        flatten(root->right);
        flatten(root->left);
        root->right = prev;
        root->left = NULL;
        prev = root;
    }
private:
    TreeNode* prev = NULL;
};
```

---

## **14. Morris Traversal** (O(1) space inorder)

```cpp
class Solution {
public:
    vector<int> inorderMorris(TreeNode* root) {
        vector<int> ans;
        TreeNode* cur = root;
        while(cur){
            if(!cur->left){
                ans.push_back(cur->val);
                cur = cur->right;
            } else {
                TreeNode* prev = cur->left;
                while(prev->right && prev->right != cur){
                    prev = prev->right;
                }
                if(!prev->right){
                    prev->right = cur;
                    cur = cur->left;
                } else {
                    prev->right = NULL;
                    ans.push_back(cur->val);
                    cur = cur->right;
                }
            }
        }
        return ans;
    }
};
```

---


## **15. Construct Binary Tree from Traversals**

### **15.1 Preorder + Inorder**

**Intuition:**

* In preorder, the first element is always the root.
* In inorder, elements to the left of root are in the left subtree, to the right are in the right subtree.

We recursively build left and right subtrees using this split.

```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        unordered_map<int,int> inMap; // value -> index in inorder
        for(int i=0; i<inorder.size(); i++) inMap[inorder[i]] = i;
        int preIdx = 0;
        return build(preorder, inorder, preIdx, 0, inorder.size()-1, inMap);
    }
    
    TreeNode* build(vector<int>& preorder, vector<int>& inorder, int& preIdx,
                    int inStart, int inEnd, unordered_map<int,int>& inMap){
        if(inStart > inEnd) return NULL;
        
        int rootVal = preorder[preIdx++];
        TreeNode* root = new TreeNode(rootVal);
        
        int inRoot = inMap[rootVal];
        
        root->left = build(preorder, inorder, preIdx, inStart, inRoot - 1, inMap);
        root->right = build(preorder, inorder, preIdx, inRoot + 1, inEnd, inMap);
        
        return root;
    }
};
```

**Time:** `O(n)` (each node processed once)
**Space:** `O(n)` (hashmap + recursion)

---

### **15.2 Postorder + Inorder**

**Intuition:**

* In postorder, the last element is the root.
* We process from the back, building right subtree first, then left (reverse of preorder case).

```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        unordered_map<int,int> inMap;
        for(int i=0; i<inorder.size(); i++) inMap[inorder[i]] = i;
        int postIdx = postorder.size() - 1;
        return build(postorder, inorder, postIdx, 0, inorder.size()-1, inMap);
    }
    
    TreeNode* build(vector<int>& postorder, vector<int>& inorder, int& postIdx,
                    int inStart, int inEnd, unordered_map<int,int>& inMap){
        if(inStart > inEnd) return NULL;
        
        int rootVal = postorder[postIdx--];
        TreeNode* root = new TreeNode(rootVal);
        
        int inRoot = inMap[rootVal];
        
        // build right first (since we're going backward in postorder)
        root->right = build(postorder, inorder, postIdx, inRoot + 1, inEnd, inMap);
        root->left = build(postorder, inorder, postIdx, inStart, inRoot - 1, inMap);
        
        return root;
    }
};
```

**Time:** `O(n)`
**Space:** `O(n)`

---

## **16. Serialize and Deserialize Binary Tree**

*(LeetCode 297)*

**Intuition:**

* Use level-order traversal.
* `null` markers for missing children.
* Store as comma-separated string.

```cpp
class Codec {
public:
    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if(!root) return "";
        string s;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            TreeNode* node = q.front(); q.pop();
            if(node){
                s += to_string(node->val) + ",";
                q.push(node->left);
                q.push(node->right);
            } else {
                s += "#,";
            }
        }
        return s;
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        if(data.empty()) return NULL;
        stringstream ss(data);
        string val;
        getline(ss, val, ',');
        TreeNode* root = new TreeNode(stoi(val));
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            TreeNode* node = q.front(); q.pop();
            
            getline(ss, val, ',');
            if(val != "#"){
                node->left = new TreeNode(stoi(val));
                q.push(node->left);
            }
            
            getline(ss, val, ',');
            if(val != "#"){
                node->right = new TreeNode(stoi(val));
                q.push(node->right);
            }
        }
        return root;
    }
};
```

**Time:** `O(n)` both serialize and deserialize
**Space:** `O(n)`

---

## **17. Burn a Binary Tree (Minimum Time from Target Node)**

**Intuition:**

1. Build parent pointers for all nodes (BFS).
2. From target, BFS outward to left, right, and parent.
3. Count levels = time to burn.

```cpp
class Solution {
public:
    int minTime(TreeNode* root, int target) {
        unordered_map<TreeNode*, TreeNode*> parent;
        TreeNode* targetNode = findParents(root, parent, target);
        return bfsBurn(targetNode, parent);
    }
    
    TreeNode* findParents(TreeNode* root, unordered_map<TreeNode*, TreeNode*>& parent, int target){
        queue<TreeNode*> q;
        q.push(root);
        TreeNode* targetNode = NULL;
        while(!q.empty()){
            TreeNode* node = q.front(); q.pop();
            if(node->val == target) targetNode = node;
            if(node->left){
                parent[node->left] = node;
                q.push(node->left);
            }
            if(node->right){
                parent[node->right] = node;
                q.push(node->right);
            }
        }
        return targetNode;
    }
    
    int bfsBurn(TreeNode* targetNode, unordered_map<TreeNode*, TreeNode*>& parent){
        unordered_set<TreeNode*> vis;
        queue<TreeNode*> q;
        q.push(targetNode);
        vis.insert(targetNode);
        int time = -1;
        
        while(!q.empty()){
            int sz = q.size();
            time++;
            for(int i=0; i<sz; i++){
                TreeNode* node = q.front(); q.pop();
                if(node->left && !vis.count(node->left)){
                    vis.insert(node->left);
                    q.push(node->left);
                }
                if(node->right && !vis.count(node->right)){
                    vis.insert(node->right);
                    q.push(node->right);
                }
                if(parent.count(node) && !vis.count(parent[node])){
                    vis.insert(parent[node]);
                    q.push(parent[node]);
                }
            }
        }
        return time;
    }
};
```

**Time:** `O(n)`
**Space:** `O(n)`

---

## **18. Maximum Width of Binary Tree**

*(LeetCode 662)*

**Intuition:**

* Assign each node an index as if it were in a complete binary tree:
  `left = 2*i`, `right = 2*i+1`.
* For each level, width = last\_index - first\_index + 1.

```cpp
class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        if(!root) return 0;
        long long ans = 0;
        queue<pair<TreeNode*, long long>> q;
        q.push({root, 0});
        
        while(!q.empty()){
            int sz = q.size();
            long long first = q.front().second;
            long long last = first;
            for(int i=0; i<sz; i++){
                auto [node, idx] = q.front(); q.pop();
                idx -= first; // normalize index to avoid overflow
                last = idx;
                if(node->left) q.push({node->left, 2*idx});
                if(node->right) q.push({node->right, 2*idx+1});
            }
            ans = max(ans, last + 1);
        }
        return (int)ans;
    }
};
```

**Time:** `O(n)`
**Space:** `O(n)`


## **19. Count Complete Tree Nodes (O(log² n))**

```cpp
class Solution {
public:
    int leftHeight(TreeNode* root){
        int h = 0;
        while(root){ h++; root = root->left; }
        return h;
    }
    int rightHeight(TreeNode* root){
        int h = 0;
        while(root){ h++; root = root->right; }
        return h;
    }
    int countNodes(TreeNode* root) {
        if(!root) return 0;
        int lh = leftHeight(root);
        int rh = rightHeight(root);
        if(lh == rh) return (1 << lh) - 1;
        return 1 + countNodes(root->left) + countNodes(root->right);
    }
};
```

---

This list covers almost **all binary tree problems from Striver’s Sheet**:

* Traversals
* Height / Depth
* Diameter
* Balanced check
* LCA
* Max path sum
* Zigzag / Level order / Boundary / Vertical / Top / Bottom view
* Symmetry
* Flattening
* Morris traversal
* Construct from traversals
* Serialize / Deserialize
* Burning tree
* Width
* Count nodes in complete tree

---


 **Binary Search Tree (BST) notes** 
---

## **1. BST Basics**

**Definition:**
A Binary Search Tree is a binary tree where for every node:

* All values in the **left subtree** are **less** than the node’s value.
* All values in the **right subtree** are **greater** than the node’s value.

**LeetCode Node structure:**

```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
```

---

## **2. Search in a BST**

**Idea:** Use BST property to skip half of the tree.

```cpp
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        while(root && root->val != val){
            root = (val < root->val) ? root->left : root->right;
        }
        return root;
    }
};
```

**Time:** `O(h)` (h = height)
**Space:** `O(1)`

---

## **3. Insert into BST**

```cpp
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if(!root) return new TreeNode(val);
        if(val < root->val) root->left = insertIntoBST(root->left, val);
        else root->right = insertIntoBST(root->right, val);
        return root;
    }
};
```

**Time:** `O(h)`
**Space:** `O(h)` recursion stack

---

## **4. Delete Node in BST**

**Idea:** 3 cases:

1. Node is leaf → just remove.
2. Node has one child → replace node with child.
3. Node has two children → replace with inorder successor.

```cpp
class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int key) {
        if(!root) return NULL;
        if(key < root->val) root->left = deleteNode(root->left, key);
        else if(key > root->val) root->right = deleteNode(root->right, key);
        else {
            if(!root->left) return root->right;
            if(!root->right) return root->left;
            TreeNode* minNode = getMin(root->right);
            root->val = minNode->val;
            root->right = deleteNode(root->right, minNode->val);
        }
        return root;
    }
    
    TreeNode* getMin(TreeNode* node){
        while(node->left) node = node->left;
        return node;
    }
};
```

**Time:** `O(h)`
**Space:** `O(h)`

---

## **5. Validate BST**

```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        return validate(root, LONG_MIN, LONG_MAX);
    }
    
    bool validate(TreeNode* node, long minVal, long maxVal){
        if(!node) return true;
        if(node->val <= minVal || node->val >= maxVal) return false;
        return validate(node->left, minVal, node->val) &&
               validate(node->right, node->val, maxVal);
    }
};
```

**Time:** `O(n)`
**Space:** `O(h)`

---

## **6. Lowest Common Ancestor in BST**

**Idea:** Use property to skip search space.

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        while(root){
            if(p->val < root->val && q->val < root->val) root = root->left;
            else if(p->val > root->val && q->val > root->val) root = root->right;
            else return root;
        }
        return NULL;
    }
};
```

**Time:** `O(h)`
**Space:** `O(1)`

---

## **7. Inorder Successor in BST**

```cpp
class Solution {
public:
    TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
        TreeNode* succ = NULL;
        while(root){
            if(p->val < root->val){
                succ = root;
                root = root->left;
            } else {
                root = root->right;
            }
        }
        return succ;
    }
};
```

**Time:** `O(h)`
**Space:** `O(1)`

---

## **8. Kth Smallest Element in BST**

```cpp
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        stack<TreeNode*> st;
        while(true){
            while(root){
                st.push(root);
                root = root->left;
            }
            root = st.top(); st.pop();
            if(--k == 0) return root->val;
            root = root->right;
        }
    }
};
```

**Time:** `O(h + k)`
**Space:** `O(h)`

---

## **9. Convert Sorted Array to BST**

```cpp
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return build(nums, 0, nums.size()-1);
    }
    
    TreeNode* build(vector<int>& nums, int l, int r){
        if(l > r) return NULL;
        int mid = l + (r-l)/2;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = build(nums, l, mid-1);
        root->right = build(nums, mid+1, r);
        return root;
    }
};
```

**Time:** `O(n)`
**Space:** `O(log n)`

---

## **10. Convert BST to Greater Sum Tree**

```cpp
class Solution {
public:
    TreeNode* bstToGst(TreeNode* root) {
        int sum = 0;
        reverseInorder(root, sum);
        return root;
    }
    
    void reverseInorder(TreeNode* node, int& sum){
        if(!node) return;
        reverseInorder(node->right, sum);
        sum += node->val;
        node->val = sum;
        reverseInorder(node->left, sum);
    }
};
```

**Time:** `O(n)`
**Space:** `O(h)`

---

## **11. BST Iterator**

*(LeetCode 173)* — Space optimized inorder traversal.

```cpp
class BSTIterator {
public:
    stack<TreeNode*> st;
    
    BSTIterator(TreeNode* root) {
        pushLeft(root);
    }
    
    int next() {
        TreeNode* node = st.top(); st.pop();
        pushLeft(node->right);
        return node->val;
    }
    
    bool hasNext() {
        return !st.empty();
    }
    
private:
    void pushLeft(TreeNode* node){
        while(node){
            st.push(node);
            node = node->left;
        }
    }
};
```

**Time per operation:** `O(1)` amortized
**Space:** `O(h)`

---

## **12. Recover BST (Fix Swapped Nodes)**

```cpp
class Solution {
public:
    TreeNode *first = NULL, *second = NULL, *prev = new TreeNode(INT_MIN);
    
    void recoverTree(TreeNode* root) {
        inorder(root);
        swap(first->val, second->val);
    }
    
    void inorder(TreeNode* root){
        if(!root) return;
        inorder(root->left);
        if(!first && prev->val > root->val) first = prev;
        if(first && prev->val > root->val) second = root;
        prev = root;
        inorder(root->right);
    }
};
```

**Time:** `O(n)`
**Space:** `O(h)`

---

This set covers **all core BST problems** in Striver’s sheet:

* Search / Insert / Delete
* Validate
* LCA
* Inorder successor
* Kth smallest
* Sorted array to BST
* Greater sum tree
* BST Iterator
* Recover BST

---


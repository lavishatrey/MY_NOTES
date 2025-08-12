 **Arrays section** 
---

## **1. Largest Element in Array**

```cpp
class Solution {
public:
    int largestElement(vector<int>& nums) {
        int mx = INT_MIN;
        for(int x : nums) mx = max(mx, x);
        return mx;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **2. Second Largest / Second Smallest**

```cpp
class Solution {
public:
    vector<int> getSecondOrderElements(int n, vector<int> a) {
        int largest = INT_MIN, secondLargest = INT_MIN;
        int smallest = INT_MAX, secondSmallest = INT_MAX;
        
        for(int x : a){
            if(x > largest){
                secondLargest = largest;
                largest = x;
            } else if(x > secondLargest && x != largest){
                secondLargest = x;
            }
            
            if(x < smallest){
                secondSmallest = smallest;
                smallest = x;
            } else if(x < secondSmallest && x != smallest){
                secondSmallest = x;
            }
        }
        return {secondLargest, secondSmallest};
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **3. Check if Array is Sorted**

```cpp
class Solution {
public:
    bool isSorted(vector<int>& arr) {
        for(int i=1; i<arr.size(); i++){
            if(arr[i] < arr[i-1]) return false;
        }
        return true;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **4. Remove Duplicates from Sorted Array** *(LeetCode 26)*

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int i=0;
        for(int j=1; j<nums.size(); j++){
            if(nums[j] != nums[i]){
                i++;
                nums[i] = nums[j];
            }
        }
        return i+1;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **5. Rotate Array by K Places**

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        k %= nums.size();
        reverse(nums.begin(), nums.end());
        reverse(nums.begin(), nums.begin()+k);
        reverse(nums.begin()+k, nums.end());
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **6. Move Zeroes to End**

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int insertPos = 0;
        for(int num : nums){
            if(num != 0) nums[insertPos++] = num;
        }
        while(insertPos < nums.size()) nums[insertPos++] = 0;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **7. Union of Two Sorted Arrays**

```cpp
class Solution {
public:
    vector<int> findUnion(vector<int> a, vector<int> b) {
        int i=0, j=0;
        vector<int> ans;
        while(i<a.size() && j<b.size()){
            if(a[i] <= b[j]){
                if(ans.empty() || ans.back() != a[i]) ans.push_back(a[i]);
                i++;
            } else {
                if(ans.empty() || ans.back() != b[j]) ans.push_back(b[j]);
                j++;
            }
        }
        while(i<a.size()){
            if(ans.empty() || ans.back() != a[i]) ans.push_back(a[i]);
            i++;
        }
        while(j<b.size()){
            if(ans.empty() || ans.back() != b[j]) ans.push_back(b[j]);
            j++;
        }
        return ans;
    }
};
```

**Time:** `O(n+m)`
**Space:** `O(1)` extra (excluding output)

---

## **8. Missing Number** *(LeetCode 268)*

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int n = nums.size();
        int sum = n*(n+1)/2;
        for(int x : nums) sum -= x;
        return sum;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **9. Maximum Consecutive Ones**

```cpp
class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int cnt = 0, mx = 0;
        for(int x : nums){
            if(x == 1) cnt++;
            else cnt = 0;
            mx = max(mx, cnt);
        }
        return mx;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **10. Longest Subarray with Sum K** *(Positive integers)*

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums, int k) {
        int i=0, sum=0, ans=0;
        for(int j=0; j<nums.size(); j++){
            sum += nums[j];
            while(sum > k){
                sum -= nums[i++];
            }
            if(sum == k) ans = max(ans, j-i+1);
        }
        return ans;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **11. Two Sum** *(LeetCode 1)*

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> mp;
        for(int i=0; i<nums.size(); i++){
            int rem = target - nums[i];
            if(mp.count(rem)) return {mp[rem], i};
            mp[nums[i]] = i;
        }
        return {};
    }
};
```

**Time:** `O(n)`
**Space:** `O(n)`

---

## **12. Kadane's Algorithm (Max Subarray Sum)** *(LeetCode 53)*

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0, ans = INT_MIN;
        for(int x : nums){
            sum = max(x, sum + x);
            ans = max(ans, sum);
        }
        return ans;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **13. Stock Buy and Sell (1 Transaction)** *(LeetCode 121)*

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int mn = INT_MAX, profit = 0;
        for(int p : prices){
            mn = min(mn, p);
            profit = max(profit, p - mn);
        }
        return profit;
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

## **14. Rearrange Array by Sign**

```cpp
class Solution {
public:
    vector<int> rearrangeArray(vector<int>& nums) {
        vector<int> pos, neg;
        for(int x : nums){
            if(x > 0) pos.push_back(x);
            else neg.push_back(x);
        }
        vector<int> ans;
        for(int i=0; i<pos.size(); i++){
            ans.push_back(pos[i]);
            ans.push_back(neg[i]);
        }
        return ans;
    }
};
```

**Time:** `O(n)`
**Space:** `O(n)`

---

## **15. Next Permutation** *(LeetCode 31)*

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int i = nums.size() - 2;
        while(i >= 0 && nums[i] >= nums[i+1]) i--;
        if(i >= 0){
            int j = nums.size() - 1;
            while(nums[j] <= nums[i]) j--;
            swap(nums[i], nums[j]);
        }
        reverse(nums.begin() + i + 1, nums.end());
    }
};
```

**Time:** `O(n)`
**Space:** `O(1)`

---

These 15 problems cover **most of the Striver-Sheet array patterns**:

* Scanning & comparing
* Two pointers
* Sliding window
* Hash maps
* Kadane’s
* Rearranging
* Permutations

---


 **Binary Search** 

---

## 1. **Binary Search Basics**

**Definition:** Search in sorted array by repeatedly dividing range in half.
**Time Complexity:** `O(log n)`
**Space Complexity:** `O(1)` (iterative)

---

### 1.1 **Standard Binary Search** (Exact match)

```cpp
class Solution {
public:
    int binarySearch(vector<int>& nums, int target) {
        int l = 0, r = nums.size()-1;
        while(l <= r){
            int mid = l + (r - l) / 2;
            if(nums[mid] == target) return mid;
            else if(nums[mid] < target) l = mid + 1;
            else r = mid - 1;
        }
        return -1;
    }
};
```

**Diagram:**

```
[2,4,7,10,13]
target=10
l=0, r=4, mid=2 → nums[2]=7 < target → l=3
l=3, r=4, mid=3 → nums[3]=10 == target → found
```

---

## 2. **Lower Bound / Upper Bound**

Used to find first index ≥ target (lower bound) or > target (upper bound).

### 2.1 Lower Bound

```cpp
class Solution {
public:
    int lowerBound(vector<int>& nums, int target) {
        int l = 0, r = nums.size(); // r = n
        while(l < r){
            int mid = l + (r - l) / 2;
            if(nums[mid] < target) l = mid + 1;
            else r = mid;
        }
        return l; // first position >= target
    }
};
```

### 2.2 Upper Bound

```cpp
class Solution {
public:
    int upperBound(vector<int>& nums, int target) {
        int l = 0, r = nums.size();
        while(l < r){
            int mid = l + (r - l) / 2;
            if(nums[mid] <= target) l = mid + 1;
            else r = mid;
        }
        return l; // first position > target
    }
};
```

---

## 3. **Binary Search on the Answer** (Monotonic Predicate)

**Idea:**
If the answer space is monotonic (e.g., "Can do in ≤ x?" → YES/NO), we can search for minimal/maximal value.

---

### 3.1 **Minimize Maximum Pages** (Book Allocation)

**Problem:** Allocate books to m students minimizing max pages.

**Predicate:** Can we allocate with max pages ≤ mid? YES → search left, NO → search right.

```cpp
class Solution {
public:
    bool canAllocate(vector<int>& pages, int m, int limit){
        int students = 1, sum = 0;
        for(int p : pages){
            if(p > limit) return false;
            if(sum + p > limit){
                students++;
                sum = p;
                if(students > m) return false;
            } else sum += p;
        }
        return true;
    }
    
    int findPages(vector<int>& pages, int m) {
        if(m > pages.size()) return -1;
        int low = *max_element(pages.begin(), pages.end());
        int high = accumulate(pages.begin(), pages.end(), 0);
        int ans = high;
        while(low <= high){
            int mid = low + (high - low) / 2;
            if(canAllocate(pages, m, mid)){
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
};
```

**Time:** `O(n log sum(pages))`
**Space:** `O(1)`

---

### 3.2 **Koko Eating Bananas**

```cpp
class Solution {
public:
    bool canEat(vector<int>& piles, int speed, int h){
        long long hours = 0;
        for(int p : piles) hours += (p + speed - 1) / speed;
        return hours <= h;
    }
    
    int minEatingSpeed(vector<int>& piles, int h) {
        int low = 1, high = *max_element(piles.begin(), piles.end()), ans = high;
        while(low <= high){
            int mid = low + (high - low) / 2;
            if(canEat(piles, mid, h)){
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
};
```

---

## 4. **Binary Search on Rotated Sorted Array**

### 4.1 Without Duplicates

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0, r = nums.size()-1;
        while(l <= r){
            int mid = l + (r - l)/2;
            if(nums[mid] == target) return mid;
            if(nums[l] <= nums[mid]){ // left sorted
                if(nums[l] <= target && target < nums[mid]) r = mid - 1;
                else l = mid + 1;
            } else { // right sorted
                if(nums[mid] < target && target <= nums[r]) l = mid + 1;
                else r = mid - 1;
            }
        }
        return -1;
    }
};
```

---

## 5. **First and Last Position of Element**

```cpp
class Solution {
public:
    int firstPos(vector<int>& nums, int target) {
        int l = 0, r = nums.size()-1, ans=-1;
        while(l <= r){
            int mid = l + (r-l)/2;
            if(nums[mid] >= target){
                if(nums[mid]==target) ans = mid;
                r = mid-1;
            } else l = mid+1;
        }
        return ans;
    }
    int lastPos(vector<int>& nums, int target) {
        int l = 0, r = nums.size()-1, ans=-1;
        while(l <= r){
            int mid = l + (r-l)/2;
            if(nums[mid] <= target){
                if(nums[mid]==target) ans = mid;
                l = mid+1;
            } else r = mid-1;
        }
        return ans;
    }
};
```

---

## 6. **Search in 2D Matrix**

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int n = matrix.size(), m = matrix[0].size();
        int l = 0, r = n*m - 1;
        while(l <= r){
            int mid = l + (r-l)/2;
            int val = matrix[mid/m][mid%m];
            if(val == target) return true;
            else if(val < target) l = mid+1;
            else r = mid-1;
        }
        return false;
    }
};
```

---

## 7. **Other Striver-Sheet Binary Search Patterns**

* **Aggressive Cows** (place cows with min distance) → binary search on distance.
* **Capacity to Ship Packages in D Days** → binary search on ship capacity.
* **Median of Two Sorted Arrays** → binary search on partition position.
* **Find Peak Element** → binary search on slope direction.

---


## **7.1 Aggressive Cows** 🐄 (Minimum Distance Placement)

**Problem:** Place `k` cows in `n` stalls (sorted positions) so that the **minimum distance** between any two cows is as large as possible.

**Intuition:**
If we can place cows with min distance ≥ `mid`, we can try bigger distances.
→ **Binary search on the answer** (distance).

**Approach:**

1. Sort stall positions.
2. Predicate: Try placing cows greedily from the first stall, placing each cow in the earliest stall possible that is ≥ `last_position + mid`.
3. If we can place all `k` cows → distance is feasible, search right; else search left.

**Code:**

```cpp
class Solution {
public:
    bool canPlace(vector<int>& stalls, int k, int dist){
        int count = 1; // first cow at stalls[0]
        int lastPos = stalls[0];
        for(int i=1; i<stalls.size(); i++){
            if(stalls[i] - lastPos >= dist){
                count++;
                lastPos = stalls[i];
                if(count >= k) return true;
            }
        }
        return false;
    }

    int aggressiveCows(vector<int>& stalls, int k) {
        sort(stalls.begin(), stalls.end());
        int low = 1;
        int high = stalls.back() - stalls.front();
        int ans = 0;
        while(low <= high){
            int mid = low + (high - low)/2;
            if(canPlace(stalls, k, mid)){
                ans = mid;
                low = mid + 1; // try bigger distance
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
};
```

**Time Complexity:** `O(n log(maxDist))`
**Space:** `O(1)`

---

## **7.2 Capacity to Ship Packages in D Days**

**Problem:** Packages in `weights[]`, ship them in order in ≤ `D` days. Find minimum ship capacity.

**Intuition:**
If capacity is too small, we need more days → binary search capacity.
Predicate: simulate loading packages in order, counting days.

**Code:**

```cpp
class Solution {
public:
    bool canShip(vector<int>& weights, int D, int cap){
        int days = 1, load = 0;
        for(int w : weights){
            if(w > cap) return false; // can't fit
            if(load + w > cap){
                days++;
                load = w;
            } else {
                load += w;
            }
        }
        return days <= D;
    }

    int shipWithinDays(vector<int>& weights, int D) {
        int low = *max_element(weights.begin(), weights.end());
        int high = accumulate(weights.begin(), weights.end(), 0);
        int ans = high;
        while(low <= high){
            int mid = low + (high - low)/2;
            if(canShip(weights, D, mid)){
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
};
```

**Time:** `O(n log(sum(weights)))`
**Space:** `O(1)`

---

## **7.3 Median of Two Sorted Arrays** (LeetCode 4)

**Problem:** Find median of two sorted arrays in `O(log(min(n,m)))`.

**Intuition:**
Binary search on partition in smaller array.
We want:

```
leftA <= rightB
leftB <= rightA
```

If not satisfied, move search boundaries.

**Code:**

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& A, vector<int>& B) {
        if(A.size() > B.size()) return findMedianSortedArrays(B, A);
        int n = A.size(), m = B.size();
        int totalLeft = (n + m + 1) / 2;
        int low = 0, high = n;

        while(low <= high){
            int cutA = (low + high) / 2;
            int cutB = totalLeft - cutA;

            int leftA = (cutA == 0) ? INT_MIN : A[cutA - 1];
            int rightA = (cutA == n) ? INT_MAX : A[cutA];
            int leftB = (cutB == 0) ? INT_MIN : B[cutB - 1];
            int rightB = (cutB == m) ? INT_MAX : B[cutB];

            if(leftA <= rightB && leftB <= rightA){
                if((n + m) % 2 == 0){
                    return (max(leftA, leftB) + min(rightA, rightB)) / 2.0;
                } else {
                    return max(leftA, leftB);
                }
            } else if(leftA > rightB){
                high = cutA - 1;
            } else {
                low = cutA + 1;
            }
        }
        return 0.0; // should not happen
    }
};
```

**Time:** `O(log(min(n,m)))`
**Space:** `O(1)`

---

## **7.4 Find Peak Element** (LeetCode 162)

**Problem:** Find index of a peak element (nums\[i] > nums\[i-1] && nums\[i] > nums\[i+1]).

**Intuition:**
Binary search on slope direction:

* If `nums[mid] < nums[mid+1]`, peak is on the right.
* Else, peak is on the left or mid.

**Code:**

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int l = 0, r = nums.size()-1;
        while(l < r){
            int mid = l + (r-l)/2;
            if(nums[mid] < nums[mid+1]){
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        return l; // or r, both same here
    }
};
```

**Time:** `O(log n)`
**Space:** `O(1)`

---
covered **all binary search patterns** 

* Standard BS
* Lower/Upper bound
* Search in rotated array
* First/last occurrence
* Search in 2D matrix
* Binary search on the answer (min/max)
* Aggressive Cows
* Ship Packages
* Median of Two Sorted Arrays
* Peak Element

---




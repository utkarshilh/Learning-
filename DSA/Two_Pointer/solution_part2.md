# 🚀 Two Pointer Handbook

# Part 2.1 - Opposite Pointer Problems

## Contents

1. LeetCode 977 - Squares of a Sorted Array
2. LeetCode 11 - Container With Most Water

---

# LeetCode 977 - Squares of a Sorted Array

## Pattern

* Opposite Direction Two Pointer

---

## Problem

Given a sorted array of integers (which may contain negative numbers), return an array of the squares of each number in **non-decreasing order**.

---

## Intuition

Although the array is sorted, **the squared values are not necessarily sorted** because negative numbers become positive after squaring.

Example:

```text
nums = [-7,-3,2,3,11]

Squares

49 9 4 9 121
```

Notice that the **largest square always comes from one of the two ends**:

* Large negative number
* Large positive number

So instead of sorting again, compare the squares of the leftmost and rightmost elements.

The larger square is placed at the end of the answer.

---

## Why Two Pointers Work

Since the original array is sorted:

```text
Negative ------------ Positive
```

The maximum absolute value will always be located at one of the ends.

Therefore,

```text
max(nums[left]^2, nums[right]^2)
```

always gives the next largest square.

---

## Algorithm

1. Create an answer array of size `n`.
2. Initialize:

   * `left = 0`
   * `right = n - 1`
   * `index = n - 1`
3. Compare the squares at both ends.
4. Place the larger square at `ans[index]`.
5. Move the corresponding pointer.
6. Decrease `index`.
7. Continue until `left > right`.

> **Note:** In your solution, you pushed larger squares into a vector and reversed it at the end. Both approaches have the same time complexity.

---

## Your Solution

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {

        int n = nums.size();

        vector<int> ans;

        int left = 0;
        int right = n - 1;

        while(left <= right){

            int xl = nums[left] * nums[left];
            int xr = nums[right] * nums[right];

            if(xl > xr){
                ans.push_back(xl);
                left++;
            }
            else{
                ans.push_back(xr);
                right--;
            }
        }

        reverse(ans.begin(), ans.end());

        return ans;
    }
};
```

---

## Dry Run

```text
nums

-4 -1 0 3 10

L          R

Squares

16       100

100 is larger

↓

ans

100
```

Move `right`.

```text
-4 -1 0 3

16 vs 9

↓

16

ans

100 16
```

Move `left`.

```text
-1 0 3

1 vs 9

↓

9

ans

100 16 9
```

Continue...

Final (after reverse):

```text
0 1 9 16 100
```

---

## Time Complexity

```text
O(n)
```

Each pointer moves at most `n` times.

---

## Space Complexity

```text
O(n)
```

The answer array is required.

---

## Key Points

* The array is already sorted.
* Largest square always comes from one of the two ends.
* No additional sorting is required.
* Your approach using `reverse()` is simple and completely valid.

---

## Similar Problems

* Two Sum II
* Container With Most Water
* Boats to Save People

---

# LeetCode 11 - Container With Most Water

## Pattern

* Opposite Direction Two Pointer

---

## Problem

Given an array where each element represents the height of a vertical line, find the maximum amount of water a container can store.

---

## Formula

For two lines:

```text
Area = Width × Height
```

where,

```text
Width = right - left

Height = min(height[left], height[right])
```

Therefore,

```text
Area = (right - left) × min(height[left], height[right])
```

---

## Intuition

Start with the widest possible container.

```text
L                       R
```

This gives the maximum width.

Now calculate the area.

The key observation is:

The height of the container is limited by the **shorter line**.

Increasing the taller line while keeping the shorter one unchanged **cannot increase the area** because the minimum height remains the same, while the width decreases.

Therefore:

* If the left height is smaller → move `left`.
* Otherwise → move `right`.

---

## Why Move the Smaller Height?

Suppose:

```text
Height

2 ............ 8

Width = 10
```

Current area:

```text
2 × 10 = 20
```

If we move the taller height:

```text
2 .......... 7

Width decreases.

Minimum height is still 2.

Area becomes:

2 × 9 = 18
```

The area can only decrease.

The only chance to increase the area is to move the smaller height and hope to find a taller line.

---

## Algorithm

1. Initialize:

   * `left = 0`
   * `right = n - 1`
2. Compute current area.
3. Update maximum area.
4. Move the pointer pointing to the smaller height.
5. Continue until pointers meet.

---

## Your Solution

```cpp
class Solution {
public:
    int maxArea(vector<int>& nums) {

        int n = nums.size();

        int left = 0;
        int right = n - 1;

        int mmax = INT_MIN;

        while(left < right){

            int temp = (right - left) * min(nums[left], nums[right]);

            mmax = max(temp, mmax);

            if(nums[left] < nums[right])
                left++;
            else
                right--;
        }

        return mmax;
    }
};
```

---

## Dry Run

```text
Height

1 8 6 2 5 4 8 3 7

L               R

Width = 8

Height = 1

Area = 8
```

Move `left`.

```text
8 6 2 5 4 8 3 7

L             R

Width = 7

Height = 7

Area = 49
```

Current maximum:

```text
49
```

Continue until pointers meet.

Final Answer:

```text
49
```

---

## Time Complexity

```text
O(n)
```

Each pointer moves at most once across the array.

---

## Space Complexity

```text
O(1)
```

No extra space is used.

---

## Key Points

* Start with the maximum possible width.
* The area is determined by the shorter line.
* Always move the pointer with the smaller height.
* Moving the taller line cannot produce a better answer while the shorter line remains unchanged.

---

## Common Mistakes

* Using the larger height instead of the smaller one.
* Moving the taller pointer.
* Forgetting to update the maximum area before moving pointers.

---

## Similar Problems

* Trapping Rain Water
* Boats to Save People
* Two Sum II

---

# Part 2.1 Summary

| Problem                   | Pattern          | Time | Space |
| ------------------------- | ---------------- | ---- | ----- |
| Squares of a Sorted Array | Opposite Pointer | O(n) | O(n)  |
| Container With Most Water | Opposite Pointer | O(n) | O(1)  |

---

## Pattern Learned

### Opposite Direction Pointer

```
Left -------------------- Right
```

### Rule 1

For **pair comparison problems**, use information from the current state to decide which pointer should move.

### Rule 2

For **maximum/minimum optimization problems**, understand **why** moving a particular pointer can improve the answer instead of moving pointers arbitrarily.

### Rule 3

In sorted arrays, each pointer visits every element at most once, giving an overall complexity of **O(n)**.




# 🚀 Two Pointer Handbook

# Part 2.2 - Fixed Pointer + Two Pointer

## Contents

1. LeetCode 15 - 3Sum
2. LeetCode 16 - 3Sum Closest

---

# LeetCode 15 - 3Sum

## Pattern

* Sorting
* Fixed Pointer + Two Pointer

---

## Problem

Given an integer array `nums`, return **all unique triplets** `[a, b, c]` such that:

```text
a + b + c = 0
```

The solution must not contain duplicate triplets.

---

# Brute Force

The simplest approach is checking every possible triplet.

```cpp
for(i)
    for(j)
        for(k)
```

Time Complexity

```text
O(n³)
```

This is too slow.

---

# Intuition

Instead of fixing three numbers, fix **one number** and search for the remaining two using Two Pointers.

After sorting,

```
i | left ........ right
```

Now,

```
nums[i] + nums[left] + nums[right]
```

behaves exactly like **Two Sum II**.

---

# Why Sorting?

Sorting provides two important benefits:

1. Enables the Two Pointer approach.
2. Makes duplicate handling much easier.

---

# Algorithm

1. Sort the array.
2. Fix one element (`i`).
3. Place:

   * `left = i + 1`
   * `right = n - 1`
4. Calculate current sum.
5. If sum is

   * equal → store answer
   * smaller → move left
   * larger → move right

Repeat for every `i`.

---

# Your Solution

You used a **set** to remove duplicate triplets.

This is completely valid and easy to understand.

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {

        int n = nums.size();

        vector<vector<int>> ans;
        map<vector<int>, int> mp;

        sort(nums.begin(), nums.end());

        for(int i = 0; i < n; i++){

            int j = i + 1;
            int k = n - 1;

            while(j < k){

                int sum = nums[i] + nums[j] + nums[k];

                if(sum == 0){

                    vector<int> temp;

                    temp.push_back(nums[i]);
                    temp.push_back(nums[j]);
                    temp.push_back(nums[k]);

                    if(mp.find(temp) == mp.end()){

                        ans.push_back(temp);
                        mp[temp] = 1;
                    }

                    j++;
                    k--;
                }

                else if(sum < 0)
                    j++;

                else
                    k--;
            }
        }

        return ans;
    }
};
```

---

# Dry Run

```
nums

-4 -1 -1 0 1 2
```

Fix

```
i = -1
```

Pointers

```
-1 | -1 0 1 2

      L     R
```

Current Sum

```
-1 + (-1) + 2

=

0
```

Store answer.

Move both pointers.

Continue.

---

# Complexity

Time

```text
O(n² log n)
```

Reason:

* Outer loop → O(n)
* Two Pointer → O(n)
* Insertion into map → O(log M)

Without using `map/set` and by skipping duplicates, the standard interview solution runs in **O(n²)**.

---

# Space Complexity

```text
O(answer)
```

because of the map/set and result storage.

---

# Key Points

* Sort first.
* Fix one element.
* Remaining problem becomes Two Sum.
* Using a `set` or `map` is an easy way to remove duplicates.

---

# Interview Note

The most common interview solution skips duplicates while traversing instead of using a `set`.

Example:

```cpp
while(left < right && nums[left] == nums[left - 1])
    left++;
```

This avoids extra memory.

Your solution is still correct and accepted.

---

# Similar Problems

* Two Sum II
* 3Sum Closest
* 4Sum

---

# LeetCode 16 - 3Sum Closest

## Pattern

* Sorting
* Fixed Pointer + Two Pointer

---

## Problem

Given an array and a target, return the sum of three integers that is **closest** to the target.

Unlike 3Sum,

there is exactly one best answer.

---

# Intuition

The solution is almost identical to 3Sum.

The only difference is:

Instead of checking for

```
sum == 0
```

we keep track of

```
abs(target - current_sum)
```

The smaller this value,

the closer we are to the target.

---

# Algorithm

1. Sort the array.
2. Fix one element.
3. Use Two Pointers.
4. Compute current sum.
5. If current difference is better,
   update answer.
6. Move pointers depending on the sum.

---

# Your Solution

```cpp
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {

        int n = nums.size();

        sort(nums.begin(), nums.end());

        int ans = INT_MAX;
        int best = INT_MAX;

        for(int i = 0; i < n; i++){

            int left = i + 1;
            int right = n - 1;

            while(left < right){

                int sum = nums[i] + nums[left] + nums[right];

                if(abs(target - sum) < best){

                    best = abs(target - sum);
                    ans = sum;
                }

                if(sum < target)
                    left++;

                else
                    right--;
            }
        }

        return ans;
    }
};
```

---

# Dry Run

```
nums

-4 -1 1 2

target

1
```

Fix

```
-4
```

Pointers

```
-4 | -1 1 2

      L   R
```

Current Sum

```
-3
```

Difference

```
4
```

Store answer.

Move left.

Continue.

Eventually,

Closest sum becomes

```
2
```

---

# Complexity

Time

```text
O(n²)
```

Outer loop:

```
O(n)
```

Inner Two Pointer:

```
O(n)
```

Overall:

```text
O(n²)
```

---

# Space Complexity

```text
O(1)
```

Ignoring sorting.

---

# Key Points

* This is almost the same problem as 3Sum.
* Instead of storing triplets, keep the closest sum.
* Always compare

```cpp
abs(target - sum)
```

before moving pointers.

---

# Common Mistakes

* Forgetting to update the answer before moving pointers.
* Comparing the sum instead of the absolute difference.
* Forgetting to sort the array.

---

# Similar Problems

* 3Sum
* 4Sum
* Two Sum II

---

# Part 2.2 Summary

| Problem      | Pattern                     | Time                                 | Space     |
| ------------ | --------------------------- | ------------------------------------ | --------- |
| 3Sum         | Fixed Pointer + Two Pointer | O(n²) *(O(n² log n) with `map/set`)* | O(answer) |
| 3Sum Closest | Fixed Pointer + Two Pointer | O(n²)                                | O(1)      |

---

# Pattern Learned

## Fixed Pointer + Two Pointer

```
Fix i

↓

left = i + 1

↓

right = n - 1
```

This converts many **3-element** or **4-element** problems into a simpler **Two Pointer** problem.

### General Template

```cpp
sort(nums.begin(), nums.end());

for(int i = 0; i < n; i++){

    int left = i + 1;
    int right = n - 1;

    while(left < right){

        // Process current combination

        if(condition)
            left++;
        else
            right--;
    }
}
```

---

## Key Takeaways

* **Sort first** to unlock the Two Pointer technique.
* **Fix one element**, then solve the remaining part using Two Pointers.
* This pattern extends naturally:

  * **2Sum** → Two Pointers
  * **3Sum** → Fix 1 + Two Pointers
  * **4Sum** → Fix 2 + Two Pointers
* When duplicates are involved, either:

  * Skip duplicates while traversing (interview-preferred), or
  * Use a `set/map` (simpler and acceptable in many cases, as in your solution).


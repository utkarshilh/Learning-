# 🚀 Two Pointer Handbook

# Part 3.1 – Trapping Rain Water (LeetCode 42)

---

# LeetCode 42 - Trapping Rain Water

## Pattern

* Prefix Maximum Arrays
* Opposite Direction Two Pointer (Space Optimized)

---

# Problem

Given an elevation map where the width of each bar is `1`, compute how much rainwater can be trapped after raining.

Example:

```text
Height

0 1 0 2 1 0 1 3 2 1 2 1
```

Output

```text
6
```

---

# Observation

Water **cannot** be stored on the first and last bars.

For every other index,

the trapped water depends on:

* The tallest bar on its left.
* The tallest bar on its right.

---

# Key Formula

For every index:

```text
Water = min(LeftMax, RightMax) - CurrentHeight
```

If this value is negative,

consider it as `0`.

---

# Why Minimum?

Consider

```text
Height

5   2   8
```

```text
Left Max = 5

Right Max = 8
```

Water level becomes

```text
min(5,8)

=

5
```

Even though the right wall is taller,

the left wall limits the water.

Similarly,

```text
Height

9 2 4
```

Water stored

```text
min(9,4)

=

4
```

Therefore,

```text
Water = min(LeftMax, RightMax) - Height
```

---

# Approach 1 - Prefix Maximum Arrays

---

## Intuition

Instead of finding the tallest left and right wall for every index repeatedly,

precompute them once.

Create two arrays:

```text
left[]

right[]
```

where

```text
left[i]

=

Maximum height from

0...

i
```

and

```text
right[i]

=

Maximum height from

i...

n-1
```

Then,

```text
Water[i]

=

min(left[i], right[i])

-

height[i]
```

---

# Algorithm

### Step 1

Build Left Maximum Array.

```text
left[i]

=

max(left[i-1], height[i])
```

---

### Step 2

Build Right Maximum Array.

```text
right[i]

=

max(right[i+1], height[i])
```

---

### Step 3

Traverse again.

```text
water += min(left[i], right[i]) - height[i]
```

---

# Your Solution

```cpp
class Solution {
public:
    int trap(vector<int>& nums) {

        int n = nums.size();

        vector<int> left(n,0);
        vector<int> right(n,0);

        left[0] = nums[0];
        right[n-1] = nums[n-1];

        int ans = 0;

        for(int i=1;i<n;i++)
            left[i]=max(left[i-1],nums[i]);

        for(int i=n-2;i>=0;i--)
            right[i]=max(right[i+1],nums[i]);

        for(int i=0;i<n;i++){

            int temp=min(left[i],right[i])-nums[i];

            ans+=temp;
        }

        return ans;
    }
};
```

---

# Dry Run

Input

```text
0 1 0 2 1 0 1
```

---

### Left Maximum

```text
0 1 1 2 2 2 2
```

---

### Right Maximum

```text
2 2 2 2 1 1 1
```

---

### Water

| Height | Left | Right | Water |
| -----: | ---: | ----: | ----: |
|      0 |    0 |     2 |     0 |
|      1 |    1 |     2 |     0 |
|      0 |    1 |     2 |     1 |
|      2 |    2 |     2 |     0 |
|      1 |    2 |     1 |     0 |
|      0 |    2 |     1 |     1 |
|      1 |    2 |     1 |     0 |

Total

```text
2
```

---

# Complexity

Time

```text
O(n)
```

Space

```text
O(n)
```

because of

```text
left[]

right[]
```

---

# Advantages

* Easy to understand.
* Simple implementation.
* Great for beginners.

---

# Disadvantages

Needs

```text
O(n)
```

extra memory.

---

# Approach 2 - Two Pointer (Optimized)

---

## Observation

The prefix solution stores

```text
left[]

right[]
```

only to know

```text
LeftMax

RightMax
```

at every position.

Instead,

we can maintain these values while traversing.

---

# Intuition

Maintain

```text
left

right

leftMax

rightMax
```

Initially,

```text
left = 0

right = n-1
```

At every step,

update

```text
leftMax

rightMax
```

Now compare

```text
leftMax

rightMax
```

---

## Why Compare Maximums?

Suppose

```text
leftMax < rightMax
```

Then,

the water trapped at the current left position depends **only on `leftMax`**.

Even if the right side becomes taller later,

the minimum will still be

```text
leftMax
```

Therefore,

we can safely calculate

```text
leftMax - height[left]
```

and move

```text
left++
```

Similarly,

if

```text
rightMax <= leftMax
```

we process the right side.

---

# Algorithm

Initialize

```text
left = 0

right = n-1

leftMax = height[0]

rightMax = height[n-1]
```

Loop while

```text
left < right
```

Update maximums.

If

```text
leftMax < rightMax
```

calculate

```text
water += leftMax - height[left]
```

Move left.

Else

calculate

```text
water += rightMax - height[right]
```

Move right.

---

# Your Solution

```cpp
class Solution {
public:
    int trap(vector<int>& nums) {

        int n = nums.size();

        int left = 0;
        int right = n-1;

        int left_max = nums[0];
        int right_max = nums[n-1];

        int water = 0;

        while(left < right){

            left_max = max(left_max, nums[left]);
            right_max = max(right_max, nums[right]);

            if(left_max < right_max){

                water += left_max - nums[left];
                left++;
            }
            else{

                water += right_max - nums[right];
                right--;
            }
        }

        return water;
    }
};
```

---

# Dry Run

Input

```text
4 2 0 3 2 5
```

Initially

```text
L              R

4 2 0 3 2 5

leftMax = 4

rightMax = 5
```

Since

```text
4 < 5
```

Process left.

Water

```text
4-4 = 0
```

Move left.

---

Current

```text
2
```

Water

```text
4-2 = 2
```

Move left.

---

Current

```text
0
```

Water

```text
4-0 = 4
```

Move left.

Continue until pointers meet.

Final answer

```text
9
```

---

# Complexity

Time

```text
O(n)
```

Space

```text
O(1)
```

---

# Prefix vs Two Pointer

| Feature             | Prefix Array | Two Pointer     |
| ------------------- | ------------ | --------------- |
| Time                | O(n)         | O(n)            |
| Space               | O(n)         | O(1)            |
| Easy to Understand  | ✅            | Slightly Harder |
| Interview Preferred | ❌            | ✅               |

---

# Key Takeaways

* Water at any position is determined by the **smaller** of the tallest bars on the left and right.
* The Prefix Max approach is straightforward and great for understanding the concept.
* The Two Pointer approach optimizes space by maintaining only `leftMax` and `rightMax`.
* In the optimized solution:

  * If `leftMax < rightMax`, process the left side.
  * Otherwise, process the right side.
* Both approaches run in **O(n)** time, but the Two Pointer approach is preferred in interviews because it uses **O(1)** extra space.

---

# Similar Problems

* Container With Most Water
* Two Sum II
* Boats to Save People
* Maximum Width Ramp



# 🚀 Two Pointer Handbook

# Part 3.2 – Pairing & Greedy Two Pointer Problems

## Contents

1. LeetCode 881 - Boats to Save People
2. LeetCode 1679 - Max Number of K-Sum Pairs

---

# LeetCode 881 - Boats to Save People

## Pattern

* Sorting
* Opposite Direction Two Pointer
* Greedy

---

# Problem

You are given the weight of each person and the maximum weight limit of a boat.

Each boat can carry **at most two people**, and the combined weight cannot exceed the given limit.

Return the **minimum number of boats** required.

---

# Initial Thought Process

Initially, it is tempting to think:

* Start from the smallest person.
* Keep adding people until the boat becomes full.

Example:

```text
1 2 2 3
```

This approach seems natural because we usually try to "fill" a boat as much as possible.

However, this idea is incorrect because **each boat can carry at most two people**, not an unlimited number.

This was the main issue with the first approach.

---

# Observation

The heaviest person **must** go on a boat.

There are only two possibilities:

1. Pair them with the lightest person.
2. Send them alone.

No other pairing can improve the answer.

---

# Why Pair With the Lightest?

Suppose

```text
Weights

1 2 3 5

Limit = 6
```

Can 5 pair with 3?

```text
5 + 3 = 8
```

Impossible.

Can 5 pair with 2?

```text
5 + 2 = 7
```

Impossible.

Can 5 pair with 1?

```text
5 + 1 = 6
```

Perfect.

If even the **lightest** person cannot fit with the heaviest one, then no one can.

So the heaviest person must go alone.

This greedy observation guarantees the optimal solution.

---

# Algorithm

1. Sort the array.
2. Place:

   * `left = 0`
   * `right = n - 1`
3. If

```text
nums[left] + nums[right] <= limit
```

pair them and move both pointers.

Otherwise,

send the heaviest person alone and move only `right`.

Increase the boat count every iteration.

---

# Your Final Solution

```cpp
class Solution {
public:
    int numRescueBoats(vector<int>& nums, int limit) {

        sort(nums.begin(), nums.end());

        int left = 0;
        int right = nums.size() - 1;

        int ans = 0;

        while(left < right){

            if(nums[left] + nums[right] <= limit){
                left++;
                right--;
            }
            else{
                right--;
            }

            ans++;
        }

        if(left == right)
            ans++;

        return ans;
    }
};
```

---

# Dry Run

Input

```text
1 2 2 3

Limit = 3
```

Initially

```text
L      R

1 2 2 3
```

Current sum

```text
1 + 3 = 4
```

Cannot pair.

Boat 1

```text
3
```

Move right.

---

Now

```text
L   R

1 2 2
```

Current sum

```text
1 + 2 = 3
```

Pair them.

Boat 2

Move both pointers.

Remaining

```text
2
```

Boat 3

Final answer

```text
3
```

---

# Complexity

Time

```text
O(n log n)
```

Sorting dominates the complexity.

Two Pointer traversal is

```text
O(n)
```

---

# Space Complexity

```text
O(1)
```

Ignoring sorting.

---

# Common Mistakes

### Mistake 1

Trying to put more than two people in one boat.

The problem clearly states:

```text
Maximum two people.
```

---

### Mistake 2

Trying to maximize the current boat weight.

The greedy strategy is not to maximize weight.

Instead,

always process the **heaviest remaining person**.

---

### Mistake 3

Forgetting the last person.

If

```cpp
left == right
```

one person is still waiting.

They need one more boat.

---

# Key Takeaways

* Sort first.
* Always process the heaviest person.
* Try pairing them with the lightest.
* If that fails, send the heaviest alone.

---

# Similar Problems

* Two Sum II
* Max Number of K Sum Pairs

---

# LeetCode 1679 - Max Number of K-Sum Pairs

## Pattern

* Sorting
* Opposite Direction Two Pointer

---

# Problem

Given an array and an integer `k`, remove as many pairs as possible such that

```text
a + b = k
```

Each element can be used **only once**.

Return the maximum number of valid pairs.

---

# Intuition

After sorting,

the problem becomes almost identical to **Two Sum II**.

The only difference is that instead of returning indices, we count valid pairs.

---

# Observation

For every pair:

* If the sum is smaller than `k`, increase the left pointer.
* If the sum is larger than `k`, decrease the right pointer.
* If the sum equals `k`, count the pair and move both pointers.

---

# Algorithm

1. Sort the array.
2. Initialize:

   * `left = 0`
   * `right = n - 1`
3. While `left < right`:

   * Compare the sum.
   * Move pointers accordingly.
   * Count every valid pair.

---

# Your Solution

```cpp
class Solution {
public:
    int maxOperations(vector<int>& nums, int k) {

        sort(nums.begin(), nums.end());

        int i = 0;
        int j = nums.size() - 1;

        int ans = 0;

        while(i < j){

            if(nums[i] + nums[j] == k){

                ans++;
                i++;
                j--;
            }

            else if(nums[i] + nums[j] < k){
                i++;
            }

            else{
                j--;
            }
        }

        return ans;
    }
};
```

---

# Dry Run

Input

```text
1 2 3 4

k = 5
```

Initially

```text
L      R

1 2 3 4
```

Current sum

```text
1 + 4 = 5
```

Valid pair.

Count = 1

Move both.

---

Now

```text
2 3
```

Current sum

```text
2 + 3 = 5
```

Count = 2

Done.

---

# Complexity

Sorting

```text
O(n log n)
```

Traversal

```text
O(n)
```

Overall

```text
O(n log n)
```

---

# Space Complexity

```text
O(1)
```

Ignoring sorting.

---

# Common Mistakes

### Forgetting to move both pointers

When a valid pair is found,

both numbers are consumed.

Therefore,

both pointers must move.

---

### Moving the wrong pointer

If the current sum is smaller,

only increasing the left pointer can increase the sum.

If the current sum is larger,

only decreasing the right pointer can reduce it.

---

# Key Takeaways

* The array should be sorted first.
* Every element can be used only once.
* Count valid pairs instead of returning them.
* This problem is a direct extension of Two Sum II.

---

# Similar Problems

* Two Sum II
* Boats to Save People
* 3Sum

---

# Part 3.2 Summary

| Problem                   | Pattern                   | Time       | Space |
| ------------------------- | ------------------------- | ---------- | ----- |
| Boats to Save People      | Greedy + Opposite Pointer | O(n log n) | O(1)  |
| Max Number of K-Sum Pairs | Opposite Pointer          | O(n log n) | O(1)  |

---

# Pattern Learned

## Greedy + Two Pointer

A common interview pattern is:

```text
Sort
        ↓
Largest Element
        ↓
Try Best Possible Pair
        ↓
Otherwise Use Alone
```

This guarantees an optimal answer for pairing problems like **Boats to Save People**.

---

## Opposite Pointer Rule

```text
If sum < target
    Move Left

If sum > target
    Move Right

If sum == target
    Process Answer
    Move Both
```

This same logic appears in:

* Two Sum II
* Max Number of K-Sum Pairs
* Boats to Save People
* 3Sum (inside the inner loop)

---

# Final Revision Notes

* **Sort first** whenever pairing decisions depend on ordering.
* **Opposite pointers** help eliminate impossible combinations efficiently.
* **Greedy reasoning** is often paired with Two Pointers to produce optimal solutions.
* Always ask:

  * Can the heaviest element be paired?
  * If not, can it safely be processed alone?
  * Does moving one pointer eliminate impossible cases?

These questions often reveal the correct Two Pointer strategy.

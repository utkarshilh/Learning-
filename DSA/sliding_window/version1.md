# Sliding Window Mastery 🚀

> Complete Notes based on my own learning journey.
>
> Language: **C++**
>
> Goal: Understand every Sliding Window pattern from scratch instead of memorizing solutions.

---

# Table of Contents

1. Introduction
2. When to use Sliding Window
3. Pattern 1 – Fixed Size Window
4. Pattern 2 – Variable Size Window
5. Pattern 3 – Constraint Based Window
6. Pattern 4 – Exactly K
7. Pattern 5 – Counting Valid Windows
8. Pattern 6 – Product Window
9. Pattern 7 – Frequency Window
10. Pattern 8 – Monotonic Queue
11. Revision Sheet

---

# 1. Introduction

Sliding Window is an optimization technique used whenever the problem asks about a **continuous subarray** or **continuous substring**.

Instead of generating every possible subarray (O(n²)), we maintain a window and move it through the array.

Most Sliding Window problems can be solved in **O(n)** because every element enters and leaves the window at most once.

---

## How to identify Sliding Window?

Usually the question contains words like:

* Continuous Subarray
* Continuous Substring
* Window Size = K
* Longest
* Shortest
* Maximum
* Minimum
* Count all subarrays satisfying a condition

---

# 2. Sliding Window Templates

---

## Fixed Size Window

```cpp
// Build first window

// Calculate answer

while(right < n){

    // Remove left contribution

    // Add right contribution

    // Update answer

    left++;
    right++;
}
```

---

## Variable Size Window

```cpp
while(right < n){

    // Add current element

    while(window becomes invalid){

        // Remove left contribution

        left++;
    }

    // Update answer

    right++;
}
```

---

## At Most K

```cpp
while(right < n){

    // Add current contribution

    while(window becomes invalid){

        // Remove left contribution

        left++;
    }

    ans += right-left+1;

    right++;
}
```

---

## Exactly K

```text
Exactly(K)

=

AtMost(K)

-

AtMost(K-1)
```

---

## Counting Valid Windows

```cpp
while(window is valid){

    ans += n-right;

    Shrink window
}
```

---

## Product Window

```cpp
Multiply current element

while(product >= k){

    Divide left element

    left++;
}
```

---

## Frequency Window

```text
Operations Needed

=

(Target × Window Size)

-

Current Window Sum
```

---

## Monotonic Queue

```text
Remove expired indices

↓

Remove smaller elements

↓

Insert current index

↓

Front = Maximum
```

---

# 3. Pattern 1 — Fixed Size Window

## Concept

Window size is already given.

Only slide the window.

---

# LeetCode 643 — Maximum Average Subarray I

## Intuition

Maintain the sum of the current window.

Instead of recalculating the sum every time, subtract the left element and add the new right element.

---

## Solution

```cpp
class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {

        int windowSum = 0;

        // Build first window
        for(int i=0;i<k;i++)
            windowSum += nums[i];

        int maxSum = windowSum;

        int left = 0;
        int right = k;

        while(right < nums.size()){

            // Remove left contribution
            windowSum -= nums[left];

            // Add new element
            windowSum += nums[right];

            // Update answer
            maxSum = max(maxSum, windowSum);

            left++;
            right++;
        }

        return (double)maxSum/k;
    }
};
```

---

## Algorithm

1. Build first window.
2. Store current sum.
3. Remove left element.
4. Add right element.
5. Update maximum.
6. Repeat.

---

## Complexity

Time : O(n)

Space : O(1)

---

# LeetCode 1343 — Number of Subarrays of Size K and Average ≥ Threshold

## Intuition

Average comparison can be converted into Sum comparison.

Instead of checking

```text
Average >= Threshold
```

check

```text
Sum >= Threshold × K
```

---

## Solution

```cpp
class Solution {
public:
    int numOfSubarrays(vector<int>& arr, int k, int threshold) {

        int target = threshold * k;

        int windowSum = 0;

        // Build first window
        for(int i=0;i<k;i++)
            windowSum += arr[i];

        int ans = 0;

        if(windowSum >= target)
            ans++;

        int left = 0;
        int right = k;

        while(right < arr.size()){

            windowSum -= arr[left];
            windowSum += arr[right];

            if(windowSum >= target)
                ans++;

            left++;
            right++;
        }

        return ans;
    }
};
```

---

## Algorithm

1. Convert average to sum.
2. Build first window.
3. Slide window.
4. Count valid windows.

---

## Complexity

Time : O(n)

Space : O(1)

---

# LeetCode 1456 — Maximum Number of Vowels in a Substring of Given Length

## Intuition

Maintain the number of vowels inside the current window.

---

## Solution

```cpp
class Solution {
public:

    bool isVowel(char ch){

        return ch=='a' || ch=='e' || ch=='i' ||
               ch=='o' || ch=='u';
    }

    int maxVowels(string s, int k) {

        int vowelCount = 0;

        // Build first window
        for(int i=0;i<k;i++)
            if(isVowel(s[i]))
                vowelCount++;

        int answer = vowelCount;

        int left = 0;
        int right = k;

        while(right < s.size()){

            if(isVowel(s[left]))
                vowelCount--;

            if(isVowel(s[right]))
                vowelCount++;

            answer = max(answer, vowelCount);

            left++;
            right++;
        }

        return answer;
    }
};
```

---

## Algorithm

1. Build first window.
2. Maintain vowel count.
3. Remove left contribution.
4. Add right contribution.
5. Update maximum vowels.

---

## Complexity

Time : O(n)

Space : O(1)

---

# Pattern 2 — Variable Size Window

## Concept

Window size is not fixed.

Expand until invalid.

Shrink until valid.

---

# LeetCode 209 — Minimum Size Subarray Sum

## Intuition

Keep expanding until sum becomes ≥ target.

Once valid,

keep shrinking to minimize the window.

---

## Solution

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {

        int left = 0;
        int right = 0;

        int windowSum = 0;

        int answer = INT_MAX;

        while(right < nums.size()){

            // Expand
            windowSum += nums[right];

            // Shrink while valid
            while(windowSum >= target){

                answer = min(answer, right-left+1);

                windowSum -= nums[left];
                left++;
            }

            right++;
        }

        return answer==INT_MAX ? 0 : answer;
    }
};
```

---

## Algorithm

1. Expand window.
2. Once sum ≥ target.
3. Keep shrinking.
4. Store minimum length.

---

## Complexity

Time : O(n)

Space : O(1)

---

# LeetCode 3 — Longest Substring Without Repeating Characters

## Intuition

Maintain frequency of every character.

If duplicate appears,

shrink until duplicate disappears.

---

## Solution

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {

        unordered_map<char,int> frequency;

        int left = 0;
        int right = 0;

        int answer = 0;

        while(right < s.size()){

            frequency[s[right]]++;

            while(frequency[s[right]] > 1){

                frequency[s[left]]--;
                left++;
            }

            answer = max(answer, right-left+1);

            right++;
        }

        return answer;
    }
};
```

---

## Algorithm

1. Expand window.
2. Store character frequency.
3. If duplicate appears,
   shrink.
4. Update maximum length.

---

## Complexity

Time : O(n)

Space : O(26) ≈ O(1)

---

# Pattern 3 — Constraint Based Window

## Concept

Maintain a constraint.

Examples:

* At most K zeros
* At most K replacements
* At most two distinct fruits

Expand.

Shrink only when constraint breaks.

---

# LeetCode 1004 — Max Consecutive Ones III

## Intuition

Treat zero count as the constraint.

Window is valid while

```
Zero Count ≤ K
```

---

## Solution

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {

        int left = 0;
        int right = 0;

        int zeroCount = 0;

        int answer = 0;

        while(right < nums.size()){

            if(nums[right]==0)
                zeroCount++;

            while(zeroCount > k){

                if(nums[left]==0)
                    zeroCount--;

                left++;
            }

            answer = max(answer,right-left+1);

            right++;
        }

        return answer;
    }
};
```

---

## Algorithm

1. Expand.
2. Count zeros.
3. If zeros exceed K,
   shrink.
4. Update longest window.

---

## Complexity

Time : O(n)

Space : O(1)



# Pattern 3 — Constraint Based Window (Continued)

---

# LeetCode 424 — Longest Repeating Character Replacement

## Intuition

Instead of tracking which character to replace, maintain the **maximum frequency** of any character inside the current window.

Characters to replace:

```text
Window Size - Max Frequency
```

If this value exceeds `k`, shrink the window.

---

## Solution

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {

        unordered_map<char,int> mp;

        int left = 0;
        int right = 0;

        int maxFrequency = 0;
        int ans = 0;

        while(right < s.size()){

            // Add current character
            mp[s[right]]++;

            // Update maximum frequency in current window
            maxFrequency = max(maxFrequency, mp[s[right]]);

            // Shrink if replacements exceed k
            while((right-left+1)-maxFrequency > k){

                mp[s[left]]--;
                left++;
            }

            ans = max(ans,right-left+1);

            right++;
        }

        return ans;
    }
};
```

---

## Algorithm

1. Expand window.
2. Count frequency.
3. Track maximum occurring character.
4. If replacements exceed `k`, shrink.
5. Update answer.

---

## Complexity

Time : O(n)

Space : O(26)

---

# LeetCode 904 — Fruit Into Baskets

## Intuition

Maintain **at most two distinct fruits** inside the window.

HashMap stores frequency.

A counter stores the number of distinct fruits.

---

## Solution

```cpp
class Solution {
public:
    int totalFruit(vector<int>& fruits) {

        unordered_map<int,int> mp;

        int left = 0;
        int right = 0;

        int distinct = 0;
        int ans = 0;

        while(right < fruits.size()){

            mp[fruits[right]]++;

            if(mp[fruits[right]]==1)
                distinct++;

            while(distinct>2){

                mp[fruits[left]]--;

                if(mp[fruits[left]]==0)
                    distinct--;

                left++;
            }

            ans=max(ans,right-left+1);

            right++;
        }

        return ans;
    }
};
```

---

## Algorithm

1. Expand.
2. Count distinct fruits.
3. If distinct > 2, shrink.
4. Update maximum window.

---

## Complexity

Time : O(n)

Space : O(2)

---

# LeetCode 1493 — Longest Subarray of 1's After Deleting One Element

## Intuition

Allow **at most one zero**.

Answer is

```text
Window Size - 1
```

because one element must always be deleted.

---

## Solution

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums) {

        int left = 0;
        int right = 0;

        int zeroCount = 0;

        int ans = 0;

        while(right < nums.size()){

            if(nums[right]==0)
                zeroCount++;

            while(zeroCount>1){

                if(nums[left]==0)
                    zeroCount--;

                left++;
            }

            ans=max(ans,right-left);

            right++;
        }

        return ans;
    }
};
```

---

## Algorithm

1. Expand.
2. Maintain at most one zero.
3. Shrink if needed.
4. Answer = window size − 1.

---

## Complexity

Time : O(n)

Space : O(1)

---

# Pattern 4 — Exactly K

---

## Core Observation

Many interview problems ask

```text
Exactly K
```

instead of

```text
At Most K
```

Instead of solving exactly K directly,

convert it into

```text
Exactly(K)

=

AtMost(K)

-

AtMost(K-1)
```

This is the biggest trick of this pattern.

---

## Generic Template

```cpp
answer = atMost(k) - atMost(k-1);
```

where

```cpp
int atMost(vector<int>& nums,int k){

    if(k<0) return 0;

    int left=0;
    int right=0;

    int answer=0;

    while(right<n){

        // Add current contribution

        while(window invalid){

            // Remove left contribution

            left++;
        }

        answer += right-left+1;

        right++;
    }

    return answer;
}
```

---

# LeetCode 930 — Binary Subarrays With Sum

## My Learning

This problem introduced the **Exactly K = AtMost(K) − AtMost(K−1)** trick.

Since the array contains only

```text
0
```

and

```text
1
```

the window sum always moves predictably.

---

## Important Observation

Every valid window contributes

```text
right-left+1
```

subarrays.

---

# LeetCode 1248 — Count Number of Nice Subarrays

## My Learning

Exactly the same template as 930.

Only change:

Convert

```text
Odd → 1

Even → 0
```

Everything else remains identical.

Huge realization:

The pattern matters more than the problem statement.

---

# LeetCode 992 — Subarrays With K Different Integers

## My Learning

Initially I misunderstood the problem.

I thought duplicate numbers cannot exist inside the window.

Wrong.

Duplicates are perfectly allowed.

Only

```text
Distinct Count
```

matters.

HashMap stores frequency.

Distinct counter stores how many different numbers currently exist.

---

## Generic Algorithm

1. Expand.
2. Increase frequency.
3. If first occurrence → distinct++.
4. Shrink until distinct ≤ k.
5. Add

```text
right-left+1
```

6. Exactly K

```text
AtMost(k)-AtMost(k-1)
```

---

# Pattern 5 — Counting Valid Windows

---

## Observation

Previous patterns counted

```text
right-left+1
```

because every starting point inside the window creates a valid subarray.

Now the question changes.

Instead of asking

```text
How many windows end here?
```

it asks

```text
How many windows START here?
```

Huge difference.

---

# LeetCode 1358 — Number of Substrings Containing All Three Characters

## My Biggest Learning

The moment the current window contains

```text
a

b

c
```

every extension towards the right is also valid.

Therefore

```text
Answer += n-right
```

instead of

```text
right-left+1
```

This was one of the biggest Sliding Window observations.

---

## Generic Algorithm

1. Expand.
2. Count

```text
a

b

c
```

3. Once valid

```text
ans += n-right
```

4. Shrink while still valid.
5. Repeat.

---

## Complexity

Time : O(n)

Space : O(1)

---

# Summary Till Now

✅ Fixed Window

✅ Variable Window

✅ Constraint Window

✅ Exactly K

✅ Counting Valid Windows

These five patterns alone solve a very large portion of Sliding Window interview questions.


# Pattern 6 — Product Window

---

## Concept

Until now every problem maintained

```text
Window Sum
```

This pattern is almost identical.

The only difference is that instead of maintaining the sum, we maintain the **product**.

---

## Generic Template

```cpp
while(right < n){

    // Expand window
    product *= nums[right];

    // Shrink until valid
    while(left <= right && product >= k){

        product /= nums[left];
        left++;
    }

    // Every subarray ending at right is valid
    ans += right-left+1;

    right++;
}
```

---

# LeetCode 713 — Subarray Product Less Than K

## Intuition

Maintain the product of the current window.

If product becomes greater than or equal to `k`, shrink the window.

Every valid window contributes

```text
right-left+1
```

subarrays.

---

## My Solution

```cpp
class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {

        // Product can never become < 1
        if(k <= 1)
            return 0;

        int n = nums.size();

        int left = 0;
        int right = 0;

        long long currentProduct = 1;

        long long answer = 0;

        while(right < n){

            // Expand window
            currentProduct *= nums[right];

            // Shrink until product becomes valid
            while(left <= right && currentProduct >= k){

                currentProduct /= nums[left];
                left++;
            }

            // Every starting point inside window forms a valid subarray
            answer += right-left+1;

            right++;
        }

        return answer;
    }
};
```

---

## Algorithm

1. Multiply current element.
2. If product becomes invalid, divide from left.
3. Continue shrinking.
4. Add

```text
right-left+1
```

to answer.

---

## Complexity

Time : O(n)

Space : O(1)

---

## My Learning

Initially I forgot one important edge case.

For

```text
k = 1
```

no positive product can ever become smaller than `1`.

So

```cpp
if(k <= 1)
    return 0;
```

is mandatory.

---

# Pattern 7 — Frequency Window

---

## Concept

This is one of the smartest Sliding Window problems.

Instead of maintaining a constraint directly,

we calculate

```text
How many operations are required
```

to make every element inside the current window equal.

---

## Biggest Observation

Always sort the array first.

Why?

Because we can only increase numbers.

Therefore,

the largest number inside the window

becomes the target.

---

## Formula

```text
Operations Needed

=

(Target × Window Size)

-

Current Window Sum
```

where

```text
Target

=

nums[right]
```

---

## Generic Template

```cpp
Sort array

Expand window

Maintain window sum

Calculate operations

while(operations > k){

    Remove left element

    Recalculate operations

}

Update answer
```

---

# LeetCode 1838 — Frequency of the Most Frequent Element

## Intuition

After sorting,

try to convert every element inside the current window into

```text
nums[right]
```

If required operations exceed `k`,

shrink.

---

## My Solution

```cpp
class Solution {
public:
    int maxFrequency(vector<int>& nums, int k) {

        long long n = nums.size();

        long long left = 0;
        long long right = 0;

        long long answer = 0;

        long long windowSum = 0;

        sort(nums.begin(), nums.end());

        while(right < n){

            // Add current element to the window sum
            windowSum += nums[right];

            // Current window size
            long long windowSize = right-left+1;

            // Sum required if every element becomes nums[right]
            long long requiredWindowSum = nums[right] * windowSize;

            // Operations needed
            long long operationsNeeded = requiredWindowSum-windowSum;

            // Shrink while operations exceed k
            while(operationsNeeded > k){

                windowSum -= nums[left];
                left++;

                // Recalculate after shrinking
                windowSize = right-left+1;

                requiredWindowSum = nums[right] * windowSize;

                operationsNeeded = requiredWindowSum-windowSum;
            }

            // Store maximum valid window
            answer = max(answer,right-left+1);

            right++;
        }

        return answer;
    }
};
```

---

## Algorithm

1. Sort array.
2. Expand window.
3. Maintain window sum.
4. Calculate operations.
5. Shrink if operations exceed `k`.
6. Update maximum frequency.

---

## Complexity

Time :

```text
O(n log n)
```

because of sorting.

Space :

```text
O(1)
```

---

## My Learning

This formula was the biggest breakthrough.

```text
Operations Needed

=

(Target × Window Size)

-

Current Window Sum
```

Initially this formula looked difficult,

but after deriving it,

the problem became straightforward.

---

# Pattern 8 — Monotonic Queue

---

## Concept

Normal Sliding Window cannot efficiently answer

```text
Maximum of every window
```

Brute Force

```text
O(n × k)
```

Need

```text
O(n)
```

Solution

```text
Monotonic Deque
```

---

## Why Deque?

Maintain elements in

```text
Decreasing Order
```

Front always stores

```text
Maximum
```

---

## Biggest Learning

Store

```text
Indices
```

instead of values.

Reason:

Need to know

```text
Which element has left the window.
```

Values alone cannot tell that.

---

## Rules

### Rule 1

Remove expired indices.

```cpp
while(!dq.empty() && dq.front() < left)
    dq.pop_front();
```

---

### Rule 2

Remove every smaller element from the back.

```cpp
while(!dq.empty() && nums[dq.back()] < nums[right])
    dq.pop_back();
```

---

### Rule 3

Insert current index.

```cpp
dq.push_back(right);
```

---

### Rule 4

Front stores maximum.

```cpp
nums[dq.front()]
```

---

# LeetCode 239 — Sliding Window Maximum

## Intuition

Every element smaller than the current element becomes useless.

Remove it immediately.

The deque always remains in decreasing order.

---

## My Solution

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {

        int n = nums.size();

        int left = 0;
        int right = 0;

        deque<int> dq;

        vector<int> answer;

        while(right < n){

            // Remove expired indices
            while(!dq.empty() && dq.front() < left)
                dq.pop_front();

            // Remove smaller elements
            while(!dq.empty() && nums[dq.back()] < nums[right])
                dq.pop_back();

            // Insert current index
            dq.push_back(right);

            // First complete window
            if(right >= k-1){

                // Front stores maximum
                answer.push_back(nums[dq.front()]);

                left++;
            }

            right++;
        }

        return answer;
    }
};
```

---

## Algorithm

1. Remove expired indices.
2. Remove smaller values.
3. Insert current index.
4. Once window forms,

```cpp
nums[dq.front()]
```

is the maximum.

5. Slide window.

---

## Complexity

Time :

```text
O(n)
```

Space :

```text
O(k)
```

---

## My Learning

Initially I wanted to keep track of

* Maximum
* Second Maximum

Then realized it becomes impossible when the maximum leaves.

Monotonic Queue automatically maintains

```text
Maximum

↓

Second Maximum

↓

Third Maximum
```

without rescanning.

---

# Final Revision Sheet

## Pattern Recognition

| Pattern          | Formula / Observation               |
| ---------------- | ----------------------------------- |
| Fixed Window     | Build first window then slide       |
| Variable Window  | Expand → Shrink → Update            |
| At Most K        | `ans += right-left+1`               |
| Exactly K        | `AtMost(k)-AtMost(k-1)`             |
| Counting Windows | `ans += n-right`                    |
| Product Window   | Maintain product instead of sum     |
| Frequency Window | `(Target × Window Size)-Window Sum` |
| Monotonic Queue  | Store indices in decreasing order   |

---

# Sliding Window Problems Solved

| LeetCode | Problem                                                 |
| -------: | ------------------------------------------------------- |
|      643 | Maximum Average Subarray I                              |
|     1343 | Number of Subarrays of Size K and Average ≥ Threshold   |
|     1456 | Maximum Number of Vowels in a Substring of Given Length |
|      209 | Minimum Size Subarray Sum                               |
|        3 | Longest Substring Without Repeating Characters          |
|     1004 | Max Consecutive Ones III                                |
|      424 | Longest Repeating Character Replacement                 |
|      904 | Fruit Into Baskets                                      |
|     1493 | Longest Subarray of 1's After Deleting One Element      |
|      930 | Binary Subarrays With Sum                               |
|     1248 | Count Number of Nice Subarrays                          |
|      992 | Subarrays With K Different Integers                     |
|     1358 | Number of Substrings Containing All Three Characters    |
|      713 | Subarray Product Less Than K                            |
|     1838 | Frequency of the Most Frequent Element                  |
|      239 | Sliding Window Maximum                                  |

---

# End Goal Achieved ✅

After completing these problems, I have learned:

* Fixed Sliding Window
* Variable Sliding Window
* Constraint Based Window
* At Most K
* Exactly K
* Counting Valid Windows
* Product Window
* Frequency Optimization
* Monotonic Queue

These patterns cover the vast majority of Sliding Window interview questions on LeetCode.

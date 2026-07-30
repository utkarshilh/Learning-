# Sliding Window Handbook 🚀

> Personal Notes
>
> Language: **C++**
>
> These notes contain my accepted solutions along with the pattern used to solve them.

---

# Pattern 1 — Fixed Size Sliding Window

## When to Identify?

- Window size is already given.
- Every window has exactly **K** elements.
- We only slide the window.

---

## Template

```cpp
// Build first window

// Calculate initial answer

while(right < n){

    // Remove left contribution

    // Add right contribution

    // Update answer

    left++;
    right++;
}
```

---

# LeetCode 643 — Maximum Average Subarray I

### Idea

Build the first window of size `k`. While sliding the window, remove the left element and add the new right element instead of recalculating the sum every time.

---

## Code

```cpp
class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {

        int windowSum = 0;

        // Build first window
        for(int i = 0; i < k; i++)
            windowSum += nums[i];

        int maxSum = windowSum;

        int left = 0;
        int right = k;

        while(right < nums.size()){

            // Remove left contribution
            windowSum -= nums[left];

            // Add new element
            windowSum += nums[right];

            // Update maximum sum
            maxSum = max(maxSum, windowSum);

            left++;
            right++;
        }

        return (double)maxSum / k;
    }
};
```

---

# LeetCode 1343 — Number of Subarrays of Size K and Average Greater Than or Equal to Threshold

### Idea

Instead of checking the average, convert it into a sum comparison.

```
Average >= Threshold

↓

Sum >= Threshold × K
```

Now it becomes a simple fixed window problem.

---

## Code

```cpp
class Solution {
public:
    int numOfSubarrays(vector<int>& arr, int k, int threshold) {

        int targetSum = threshold * k;

        int windowSum = 0;

        // Build first window
        for(int i = 0; i < k; i++)
            windowSum += arr[i];

        int answer = 0;

        if(windowSum >= targetSum)
            answer++;

        int left = 0;
        int right = k;

        while(right < arr.size()){

            // Remove left contribution
            windowSum -= arr[left];

            // Add current element
            windowSum += arr[right];

            // Count valid window
            if(windowSum >= targetSum)
                answer++;

            left++;
            right++;
        }

        return answer;
    }
};
```

---

# LeetCode 1456 — Maximum Number of Vowels in a Substring of Given Length

### Idea

Maintain the number of vowels inside the current window. While sliding, remove the left character's contribution and add the new right character's contribution.

---

## Code

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
        for(int i = 0; i < k; i++)
            if(isVowel(s[i]))
                vowelCount++;

        int answer = vowelCount;

        int left = 0;
        int right = k;

        while(right < s.size()){

            // Remove left contribution
            if(isVowel(s[left]))
                vowelCount--;

            // Add current character
            if(isVowel(s[right]))
                vowelCount++;

            // Update answer
            answer = max(answer, vowelCount);

            left++;
            right++;
        }

        return answer;
    }
};
```

---

# Pattern 1 Summary

### Characteristics

- Window size is fixed.
- Build the first window once.
- Remove left contribution.
- Add right contribution.
- Update the answer.
- Repeat until the end of the array.

### Problems Covered

- ✅ 643 — Maximum Average Subarray I
- ✅ 1343 — Number of Subarrays of Size K and Average ≥ Threshold
- ✅ 1456 — Maximum Number of Vowels in a Substring of Given Length

# Pattern 2 — Variable Size Sliding Window

## When to Identify?

- Window size is **not fixed**.
- We keep expanding the window.
- Once the window becomes invalid, shrink it until it becomes valid again.

---

## Template

```cpp
while(right < n){

    // Expand the window
    add current element

    // Shrink until window becomes valid
    while(window is invalid){

        remove left contribution

        left++;
    }

    // Update answer

    right++;
}
```

---

# LeetCode 209 — Minimum Size Subarray Sum

### Idea

Expand the window until the sum becomes greater than or equal to the target. Once the window is valid, keep shrinking it to obtain the minimum possible window.

---

## Code

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {

        int left = 0;
        int right = 0;

        int windowSum = 0;
        int answer = INT_MAX;

        while(right < nums.size()){

            // Expand window
            windowSum += nums[right];

            // Shrink while window is valid
            while(windowSum >= target){

                answer = min(answer, right - left + 1);

                windowSum -= nums[left];
                left++;
            }

            right++;
        }

        return answer == INT_MAX ? 0 : answer;
    }
};
```

---

# LeetCode 3 — Longest Substring Without Repeating Characters

### Idea

Maintain the frequency of every character inside the window. If a duplicate character appears, shrink the window until every character becomes unique again.

---

## Code

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {

        unordered_map<char,int> frequency;

        int left = 0;
        int right = 0;

        int answer = 0;

        while(right < s.size()){

            // Include current character
            frequency[s[right]]++;

            // Remove duplicates
            while(frequency[s[right]] > 1){

                frequency[s[left]]--;
                left++;
            }

            // Update longest window
            answer = max(answer, right - left + 1);

            right++;
        }

        return answer;
    }
};
```

---

# Pattern 2 Summary

### Characteristics

- Window size is dynamic.
- Expand first.
- If the condition breaks, shrink until it becomes valid.
- Update the answer after obtaining a valid window.

### Problems Covered

- ✅ 209 — Minimum Size Subarray Sum
- ✅ 3 — Longest Substring Without Repeating Characters

# Pattern 3 — Constraint Based Sliding Window

## When to Identify?

- The window should satisfy a constraint.
- Examples:
  - At most **K** zeros.
  - At most **K** distinct elements.
  - At most **K** replacements.
- Expand the window first.
- If the constraint breaks, shrink until it becomes valid again.

---

## Template

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

# LeetCode 1004 — Max Consecutive Ones III

### Idea

Treat the number of zeros as the constraint. Expand the window normally, and whenever the zero count becomes greater than `k`, shrink the window until the constraint becomes valid again.

---

## Code

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {

        int left = 0;
        int right = 0;

        int zeroCount = 0;
        int answer = 0;

        while(right < nums.size()){

            // Expand window
            if(nums[right] == 0)
                zeroCount++;

            // Shrink until valid
            while(zeroCount > k){

                if(nums[left] == 0)
                    zeroCount--;

                left++;
            }

            // Update answer
            answer = max(answer, right - left + 1);

            right++;
        }

        return answer;
    }
};
```

---

# LeetCode 424 — Longest Repeating Character Replacement

### Idea

Keep track of the character having the highest frequency inside the window. The remaining characters are the ones that need replacement. If the required replacements become greater than `k`, shrink the window.

---

## Code

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {

        unordered_map<char,int> mp;

        int left = 0;
        int right = 0;

        int maxFrequency = 0;
        int answer = 0;

        while(right < s.size()){

            // Add current character
            mp[s[right]]++;

            // Update highest frequency
            maxFrequency = max(maxFrequency, mp[s[right]]);

            // Shrink until replacements <= k
            while((right - left + 1) - maxFrequency > k){

                mp[s[left]]--;
                left++;
            }

            // Update answer
            answer = max(answer, right - left + 1);

            right++;
        }

        return answer;
    }
};
```

---

# LeetCode 904 — Fruit Into Baskets

### Idea

Maintain the frequency of every fruit inside the window. The window is valid only while there are at most two distinct fruits.

---

## Code

```cpp
class Solution {
public:
    int totalFruit(vector<int>& fruits) {

        unordered_map<int,int> mp;

        int left = 0;
        int right = 0;

        int distinct = 0;
        int answer = 0;

        while(right < fruits.size()){

            // Add current fruit
            mp[fruits[right]]++;

            if(mp[fruits[right]] == 1)
                distinct++;

            // Shrink until only two distinct fruits remain
            while(distinct > 2){

                mp[fruits[left]]--;

                if(mp[fruits[left]] == 0)
                    distinct--;

                left++;
            }

            // Update answer
            answer = max(answer, right - left + 1);

            right++;
        }

        return answer;
    }
};
```

---

# LeetCode 1493 — Longest Subarray of 1's After Deleting One Element

### Idea

Allow at most one zero inside the window. Since one element must always be deleted, the answer is `windowSize - 1`, which is equivalent to `right - left`.

---

## Code

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums) {

        int left = 0;
        int right = 0;

        int zeroCount = 0;
        int answer = 0;

        while(right < nums.size()){

            // Expand window
            if(nums[right] == 0)
                zeroCount++;

            // Shrink until only one zero remains
            while(zeroCount > 1){

                if(nums[left] == 0)
                    zeroCount--;

                left++;
            }

            // One element must be deleted
            answer = max(answer, right - left);

            right++;
        }

        return answer;
    }
};
```

---

# Pattern 3 Summary

### Characteristics

- Window follows a constraint.
- Expand first.
- If the constraint breaks, shrink.
- Update the answer once the window becomes valid again.

### Problems Covered

- ✅ 1004 — Max Consecutive Ones III
- ✅ 424 — Longest Repeating Character Replacement
- ✅ 904 — Fruit Into Baskets
- ✅ 1493 — Longest Subarray of 1's After Deleting One Element



# Pattern 4 — Exactly K

## When to Identify?

- The question asks for **Exactly K**.
- Examples:
  - Exactly K odd numbers.
  - Exactly K distinct integers.
  - Exactly K sum.

Instead of solving **Exactly K** directly, convert it into:

```
Exactly(K) = AtMost(K) - AtMost(K-1)
```

This is one of the most important Sliding Window tricks.

---

## Template

```cpp
int answer = atMost(k) - atMost(k-1);
```

### At Most K Template

```cpp
int atMost(vector<int>& nums, int k){

    if(k < 0)
        return 0;

    int left = 0;
    int right = 0;

    int answer = 0;

    while(right < nums.size()){

        // Add current contribution

        while(window becomes invalid){

            // Remove left contribution

            left++;
        }

        // Count every valid subarray
        answer += right-left+1;

        right++;
    }

    return answer;
}
```

---

# LeetCode 930 — Binary Subarrays With Sum

### Idea

Count the number of subarrays having **at most goal** and **at most goal-1**. Their difference gives the number of subarrays having **exactly goal**.

---

## Code

```cpp
class Solution {
public:
    int numSubarraysWithSum(vector<int>& nums, int goal) {

        return call(nums, goal) - call(nums, goal - 1);
    }

    int call(vector<int>& nums, int goal){

        if(goal < 0)
            return 0;

        int left = 0;
        int right = 0;

        int windowSum = 0;
        int answer = 0;

        while(right < nums.size()){

            // Expand window
            windowSum += nums[right];

            // Shrink until valid
            while(windowSum > goal){

                windowSum -= nums[left];
                left++;
            }

            // Count all valid subarrays
            answer += right - left + 1;

            right++;
        }

        return answer;
    }
};
```

---

# LeetCode 1248 — Count Number of Nice Subarrays

### Idea

Treat every **odd number as 1** and every **even number as 0**. The problem then becomes identical to LeetCode 930.

---

## Code

```cpp
class Solution {
public:
    int numberOfSubarrays(vector<int>& nums, int k) {

        return call(nums, k) - call(nums, k - 1);
    }

    int call(vector<int>& nums, int k){

        if(k < 0)
            return 0;

        int left = 0;
        int right = 0;

        int oddCount = 0;
        int answer = 0;

        while(right < nums.size()){

            // Count odd numbers
            if(nums[right] % 2)
                oddCount++;

            // Shrink until valid
            while(oddCount > k){

                if(nums[left] % 2)
                    oddCount--;

                left++;
            }

            // Count valid subarrays
            answer += right - left + 1;

            right++;
        }

        return answer;
    }
};
```

---

# LeetCode 992 — Subarrays With K Different Integers

### Idea

Maintain the frequency of every number inside the window. Count subarrays having **at most K distinct numbers**, then subtract the answer for **at most K-1**.

---

## Code

```cpp
class Solution {
public:
    int subarraysWithKDistinct(vector<int>& nums, int k) {

        return call(nums, k) - call(nums, k - 1);
    }

    int call(vector<int>& nums, int k){

        int left = 0;
        int right = 0;

        unordered_map<int,int> frequency;

        int distinctCount = 0;
        int answer = 0;

        while(right < nums.size()){

            // Add current element
            frequency[nums[right]]++;

            if(frequency[nums[right]] == 1)
                distinctCount++;

            // Shrink until valid
            while(distinctCount > k){

                frequency[nums[left]]--;

                if(frequency[nums[left]] == 0)
                    distinctCount--;

                left++;
            }

            // Count valid subarrays
            answer += right - left + 1;

            right++;
        }

        return answer;
    }
};
```

---

# Pattern 4 Summary

### Characteristics

- Questions ask for **Exactly K**.
- Convert them into **At Most K**.
- Final Answer:

```
Exactly(K)
=
AtMost(K)
-
AtMost(K-1)
```

### Problems Covered

- ✅ 930 — Binary Subarrays With Sum
- ✅ 1248 — Count Number of Nice Subarrays
- ✅ 992 — Subarrays With K Different Integers

> **My Biggest Learning:** Once I understood the `AtMost(K) - AtMost(K-1)` trick, I realized these three problems are essentially the same pattern with only the window condition changing.
>
# Pattern 5 — Counting Valid Windows

## When to Identify?

- The question asks for the **number of valid substrings/subarrays**.
- Once the current window becomes valid, **every extension towards the right also remains valid**.
- Instead of adding `right - left + 1`, we add:

```
n - right
```

because every substring ending after `right` is also valid.

---

## Template

```cpp
while(right < n){

    // Expand window

    while(window becomes valid){

        // Every extension is also valid
        answer += n - right;

        // Shrink window
        remove left contribution;
        left++;
    }

    right++;
}
```

---

# LeetCode 1358 — Number of Substrings Containing All Three Characters

### Idea

Expand the window until it contains at least one `'a'`, `'b'`, and `'c'`. Once the window becomes valid, every substring extending to the right is also valid, so add `n - right` and continue shrinking.

---

## Code

```cpp
class Solution {
public:
    int numberOfSubstrings(string s) {

        int n = s.size();

        int left = 0;
        int right = 0;

        int a = 0;
        int b = 0;
        int c = 0;

        int answer = 0;

        while(right < n){

            // Expand window
            if(s[right] == 'a')
                a++;
            else if(s[right] == 'b')
                b++;
            else
                c++;

            // Shrink while window is valid
            while(a >= 1 && b >= 1 && c >= 1){

                // Every extension to the right is also valid
                answer += n - right;

                // Remove left contribution
                if(s[left] == 'a')
                    a--;
                else if(s[left] == 'b')
                    b--;
                else
                    c--;

                left++;
            }

            right++;
        }

        return answer;
    }
};
```

---

# Pattern 5 Summary

### Characteristics

- Count **all valid substrings**.
- Once the window becomes valid, every extension towards the right is also valid.
- Add:

```
n - right
```

instead of

```
right - left + 1
```

### Problems Covered

- ✅ 1358 — Number of Substrings Containing All Three Characters

> **My Biggest Learning:** Unlike previous counting problems where I used `right - left + 1`, this pattern counts all future extensions of the current valid window, so the contribution becomes `n - right`.
>
> # Pattern 6 — Product Sliding Window

## When to Identify?

- The question involves the **product** of elements instead of their sum.
- All numbers are **positive**.
- Expand the window by multiplying the current element.
- Shrink the window by dividing the left element until the product becomes valid.

> **Important:** This technique works only because all numbers are positive. With negative numbers or zeros (depending on the condition), this approach does not hold.

---

## Template

```cpp
if(k <= 1)
    return 0;

while(right < n){

    // Expand window
    product *= nums[right];

    // Shrink until product becomes valid
    while(left <= right && product >= k){

        product /= nums[left];
        left++;
    }

    // Count valid subarrays
    answer += right - left + 1;

    right++;
}
```

---

# LeetCode 713 — Subarray Product Less Than K

### Idea

Maintain the product of the current window. If the product becomes greater than or equal to `k`, keep removing elements from the left until the window becomes valid again. Every valid window contributes `right - left + 1` subarrays.

---

## Code

```cpp
class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {

        if(k <= 1)
            return 0;

        int n = nums.size();

        int left = 0;
        int right = 0;

        long long product = 1;
        long long answer = 0;

        while(right < n){

            // Expand window
            product *= nums[right];

            // Shrink until window becomes valid
            while(left <= right && product >= k){

                product /= nums[left];
                left++;
            }

            // Count all valid subarrays
            answer += right - left + 1;

            right++;
        }

        return answer;
    }
};
```

---

# Pattern 6 Summary

### Characteristics

- Maintain **product** instead of **sum**.
- Expand using multiplication.
- Shrink using division.
- Window remains valid while:

```
Product < K
```

- Count valid subarrays using:

```
right - left + 1
```

### Problems Covered

- ✅ 713 — Subarray Product Less Than K

> **My Biggest Learning:** Initially, I forgot the edge case `k <= 1`, which caused a buffer overflow because the window could never become valid. Adding `if(k <= 1) return 0;` fixes the issue.

# Pattern 7 — Frequency Sliding Window

## When to Identify?

- The question asks to make multiple elements **equal** using a limited number of operations.
- We are allowed to **increase** elements only.
- Sort the array first.
- Treat the **largest element in the current window** as the target.

---

## Template

```cpp
sort(nums.begin(), nums.end());

while(right < n){

    // Expand window
    windowSum += nums[right];

    // Calculate operations needed
    windowSize = right - left + 1;
    targetSum = nums[right] * windowSize;
    operationsNeeded = targetSum - windowSum;

    // Shrink until valid
    while(operationsNeeded > k){

        windowSum -= nums[left];
        left++;

        // Recalculate after shrinking
        windowSize = right - left + 1;
        targetSum = nums[right] * windowSize;
        operationsNeeded = targetSum - windowSum;
    }

    // Update answer
    answer = max(answer, right - left + 1);

    right++;
}
```

---

# LeetCode 1838 — Frequency of the Most Frequent Element

### Idea

After sorting the array, assume the largest element in the current window is the target. Calculate the operations required to make every element equal to it. If the operations exceed `k`, shrink the window.

---

## Code

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

            // Add current element
            windowSum += nums[right];

            // Current window size
            long long windowSize = right - left + 1;

            // Sum required if every element becomes nums[right]
            long long targetSum = nums[right] * windowSize;

            // Operations required
            long long operationsNeeded = targetSum - windowSum;

            // Shrink until operations become valid
            while(operationsNeeded > k){

                windowSum -= nums[left];
                left++;

                // Recalculate after shrinking
                windowSize = right - left + 1;
                targetSum = nums[right] * windowSize;
                operationsNeeded = targetSum - windowSum;
            }

            // Store maximum valid window
            answer = max(answer, right - left + 1);

            right++;
        }

        return answer;
    }
};
```

---

# Pattern 7 Summary

### Characteristics

- Sort the array first.
- The largest element in the current window becomes the target.
- Maintain the window sum.
- Calculate operations using:

```
Operations Needed
=
(Target × Window Size)
-
Window Sum
```

- Shrink if operations exceed `k`.
- Update the maximum valid window.

### Problems Covered

- ✅ 1838 — Frequency of the Most Frequent Element

> **My Biggest Learning:** The key realization was deriving the formula:
>
> ```
> Operations Needed
> =
> (Target × Window Size)
> -
> Window Sum
> ```
>
> Once I understood this, the problem became a standard Sliding Window question with a new validity condition.

# Pattern 8 — Monotonic Queue (Deque)

## When to Identify?

- The question asks for the **maximum** or **minimum** element in every window.
- A normal Sliding Window is not enough because finding the maximum repeatedly takes `O(k)`.
- Use a **Monotonic Deque** to maintain the maximum (or minimum) in `O(1)` time.

> **Important:** Always store **indices**, not values, so that we know when an element goes out of the current window.

---

## Template

```cpp
while(right < n){

    // Remove indices outside the current window
    while(!dq.empty() && dq.front() < left)
        dq.pop_front();

    // Remove smaller elements from the back
    while(!dq.empty() && nums[dq.back()] < nums[right])
        dq.pop_back();

    // Insert current index
    dq.push_back(right);

    // First window is ready
    if(right >= k - 1){

        answer.push_back(nums[dq.front()]);

        left++;
    }

    right++;
}
```

---

# LeetCode 239 — Sliding Window Maximum

### Idea

Maintain a deque in decreasing order of values. Remove smaller elements from the back because they can never become the maximum. The front of the deque always stores the index of the maximum element in the current window.

---

## Code

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

            // Remove indices outside the current window
            while(!dq.empty() && dq.front() < left)
                dq.pop_front();

            // Remove smaller elements from the back
            while(!dq.empty() && nums[dq.back()] < nums[right])
                dq.pop_back();

            // Insert current index
            dq.push_back(right);

            // First complete window is formed
            if(right >= k - 1){

                // Front always stores the maximum element
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

# Pattern 8 Summary

### Characteristics

- Used to find **maximum/minimum** in every window.
- Store **indices**, not values.
- Remove expired indices from the front.
- Remove smaller elements from the back.
- Front of the deque always contains the answer.

### Problems Covered

- ✅ 239 — Sliding Window Maximum

> **My Biggest Learning:**
>
> Initially, I thought of maintaining the **largest** and **second largest** element manually, but that becomes difficult when the largest element leaves the window.
>
> The Monotonic Deque automatically maintains the correct order, and storing **indices** allows us to efficiently remove elements that are no longer inside the current window.

# Sliding Window Cheat Sheet 🚀

---

# Pattern Recognition

| Pattern | Question Clue | Formula / Observation |
|----------|---------------|----------------------|
| Fixed Window | Window size is fixed | Build first window and slide |
| Variable Window | Window size is dynamic | Expand → Shrink → Update |
| Constraint Window | At most K / K replacements / K zeros | Expand until invalid → Shrink |
| Exactly K | Exactly K | AtMost(K) - AtMost(K-1) |
| Counting Windows | Count valid substrings | `answer += n - right` |
| Product Window | Product < K | Maintain product instead of sum |
| Frequency Window | Make elements equal | `(Target × Window Size) - Window Sum` |
| Monotonic Queue | Maximum / Minimum in every window | Maintain decreasing/increasing deque |

---

# Sliding Window Templates

## 1. Fixed Window

```cpp
Build first window

while(right < n){

    Remove left contribution

    Add right contribution

    Update answer

    left++;
    right++;
}
```

---

## 2. Variable Window

```cpp
while(right < n){

    Expand window

    while(window becomes invalid){

        Remove left contribution

        left++;
    }

    Update answer

    right++;
}
```

---

## 3. At Most K

```cpp
while(right < n){

    Add current element

    while(window becomes invalid){

        Remove left contribution

        left++;
    }

    answer += right-left+1;

    right++;
}
```

---

## 4. Exactly K

```cpp
Exactly(K)
=
AtMost(K)
-
AtMost(K-1)
```

---

## 5. Counting Valid Windows

```cpp
while(window becomes valid){

    answer += n-right;

    Remove left contribution

    left++;
}
```

---

## 6. Product Window

```cpp
product *= nums[right];

while(product >= k){

    product /= nums[left];

    left++;
}
```

---

## 7. Frequency Window

```cpp
Operations Needed

=

(Target × Window Size)

-

Window Sum
```

---

## 8. Monotonic Queue

```cpp
Remove expired indices

Remove smaller elements

Insert current index

Front stores answer
```

---

# Problems Covered

## Pattern 1 — Fixed Window

- ✅ 643 — Maximum Average Subarray I
- ✅ 1343 — Number of Subarrays of Size K and Average ≥ Threshold
- ✅ 1456 — Maximum Number of Vowels in a Substring of Given Length

---

## Pattern 2 — Variable Window

- ✅ 209 — Minimum Size Subarray Sum
- ✅ 3 — Longest Substring Without Repeating Characters

---

## Pattern 3 — Constraint Window

- ✅ 1004 — Max Consecutive Ones III
- ✅ 424 — Longest Repeating Character Replacement
- ✅ 904 — Fruit Into Baskets
- ✅ 1493 — Longest Subarray of 1's After Deleting One Element

---

## Pattern 4 — Exactly K

- ✅ 930 — Binary Subarrays With Sum
- ✅ 1248 — Count Number of Nice Subarrays
- ✅ 992 — Subarrays With K Different Integers

---

## Pattern 5 — Counting Valid Windows

- ✅ 1358 — Number of Substrings Containing All Three Characters

---

## Pattern 6 — Product Window

- ✅ 713 — Subarray Product Less Than K

---

## Pattern 7 — Frequency Window

- ✅ 1838 — Frequency of the Most Frequent Element

---

## Pattern 8 — Monotonic Queue

- ✅ 239 — Sliding Window Maximum

---

# Most Important Formulas

## Exactly K

```cpp
Exactly(K)
=
AtMost(K)
-
AtMost(K-1)
```

---

## Counting Valid Windows

```cpp
answer += n-right;
```

---

## Count All Valid Subarrays

```cpp
answer += right-left+1;
```

---

## Frequency Window

```cpp
Operations Needed

=

(Target × Window Size)

-

Window Sum
```

---

# Biggest Learnings

### Pattern 1

- Build the first window only once.
- Slide by removing one element and adding one element.

---

### Pattern 2

- Expand first.
- Shrink only when the window becomes invalid.

---

### Pattern 3

- Every problem has a different constraint.
- The sliding window logic remains the same.

---

### Pattern 4

Instead of solving **Exactly K**, solve

```cpp
AtMost(K)
```

and

```cpp
AtMost(K-1)
```

---

### Pattern 5

When the window becomes valid,

every extension to the right also remains valid.

Therefore,

```cpp
answer += n-right;
```

---

### Pattern 6

Maintain **product**, not sum.

Don't forget:

```cpp
if(k <= 1)
    return 0;
```

---

### Pattern 7

The most important derivation:

```cpp
(Target × Window Size)
-
Window Sum
```

---

### Pattern 8

Always store **indices** inside the deque.

Never store values.

---

# Time Complexity

| Pattern | Complexity |
|----------|-----------|
| Fixed Window | O(n) |
| Variable Window | O(n) |
| Constraint Window | O(n) |
| At Most K | O(n) |
| Exactly K | O(n) |
| Counting Windows | O(n) |
| Product Window | O(n) |
| Frequency Window | O(n log n) (sorting) |
| Monotonic Queue | O(n) |

---

# Final Progress

| Pattern | Status |
|----------|--------|
| ✅ Fixed Window | Completed |
| ✅ Variable Window | Completed |
| ✅ Constraint Window | Completed |
| ✅ Exactly K | Completed |
| ✅ Counting Windows | Completed |
| ✅ Product Window | Completed |
| ✅ Frequency Window | Completed |
| ✅ Monotonic Queue | Completed |

---

# Sliding Window Mastered ✅

After completing these problems, I can now identify and solve:

- Fixed Window
- Variable Window
- Constraint-Based Window
- At Most K
- Exactly K
- Counting Valid Windows
- Product-Based Windows
- Frequency Optimization
- Monotonic Queue

These patterns cover the majority of Sliding Window questions asked in coding interviews.

# Part 10 — Sliding Window Pattern Recognition

> The goal of this section is **not to solve** the problems.
>
> It is only to train yourself to quickly identify **which Sliding Window pattern** should be used.

---

# Pattern 1 — Fixed Size Sliding Window

## Identification

- Window size is already given.
- Every window contains exactly **K** elements.
- Slide the window one step at a time.

### Problems

| LeetCode | Problem |
|----------:|---------|
| 643 | Maximum Average Subarray I |
| 1343 | Number of Subarrays of Size K and Average ≥ Threshold |
| 1456 | Maximum Number of Vowels in a Substring of Given Length |
| 1423 | Maximum Points You Can Obtain from Cards |
| 1052 | Grumpy Bookstore Owner |
| 2461 | Maximum Sum of Distinct Subarrays With Length K |

---

# Pattern 2 — Variable Size Sliding Window

## Identification

- Window size is **not fixed**.
- Expand first.
- Shrink when the condition becomes invalid.
- Find the maximum/minimum length.

### Problems

| LeetCode | Problem |
|----------:|---------|
| 209 | Minimum Size Subarray Sum |
| 3 | Longest Substring Without Repeating Characters |
| 76 | Minimum Window Substring |
| 340 | Longest Substring with At Most K Distinct Characters |
| 159 | Longest Substring with At Most Two Distinct Characters |
| 1208 | Get Equal Substrings Within Budget |

---

# Pattern 3 — Constraint Based Window

## Identification

Questions containing:

- At most K
- Replace at most K
- Flip at most K
- Choose at most K

### Problems

| LeetCode | Problem |
|----------:|---------|
| 1004 | Max Consecutive Ones III |
| 424 | Longest Repeating Character Replacement |
| 904 | Fruit Into Baskets |
| 1493 | Longest Subarray of 1's After Deleting One Element |
| 2024 | Maximize the Confusion of an Exam |
| 1839 | Longest Substring Of All Vowels in Order |

---

# Pattern 4 — Exactly K

## Identification

Questions containing

- Exactly K
- Exactly goal
- Exactly distinct
- Exactly odd

Think immediately

```
Exactly(K)

↓

AtMost(K)

-

AtMost(K-1)
```

### Problems

| LeetCode | Problem |
|----------:|---------|
| 930 | Binary Subarrays With Sum |
| 1248 | Count Number of Nice Subarrays |
| 992 | Subarrays With K Different Integers |
| 560* | Subarray Sum Equals K *(Prefix Sum approach instead of Sliding Window because negative numbers exist.)* |

---

# Pattern 5 — Counting Valid Windows

## Identification

Questions asking

```
Count

Number of substrings

Number of subarrays
```

where once the window becomes valid,

every extension also remains valid.

### Problems

| LeetCode | Problem |
|----------:|---------|
| 1358 | Number of Substrings Containing All Three Characters |
| 2962 | Count Subarrays Where Max Element Appears at Least K Times |
| 2799 | Count Complete Subarrays in an Array |

---

# Pattern 6 — Product Window

## Identification

Questions involving

```
Product
```

instead of

```
Sum
```

All numbers should generally be positive.

### Problems

| LeetCode | Problem |
|----------:|---------|
| 713 | Subarray Product Less Than K |

---

# Pattern 7 — Frequency Window

## Identification

Questions asking

```
Make all elements equal

Maximum frequency

Operations
```

Usually involves sorting first.

### Problems

| LeetCode | Problem |
|----------:|---------|
| 1838 | Frequency of the Most Frequent Element |

---

# Pattern 8 — Monotonic Queue

## Identification

Questions asking

- Maximum in every window
- Minimum in every window

Need

```
O(n)
```

instead of

```
O(n × k)
```

### Problems

| LeetCode | Problem |
|----------:|---------|
| 239 | Sliding Window Maximum |
| 862* | Shortest Subarray with Sum at Least K *(Monotonic Deque + Prefix Sum)* |
| 1438 | Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit |
| 1696 | Jump Game VI |
| 1425 | Constrained Subsequence Sum |

---

# Decision Tree

```
Is window size fixed?

│

├── YES
│      ↓
│   Fixed Window
│
└── NO

      │

      ├── Need maximum/minimum length?
      │         ↓
      │   Variable Window
      │
      ├── At most K?
      │         ↓
      │   Constraint Window
      │
      ├── Exactly K?
      │         ↓
      │   AtMost(K)-AtMost(K-1)
      │
      ├── Count valid windows?
      │         ↓
      │   Counting Window
      │
      ├── Product?
      │         ↓
      │   Product Window
      │
      ├── Make values equal?
      │         ↓
      │   Frequency Window
      │
      └── Maximum/Minimum of every window?
                ↓
         Monotonic Queue
```

---

# Final Checklist Before Solving

✅ Is the window size fixed?

✅ Is the window variable?

✅ Does the question say **At Most K**?

✅ Does the question say **Exactly K**?

✅ Am I counting subarrays?

✅ Is it product instead of sum?

✅ Is there a frequency/operations formula?

✅ Do I need a Monotonic Deque?

---

# Sliding Window Complete 🎉

You have now covered the complete Sliding Window roadmap:

- ✅ Fixed Window
- ✅ Variable Window
- ✅ Constraint Window
- ✅ Exactly K
- ✅ Counting Valid Windows
- ✅ Product Window
- ✅ Frequency Window
- ✅ Monotonic Queue

**Total Problems Solved:** 16

This handbook now serves as a complete reference for revising the Sliding Window pattern before coding interviews.

# 🚀 Two Pointer Handbook

## Part 1 - Basic Two Pointer & Fast-Slow Pointer Problems

---

# Contents

1. LeetCode 167 - Two Sum II
2. LeetCode 125 - Valid Palindrome
3. LeetCode 26 - Remove Duplicates from Sorted Array
4. LeetCode 27 - Remove Element

---

# LeetCode 167 - Two Sum II (Input Array Is Sorted)

## Pattern

* Opposite Direction Two Pointer

---

## Problem

Given a **sorted** array of integers, return the **1-based indices** of the two numbers such that they add up to the target.

There is exactly one solution.

---

## Intuition

The most important observation is that **the array is already sorted**.

Instead of checking every possible pair using two loops, place:

* One pointer at the beginning.
* One pointer at the end.

Since the array is sorted:

* If the current sum is **smaller** than the target, we need a **larger** value. Move the **left pointer** to the right.
* If the current sum is **greater** than the target, we need a **smaller** value. Move the **right pointer** to the left.

Each move eliminates many unnecessary comparisons.

---

## Algorithm

1. Initialize:

   * `left = 0`
   * `right = n - 1`
2. While `left < right`:

   * Calculate current sum.
   * If sum equals target, return indices.
   * If sum is smaller, move `left`.
   * Otherwise move `right`.

---

## Dry Run

```text
nums = [2,7,11,15]

target = 9

L           R

2 7 11 15

sum = 17

Too large

↓

Move Right

L      R

2 7 11

sum = 13

Too large

↓

Move Right

L   R

2 7

sum = 9

Answer Found
```

---

## Code

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {

        vector<int> ans;

        int left = 0;
        int right = nums.size() - 1;

        while(left < right){

            if(nums[left] + nums[right] == target){

                ans.push_back(left + 1);
                ans.push_back(right + 1);

                return ans;
            }

            else if(nums[left] + nums[right] > target){
                right--;
            }

            else{
                left++;
            }
        }

        return ans;
    }
};
```

---

## Time Complexity

```text
O(n)
```

Each pointer visits every element at most once.

---

## Space Complexity

```text
O(1)
```

---

## Key Points

* Array must be sorted.
* Never move both pointers unless the answer is found.
* Left pointer increases the sum.
* Right pointer decreases the sum.

---

## Similar Problems

* Boats to Save People
* Max Number of K Sum Pairs
* Container With Most Water

---

# LeetCode 125 - Valid Palindrome

## Pattern

* Opposite Direction Two Pointer

---

## Problem

Determine whether a string is a palindrome after:

* Ignoring all non-alphanumeric characters.
* Treating uppercase and lowercase letters as the same.

---

## Intuition

A palindrome reads the same from both ends.

However, the string may contain:

* Spaces
* Commas
* Symbols
* Punctuation

These characters should be ignored.

So before comparing:

* Skip invalid characters.
* Convert both characters to lowercase.
* Compare.

---

## Algorithm

1. Place one pointer at the beginning.
2. Place another pointer at the end.
3. If left character is invalid, move left.
4. If right character is invalid, move right.
5. Otherwise compare both characters.
6. If different → return false.
7. Continue until pointers cross.

---

## Dry Run

```text
"A man, a plan, a canal: Panama"

Left                        Right

A man, a plan, a canal: Panama

↓

Skip spaces

↓

Skip comma

↓

Compare

A == a

m == m

a == a

...

All characters matched

Return true
```

---

## Code

```cpp
class Solution {
public:
    bool isPalindrome(string s) {

        int left = 0;
        int right = s.size() - 1;

        while(left < right){

            char l = s[left];
            char r = s[right];

            if(!isalnum(l)){
                left++;
            }
            else if(!isalnum(r)){
                right--;
            }
            else if(tolower(l) == tolower(r)){
                left++;
                right--;
            }
            else{
                return false;
            }
        }

        return true;
    }
};
```

---

## Time Complexity

```text
O(n)
```

---

## Space Complexity

```text
O(1)
```

---

## Key Points

* Ignore all special characters.
* Use `isalnum()` to check valid characters.
* Use `tolower()` for case-insensitive comparison.
* Compare only valid characters.

---

## Similar Problems

* Reverse String
* Backspace String Compare

---

# LeetCode 26 - Remove Duplicates from Sorted Array

## Pattern

* Fast & Slow Pointer

---

## Problem

Remove duplicates from a sorted array **in-place** and return the number of unique elements.

---

## Intuition

Since the array is sorted, duplicate values always appear together.

Maintain:

* `index` → Last unique element.
* `i` → Current element being checked.

Whenever a new value is found:

* Increase `index`.
* Copy the new value.

Everything before `index` always contains unique elements.

---

## Algorithm

1. If array size is 1, return 1.
2. Start:

   * `index = 0`
   * `i = 1`
3. Compare current element with last unique element.
4. If different:

   * Move index.
   * Copy element.
5. Return `index + 1`.

---

## Your Final Solution (Cleaner Version)

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {

        int n = nums.size();

        if(n == 1)
            return 1;

        int i = 1;
        int index = 0;

        while(i < n){

            if(nums[index] == nums[i]){
                i++;
            }
            else{
                nums[++index] = nums[i];
                i++;
            }
        }

        return index + 1;
    }
};
```

---

## Dry Run

```text
1 1 2 2 3

index

↓

1 1 2 2 3

i

↓

Duplicate

Move i

-----------------

1 1 2 2 3

index

↓

      i

Different

Copy

↓

1 2 2 2 3

index

↓

```

Continue until end.

Answer

```text
1 2 3
```

---

## Time Complexity

```text
O(n)
```

---

## Space Complexity

```text
O(1)
```

---

## Key Points

* Array must be sorted.
* Everything before `index` is always unique.
* Fast pointer scans.
* Slow pointer stores answer.

---

## Similar Problems

* Remove Duplicates II
* Move Zeroes

---

# LeetCode 27 - Remove Element

## Pattern

* Fast & Slow Pointer

---

## Problem

Remove every occurrence of a given value from the array **in-place**.

Return the number of remaining elements.

---

## Intuition

Instead of removing elements one by one, simply overwrite unwanted elements.

Maintain:

* `fast` → Scans every element.
* `slow` → Stores valid elements.

Whenever a valid element is found:

* Copy it to the next available position.

---

## Algorithm

1. Initialize:

   * `slow = -1`
   * `fast = 0`
2. Traverse entire array.
3. If current value is not equal to target:

   * Copy it.
4. Return number of valid elements.

---

## Your Final Solution

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {

        int n = nums.size();

        int slow = -1;
        int fast = 0;

        while(fast < n){

            if(nums[fast] != val)
                nums[++slow] = nums[fast++];

            else
                fast++;
        }

        return slow + 1;
    }
};
```

---

## Dry Run

```text
nums

3 2 2 3

remove = 3

fast

↓

3 2 2 3

Ignore

-----------------

fast

↓

3 2 2 3

Copy

↓

2

-----------------

fast

↓

3 2 2 3

Copy

↓

2 2

Answer

Length = 2
```

---

## Time Complexity

```text
O(n)
```

---

## Space Complexity

```text
O(1)
```

---

## Key Points

* No sorting required.
* Order of remaining elements is preserved.
* Fast pointer scans.
* Slow pointer builds the final array.

---

## Similar Problems

* Move Zeroes
* Remove Duplicates
* Remove Duplicates II

---

# Part 1 Summary

| Problem           | Pattern             | Time | Space |
| ----------------- | ------------------- | ---- | ----- |
| Two Sum II        | Opposite Pointer    | O(n) | O(1)  |
| Valid Palindrome  | Opposite Pointer    | O(n) | O(1)  |
| Remove Duplicates | Fast & Slow Pointer | O(n) | O(1)  |
| Remove Element    | Fast & Slow Pointer | O(n) | O(1)  |

---

## Pattern Learned

### Opposite Pointer

* Start from both ends.
* Used mainly in sorted arrays and strings.
* Move one pointer based on the current condition.

### Fast & Slow Pointer

* Fast pointer explores the array.
* Slow pointer stores or builds the answer.
* Frequently used for in-place array modification.

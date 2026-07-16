# 🚀 Two Pointer Interview Handbook

> A Complete Interview Guide from Basics to Advanced

---

# Table of Contents

* [1. Introduction](#1-introduction)
* [2. What is the Two Pointer Technique?](#2-what-is-the-two-pointer-technique)
* [3. Why Use Two Pointers?](#3-why-use-two-pointers)
* [4. Time & Space Complexity](#4-time--space-complexity)
* [5. When Should You Think About Two Pointers?](#5-when-should-you-think-about-two-pointers)
* [6. Types of Two Pointer Problems](#6-types-of-two-pointer-problems)
* [7. Pattern Recognition Guide](#7-pattern-recognition-guide)
* [8. General Templates](#8-general-templates)
* [9. Common Interview Mistakes](#9-common-interview-mistakes)
* [10. Quick Revision Cheat Sheet](#10-quick-revision-cheat-sheet)

---

# 1. Introduction

The **Two Pointer Technique** is one of the most frequently used problem-solving patterns in coding interviews.

Instead of using nested loops to compare multiple elements, we maintain **two indices (pointers)** and move them according to the problem's conditions.

Many brute-force solutions with **O(n²)** complexity can be optimized to **O(n)** using this technique.

It is commonly used in:

* Arrays
* Strings
* Linked Lists
* Interval Problems
* Sliding Window (extension of Two Pointers)

Mastering this pattern significantly improves your ability to solve medium-level interview problems.

---

# 2. What is the Two Pointer Technique?

The Two Pointer technique uses **two variables (indices)** that traverse a data structure in a controlled manner.

Example:

```text
Index

0 1 2 3 4 5 6

Array

2 4 6 8 10 12 14

L             R
```

The pointers may:

* Move towards each other
* Move together
* Move independently
* Represent a window
* Traverse two different arrays

Unlike brute force, we don't restart comparisons repeatedly.

Every pointer movement eliminates unnecessary work.

---

# 3. Why Use Two Pointers?

Suppose we want to find two numbers whose sum equals a target.

### Brute Force

```cpp
for(int i=0;i<n;i++)
{
    for(int j=i+1;j<n;j++)
    {
        ...
    }
}
```

Time Complexity:

```text
O(n²)
```

If the array is sorted, we don't need to compare every pair.

Instead,

```text
Left ------------->

<------------- Right
```

At every step we eliminate a large number of impossible combinations.

This reduces complexity to

```text
O(n)
```

---

# 4. Time & Space Complexity

Most Two Pointer algorithms have:

| Complexity | Value |
| ---------- | ----- |
| Time       | O(n)  |
| Space      | O(1)  |

### Why is Time Complexity O(n)?

Consider:

```text
1 2 3 4 5 6 7

L           R
```

* Left pointer moves from left to right.
* Right pointer moves from right to left.

Each pointer visits every element **at most once**.

Maximum movements:

```text
Left Pointer  = n

Right Pointer = n
```

Total

```text
2n
```

Ignoring constants,

```text
O(n)
```

---

# 5. When Should You Think About Two Pointers?

Whenever you encounter these keywords, immediately check if Two Pointers can be applied.

## Arrays

* Sorted Array
* Pair Sum
* Triplet Sum
* Quadruplet Sum
* Merge Arrays
* Remove Duplicates
* Remove Element
* Maximum Area
* Water Trapping

---

## Strings

* Palindrome
* Reverse String
* Compare Characters
* Ignore Special Characters
* Backspace Processing

---

## Multiple Arrays

* Merge
* Compare
* Intersection

---

## Common Interview Hint

If the interviewer explicitly says:

> "The array is sorted."

Always ask yourself:

> "Can I solve this using Two Pointers?"

Many interview questions are intentionally designed around this clue.

---

# 6. Types of Two Pointer Problems

There are five major categories.

---

# Type 1 — Opposite Direction Pointers

Both pointers start from opposite ends.

```text
L ----------->

<----------- R
```

Used when:

* Array is sorted
* Compare both ends
* Find pair sum
* Maximize/minimize something

Examples:

* Two Sum II
* Container With Most Water
* Boats to Save People
* Trapping Rain Water
* Valid Palindrome

---

# Type 2 — Fast & Slow Pointer

Both pointers move in the same direction.

```text
Slow

↓

Fast ---------->
```

Usually,

* Slow points to the answer.
* Fast explores the array.

Examples

* Remove Duplicates
* Remove Element
* Move Zeroes

---

# Type 3 — Fixed Pointer + Two Pointer

Fix one index.

Run two pointers on the remaining array.

```text
Fixed

↓

1 2 3 4 5 6

  L       R
```

Examples

* 3Sum
* 3Sum Closest
* 4Sum

---

# Type 4 — Two Sorted Arrays

Each pointer belongs to a different array.

```text
Array A

^

Array B

^
```

Examples

* Merge Sorted Array
* Interval List Intersection

---

# Type 5 — Sliding Window (Extension)

Both pointers move forward.

Instead of comparing values,

they represent a **window**.

```text
L -------- R
```

Examples

* Longest Substring Without Repeating Characters
* Minimum Size Subarray Sum
* Maximum Consecutive Ones

> Sliding Window is essentially an extension of the Two Pointer technique and will be covered separately.

---

# 7. Pattern Recognition Guide

Use this checklist during interviews.

## Case 1

Question contains

```text
Sorted Array
```

Possible patterns:

* Two Sum II
* Remove Duplicates
* Merge
* Squares of Sorted Array

---

## Case 2

Question asks

```text
Find Pair
```

Possible pattern

```text
Opposite Two Pointer
```

---

## Case 3

Question asks

```text
Find Triplets
```

Possible pattern

```text
Sort

↓

Fix One Element

↓

Two Pointer
```

---

## Case 4

Question asks

```text
Merge Two Sorted Arrays
```

Pattern

```text
Two Arrays

↓

Two Pointers
```

---

## Case 5

Question contains

```text
Palindrome
```

Pattern

```text
Compare

↓

Left

↓

Right
```

---

## Case 6

Question contains

```text
Remove

Move

Compress
```

Pattern

```text
Fast & Slow Pointer
```

---

# Decision Tree

```text
                    START
                      │
                      │
          Is Array Sorted?
               /          \
             Yes          No
              │            │
              │            │
      Need Pair Sum?   Can Sorting Help?
          /     \          /      \
        Yes     No       Yes      No
        │        │        │
   Two Sum II    │      Sort
                 │        │
         Remove Duplicates?
             │
             ▼
      Fast & Slow Pointer

Need Triplets?

↓

3Sum

Need Quadruplets?

↓

4Sum

Need Merge?

↓

Merge Two Arrays

Need Compare?

↓

Palindrome
```

---

# 8. General Templates

---

## Template 1 — Opposite Pointer

```cpp
int left = 0;
int right = n - 1;

while(left < right)
{
    if(condition)
    {
        left++;
    }
    else
    {
        right--;
    }
}
```

---

## Template 2 — Fast & Slow Pointer

```cpp
int slow = 0;

for(int fast = 0; fast < n; fast++)
{
    if(valid)
    {
        nums[slow++] = nums[fast];
    }
}
```

---

## Template 3 — Fixed Pointer + Two Pointer

```cpp
sort(nums.begin(), nums.end());

for(int i = 0; i < n; i++)
{
    int left = i + 1;
    int right = n - 1;

    while(left < right)
    {
        ...
    }
}
```

---

## Template 4 — Two Sorted Arrays

```cpp
int i = 0;
int j = 0;

while(i < n && j < m)
{
    ...
}
```

---

## Template 5 — Sliding Window

```cpp
int left = 0;

for(int right = 0; right < n; right++)
{
    while(window_invalid)
    {
        left++;
    }
}
```

---

# 9. Common Interview Mistakes

## 1. Forgetting to Sort

Many problems require sorting before applying Two Pointers.

Examples:

* 3Sum
* 4Sum
* Boats to Save People

---

## 2. Moving the Wrong Pointer

Always ask:

> Which movement gets me closer to the answer?

Never move a pointer randomly.

---

## 3. Infinite Loops

Always ensure at least one pointer moves during every iteration.

Incorrect:

```cpp
while(left < right)
{
    if(sum == target)
    {
        // forgot to move pointers
    }
}
```

---

## 4. Integer Overflow

In problems like 4Sum,

avoid:

```cpp
int sum = a + b + c + d;
```

Instead:

```cpp
long long sum =
(long long)a + b + c + d;
```

---

## 5. Duplicate Handling

Questions like

* 3Sum
* 4Sum

require careful duplicate handling.

Always check the problem statement before deciding how to remove duplicates.

---

## 6. Wrong Loop Condition

Examples:

```cpp
while(left < right)
```

vs

```cpp
while(left <= right)
```

Choosing the wrong condition often causes incorrect answers or infinite loops.

---

# 10. Quick Revision Cheat Sheet

## Opposite Pointer

```text
Sorted Array

+

Pair

↓

Two Pointer
```

---

## Fast & Slow

```text
Remove

Compress

Shift

↓

Fast Slow Pointer
```

---

## Fixed Pointer

```text
Triplets

↓

Fix One

↓

Two Pointer
```

---

## Two Arrays

```text
Merge

Intersection

↓

Two Pointers
```

---

## Strings

```text
Palindrome

Backspace

↓

Two Pointer
```

---

# Summary Table

| Pattern             | Typical Complexity |
| ------------------- | ------------------ |
| Opposite Pointer    | O(n)               |
| Fast & Slow         | O(n)               |
| Fixed + Two Pointer | O(n²)              |
| Four Sum            | O(n³)              |
| Merge Two Arrays    | O(n+m)             |
| Sliding Window      | O(n)               |

---

# Key Takeaways

* Two Pointers is an optimization technique that often reduces **O(n²)** solutions to **O(n)**.
* Always look for clues such as **sorted arrays**, **pair problems**, **palindromes**, and **merge operations**.
* Learn to identify which pointer should move and **why**.
* Understanding the **pattern** is more important than memorizing solutions.

---

**➡️ Next Section:** *LeetCode 167 – Two Sum II (Complete Notes)*

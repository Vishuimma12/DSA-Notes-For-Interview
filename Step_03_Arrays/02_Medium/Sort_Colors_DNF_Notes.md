# 🎨 Sort Colors (0s, 1s, 2s) — Dutch National Flag Algorithm | Step 3.2

> **Lecture Summary:** Sort an array containing only 0s, 1s, and 2s **in a single pass** using the **Dutch National Flag Algorithm** — a 3-pointer technique with a beautiful intuition. One of the most commonly asked medium problems.
>
> 🔗 **Problem Link:** [Sort Colors — LeetCode 75](https://leetcode.com/problems/sort-colors/)

---

## 📌 Table of Contents

1. [Problem Statement](#1--problem-statement)
2. [Approach 1: Brute Force — Sorting — O(n log n)](#2--approach-1-brute-force--sorting--on-log-n)
3. [Approach 2: Better — Counting — O(2n)](#3--approach-2-better--counting--o2n)
4. [Approach 3: Optimal — Dutch National Flag — O(n)](#4--approach-3-optimal--dutch-national-flag--on)
5. [Full Dry Run](#5--full-dry-run)
6. [C++ Implementation](#6--c-implementation)
7. [Time Complexity Analysis](#7--time-complexity-analysis)
8. [Practice Questions](#8--practice-questions)
9. [Interview Questions & Answers](#9--interview-questions--answers)

---

## 1. 📘 Problem Statement

> Given an array consisting of **only 0s, 1s, and 2s**, sort the array **in-place** in a single pass.

```
Input:  [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]
Output: [0, 0, 0, 0, 0, 1, 1, 1, 1, 2, 2, 2]
```

> The numbers represent colors: 0=Red, 1=White, 2=Blue (the Dutch flag 🇳🇱).

---

## 2. ❌ Approach 1: Brute Force — Sorting — O(n log n)

> Use any standard sorting algorithm (Merge Sort, Quick Sort, etc.).

```cpp
void sortColors(vector<int>& nums) {
    sort(nums.begin(), nums.end());
}
```

|       Time       |                   Space                   |
| :--------------: | :---------------------------------------: |
| **O(n log n)** ❌ | O(n) for Merge Sort / O(1) for Quick Sort |

> ⚠️ This ignores the key constraint that the array **only has 3 distinct values**. We can do much better!

---

## 3. ⚡ Approach 2: Better — Counting — O(2n)

> **Idea:** Count occurrences of 0, 1, and 2. Then overwrite the array.

```
Pass 1: Count → count0=5, count1=4, count2=3

Pass 2: Overwrite
  [0,0,0,0,0, 1,1,1,1, 2,2,2]
   ←count0→   ←cnt1→   ←c2→
```

```cpp
void sortColors(vector<int>& nums) {
    int cnt0 = 0, cnt1 = 0, cnt2 = 0;

    // Pass 1: Count
    for (int x : nums) {
        if (x == 0) cnt0++;
        else if (x == 1) cnt1++;
        else cnt2++;
    }

    // Pass 2: Overwrite
    for (int i = 0; i < cnt0; i++) nums[i] = 0;
    for (int i = cnt0; i < cnt0 + cnt1; i++) nums[i] = 1;
    for (int i = cnt0 + cnt1; i < nums.size(); i++) nums[i] = 2;
}
```

|          Time          |  Space   |
| :--------------------: | :------: |
| **O(2n)** — two passes | **O(1)** |

> **Interviewer:** *"Can you do it in a single pass?"* → This leads us to the optimal solution.

---

## 4. 🏆 Approach 3: Optimal — Dutch National Flag Algorithm — O(n)

### The Three Rules (Core Intuition)

The algorithm uses **three pointers**: `low`, `mid`, and `high`, which divide the array into four regions:

```
┌──────────────┬──────────────┬──────────────────┬──────────────┐
│  All 0s      │   All 1s     │  UNSORTED (0/1/2)│   All 2s     │
│              │              │                  │              │
│  [0...low-1] │ [low...mid-1]│  [mid...high]    │ [high+1...n-1]│
└──────────────┴──────────────┴──────────────────┴──────────────┘
```

| Region     | Range              | Contains                      |
| ---------- | ------------------ | ----------------------------- |
| 🔴 Left     | `[0 ... low-1]`    | All **0s** (sorted)           |
| ⚪ Middle   | `[low ... mid-1]`  | All **1s** (sorted)           |
| ❓ Unsorted | `[mid ... high]`   | **0s, 1s, 2s** (to be sorted) |
| 🔵 Right    | `[high+1 ... n-1]` | All **2s** (sorted)           |

> **Goal:** Shrink the unsorted region `[mid...high]` to zero. Once `mid > high`, the entire array is sorted!

### Initial State

```
Array: [2, 0, 2, 1, 1, 0]
        ^                ^
       low              high
       mid

The entire array is "unsorted" region → mid=0, high=n-1
```

### The Three Cases (What to Do at `arr[mid]`)

```
┌─────────────────────────────────────────────────────────────────┐
│ CASE 1: arr[mid] == 0                                          │
│   → This 0 belongs in the LEFT section                         │
│   → swap(arr[low], arr[mid])                                   │
│   → low++, mid++   (both sorted sections grow)                 │
│                                                                │
│ CASE 2: arr[mid] == 1                                          │
│   → This 1 is ALREADY in the correct section!                  │
│   → Just mid++     (1s section grows, nothing else changes)    │
│                                                                │
│ CASE 3: arr[mid] == 2                                          │
│   → This 2 belongs in the RIGHT section                        │
│   → swap(arr[mid], arr[high])                                  │
│   → high--         (2s section grows)                          │
│   → Do NOT move mid! (the swapped element needs to be checked) │
└─────────────────────────────────────────────────────────────────┘
```

### ⚠️ Why Don't We Move `mid` in Case 3?

```
When arr[mid] is 2, we swap it with arr[high].
But what came FROM arr[high]? We don't know! It could be 0, 1, or 2.

If it's a 0 → it needs to go left (Case 1)
If it's a 1 → it stays (Case 2)
If it's a 2 → it needs to go right again (Case 3)

We MUST check the new arr[mid] before moving → so mid stays put.

In Case 1, the swap brings arr[low] to mid. Since low ≤ mid, 
arr[low] was already processed (it's either 0 or 1), so it's safe
to advance mid.
```

---

## 5. 🔍 Full Dry Run

```
arr = [2, 0, 2, 1, 1, 0],  low=0, mid=0, high=5

─────────────────────────────────────────────────────────
Step 1: arr[mid=0] = 2 → CASE 3
        swap(arr[0], arr[5]) → [0, 0, 2, 1, 1, 2]
        high-- → high=4
        (mid stays at 0 — must check swapped element!)

Step 2: arr[mid=0] = 0 → CASE 1
        swap(arr[0], arr[0]) → [0, 0, 2, 1, 1, 2]  (self-swap)
        low++, mid++ → low=1, mid=1

Step 3: arr[mid=1] = 0 → CASE 1
        swap(arr[1], arr[1]) → [0, 0, 2, 1, 1, 2]  (self-swap)
        low++, mid++ → low=2, mid=2

Step 4: arr[mid=2] = 2 → CASE 3
        swap(arr[2], arr[4]) → [0, 0, 1, 1, 2, 2]
        high-- → high=3
        (mid stays at 2)

Step 5: arr[mid=2] = 1 → CASE 2
        mid++ → mid=3

Step 6: arr[mid=3] = 1 → CASE 2
        mid++ → mid=4

STOP: mid(4) > high(3) → unsorted region is EMPTY!
─────────────────────────────────────────────────────────

Result: [0, 0, 1, 1, 2, 2] ✅

Verify regions:
  [0...low-1] = [0..1] = {0, 0}      ← all 0s ✅
  [low...mid-1] = [2..3] = {1, 1}    ← all 1s ✅
  [high+1...n-1] = [4..5] = {2, 2}   ← all 2s ✅
```

---

## 6. 💻 C++ Implementation

```cpp
void sortColors(vector<int>& nums) {
    int low = 0, mid = 0, high = nums.size() - 1;

    while (mid <= high) {
        if (nums[mid] == 0) {
            swap(nums[low], nums[mid]);
            low++;
            mid++;
        }
        else if (nums[mid] == 1) {
            mid++;
        }
        else {  // nums[mid] == 2
            swap(nums[mid], nums[high]);
            high--;
            // Don't move mid! Must check the swapped element.
        }
    }
}
```

> 🎯 **That's it.** ~15 lines of code for one of the most elegant sorting algorithms!

---

## 7. ⏱️ Time Complexity Analysis

> **"But there's a while loop — how is it O(n)?"**

```
At every step, ONE of these happens:
  • Case 0: mid moves right (+1 to sorted count)
  • Case 1: mid moves right (+1 to sorted count)
  • Case 2: high moves left (+1 to sorted count)

In EVERY case, the unsorted region [mid...high] shrinks by exactly 1.

Starting size of unsorted region = n
After n steps → unsorted region = 0 → DONE!

Time: O(n) — exactly one pass!
```

|           Time           |         Space         |
| :----------------------: | :-------------------: |
| **O(n)** — single pass ✅ | **O(1)** — in-place ✅ |

### Approach Comparison

| Approach                |    Time    |  Space   |    Passes    |
| ----------------------- | :--------: | :------: | :----------: |
| Merge Sort              | O(n log n) |   O(n)   |   Multiple   |
| Counting                |   O(2n)    |   O(1)   | **2** passes |
| **Dutch National Flag** |  **O(n)**  | **O(1)** | **1** pass ✅ |

---

## 8. 📝 Practice Questions

### Easy

1. 🔗 [Sort Colors — LeetCode 75](https://leetcode.com/problems/sort-colors/) *(Covered in lecture)*
2. 🔗 [Move Zeroes — LeetCode 283](https://leetcode.com/problems/move-zeroes/) *(Simpler variant: partition around 0)*
3. 🔗 [Segregate 0s and 1s — GeeksforGeeks](https://www.geeksforgeeks.org/problems/segregate-0s-and-1s5106/1) *(Two-way partition)*

### Medium

4. 🔗 [Sort Array By Parity — LeetCode 905](https://leetcode.com/problems/sort-array-by-parity/) *(Evens before odds — 2-way DNF)*
5. 🔗 [Sort Array By Parity II — LeetCode 922](https://leetcode.com/problems/sort-array-by-parity-ii/)
6. 🔗 [Partition Array According to Given Pivot — LeetCode 2161](https://leetcode.com/problems/partition-array-according-to-given-pivot/) *(3-way partition)*
7. 🔗 [Wiggle Sort II — LeetCode 324](https://leetcode.com/problems/wiggle-sort-ii/)

### Challenging

8. 🔗 [Sort an Array — LeetCode 912](https://leetcode.com/problems/sort-an-array/) *(General sorting — compare with DNF efficiency)*
9. Extend Dutch National Flag to **4 colors** (0, 1, 2, 3) — How would you modify the algorithm?
10. 🔗 [3-Way Partitioning — GeeksforGeeks](https://www.geeksforgeeks.org/problems/three-way-partitioning/1) *(Generic 3-way partition around a range)*

---

## 9. 🎯 Interview Questions & Answers

### Q1: Walk me through the Dutch National Flag Algorithm's intuition.

> The array has only 3 values. I divide it into **4 regions** using 3 pointers (`low`, `mid`, `high`):
>
> ```
> [0s sorted | 1s sorted | UNSORTED | 2s sorted]
>  0..low-1    low..mid-1  mid..high  high+1..n-1
> ```
>
> I process `arr[mid]`:
> - **0** → swap with `low` (send it left), advance both `low` and `mid`
> - **1** → already in place, just advance `mid`
> - **2** → swap with `high` (send it right), shrink `high` only
>
> Each step shrinks the unsorted region by 1. After n steps → fully sorted.

---

### Q2: Why is the Counting approach not optimal despite being O(n)?

> Counting takes **two passes**:
> 1. First pass to COUNT each value
> 2. Second pass to OVERWRITE the array
>
> The Dutch National Flag does it in a **single pass** — it sorts while traversing. This matters because:
> - Single pass = better cache performance
> - Single pass = works on **streaming data** (you see each element only once)
> - In an interview, "can you do it in one pass?" is the follow-up question
>
> Also, counting **overwrites** the array. If the elements were objects with metadata (not just integers), overwriting would lose that data. DNF preserves element identity through swaps.

---

### Q3: Why don't we increment `mid` after swapping with `high` (Case 3)?

> When we swap `arr[mid]` with `arr[high]`, the element that comes FROM `arr[high]` is **unknown** — it could be 0, 1, or 2. We haven't processed it yet.
>
> - If it's 0 → needs to go left (Case 1)
> - If it's 1 → stays (Case 2)
> - If it's 2 → needs to go right again (Case 3)
>
> We must re-examine `arr[mid]` in the next iteration. Moving `mid` forward would skip this unprocessed element → **wrong answer**.
>
> In contrast, when we swap with `low` (Case 1), `arr[low]` was already between `low` and `mid-1` — it was already processed as a 1 (or is the same position). So it's safe to advance `mid`.

---

### Q4: Can this algorithm be generalized to K distinct values?

> The Dutch National Flag specifically works for **3 values** in a single pass.
>
> For **K values**:
> - **K=2:** Simple two-pointer partition (like separating odds/evens) — O(n)
> - **K=3:** Dutch National Flag — O(n)
> - **K=4+:** You'd need multiple passes or different strategies. One approach:
>   - Sort in O(n) using **Counting Sort** (since values are bounded)
>   - This works for any K where values are in a known small range
>
> Counting Sort for bounded integers: O(n + K) time, O(K) space.

---

### Q5: How is this different from the Partition step in Quick Sort?

> | Feature | Quick Sort Partition | Dutch National Flag |
> |---------|:--------------------:|:-------------------:|
> | **Values** | Any (relative to pivot) | Exactly 3 (0, 1, 2) |
> | **Regions** | 2 (<pivot, ≥pivot) | **3** (<1, =1, >1) |
> | **Pointers** | 2 (i, j) | **3** (low, mid, high) |
> | **Use case** | General sorting | Bounded value sorting |
>
> Quick Sort's Lomuto/Hoare partition creates a **2-way split**. DNF creates a **3-way split** — which is why it's also called **3-way partitioning**. Interestingly, Quick Sort can be improved using 3-way partitioning for arrays with many duplicate elements!

---

### Q6: What happens if the array has only two distinct values (e.g., only 0s and 1s)?

> The algorithm still works perfectly! The `high` pointer would never encounter a 2, so Case 3 never triggers. It effectively reduces to a two-pointer partition.
>
> But a simpler approach for 2 values: single pointer tracking where to place the next 0:
>
> ```cpp
> int j = 0;
> for (int i = 0; i < n; i++) {
>     if (arr[i] == 0) swap(arr[i], arr[j++]);
> }
> ```

---

### Q7: Prove correctness — how do you know the invariant is maintained?

> **Invariant at every iteration:**
> ```
> arr[0..low-1]   = all 0s
> arr[low..mid-1] = all 1s
> arr[mid..high]  = unsorted
> arr[high+1..n-1] = all 2s
> ```
>
> **Base case:** `low=0, mid=0, high=n-1`
> - `[0..-1]` = empty → all 0s ✅ (vacuously true)
> - `[0..-1]` = empty → all 1s ✅
> - `[0..n-1]` = full array → unsorted ✅
> - `[n..n-1]` = empty → all 2s ✅
>
> **Each case preserves the invariant:**
> - Case 0: 0 goes to correct section, both sorted regions grow ✅
> - Case 1: 1 stays, 1s region grows ✅
> - Case 2: 2 goes to correct section, 2s region grows ✅
>
> **Termination:** `mid > high` → unsorted region is empty → entire array sorted ✅

---

### Q8: The interviewer says "explain it like I'm five."

> Imagine you have a box of red, white, and blue balls mixed up. You want all reds on the left, whites in the middle, blues on the right.
>
> You have three invisible walls:
> - Left wall (`low`): everything before it is red ✅
> - Right wall (`high`): everything after it is blue ✅
> - Your hand (`mid`): scanning through the unsorted pile
>
> Pick up the ball at your hand:
> - **Red?** Toss it to the left wall. Wall moves right. Your hand moves right.
> - **White?** It's already in the right zone. Just move your hand right.
> - **Blue?** Toss it to the right wall. Wall moves left. DON'T move your hand — check what you got back!
>
> When your hand meets the right wall → everything is sorted! 🎉

---

## 🧩 Quick Revision Summary

```
┌──────────────────────────────────────────────────────────────────┐
│         DUTCH NATIONAL FLAG ALGORITHM — SUMMARY                  │
│                                                                   │
│  3 POINTERS: low=0, mid=0, high=n-1                              │
│                                                                   │
│  WHILE mid <= high:                                               │
│  ┌──────────────────────────────────────────────────────────┐     │
│  │ arr[mid] == 0 → swap(arr[low], arr[mid]), low++, mid++  │     │
│  │ arr[mid] == 1 → mid++                                   │     │
│  │ arr[mid] == 2 → swap(arr[mid], arr[high]), high--       │     │
│  │                 ⚠️ DO NOT move mid!                      │     │
│  └──────────────────────────────────────────────────────────┘     │
│                                                                   │
│  REGIONS:                                                         │
│  [0..low-1]=0s  [low..mid-1]=1s  [mid..high]=?  [high+1..n-1]=2s│
│                                                                   │
│  TIME: O(n) single pass  │  SPACE: O(1) in-place                 │
│                                                                   │
│  KEY INSIGHT: Every step shrinks unsorted region by exactly 1     │
│  → n elements → n steps → O(n)                                   │
│                                                                   │
│  ⚠️ WHY mid stays in Case 3:                                     │
│  → The element swapped FROM high is UNKNOWN → must be checked    │
└──────────────────────────────────────────────────────────────────┘
```

---

*📌 Source: Striver's A2Z DSA Course — Step 3.2: Arrays Medium (Problem 2)*

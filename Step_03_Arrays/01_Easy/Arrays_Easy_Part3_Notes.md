# 📦 Arrays Easy — Part 3 | Striver's A2Z DSA Course (Step 3.1 Final)

> **Lecture Summary:** Final problems of Step 3.1 covering the **Longest Subarray with Sum K** — a crucial problem that introduces **prefix sum + hashing** and **two-pointer sliding window** techniques. This is one of the most commonly asked interview questions.

---

## 📌 Table of Contents

1. [What is a Subarray?](#1--what-is-a-subarray)
2. [Problem: Longest Subarray with Sum K](#2--problem-longest-subarray-with-sum-k)
3. [Approach 1: Brute Force — O(n³)](#3--approach-1-brute-force--on³)
4. [Approach 2: Optimized Brute — O(n²)](#4--approach-2-optimized-brute--on²)
5. [Approach 3: Better — Prefix Sum + HashMap](#5--approach-3-better--prefix-sum--hashmap)
6. [Approach 4: Optimal — Two Pointer / Sliding Window](#6--approach-4-optimal--two-pointer--sliding-window)
7. [Which Approach to Use When?](#7--which-approach-to-use-when)
8. [C++ Implementations](#8--c-implementations)
9. [Practice Questions](#9--practice-questions)
10. [Interview Questions & Answers](#10--interview-questions--answers)

---

## 1. 📘 What is a Subarray?

> A **subarray** is a **contiguous** (continuous) part of an array.

```
Array: [1, 2, 3, 4, 5]

✅ Subarrays:  [1]  [1,2]  [1,2,3]  [2,3,4]  [3,4,5]  [1,2,3,4,5]
❌ NOT subarrays: [1,3]  [1,4]  [2,5]  (not contiguous!)

These non-contiguous selections are called SUBSEQUENCES, not subarrays.
```

### How Many Subarrays Exist?

```
For an array of size n:
  Starting at index 0: n subarrays  (length 1, 2, ..., n)
  Starting at index 1: n-1 subarrays
  ...
  Starting at index n-1: 1 subarray

  Total = n + (n-1) + ... + 1 = n(n+1)/2 ≈ O(n²)
```

### Generating All Subarrays — The Pattern

```
Array: [a, b, c, d]

i=0:  [a]  [a,b]  [a,b,c]  [a,b,c,d]     → j goes from 0 to 3
i=1:  [b]  [b,c]  [b,c,d]                  → j goes from 1 to 3
i=2:  [c]  [c,d]                            → j goes from 2 to 3
i=3:  [d]                                   → j goes from 3 to 3

Pattern: subarray is from index i to index j, where j starts at i
```

```cpp
for (int i = 0; i < n; i++) {       // Starting index
    for (int j = i; j < n; j++) {   // Ending index
        // Subarray is arr[i..j]
    }
}
```

---

## 2. 🟡 Problem: Longest Subarray with Sum K

> **Problem:** Given an array of integers and a target sum K, find the **length** of the **longest** subarray whose elements sum to K.
>
> 🔗 **Problem Links:**
> - [Longest Subarray with Sum K (Positives) — Coding Ninjas](https://www.naukri.com/code360/problems/longest-subarray-with-sum-k_6209586)
> - [Longest Subarray with Sum K (Positives+Negatives) — Coding Ninjas](https://www.naukri.com/code360/problems/longest-subarray-with-sum-k_5382123)

```
Input:  arr = [1, 2, 3, 1, 1, 1, 1, 4, 2, 3],  K = 3
Output: 3

Explanation:
  Subarrays with sum = 3:
    [1, 2]         → length 2
    [3]            → length 1
    [1, 1, 1]      → length 3  ← LONGEST ✅
    [1, 2] (later) → length 2

  Longest length = 3
```

---

## 3. ❌ Approach 1: Brute Force — O(n³)

> Generate every subarray, compute its sum, check if sum = K.

```
For each starting index i:
  For each ending index j (from i to n-1):
    Compute sum of arr[i..j] using a third loop
    If sum == K → update maxLength
```

```cpp
int longestSubarray(vector<int>& arr, int k) {
    int n = arr.size();
    int maxLen = 0;

    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            // Compute sum of subarray arr[i..j]
            int sum = 0;
            for (int p = i; p <= j; p++) {
                sum += arr[p];
            }
            if (sum == k) {
                maxLen = max(maxLen, j - i + 1);
            }
        }
    }
    return maxLen;
}
```

|    Time     | Space |
| :---------: | :---: |
| **O(n³)** ❌ | O(1)  |

---

## 4. ⚡ Approach 2: Optimized Brute — O(n²)

> **Observation:** When extending a subarray from `[i..j]` to `[i..j+1]`, you just add **one new element**. No need for a third loop!

```
Old sum for [i..j] = S
New sum for [i..j+1] = S + arr[j+1]    ← Just add the new element!
```

```cpp
int longestSubarray(vector<int>& arr, int k) {
    int n = arr.size();
    int maxLen = 0;

    for (int i = 0; i < n; i++) {
        long long sum = 0;
        for (int j = i; j < n; j++) {
            sum += arr[j];    // Incrementally add
            if (sum == k) {
                maxLen = max(maxLen, j - i + 1);
            }
        }
    }
    return maxLen;
}
```

|                   Time                   | Space |
| :--------------------------------------: | :---: |
| **O(n²)** — better, but still not enough | O(1)  |

---

## 5. 🧠 Approach 3: Better — Prefix Sum + HashMap

> **This is OPTIMAL for arrays with positives, negatives, AND zeros.**

### The Reverse Mathematics Intuition

```
Imagine you're standing at index i, and the prefix sum (sum from 0 to i) = X

If you're looking for a subarray with sum K ending at index i:
  There must exist some earlier index j where prefix sum = X - K

  Why? Because:
    prefix_sum[0..i] - prefix_sum[0..j] = sum of subarray[j+1..i]
    X - (X - K) = K ✅

So: If you find (X - K) in your hash → a subarray with sum K exists!
```

**Visual:**

```
arr:  [... ... ... ... ... ... ...]
       0         j              i

prefix_sum[0..j] = X - K
prefix_sum[0..i] = X

Sum of subarray [j+1..i] = X - (X-K) = K ✅
```

### Step-by-Step Dry Run

```
arr = [1, 2, 3, 1, 1, 1, 1, 4, 2, 3],  K = 3
HashMap: {},  sum = 0,  maxLen = 0

i=0: sum = 1. Is 1 == 3? No. Need (1-3)=-2 in map? No. Store {1: 0}
i=1: sum = 3. Is 3 == 3? YES → maxLen = max(0, 1+1) = 2
     Store {1:0, 3:1}

i=2: sum = 6. Is 6 == 3? No. Need (6-3)=3 in map? YES at index 1!
     Length = 2 - 1 = 1. maxLen = max(2, 1) = 2. Store {1:0, 3:1, 6:2}

i=3: sum = 7. Need 4? No. Store {1:0, 3:1, 6:2, 7:3}

i=4: sum = 8. Need 5? No. Store {1:0, 3:1, 6:2, 7:3, 8:4}

i=5: sum = 9. Need 6? YES at index 2!
     Length = 5 - 2 = 3. maxLen = max(2, 3) = 3 ✅
     Store {1:0, 3:1, 6:2, 7:3, 8:4, 9:5}

i=6: sum = 10. Need 7? YES at index 3!
     Length = 6 - 3 = 3. maxLen = max(3, 3) = 3

... (remaining elements won't produce longer subarrays)

Answer: 3 ✅
```

### ⚠️ Critical Edge Case: Zeros in the Array!

```
arr = [2, 0, 0, 3],  K = 3

If we update the prefix sum in the HashMap every time:

i=0: sum=2, store {2: 0}
i=1: sum=2, store {2: 1}  ← OVERWRITTEN! 😱
i=2: sum=2, store {2: 2}  ← OVERWRITTEN AGAIN!
i=3: sum=5, need (5-3)=2, found at index 2
     Length = 3 - 2 = 1   ← WRONG! Should be 3 (subarray [0,0,3])

The correct answer should use index 0 (the LEFTMOST occurrence of sum=2).
```

> 🔑 **Rule: NEVER update an existing prefix sum in the HashMap!** Always keep the **leftmost** (first) occurrence. This ensures the **longest** subarray is found.

```cpp
// WRONG ❌
prefixMap[sum] = i;              // Overwrites every time

// CORRECT ✅
if (prefixMap.find(sum) == prefixMap.end()) {
    prefixMap[sum] = i;          // Only store FIRST occurrence
}
```

|                         Time                         |        Space         |
| :--------------------------------------------------: | :------------------: |
| **O(n log n)** ordered map / **O(n)** unordered map* | **O(n)** for HashMap |

> *Worst case with unordered_map (hash collisions): O(n²). Ordered map guarantees O(n log n).

---

## 6. 🏆 Approach 4: Optimal — Two Pointer / Sliding Window

> **Works ONLY for arrays with positives and zeros (no negatives!)**

### Why Only Positives?

```
Key property: When all elements are ≥ 0, adding an element to the window
can ONLY increase or maintain the sum. It can NEVER decrease it.

This monotonic behavior lets us confidently shrink from the left
when sum exceeds K.

With negatives, adding an element COULD decrease the sum, breaking
the shrink logic.
```

### The Sliding Window Approach

```
1. Start with left = 0, right = 0, sum = arr[0]
2. If sum < K → expand window: right++, sum += arr[right]
3. If sum > K → shrink window: sum -= arr[left], left++
4. If sum == K → update maxLength, then expand
5. Stop when right reaches end
```

### Detailed Dry Run

```
arr = [1, 2, 3, 1, 1, 1, 1, 4, 2, 3],  K = 6
left = 0, right = 0, sum = 1, maxLen = 0

Step 1: sum=1 < 6 → expand. right=1, sum=3
Step 2: sum=3 < 6 → expand. right=2, sum=6
Step 3: sum=6 == 6 → maxLen=3. expand. right=3, sum=7
Step 4: sum=7 > 6 → shrink! sum-=arr[0]=1 → sum=6, left=1
Step 5: sum=6 == 6 → maxLen=max(3,3)=3. expand. right=4, sum=7
Step 6: sum=7 > 6 → shrink! sum-=arr[1]=2 → sum=5, left=2
Step 7: sum=5 < 6 → expand. right=5, sum=6
Step 8: sum=6 == 6 → maxLen=max(3,4)=4 ✅ [3,1,1,1]
Step 9: expand. right=6, sum=7
Step 10: sum=7 > 6 → shrink! sum-=arr[2]=3 → sum=4, left=3
Step 11: sum=4 < 6 → expand. right=7, sum=8
Step 12: sum=8 > 6 → shrink! sum-=1→7, left=4. Still >6.
         shrink! sum-=1→6, left=5.
Step 13: sum=6 == 6 → maxLen=max(4,3)=4. expand. right=8, sum=8
Step 14: sum=8 > 6 → shrink! sum-=1→7, left=6. shrink! sum-=1→6, left=7
Step 15: sum=6 == 6 → maxLen=4. expand. right=9, sum=9
Step 16: sum=9 > 6 → shrink! sum-=4→5, left=8.
Step 17: sum=5 < 6. right at end → STOP.

Answer: 4 ✅  (subarray [3, 1, 1, 1])
```

### ⏱️ Time Complexity — Why O(2n), Not O(n²)?

```
Despite the inner while loop, the time is O(2n):

• The RIGHT pointer moves from 0 to n-1    → at most n moves
• The LEFT pointer moves from 0 to n-1     → at most n moves

Total pointer movements ≤ 2n

The inner while does NOT run n times for EVERY outer step.
It "catches up" gradually. Think of it as two runners on a track,
both running forward. Combined distance = 2n, not n².
```

|        Time        |   Space    |
| :----------------: | :--------: |
| **O(2n) ≈ O(n)** ✅ | **O(1)** ✅ |

---

## 7. 📊 Which Approach to Use When?

| Array Contains            | Optimal Approach             |    Time    | Space |
| ------------------------- | ---------------------------- | :--------: | :---: |
| **Positives only**        | Two Pointer (Sliding Window) |    O(n)    | O(1)  |
| **Positives + Zeros**     | Two Pointer (Sliding Window) |    O(n)    | O(1)  |
| **Positives + Negatives** | Prefix Sum + HashMap         | O(n log n) | O(n)  |
| **Any integers**          | Prefix Sum + HashMap         | O(n log n) | O(n)  |

> 💡 In an interview, explain **all approaches** and then clarify: *"If the array has only positives and zeros, I can optimize to O(n) time and O(1) space using two pointers. For arrays with negatives, the HashMap approach is optimal."*

---

## 8. 💻 C++ Implementations

### Better Solution — Prefix Sum + HashMap (Works for ALL cases)

```cpp
int longestSubarraySum(vector<int>& arr, long long k) {
    map<long long, int> prefixMap;  // prefix_sum → first index
    long long sum = 0;
    int maxLen = 0;
    int n = arr.size();

    for (int i = 0; i < n; i++) {
        sum += arr[i];

        // Case 1: Entire prefix from 0 to i has sum K
        if (sum == k) {
            maxLen = max(maxLen, i + 1);
        }

        // Case 2: Check if (sum - K) exists in map
        long long remaining = sum - k;
        if (prefixMap.find(remaining) != prefixMap.end()) {
            int len = i - prefixMap[remaining];
            maxLen = max(maxLen, len);
        }

        // Store prefix sum ONLY if not already present (for longest!)
        if (prefixMap.find(sum) == prefixMap.end()) {
            prefixMap[sum] = i;
        }
    }

    return maxLen;
}
```

### Optimal Solution — Two Pointer / Sliding Window (Positives + Zeros Only)

```cpp
int longestSubarraySum(vector<int>& arr, long long k) {
    int left = 0, right = 0;
    long long sum = arr[0];
    int maxLen = 0;
    int n = arr.size();

    while (right < n) {
        // Shrink window if sum exceeds K
        while (left <= right && sum > k) {
            sum -= arr[left];
            left++;
        }

        // Check if current window has sum K
        if (sum == k) {
            maxLen = max(maxLen, right - left + 1);
        }

        // Expand window
        right++;
        if (right < n) {
            sum += arr[right];
        }
    }

    return maxLen;
}
```

### Key Implementation Notes

| Detail                        | Why?                                                                                                |
| ----------------------------- | --------------------------------------------------------------------------------------------------- |
| `long long sum`               | Sum can overflow `int` for large arrays                                                             |
| `map` vs `unordered_map`      | `map` → O(n log n) guaranteed. `unordered_map` → O(n) average, but O(n²) worst case with collisions |
| Never overwrite prefix in map | Keeps **leftmost** index → ensures **longest** subarray                                             |
| `left <= right` in shrink     | Ensures window doesn't become invalid (left past right)                                             |

---

## 9. 📝 Practice Questions

### Easy

1. 🔗 [Subarray Sum Equals K — LeetCode 560](https://leetcode.com/problems/subarray-sum-equals-k/) *(Count subarrays, not longest — uses same prefix sum concept)*
2. 🔗 [Running Sum of 1D Array — LeetCode 1480](https://leetcode.com/problems/running-sum-of-1d-array/) *(Basic prefix sum)*
3. 🔗 [Maximum Subarray (Kadane's) — LeetCode 53](https://leetcode.com/problems/maximum-subarray/)

### Medium

4. 🔗 [Minimum Size Subarray Sum — LeetCode 209](https://leetcode.com/problems/minimum-size-subarray-sum/) *(Sliding window — shortest subarray with sum ≥ target)*
5. 🔗 [Continuous Subarray Sum — LeetCode 523](https://leetcode.com/problems/continuous-subarray-sum/) *(Sum is multiple of K)*
6. 🔗 [Subarray Product Less Than K — LeetCode 713](https://leetcode.com/problems/subarray-product-less-than-k/) *(Sliding window on products)*
7. 🔗 [Max Consecutive Ones III — LeetCode 1004](https://leetcode.com/problems/max-consecutive-ones-iii/) *(Sliding window variant)*
8. [Longest Subarray with Sum K — Coding Ninjas (Positives)](https://www.naukri.com/code360/problems/longest-subarray-with-sum-k_6209586) *(Covered in lecture)*
9. [Longest Subarray with Sum K — Coding Ninjas (All integers)](https://www.naukri.com/code360/problems/longest-subarray-with-sum-k_5382123) *(Covered in lecture)*

### Challenging

10. 🔗 [Shortest Subarray with Sum ≥ K — LeetCode 862](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/) *(With negatives — uses deque)*
11. 🔗 [Binary Subarrays With Sum — LeetCode 930](https://leetcode.com/problems/binary-subarrays-with-sum/)
12. Number of Subarrays with Sum K *(Assignment from lecture — use prefix sum hashing!)*

---

## 10. 🎯 Interview Questions & Answers

### Q1: What is a subarray vs subsequence vs subset?

> | Concept         | Contiguous? | Order preserved? | Example from [1,2,3,4] |
> |-----------------|:-----------:|:----------------:|-------------------------|
> | **Subarray**    | Yes ✅       | Yes ✅            | [2,3], [1,2,3]         |
> | **Subsequence** | No ❌        | Yes ✅            | [1,3], [2,4], [1,4]    |
> | **Subset**      | No ❌        | No ❌             | {3,1}, {4,2,1}         |
>
> **Count:** Subarrays = n(n+1)/2, Subsequences = 2ⁿ, Subsets = 2ⁿ

---

### Q2: Explain the Prefix Sum technique.

> **Prefix sum** = cumulative sum from the start of the array up to each index.
>
> ```
> arr:        [1, 2, 3, 1, 1]
> prefix_sum: [1, 3, 6, 7, 8]
>
> Sum of subarray [i..j] = prefix[j] - prefix[i-1]
> Sum of [1..3] = prefix[3] - prefix[0] = 7 - 1 = 6 ✅ (2+3+1=6)
> ```
>
> This converts any "subarray sum" query from O(n) to O(1) after O(n) preprocessing.

---

### Q3: Why do you NOT update existing prefix sums in the HashMap?

> Because we want the **longest** subarray. Consider:
>
> ```
> arr = [2, 0, 0, 3],  K = 3
> Prefix sums: 2, 2, 2, 5
>
> At index 3: sum=5, looking for 5-3=2
>   If we stored {2: 0} → length = 3-0 = 3 ✅ (subarray [0,0,3])
>   If we updated {2: 2} → length = 3-2 = 1 ❌ (only [3])
> ```
>
> The **leftmost** occurrence of a prefix sum gives the **longest** subarray ending at the current index. Overwriting destroys this information.

---

### Q4: Why does the Two-Pointer approach NOT work for arrays with negatives?

> The two-pointer sliding window relies on a **monotonic** property:
> - Adding an element → sum **increases** (or stays same with zeros)
> - Removing from left → sum **decreases**
>
> With negatives, adding an element could **decrease** the sum, making the shrink decision invalid:
>
> ```
> arr = [1, -1, 4],  K = 4
>
> Window [1,-1,4]: sum = 4 ✅
> But if sum exceeded K and we shrink from left:
>   We might remove a negative, INCREASING the sum — opposite of intent!
> ```
>
> The monotonic invariant breaks → two pointers give wrong answers.

---

### Q5: Explain the O(2n) time complexity of the sliding window.

> Despite having a `while` loop inside another `while` loop, it's NOT O(n²):
>
> ```
> The RIGHT pointer moves forward at most n times total.
> The LEFT pointer moves forward at most n times total.
> Neither pointer EVER moves backward.
>
> Total operations = (right movements) + (left movements) ≤ n + n = 2n
> ```
>
> This is called **amortized analysis**. The inner loop doesn't run n times per outer iteration — it's spread across all iterations. Like two people walking on a road: together they walk 2× the road length, not road² distance.

---

### Q6: When would you use `map` vs `unordered_map` for prefix sum hashing?

> | Type            | Avg Time  | Worst Time | When to Use |
> |-----------------|:---------:|:----------:|-------------|
> | `unordered_map` | O(1) per lookup | **O(n)** with collisions | When speed matters and collision risk is low |
> | `map` (ordered) | O(log n) per lookup | O(log n) always | When you need **guaranteed** performance |
>
> **In interviews:** Mention that `unordered_map` is O(n) average but can degrade to O(n²) total in worst case. The interviewer will appreciate this awareness.

---

### Q7: How would you modify this to COUNT subarrays with sum K instead of finding the longest?

> Instead of storing only the **first** occurrence, store the **count** of each prefix sum:
>
> ```cpp
> unordered_map<long long, int> prefixCount;  // prefix_sum → count
> prefixCount[0] = 1;  // Empty prefix has sum 0
> long long sum = 0;
> int count = 0;
>
> for (int i = 0; i < n; i++) {
>     sum += arr[i];
>     if (prefixCount.count(sum - k)) {
>         count += prefixCount[sum - k];  // All occurrences contribute
>     }
>     prefixCount[sum]++;
> }
> ```
>
> This is [LeetCode 560 — Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/).

---

### Q8: What is the difference between Sliding Window and Two Pointer?

> They're closely related but have subtle differences:
>
> | Aspect          | Two Pointer              | Sliding Window           |
> |-----------------|--------------------------|--------------------------|
> | **Pointers**    | Usually converge (left→, ←right) | Both move right (left→, right→) |
> | **Window**      | May not form a window    | Always defines a window  |
> | **Use case**    | Sorted arrays, pair sums | Subarray/substring problems |
> | **Example**     | Two Sum (sorted)         | Longest subarray with sum K |
>
> "Sliding Window" IS a type of Two-Pointer technique where both pointers move in the same direction, defining a variable-size window.

---

### Q9: What if K = 0? Does the algorithm still work?

> **Yes!** Both approaches handle K = 0 correctly:
>
> - **HashMap:** Looks for `prefix_sum - 0 = prefix_sum` in the map. If the same prefix sum appears twice, the subarray between those indices has sum 0.
> - **Two Pointer:** When sum exceeds 0 (which it always will with positives), it shrinks. When sum = 0, it records the length.
>
> Edge: In an all-zeros array like `[0, 0, 0]` with K = 0, the entire array is the answer (length 3).

---

## 🧩 Quick Revision Summary

```
┌────────────────────────────────────────────────────────────────────┐
│          LONGEST SUBARRAY WITH SUM K — DECISION TREE               │
│                                                                     │
│  Array has NEGATIVES?                                               │
│  ├── YES → Use Prefix Sum + HashMap (Better = Optimal)              │
│  │         Time: O(n log n) or O(n) avg                             │
│  │         Space: O(n) for HashMap                                  │
│  │                                                                  │
│  └── NO (only positives & zeros)                                    │
│       → Use Two Pointer / Sliding Window (Optimal)                  │
│         Time: O(2n) ≈ O(n)                                          │
│         Space: O(1) ✅                                               │
│                                                                     │
│  KEY CONCEPTS:                                                      │
│  • Prefix Sum: sum from index 0 to i                                │
│  • Reverse Math: if prefix[i]=X, need prefix[j]=X-K earlier        │
│  • Never overwrite prefix in HashMap → keeps leftmost (longest)    │
│  • Sliding window: expand right, shrink left when sum > K           │
│  • O(2n) ≠ O(n²): both pointers move forward only → amortized     │
│                                                                     │
│  STEP 3.1 COMPLETE! ✅ (11 Easy Array Problems Solved)              │
└────────────────────────────────────────────────────────────────────┘
```

> 📋 **Assignment from Lecture:** Solve "Count of Subarrays with Sum K" using the prefix sum + hashing technique!

---

*📌 Source: Striver's A2Z DSA Course — Step 3.1: Arrays Easy (Final)*

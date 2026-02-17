# 📦 Arrays — Striver's A2Z DSA Course (Step 3.2 - Maximum Subarray Sum)
> **Lecture Summary:** This lecture covers the **Maximum Subarray Sum** problem — one of the most important and frequently asked interview problems in DSA. It explains the classic **Kadane's Algorithm** from scratch using the **Brute → Better → Optimal** approach, and also covers the follow-up: **printing the actual subarray**.

---

## 📌 Table of Contents
1. [Problem Statement & Subarray Definition](#1--problem-statement--subarray-definition)
2. [Interview Strategy Recap: Brute → Better → Optimal](#2--interview-strategy-recap-brute--better--optimal)
3. [Approach 1: Brute Force — O(n³)](#3--approach-1-brute-force--on³)
4. [Approach 2: Better — O(n²)](#4--approach-2-better--on²)
5. [Approach 3: Optimal — Kadane's Algorithm O(n)](#5--approach-3-optimal--kadanes-algorithm-on)
6. [Follow-Up: Print the Subarray with Maximum Sum](#6--follow-up-print-the-subarray-with-maximum-sum)
7. [Edge Case: All Negative Elements](#7--edge-case-all-negative-elements)
8. [Practice Questions](#8--practice-questions)
9. [Interview Questions & Answers](#9--interview-questions--answers)

---

## 1. 📘 Problem Statement & Subarray Definition

> **Problem:** Given an array, find the **maximum sum** among all possible subarrays.
>
> 🔗 **Problem Link:** [Maximum Subarray — LeetCode 53](https://leetcode.com/problems/maximum-subarray/)

```
Input:  [-2, -3, 4, -1, -2, 1, 5, -3]
Output: 7
Explanation: Subarray [4, -1, -2, 1, 5] gives the maximum sum = 7
```

### What is a Subarray?

> A **subarray** is a **contiguous** part of the array. Contiguous means the elements must be adjacent — no skipping allowed.

```
Array:     [-2, -3,  4, -1, -2,  1,  5, -3]
Index:       0   1   2   3   4   5   6   7

✅ Valid Subarrays (contiguous):
   [-2, -3]          → indices 0 to 1
   [4, -1, -2]       → indices 2 to 4
   [4, -1, -2, 1, 5] → indices 2 to 6  ← this gives sum = 7 (ANSWER)
   [-3]              → single element is also a valid subarray
   Entire array      → also a valid subarray

❌ NOT a Subarray (non-contiguous):
   [-2, 4, 5]  → skips elements in between → this is a SUBSEQUENCE, not a subarray
```

> 🔑 **Key Rule:** Even a single element counts as a subarray. The entire array also counts. You cannot skip elements — that becomes a **subsequence**.

---

## 2. 🎯 Interview Strategy Recap: Brute → Better → Optimal

```
For this problem, the progression is:
  Brute:   Try ALL subarrays with 3 loops → O(n³)
  Better:  Try ALL subarrays with 2 loops → O(n²)
  Optimal: Kadane's Algorithm (single pass) → O(n) ✅
```

> 💡 Always present your thought process level by level. **Never jump to Kadane's directly** — explain that you know brute force first, then improve.

---

## 3. 🔴 Approach 1: Brute Force — O(n³)

> **Idea:** Generate every possible subarray using two loops (start `i`, end `j`), then compute the sum of each using a third inner loop `k`.

### How to Generate All Subarrays?
```
Stand at index i=0:
  j=0: subarray [arr[0]]           → just -2
  j=1: subarray [arr[0]..arr[1]]   → -2, -3
  j=2: subarray [arr[0]..arr[2]]   → -2, -3, 4
  ... and so on till j = n-1

Then stand at index i=1:
  j=1: subarray [arr[1]]           → just -3
  j=2: subarray [arr[1]..arr[2]]   → -3, 4
  ... and so on

Continue until i = n-1
```

```cpp
// Brute Force — O(n³)
int maxSubarraySum(vector<int>& arr, int n) {
    int maximum = INT_MIN;

    for (int i = 0; i < n; i++) {           // Start of subarray
        for (int j = i; j < n; j++) {       // End of subarray
            int sum = 0;
            for (int k = i; k <= j; k++) {  // Sum of subarray [i..j]
                sum += arr[k];
            }
            maximum = max(sum, maximum);     // Track global max
        }
    }
    return maximum;
}
```

**Dry Run:**
```
arr = [-2, -3, 4, -1, -2, 1, 5, -3]

i=0, j=0: sum = -2         → max = -2
i=0, j=1: sum = -2-3 = -5  → max = -2
i=0, j=2: sum = -5+4 = -1  → max = -1
...
i=2, j=6: sum = 4-1-2+1+5 = 7 → max = 7 ✅
```

| Time | Space |
| :--------: | :---: |
| **O(n³)** ❌ | O(1) |

> ⚠️ Near O(n³) — the inner loops don't always run n times, but it's approximately cubic. Interviewer will immediately ask you to optimize.

---

## 4. 🟡 Approach 2: Better — O(n²)

> **Key Insight:** When extending a subarray from `i` to `j`, you don't need to recompute the sum from scratch each time!
> Whatever was the **sum of [i..j-1]**, just **add arr[j]** to get the sum of **[i..j]**.

```
Instead of recalculating from scratch each time (3 loops),
maintain a running sum as j extends (2 loops):

i=0:
  j=0: sum = arr[0] = -2
  j=1: sum = -2 + arr[1] = -2 + (-3) = -5
  j=2: sum = -5 + arr[2] = -5 + 4 = -1
  j=3: sum = -1 + arr[3] = -1 + (-1) = -2
  ... and so on

i=1:
  j=1: sum = arr[1] = -3
  j=2: sum = -3 + arr[2] = -3 + 4 = 1
  ... and so on
```

```cpp
// Better — O(n²)
int maxSubarraySum(vector<int>& arr, int n) {
    int maximum = INT_MIN;

    for (int i = 0; i < n; i++) {       // Start of subarray
        int sum = 0;
        for (int j = i; j < n; j++) {   // Extend subarray one by one
            sum += arr[j];              // Just add next element (no inner k loop!)
            maximum = max(sum, maximum);
        }
    }
    return maximum;
}
```

| Time | Space |
| :---------: | :---: |
| **O(n²)** ⚠️ | O(1) |

> 💡 Eliminated one loop by maintaining a **running sum**. Still O(n²) — interviewer will push further.

---

## 5. 🟢 Approach 3: Optimal — Kadane's Algorithm O(n) 🏆

> **Core Intuition:** Carrying a **negative sum** forward will **always reduce** future sums. So whenever the running sum drops below zero — **reset it to zero** and start fresh.

### The Algorithm Logic

```
Rule 1: Keep adding elements to the running sum.
Rule 2: Track the maximum sum seen so far (update at each step).
Rule 3: If sum < 0 at any point → reset sum to 0 (don't carry negatives forward).
```

### Detailed Dry Run

```
arr = [-2, -3, 4, -1, -2, 1, 5, -3]
      Start: sum = 0, maximum = INT_MIN

Step 1 → arr[0] = -2:
  sum = 0 + (-2) = -2
  maximum = max(-2, INT_MIN) = -2
  sum < 0? YES → reset sum = 0
  ┌─────────────────────────────────────┐
  │ Reasoning: Carrying -2 to next step │
  │ gives -2 + (-3) = -5                │
  │ But starting fresh: just -3         │
  │ -3 > -5 ✅ So reset makes sense!   │
  └─────────────────────────────────────┘

Step 2 → arr[1] = -3:
  sum = 0 + (-3) = -3
  maximum = max(-3, -2) = -2  (no update, -3 < -2)
  sum < 0? YES → reset sum = 0

Step 3 → arr[2] = 4:
  sum = 0 + 4 = 4
  maximum = max(4, -2) = 4  ✅ New max!
  sum < 0? NO → keep sum = 4

Step 4 → arr[3] = -1:
  sum = 4 + (-1) = 3
  maximum = max(3, 4) = 4  (no update)
  sum < 0? NO → keep sum = 3
  ┌──────────────────────────────────────────┐
  │ Why not reset at -1?                     │
  │ sum = 3 > 0, so carrying it is still a   │
  │ POSITIVE contribution to future elements │
  └──────────────────────────────────────────┘

Step 5 → arr[4] = -2:
  sum = 3 + (-2) = 1
  maximum = max(1, 4) = 4  (no update)
  sum < 0? NO → keep sum = 1

Step 6 → arr[5] = 1:
  sum = 1 + 1 = 2
  maximum = max(2, 4) = 4  (no update)
  sum < 0? NO → keep sum = 2

Step 7 → arr[6] = 5:
  sum = 2 + 5 = 7
  maximum = max(7, 4) = 7  ✅ New max!
  sum < 0? NO → keep sum = 7

Step 8 → arr[7] = -3:
  sum = 7 + (-3) = 4
  maximum = max(4, 7) = 7  (no update)
  sum < 0? NO → keep sum = 4

Final Answer: maximum = 7 ✅
Subarray: [4, -1, -2, 1, 5]
```

> ⚠️ **Crucial Insight on Step 4 & 5:** Even though `-1` and `-2` individually are negative, we **don't reset** because the **running sum is still positive** (3 and 1 respectively). The reset rule is **sum < 0**, NOT **current element < 0**. This is the heart of Kadane's Algorithm!

### Code

```cpp
// Kadane's Algorithm — O(n), O(1)
long long maxSubarraySum(vector<int>& arr, int n) {
    long long sum = 0;
    long long maxi = LONG_MIN;  // Use LONG_MIN since return type is long long

    for (int i = 0; i < n; i++) {
        sum += arr[i];                      // Add current element
        maxi = max(maxi, sum);              // Update max if current sum is better
        if (sum < 0) sum = 0;              // Reset if sum went negative
    }
    return maxi;
}
```

| Time | Space |
| :----------------------: | :-------------------: |
| **O(n)** — single pass ✅ | **O(1)** — no extra space ✅ |

---

## 6. 🔵 Follow-Up: Print the Subarray with Maximum Sum

> The interviewer may ask: *"Can you also tell me which subarray gives the maximum sum?"*
>
> There might be **multiple valid subarrays** with the same max sum — returning any one is acceptable.

### Key Observation for Tracking the Subarray

```
Every time sum resets to 0, we are starting a NEW subarray from the NEXT index.
So:
  → Track 'start' whenever sum becomes 0 (= index where new subarray begins)
  → Track 'ansStart' and 'ansEnd' whenever a new maximum is found
```

### Dry Run — Tracking Indices

```
arr = [-2, -3, 4, -1, -2, 1, 5, -3]
      sum = 0, maxi = INT_MIN, start = 0, ansStart = -1, ansEnd = -1

i=0: arr[0]=-2 → sum=-2 → maxi=-2 (ansStart=0, ansEnd=0) → sum<0 → reset, start=1
i=1: arr[1]=-3 → sum=-3 → -3 < -2, no update → sum<0 → reset, start=2
i=2: arr[2]= 4 → sum= 4 → maxi=4 (ansStart=2, ansEnd=2) → sum>0, keep
i=3: arr[3]=-1 → sum= 3 → 3 < 4, no update → sum>0, keep
i=4: arr[4]=-2 → sum= 1 → 1 < 4, no update → sum>0, keep
i=5: arr[5]= 1 → sum= 2 → 2 < 4, no update → sum>0, keep
i=6: arr[6]= 5 → sum= 7 → maxi=7 (ansStart=2, ansEnd=6) ✅ → sum>0, keep
i=7: arr[7]=-3 → sum= 4 → 4 < 7, no update → sum>0, keep

Result: Subarray from index 2 to 6 = [4, -1, -2, 1, 5], sum = 7 ✅
```

### Code with Subarray Printing

```cpp
// Kadane's + Print Subarray — O(n), O(1)
long long maxSubarraySum(vector<int>& arr, int n) {
    long long sum = 0;
    long long maxi = LONG_MIN;

    int start = 0;       // Tracks start of current candidate subarray
    int ansStart = -1;   // Final answer: start index
    int ansEnd = -1;     // Final answer: end index

    for (int i = 0; i < n; i++) {
        if (sum == 0) start = i;  // Starting a new subarray from here

        sum += arr[i];

        if (sum > maxi) {
            maxi = sum;
            ansStart = start;  // Lock in the start of this best subarray
            ansEnd = i;        // Current index is the end
        }

        if (sum < 0) sum = 0;  // Reset — don't carry negatives
    }

    // Print the subarray
    cout << "Subarray: [";
    for (int i = ansStart; i <= ansEnd; i++) {
        cout << arr[i];
        if (i < ansEnd) cout << ", ";
    }
    cout << "]" << endl;

    return maxi;
}
```

> 🔑 **Why `if (sum == 0) start = i` and not `if (sum < 0)`?**
> We reset `start` AFTER we reset `sum` to 0. So when `sum` is already 0 at the beginning of the next iteration, we mark that index as the new potential start. This ensures `start` always points to the beginning of the current running subarray.

| Time | Space |
| :----------------------: | :-------------------: |
| **O(n)** — single pass ✅ | **O(1)** — in-place ✅ |

---

## 7. ⚠️ Edge Case: All Negative Elements

> **What if every element in the array is negative?**
>
> Example: `[-4, -2, -3, -1]`

```
According to problem constraint: "including empty subarray"
→ If no subarray gives a positive sum, you are allowed to return 0
  (taking an EMPTY subarray which contributes 0)

But in LeetCode's version of this problem:
→ You MUST pick at least one element
→ So the answer for [-4, -2, -3, -1] = -1 (the least negative element)
```

### Handling Both Variants

```cpp
// Variant 1: Empty subarray allowed (return 0 if all negative)
if (maxi < 0) return 0;
return maxi;

// Variant 2: Must pick at least one element (LeetCode default)
return maxi;  // INT_MIN initialized catches the least negative
```

> ⚠️ **Always clarify with the interviewer:** *"Should I consider the empty subarray, or must I pick at least one element?"*

---

## 8. 📝 Practice Questions

### Easy
1. 🔗 [Maximum Subarray — LeetCode 53](https://leetcode.com/problems/maximum-subarray/) *(Covered in lecture)*
2. 🔗 [Maximum Sum Circular Subarray — LeetCode 918](https://leetcode.com/problems/maximum-sum-circular-subarray/) *(Kadane's variant)*
3. 🔗 [Best Time to Buy and Sell Stock — LeetCode 121](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) *(Kadane's intuition applied)*

### Medium
4. 🔗 [Maximum Product Subarray — LeetCode 152](https://leetcode.com/problems/maximum-product-subarray/) *(Track min AND max — negatives flip sign)*
5. 🔗 [Subarray Sum Equals K — LeetCode 560](https://leetcode.com/problems/subarray-sum-equals-k/) *(Prefix sum + HashMap)*
6. 🔗 [Longest Turbulent Subarray — LeetCode 978](https://leetcode.com/problems/longest-turbulent-subarray/) *(Kadane's variant for length)*
7. 🔗 [Maximum Sum of Two Non-Overlapping Subarrays — LeetCode 1031](https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/)

### Challenging
8. 🔗 [Maximum Subarray Sum with One Deletion — LeetCode 1186](https://leetcode.com/problems/maximum-subarray-sum-with-one-deletion/) *(DP variant)*
9. 🔗 [K-Concatenation Maximum Sum — LeetCode 1191](https://leetcode.com/problems/k-concatenation-maximum-sum/)
10. 🔗 [Maximum Submatrix Sum — 2D Kadane's](https://www.geeksforgeeks.org/maximum-sum-rectangle-in-a-2d-matrix-dp-27/) *(Extend Kadane's to 2D matrices)*

---

## 9. 🎯 Interview Questions & Answers

### Q1: What is a subarray? How is it different from a subsequence and a subset?

> | Term | Contiguous? | Order Maintained? | Example from [1,2,3,4] |
> |------|:-----------:|:-----------------:|------------------------|
> | **Subarray** | ✅ Yes | ✅ Yes | [2,3,4] |
> | **Subsequence** | ❌ No | ✅ Yes | [1,3,4] (skip 2) |
> | **Subset** | ❌ No | ❌ No | {1,4,2} |
>
> Subarrays are the most restrictive — elements must be **adjacent** in the original array.

---

### Q2: Explain Kadane's Algorithm in simple terms.

> Kadane's algorithm is a **greedy/dynamic programming** approach with one core rule:
>
> *"Carry your current sum forward only if it is positive. The moment it goes negative, drop it and start fresh."*
>
> The idea is: a **negative prefix sum** will always reduce any future sum it is added to. So there is zero benefit in carrying it. By resetting to zero, we give the next element a "clean slate" to start a new potential maximum subarray.
>
> ```
> Think of it like this: you're a traveler collecting gems (positive values)
> and paying tolls (negative values). If your total debt exceeds your gems,
> abandon that path and start a new route from the next gem.
> ```

---

### Q3: Why do we NOT reset when the current element is negative, but only when the running SUM is negative?

> This is the **most common interview trap** with Kadane's Algorithm.
>
> ```
> arr = [4, -1, -2, 1, 5]
> ```
>
> If we reset whenever we see a **negative element**, we'd restart at `-1` and at `-2`, missing the connection between 4 and 5. But these negatives only cost us 3 total, while the connection they provide between 4 and 5 contributes 9. Net gain = +6.
>
> We only reset when the **cumulative sum goes negative** because at that point, the prefix is actively **hurting** us. As long as the sum stays positive (even while passing through negatives), carrying it forward adds value.

---

### Q4: What is the time and space complexity of Kadane's Algorithm?

> **Time Complexity: O(n)**
> We traverse the array exactly once with a single loop. No nested iteration.
>
> **Space Complexity: O(1)**
> We only maintain two variables: `sum` (current running sum) and `maxi` (global maximum). No extra arrays, maps, or sets.
>
> This is provably optimal for this problem — you must look at every element at least once, so O(n) is the theoretical lower bound.

---

### Q5: How do you handle the case when all elements are negative?

> **Two scenarios based on problem statement:**
>
> **Scenario 1:** Empty subarray is allowed.
> → Return `0` (the sum of an empty subarray).
> → Implementation: `if (maxi < 0) return 0;`
>
> **Scenario 2:** Must pick at least one element (LeetCode's version).
> → Return the **least negative** element (the maximum among all negatives).
> → Implementation: Initialize `maxi = INT_MIN` (or `LONG_MIN`). Kadane's naturally handles it — the max will be updated to the highest (least negative) single element.
>
> ⚠️ Always initialize `maxi = INT_MIN`, NOT `maxi = 0`. If you initialize to 0, you'll incorrectly return 0 for all-negative arrays when an answer is required.

---

### Q6: Can you print the actual subarray, not just the sum? How would you modify the algorithm?

> Yes. Track two additional variables:
> - `start`: marks the beginning of the **current candidate subarray** (reset whenever `sum` becomes 0)
> - `ansStart` and `ansEnd`: lock in the start and end indices whenever a **new maximum** is found
>
> ```cpp
> if (sum == 0) start = i;       // New subarray begins here
> sum += arr[i];
> if (sum > maxi) {
>     maxi = sum;
>     ansStart = start;          // Save the start of this best subarray
>     ansEnd = i;                // Current index is the end
> }
> if (sum < 0) sum = 0;
> ```
>
> No change in time (O(n)) or space (O(1)) complexity — just two extra integer variables.

---

### Q7: What is the difference between the Brute Force O(n³), Better O(n²), and Optimal O(n) approaches?

> | Approach | Loops | Key Idea | Time | Space |
> |----------|:-----:|----------|:----:|:-----:|
> | **Brute Force** | 3 (i, j, k) | Generate subarray [i..j], sum via k loop | O(n³) | O(1) |
> | **Better** | 2 (i, j) | Maintain running sum — add arr[j] incrementally | O(n²) | O(1) |
> | **Optimal (Kadane's)** | 1 (i) | Discard negative prefix sums greedily | O(n) | O(1) |
>
> Each step eliminates one loop by leveraging a key insight:
> - O(n²): "I don't need to resum — just extend."
> - O(n): "I don't need to try every start — negative sums aren't worth keeping."

---

### Q8: Is Kadane's Algorithm a Greedy or Dynamic Programming algorithm?

> It's a beautiful intersection of **both**:
>
> **Greedy aspect:** At each step, we make a locally optimal decision — if the current sum is negative, discard it (greedy choice).
>
> **DP aspect:** The recurrence relation is:
> ```
> dp[i] = max(arr[i], dp[i-1] + arr[i])
>       = "Either start fresh at arr[i], or extend the previous subarray"
> ```
>
> Kadane's optimizes this DP by not storing the entire `dp[]` array — we only ever need the previous value, reducing space from O(n) to O(1). So it's **space-optimized DP with greedy decision-making**.

---

### Q9: What are some real-world applications of Maximum Subarray Sum?

> 1. **Stock Market Analysis:** Find the period of maximum profit (buy low, sell high window).
> 2. **Signal Processing:** Find the signal segment with maximum energy/amplitude.
> 3. **Genomics:** Find the region in a DNA sequence with maximum scoring motif.
> 4. **Image Processing:** Identify the rectangular region of maximum brightness (2D Kadane's).
> 5. **Financial Analytics:** Identify the best consecutive trading days for maximum cumulative gain.

---

### Q10: How would you extend Kadane's Algorithm to a 2D matrix (Maximum Sum Submatrix)?

> The idea is to **fix two rows** and then apply Kadane's on the **column sums** between those rows.
>
> ```
> For each pair (top_row, bottom_row):
>   1. Compress all rows between top and bottom into a 1D array
>      (column sums: colSum[j] = sum of arr[top..bottom][j])
>   2. Apply Kadane's on this 1D colSum array
>   3. Track the maximum across all (top, bottom) pairs
> ```
>
> **Time Complexity:** O(n² × m) where n = rows, m = columns. For square matrix: O(n³).
> This is significantly better than the brute force O(n⁴) for 2D.

---

## 🧩 Quick Revision Summary

```
┌────────────────────────────────────────────────────────────────────┐
│              MAXIMUM SUBARRAY SUM (Kadane's Algorithm)             │
│                                                                    │
│  Definition:                                                       │
│  → Subarray = contiguous elements (no skipping!)                   │
│  → Goal: find subarray with highest sum                            │
│                                                                    │
│  Approach 1: Brute Force                                           │
│  → 3 loops (i, j, k): generate + sum all subarrays                │
│  → Time: O(n³)   Space: O(1)                                      │
│                                                                    │
│  Approach 2: Better                                                │
│  → 2 loops (i, j): maintain running sum, add arr[j]               │
│  → Time: O(n²)   Space: O(1)                                      │
│                                                                    │
│  Approach 3: Optimal — Kadane's Algorithm ✅                       │
│  → 1 loop: reset sum to 0 when it goes negative                   │
│  → Time: O(n)    Space: O(1)                                      │
│                                                                    │
│  KEY RULE of Kadane's:                                             │
│  → Reset when SUM < 0 (NOT when element < 0!)                     │
│  → Negative prefix always reduces future sums                     │
│                                                                    │
│  Follow-up: Print the subarray                                     │
│  → Track 'start' when sum resets to 0                             │
│  → Lock ansStart & ansEnd when new max is found                   │
│  → Same O(n) time, O(1) space                                     │
│                                                                    │
│  Edge case: all negatives                                          │
│  → Init maxi = INT_MIN (never 0!)                                  │
│  → Return 0 only if empty subarray is allowed                     │
└────────────────────────────────────────────────────────────────────┘
```

> 💬 *"The genius of Kadane's is not in its code (which is 5 lines) — it's in understanding WHY negative prefixes are worthless. Once that clicks, the algorithm is obvious."*

---

*📌 Source: Striver's A2Z DSA Course — Step 3.2: Arrays — Maximum Subarray Sum (Kadane's Algorithm)*

# 📘 Maximum Subarray Sum — Kadane's Algorithm
### 🗂️ Topic: Arrays | Step 3.2 | Striver A-to-Z DSA Course

---

## 📌 Problem Statement

> Given an integer array, find the **contiguous subarray** which has the **largest sum** and return that sum.

**LeetCode Link:** [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

---

## 🔑 Key Concepts

### What is a Subarray?
- A **subarray** is a **contiguous part** of an array.
- Even a **single element** counts as a subarray.
- The **entire array** itself is also a valid subarray.
- Elements with a **gap/discontinuity** are **NOT** a subarray — they form a **subsequence**.

```
Array:  [-2, -3, 4, -1, -2, 1, 5, -3]

✅ Valid subarray:   [4, -1, -2, 1, 5]   (contiguous)
❌ Not a subarray:  [4, 1, 5]            (discontinuous → subsequence)
```

---

## 🚀 Approach 1: Brute Force — O(N³)

### 💡 Idea
Try **all possible subarrays** and compute their sum. Track the maximum.

### 🔍 Logic
- Outer loop `i` → starting index of subarray
- Middle loop `j` → ending index of subarray
- Inner loop `k` → traverse from `i` to `j` to compute sum

### 💻 C++ Code

```cpp
#include <bits/stdc++.h>
using namespace std;

int maxSubarrayBruteForce(vector<int>& arr, int n) {
    int maxi = INT_MIN;

    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            int sum = 0;
            for (int k = i; k <= j; k++) {
                sum += arr[k];
            }
            maxi = max(maxi, sum);
        }
    }
    return maxi;
}

int main() {
    vector<int> arr = {-2, -3, 4, -1, -2, 1, 5, -3};
    int n = arr.size();
    cout << "Max Subarray Sum: " << maxSubarrayBruteForce(arr, n) << endl;
    // Output: 7
    return 0;
}
```

### 📊 Complexity
| | Complexity |
|---|---|
| ⏱️ Time | O(N³) |
| 🗂️ Space | O(1) |

---

## ⚡ Approach 2: Better — O(N²)

### 💡 Idea
Eliminate the inner `k` loop. Instead of recomputing the sum from scratch, **carry forward the running sum** as `j` moves.

### 🔍 Logic
- Outer loop `i` → starting index
- Inner loop `j` → ending index; keep adding `arr[j]` to running sum

### 💻 C++ Code

```cpp
#include <bits/stdc++.h>
using namespace std;

int maxSubarrayBetter(vector<int>& arr, int n) {
    int maxi = INT_MIN;

    for (int i = 0; i < n; i++) {
        int sum = 0;
        for (int j = i; j < n; j++) {
            sum += arr[j];           // carry forward sum
            maxi = max(maxi, sum);
        }
    }
    return maxi;
}

int main() {
    vector<int> arr = {-2, -3, 4, -1, -2, 1, 5, -3};
    int n = arr.size();
    cout << "Max Subarray Sum: " << maxSubarrayBetter(arr, n) << endl;
    // Output: 7
    return 0;
}
```

### 📊 Complexity
| | Complexity |
|---|---|
| ⏱️ Time | O(N²) |
| 🗂️ Space | O(1) |

---

## 🏆 Approach 3: Optimal — Kadane's Algorithm O(N)

### 💡 Core Intuition
> **"A negative running sum will only REDUCE future sums — drop it and restart from 0."**

- Keep adding elements to `sum`.
- If at any point `sum < 0`, **reset sum to 0** (discard the current subarray start).
- Track the maximum sum seen so far.

### 🔍 Dry Run

```
Array:  [-2, -3, 4, -1, -2, 1, 5, -3]

Index   Element   Sum    Action              Maxi
  0       -2      -2     sum<0, reset→0      -2
  1       -3      -3     sum<0, reset→0      -2
  2        4       4     carry forward        4
  3       -1       3     carry forward        4
  4       -2       1     carry forward        4
  5        1       2     carry forward        4
  6        5       7     new maximum!         7  ✅
  7       -3       4     carry forward        7

Answer: 7  →  Subarray [4, -1, -2, 1, 5]
```

### 🔑 Why carry a positive sum even through negatives?
> If current sum is **positive**, adding more elements can **only help or maintain** the total. Only a **negative** running sum drags future results down.

### 💻 C++ Code (Return Max Sum)

```cpp
#include <bits/stdc++.h>
using namespace std;

long long maxSubarrayKadane(vector<int>& arr, int n) {
    long long sum = 0;
    long long maxi = LLONG_MIN;

    for (int i = 0; i < n; i++) {
        sum += arr[i];
        maxi = max(maxi, sum);

        if (sum < 0) {
            sum = 0;  // Reset: negative sum is useless going forward
        }
    }

    // Edge case: if all elements negative, return 0 (empty subarray allowed)
    if (maxi < 0) maxi = 0;

    return maxi;
}

int main() {
    vector<int> arr = {-2, -3, 4, -1, -2, 1, 5, -3};
    int n = arr.size();
    cout << "Max Subarray Sum: " << maxSubarrayKadane(arr, n) << endl;
    // Output: 7
    return 0;
}
```

### 📊 Complexity
| | Complexity |
|---|---|
| ⏱️ Time | O(N) |
| 🗂️ Space | O(1) |

---

## 🔍 Follow-Up: Print the Actual Subarray

### 💡 Key Observation
- Every time `sum == 0`, we are **starting a fresh subarray**.
- Track `start` index whenever sum resets to 0.
- When a new maximum is found, record `ansStart = start` and `ansEnd = i`.

### 💻 C++ Code (Print Subarray)

```cpp
#include <bits/stdc++.h>
using namespace std;

long long maxSubarrayWithIndices(vector<int>& arr, int n) {
    long long sum = 0;
    long long maxi = LLONG_MIN;

    int start = 0;
    int ansStart = -1, ansEnd = -1;

    for (int i = 0; i < n; i++) {
        if (sum == 0) start = i;  // New subarray starts here

        sum += arr[i];

        if (sum > maxi) {
            maxi = sum;
            ansStart = start;   // Record where this subarray began
            ansEnd = i;         // Record where it currently ends
        }

        if (sum < 0) {
            sum = 0;  // Reset
        }
    }

    // Print the subarray
    cout << "Subarray: [";
    for (int i = ansStart; i <= ansEnd; i++) {
        cout << arr[i];
        if (i != ansEnd) cout << ", ";
    }
    cout << "]" << endl;

    return maxi;
}

int main() {
    vector<int> arr = {-2, -3, 4, -1, -2, 1, 5, -3};
    int n = arr.size();
    cout << "Max Sum: " << maxSubarrayWithIndices(arr, n) << endl;
    // Output: Subarray: [4, -1, -2, 1, 5]
    //         Max Sum: 7
    return 0;
}
```

---

## 📊 Complexity Comparison Table

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute Force | O(N³) | O(1) | 3 nested loops |
| Better | O(N²) | O(1) | 2 loops, carry sum |
| **Kadane's (Optimal)** | **O(N)** | **O(1)** | **Single pass ✅** |

---

## 🧩 Edge Cases to Remember

```cpp
// All negatives → return 0 (if empty subarray allowed)
arr = {-4, -2, -3, -1}  →  answer = 0

// All negatives → return max element (if empty subarray NOT allowed)
arr = {-4, -2, -3, -1}  →  answer = -1

// Single element
arr = {5}  →  answer = 5

// All positives
arr = {1, 2, 3}  →  answer = 6 (entire array)
```

---

## 🎯 Practice Problems

### 🟡 LeetCode

| # | Problem | Difficulty | Company Tags |
|---|---|---|---|
| [53](https://leetcode.com/problems/maximum-subarray/) | **Maximum Subarray** ⭐ (Main Problem) | Medium | `Amazon` `Microsoft` `Google` `Apple` `Adobe` `LinkedIn` |
| [152](https://leetcode.com/problems/maximum-product-subarray/) | Maximum Product Subarray | Medium | `Amazon` `Microsoft` `Google` |
| [918](https://leetcode.com/problems/maximum-sum-circular-subarray/) | Maximum Sum Circular Subarray | Medium | `Google` `Amazon` |
| [1749](https://leetcode.com/problems/maximum-absolute-sum-of-any-subarray/) | Maximum Absolute Sum of Any Subarray | Medium | — |
| [560](https://leetcode.com/problems/subarray-sum-equals-k/) | Subarray Sum Equals K | Medium | `Facebook` `Google` `Amazon` `Microsoft` |
| [974](https://leetcode.com/problems/subarray-sums-divisible-by-k/) | Subarray Sums Divisible by K | Medium | `Google` `Facebook` |
| [1186](https://leetcode.com/problems/maximum-subarray-sum-with-one-deletion/) | Max Subarray Sum with One Deletion | Medium | `Google` |
| [2321](https://leetcode.com/problems/maximum-score-of-spliced-array/) | Maximum Score of Spliced Array | Hard | — |

---

### 🟠 HackerRank

| Problem | Link |
|---|---|
| **Max Subarray** | [HackerRank — Max Subarray](https://www.hackerrank.com/challenges/maxsubarray/problem) |
| **Angry Children** (Min difference subarray variant) | [HackerRank — Angry Children](https://www.hackerrank.com/challenges/angry-children/problem) |

---

### 🔵 CodeChef

| Problem | Link | Difficulty |
|---|---|---|
| **Subarray with Max Sum** | [CodeChef Practice](https://www.codechef.com/practice/course/arrays/ARRAYS/problems/MAXSUB) | Easy |
| **Chef and Array** | [CodeChef](https://www.codechef.com/problems/CHEFARRAY) | Medium |

---

### 🔴 Codeforces

| Problem | Link | Difficulty |
|---|---|---|
| **Maximal Sum** (CF 366C variant) | [Codeforces — 366C](https://codeforces.com/problemset/problem/366/C) | 1500 |
| **Good Subarrays** | [Codeforces — 1398C](https://codeforces.com/problemset/problem/1398/C) | 1500 |
| **Maximum Subarray** | [Codeforces — EDU](https://codeforces.com/edu/course/2/lesson/9/2/practice) | Medium |

---

## 🎤 Interview-Based Questions

### 🔹 Conceptual / Theory

**Q1.** What is the difference between a **subarray**, **subsequence**, and **subset**?
> - **Subarray**: Contiguous part of array. Order preserved. e.g. `[3,4,5]` from `[1,2,3,4,5]`
> - **Subsequence**: Non-contiguous but order preserved. e.g. `[1,3,5]`
> - **Subset**: Any combination, order not required. e.g. `{1,5,3}`

---

**Q2.** Explain Kadane's Algorithm in simple terms.
> "We scan left to right keeping a running sum. If the running sum becomes negative, we discard everything seen so far and start fresh — because a negative sum will only hurt any future subarray we try to build. We continuously update the global maximum."

---

**Q3.** When does Kadane's Algorithm reset the sum to 0?
> Whenever `sum < 0` after adding the current element. A negative running sum can never benefit the continuation of a subarray.

---

**Q4.** What if the array contains **all negative numbers**?
> - If **empty subarray is allowed** → return `0`.
> - If **empty subarray is NOT allowed** → return the maximum (least negative) element. Modify the algorithm to initialize `maxi` as `arr[0]` and never allow reset to 0.

---

**Q5.** What is the time and space complexity of Kadane's Algorithm?
> **Time: O(N)** — single pass through the array.
> **Space: O(1)** — only a few variables used.

---

### 🔹 Coding Follow-Up Questions

**Q6.** How do you **print the actual subarray** (not just the sum)?
> Track `start` index whenever `sum` resets to 0. When `sum > maxi`, record `ansStart = start` and `ansEnd = i`. Print `arr[ansStart...ansEnd]`.

---

**Q7.** Can Kadane's Algorithm be modified for **Maximum Product Subarray**?
> Yes, but you need to track both `maxProduct` and `minProduct` at each step (because a negative × negative = positive). This is LeetCode 152.

---

**Q8.** How would you solve **Maximum Sum Circular Subarray**?
> Two cases:
> 1. Maximum subarray lies in the **middle** → standard Kadane's
> 2. Maximum subarray **wraps around** → `Total Sum - Minimum Subarray Sum`
> Answer = `max(Kadane's result, totalSum - minSubarraySum)`
> Edge case: if all numbers are negative, return Kadane's result only.

---

**Q9.** How to find **number of subarrays** with maximum sum?
> After finding `maxi` via Kadane's, run a second pass counting all subarrays whose sum equals `maxi`.

---

**Q10.** What if there are **duplicate maximum subarrays**? Which one to return?
> The problem says return **any one**. By default Kadane's returns the **first one encountered** (leftmost). If you want the longest among them, add a length-tracking comparison.

---

**Q11.** *(Google/Amazon Level)*: Given an array, find the maximum sum of a subarray **with at most one deletion** allowed.
> LeetCode 1186 — Use DP with two states: `dp_no_del[i]` = max sum ending at `i` with no deletion, `dp_one_del[i]` = max sum ending at `i` with one deletion already used.

---

**Q12.** *(Facebook Level)*: Two arrays are given. You can swap one subarray between them. Find the maximum sum you can achieve in the first array.
> LeetCode 2321 — Apply Kadane's twice: once for gain from swapping into arr1, once for gain from swapping into arr2.

---

## 🧠 Quick Revision Summary

```
📌 Kadane's Algorithm — 3 Rules:
   1. Add current element to running sum
   2. Update maxi = max(maxi, sum)
   3. If sum < 0 → reset sum = 0

📌 To print subarray:
   → Track start index whenever sum == 0
   → Record ansStart & ansEnd when sum > maxi

📌 Edge case (all negatives, empty subarray allowed):
   → if maxi < 0: return 0

📌 Complexities: Time = O(N), Space = O(1)
```

---

*Notes based on Striver's A-to-Z DSA Course — Step 3.2*
*Topic: Arrays → Maximum Subarray Sum*

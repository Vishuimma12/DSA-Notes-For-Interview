# 🔀 Merge Sort — Striver's A2Z DSA Course (Step 2.2)

> **Lecture Summary:** This lecture covers the **Merge Sort** algorithm — a Divide and Conquer based sorting algorithm that runs in **O(n log n)** time, making it significantly faster than Selection Sort, Bubble Sort, and Insertion Sort (all O(n²)).

> **Prerequisite:** Step 1.5 — Recursion (you must understand recursion & backtracking before this lecture).

---

## 📌 Table of Contents

1. [Why Merge Sort?](#1--why-merge-sort)
2. [Core Algorithm — Divide & Merge](#2--core-algorithm--divide--merge)
3. [Pseudo Code](#3--pseudo-code)
4. [Recursion Dry Run](#4--recursion-dry-run)
5. [The Merge Algorithm (In Depth)](#5--the-merge-algorithm-in-depth)
6. [C++ Implementation](#6--c-implementation)
7. [Time & Space Complexity](#7--time--space-complexity)
8. [Practice Questions](#8--practice-questions)
9. [Interview Questions & Answers](#9--interview-questions--answers)

---

## 1. 🚀 Why Merge Sort?

| Algorithm      | Time Complexity  |
| -------------- | :--------------: |
| Selection Sort |      O(n²)       |
| Bubble Sort    |      O(n²)       |
| Insertion Sort |      O(n²)       |
| **Merge Sort** | **O(n log n)** ✨ |

> Merge Sort is **significantly faster** for large datasets. For example, for `n = 1,000,000`:
> - O(n²) = 10¹² operations 😵
> - O(n log n) ≈ 20 × 10⁶ operations ✅

---

## 2. 🧠 Core Algorithm — Divide & Merge

> **Two magic words: DIVIDE and MERGE**

### The Idea

1. **DIVIDE** the array into two halves
2. Recursively **DIVIDE** each half until you reach single elements
3. **MERGE** them back together in sorted order

### Visual Walkthrough

```
Original Array: [3, 1, 2, 4, 1, 5, 2, 6, 4]

                    [3, 1, 2, 4, 1, 5, 2, 6, 4]
                   /                             \
          [3, 1, 2, 4, 1]                    [5, 2, 6, 4]
          /             \                    /           \
      [3, 1, 2]       [4, 1]           [5, 2]         [6, 4]
      /       \        /   \            /   \           /   \
   [3, 1]    [2]    [4]   [1]       [5]    [2]      [6]   [4]
   /    \
 [3]   [1]
  ↑ Single elements — STOP dividing!

Now MERGE back up (in sorted order):

 [3] [1] → merge → [1, 3]
 [1, 3] [2] → merge → [1, 2, 3]
 [4] [1] → merge → [1, 4]
 [1, 2, 3] [1, 4] → merge → [1, 1, 2, 3, 4]  ← Left half sorted! ✅

 [5] [2] → merge → [2, 5]
 [6] [4] → merge → [4, 6]
 [2, 5] [4, 6] → merge → [2, 4, 5, 6]  ← Right half sorted! ✅

 [1, 1, 2, 3, 4] [2, 4, 5, 6] → merge → [1, 1, 2, 2, 3, 4, 4, 5, 6] ✅
```

### Key Observations

- **We don't actually create new arrays** during division — we use **index pointers** (`low`, `mid`, `high`) to define "hypothetical" subarrays within the original array.
- Division stops when a subarray has **only 1 element** (a single element is always sorted).
- The real work happens during **MERGE** — combining two sorted halves into one sorted array.

---

## 3. 📝 Pseudo Code

### Merge Sort Function

```
mergeSort(arr, low, high):
    // Base case: single element or invalid range
    if low >= high:
        return

    mid = (low + high) / 2

    // Step 1: Sort the left half
    mergeSort(arr, low, mid)

    // Step 2: Sort the right half
    mergeSort(arr, mid + 1, high)

    // Step 3: Merge both sorted halves
    merge(arr, low, mid, high)
```

### Understanding the Parameters

```
Array: [3, 2, 4, 1, 3]
Index:  0  1  2  3  4
        ↑           ↑
       low         high

mid = (0 + 4) / 2 = 2

Left half:  arr[low..mid]     → arr[0..2] → [3, 2, 4]
Right half: arr[mid+1..high]  → arr[3..4] → [1, 3]
```

> 🔑 **Base Case:** When `low >= high`, the subarray has 0 or 1 elements — there's nothing to sort, so **return**.

### Merge Function

```
merge(arr, low, mid, high):
    temp = []               // Temporary array
    left = low              // Pointer for left half
    right = mid + 1         // Pointer for right half

    // Compare and pick the smaller element
    while left <= mid AND right <= high:
        if arr[left] <= arr[right]:
            temp.add(arr[left])
            left++
        else:
            temp.add(arr[right])
            right++

    // Copy remaining elements from left half
    while left <= mid:
        temp.add(arr[left])
        left++

    // Copy remaining elements from right half
    while right <= high:
        temp.add(arr[right])
        right++

    // Copy back to original array
    for i = low to high:
        arr[i] = temp[i - low]
```

---

## 4. 🔄 Recursion Dry Run

Let's trace through a smaller example to see **exactly** how the recursion unfolds.

```
Array: [3, 2, 4, 1, 3]
Index:  0  1  2  3  4
```

### Call Stack Trace

```
mergeSort(arr, 0, 4)
│   mid = 2
│
├── mergeSort(arr, 0, 2)          ← Sort left half [3, 2, 4]
│   │   mid = 1
│   │
│   ├── mergeSort(arr, 0, 1)      ← Sort [3, 2]
│   │   │   mid = 0
│   │   │
│   │   ├── mergeSort(arr, 0, 0)  ← Base case! [3] → return
│   │   │
│   │   ├── mergeSort(arr, 1, 1)  ← Base case! [2] → return
│   │   │
│   │   └── merge(arr, 0, 0, 1)  ← Merge [3] and [2] → [2, 3] ✅
│   │
│   ├── mergeSort(arr, 2, 2)      ← Base case! [4] → return
│   │
│   └── merge(arr, 0, 1, 2)      ← Merge [2, 3] and [4] → [2, 3, 4] ✅
│
├── mergeSort(arr, 3, 4)          ← Sort right half [1, 3]
│   │   mid = 3
│   │
│   ├── mergeSort(arr, 3, 3)      ← Base case! [1] → return
│   │
│   ├── mergeSort(arr, 4, 4)      ← Base case! [3] → return
│   │
│   └── merge(arr, 3, 3, 4)      ← Merge [1] and [3] → [1, 3] ✅
│
└── merge(arr, 0, 2, 4)          ← Final merge [2, 3, 4] and [1, 3]
                                     → [1, 2, 3, 3, 4] ✅ SORTED!
```

### How Recursion Flows

```
Pattern: Always go LEFT first → then RIGHT → then MERGE

         ↓ go left
         ↓ go left
         ↓ go left
         ✗ base case → come back
         ↓ go right
         ✗ base case → come back
         ★ MERGE
         → come back up
         ★ MERGE at higher level
         → repeat for right side
```

> 🔑 **Think of it like a tree:** The recursion goes all the way down the **left** branch first, then the **right** branch, and merges on the way **back up**.

---

## 5. 🔗 The Merge Algorithm (In Depth)

This is where the **actual sorting** happens. Given two **already-sorted** halves, merge them into one sorted array.

### Step-by-Step Example

```
Left half:  [1, 1, 2, 3, 4]    (indices 0–4, left pointer starts at 0)
Right half: [2, 4, 5, 6]       (indices 5–8, right pointer starts at 5)

Temporary array: [ ]

Comparison 1: 1 vs 2  → take 1 (left),  left++    temp: [1]
Comparison 2: 1 vs 2  → take 1 (left),  left++    temp: [1, 1]
Comparison 3: 2 vs 2  → take 2 (left),  left++    temp: [1, 1, 2]
Comparison 4: 3 vs 2  → take 2 (right), right++   temp: [1, 1, 2, 2]
Comparison 5: 3 vs 4  → take 3 (left),  left++    temp: [1, 1, 2, 2, 3]
Comparison 6: 4 vs 4  → take 4 (left),  left++    temp: [1, 1, 2, 2, 3, 4]

Left exhausted! ← No more elements on the left

Copy remaining right: 4, 5, 6  →  temp: [1, 1, 2, 2, 3, 4, 4, 5, 6]

Copy temp back to arr[0..8] ✅
```

### Three Phases of Merge

```
Phase 1: Compare left[i] and right[j], pick the smaller one → add to temp
         (repeat until ONE side is exhausted)

Phase 2: If left has remaining elements → copy all to temp

Phase 3: If right has remaining elements → copy all to temp

Finally: Copy temp back into the original array (from low to high)
```

> 🔑 **Why does merge work?** Because both halves are **individually sorted**. The smallest unprocessed element is always at one of the two pointers, so a single comparison is enough to decide which element goes next.

---

## 6. 💻 C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

void merge(vector<int>& arr, int low, int mid, int high) {
    vector<int> temp;       // Temporary array
    int left = low;         // Starting index of left half
    int right = mid + 1;    // Starting index of right half

    // Phase 1: Compare and pick smaller elements
    while (left <= mid && right <= high) {
        if (arr[left] <= arr[right]) {
            temp.push_back(arr[left]);
            left++;
        } else {
            temp.push_back(arr[right]);
            right++;
        }
    }

    // Phase 2: Copy remaining elements from left half
    while (left <= mid) {
        temp.push_back(arr[left]);
        left++;
    }

    // Phase 3: Copy remaining elements from right half
    while (right <= high) {
        temp.push_back(arr[right]);
        right++;
    }

    // Copy back to original array
    for (int i = low; i <= high; i++) {
        arr[i] = temp[i - low];
    }
}

void mergeSort(vector<int>& arr, int low, int high) {
    // Base case: single element or invalid
    if (low >= high) return;

    int mid = (low + high) / 2;

    mergeSort(arr, low, mid);        // Sort left half
    mergeSort(arr, mid + 1, high);   // Sort right half
    merge(arr, low, mid, high);      // Merge sorted halves
}

int main() {
    vector<int> arr = {3, 1, 2, 4, 1, 5, 2, 6, 4};
    int n = arr.size();

    mergeSort(arr, 0, n - 1);

    // Print sorted array
    for (int i = 0; i < n; i++) {
        cout << arr[i] << " ";
    }
    cout << endl;

    return 0;
}
```

### Key Implementation Notes

| Detail                               | Why?                                                              |
| ------------------------------------ | ----------------------------------------------------------------- |
| `vector<int>&` (pass by reference)   | So changes reflect in the original array without returning        |
| `arr[left] <= arr[right]` (use `<=`) | Ensures **stability** — equal elements maintain original order    |
| `temp[i - low]`                      | Because `temp` starts at index 0, but `arr` range starts at `low` |
| `low >= high` base case              | Handles single element (`low == high`) and invalid ranges         |
| `mid = (low + high) / 2`             | Can also use `low + (high - low) / 2` to avoid integer overflow   |

---

## 7. ⏱️ Time & Space Complexity

### Time Complexity

```
At each level, the total work done by all merges = O(n)
Number of levels (how many times we can divide by 2) = O(log₂ n)

Total = O(n) × O(log n) = O(n log n)
```

```
Level 0:  [n]                           → 1 merge of n     = n
Level 1:  [n/2] [n/2]                   → 2 merges of n/2  = n
Level 2:  [n/4] [n/4] [n/4] [n/4]       → 4 merges of n/4  = n
   ...
Level k:  [1] [1] [1] ... [1]           → n merges of 1    = n

Number of levels k = log₂(n)
Total work = n × log₂(n) = O(n log n)
```

### Why log₂(n) levels?

```
Example with n = 16:
16 → 8 → 4 → 2 → 1   (4 divisions = log₂(16) = 4)

Each step divides by 2, so we can divide at most log₂(n) times
before reaching single elements.
```

### Complexity Summary

| Case        | Complexity |
| ----------- | :--------: |
| **Best**    | O(n log n) |
| **Average** | O(n log n) |
| **Worst**   | O(n log n) |
| **Space**   |  **O(n)**  |

> ⚠️ **Space Complexity is O(n):** The `temp` array used during merge can hold up to `n` elements. This is the **trade-off** — Merge Sort uses extra memory, unlike in-place algorithms like Insertion Sort.

### Comparison with O(n²) Algorithms

| n         |     O(n²)      | O(n log n)  | Speedup  |
| --------- | :------------: | :---------: | :------: |
| 100       |     10,000     |    ~664     |   ~15×   |
| 1,000     |   1,000,000    |   ~9,966    |  ~100×   |
| 100,000   | 10,000,000,000 | ~1,660,964  | ~6,000×  |
| 1,000,000 |      10¹²      | ~19,931,569 | ~50,000× |

---

## 8. 📝 Practice Questions

### Easy

1. **Implement Merge Sort** for an array and print the array after each merge step.
2. **Merge Two Sorted Arrays** into one sorted array without using extra space for one of them (the merge subroutine in isolation). *(LeetCode 88) ✅*
3. Sort an array using Merge Sort and **count the total number of merge operations** performed.

### Medium

4. **Count Inversions** in an array using Merge Sort. An inversion is a pair `(i, j)` where `i < j` but `arr[i] > arr[j]`. *(Classic interview problem)*
5. **Sort a Linked List** using Merge Sort. *(LeetCode 148)*
6. **Merge k Sorted Lists** — extend the merge idea to k sorted arrays/lists. *(LeetCode 23)*
7. Implement **Bottom-Up (Iterative) Merge Sort** without recursion.
8. **Count of Smaller Numbers After Self** — for each element, count how many elements to its right are smaller. *(LeetCode 315)*

### Challenging

9. **Reverse Pairs** — Count pairs `(i, j)` where `i < j` and `arr[i] > 2 * arr[j]`. *(LeetCode 493)*
10. **Count of Range Sum** — Count range sums that lie in `[lower, upper]`. *(LeetCode 327)*
11. Implement **In-Place Merge Sort** with O(1) extra space (significantly harder).

---

## 9. 🎯 Interview Questions & Answers

### Q1: Explain Merge Sort in simple terms.

> Merge Sort follows the **Divide and Conquer** strategy:
> 1. **Divide** the array into two halves by finding the middle index
> 2. **Recursively sort** each half
> 3. **Merge** the two sorted halves back into a single sorted array
>
> The recursion stops when a subarray has only one element (which is trivially sorted). The merge step does the real sorting by comparing elements from both halves.

---

### Q2: What is the time complexity of Merge Sort? Why is it O(n log n)?

> - **Dividing** takes O(log n) levels — we divide by 2 each time, so `n → n/2 → n/4 → ... → 1` takes log₂(n) steps
> - **Merging** at each level processes all `n` elements total
> - Combined: **O(n) work per level × O(log n) levels = O(n log n)**
>
> This holds for **best, average, AND worst** case — unlike Quick Sort, Merge Sort has **guaranteed** O(n log n) performance.

---

### Q3: What is the space complexity? Can Merge Sort be done in-place?

> **Standard Merge Sort: O(n) space** — the temporary array during merging can hold up to `n` elements.
>
> **Additionally**, recursion uses O(log n) stack space.
>
> **In-place Merge Sort** is theoretically possible but extremely complex and impractical. The standard implementation trades O(n) space for simplicity and guaranteed O(n log n) time.

---

### Q4: Is Merge Sort stable? Why does it matter?

> **Yes, Merge Sort is stable** ✅ — when two elements are equal, the one from the **left** half is picked first (because of the `<=` condition in merge). This preserves the original relative order of equal elements.
>
> **Why it matters:** When sorting records by multiple keys (e.g., sort by grade, then by name), a stable sort ensures the secondary ordering is maintained.
>
> ```cpp
> // This condition ensures stability:
> if (arr[left] <= arr[right])  // Use <=, NOT <
> ```

---

### Q5: Merge Sort vs Quick Sort — when would you choose one over the other?

> | Aspect           | Merge Sort          | Quick Sort          |
> |------------------|---------------------|---------------------|
> | **Worst case**   | O(n log n) always ✅ | O(n²) worst case ❌  |
> | **Average case** | O(n log n)          | O(n log n)          |
> | **Space**        | O(n) extra ❌        | O(log n) in-place ✅ |
> | **Stable?**      | Yes ✅               | No ❌                |
> | **Cache performance** | Worse (not in-place) | Better (in-place) |
>
> **Choose Merge Sort when:**
> - You need **guaranteed O(n log n)** (no worst-case degradation)
> - You need a **stable** sort
> - You're sorting **linked lists** (no random access needed, O(1) extra space)
>
> **Choose Quick Sort when:**
> - Memory is limited (in-place)
> - Average-case performance is sufficient
> - Working with arrays (better cache locality)

---

### Q6: How does Merge Sort work on linked lists? Why is it preferred over Quick Sort for lists?

> For linked lists, Merge Sort is ideal because:
> 1. **Finding the middle** can be done with the slow-fast pointer technique — O(n)
> 2. **Merging** two sorted linked lists requires only pointer changes — **O(1) extra space** (no temporary array needed!)
> 3. **No random access** is required (unlike Quick Sort's partitioning)
>
> This makes linked list Merge Sort **O(n log n) time with O(log n) stack space** — much better than array-based Merge Sort.

---

### Q7: How can you count inversions using Merge Sort?

> An **inversion** is a pair `(i, j)` where `i < j` but `arr[i] > arr[j]`.
>
> During the merge step, when we pick an element from the **right half** (because it's smaller than the current left element), **all remaining elements in the left half** form inversions with this right element.
>
> ```
> Left:  [2, 5, 8]    Right: [1, 4, 7]
>              ↑                ↑
>          left=0           right=0
>
> 1 < 2 → pick 1 from right
> Inversions += (mid - left + 1) = 3
> Because 2, 5, 8 are ALL greater than 1
> ```
>
> This gives us inversion count in **O(n log n)** instead of the brute force O(n²).

---

### Q8: What is the recurrence relation for Merge Sort?

> ```
> T(n) = 2T(n/2) + O(n)
>
> Where:
>   2T(n/2) → Two recursive calls on halves
>   O(n)    → Merge step
>
> By Master's Theorem (Case 2: a = 2, b = 2, k = 1):
>   T(n) = O(n log n)
> ```

---

### Q9: What happens if we use `<` instead of `<=` in the merge comparison?

> ```cpp
> // Using <= (CORRECT — stable sort)
> if (arr[left] <= arr[right])
>
> // Using < (INCORRECT — unstable sort)
> if (arr[left] < arr[right])
> ```
>
> With `<`, when elements are **equal**, the right element is picked first, **breaking stability**. Equal elements from the left half should always go first to maintain their original relative order.

---

### Q10: Can Merge Sort be implemented iteratively (bottom-up)?

> **Yes!** Instead of recursively dividing, you start with subarrays of size 1 and **double the size** at each step:
>
> ```
> Size 1: Merge pairs [a][b] → [a,b], [c][d] → [c,d], ...
> Size 2: Merge pairs [a,b][c,d] → [a,b,c,d], ...
> Size 4: Merge pairs [a,b,c,d][e,f,g,h] → [a,b,c,d,e,f,g,h]
> ...until size >= n
> ```
>
> This eliminates the **O(log n) recursion stack space**, though the O(n) temporary array is still needed.

---

### Q11: Why do we use `temp[i - low]` when copying back to the original array?

> The `temp` array starts at index **0**, but the original array range starts at index **low**. The offset `i - low` maps array indices correctly:
>
> ```
> Original: arr[3], arr[4], arr[5]  (low = 3)
> Temp:     temp[0], temp[1], temp[2]
>
> When i = 3: temp[3 - 3] = temp[0]  ✅
> When i = 4: temp[4 - 3] = temp[1]  ✅
> When i = 5: temp[5 - 3] = temp[2]  ✅
> ```

---

### Q12: What is the difference between Merge Sort and the previously learned O(n²) algorithms?

> | Property             | O(n²) Algorithms | Merge Sort |
> |----------------------|:----------------:|:----------:|
> | **Approach**         | Iterative        | Divide & Conquer (Recursive) |
> | **Time Complexity**  | O(n²)            | O(n log n) |
> | **Space**            | O(1)             | O(n) |
> | **Stable?**          | Varies           | Always stable |
> | **Best for**         | Small/nearly sorted arrays | Large datasets |
> | **Guaranteed perf.** | No (varies by case) | Yes (always n log n) |

---

## 🧩 Quick Revision Summary

```
┌──────────────────────────────────────────────────┐
│                   MERGE SORT                      │
│                                                   │
│  1. DIVIDE:  Split array into two halves          │
│              (using mid = (low + high) / 2)       │
│                                                   │
│  2. CONQUER: Recursively sort each half           │
│              (stop when single element)            │
│                                                   │
│  3. MERGE:   Combine two sorted halves            │
│              (using two-pointer technique)         │
│                                                   │
│  Time:  O(n log n) — Best, Average, Worst         │
│  Space: O(n) — for temporary merge array          │
│  Stable: YES ✅                                    │
│  In-place: NO ❌                                   │
└──────────────────────────────────────────────────┘
```

> 💬 *"Merge Sort guarantees O(n log n) in ALL cases. When you need reliability and stability, Merge Sort is your best friend."*

---

*📌 Source: Striver's A2Z DSA Course — Step 2.2: Merge Sort*


# ⚡ Quick Sort — Striver's A2Z DSA Course (Step 2.2)

> **Lecture Summary:** This lecture covers the **Quick Sort** algorithm — a Divide and Conquer sorting algorithm with **O(n log n)** average time complexity and **O(1) extra space** (no temporary array), making it slightly better than Merge Sort in terms of memory usage.

> **Prerequisite:** Merge Sort lecture (for understanding the recursion pattern and n log n analysis).

---

## 📌 Table of Contents

1. [Why Quick Sort?](#1--why-quick-sort)
2. [Core Algorithm — Pick a Pivot](#2--core-algorithm--pick-a-pivot)
3. [The Partition Algorithm (In Depth)](#3--the-partition-algorithm-in-depth)
4. [Pseudo Code](#4--pseudo-code)
5. [Complete Dry Run](#5--complete-dry-run)
6. [C++ Implementation](#6--c-implementation)
7. [Time & Space Complexity](#7--time--space-complexity)
8. [Practice Questions](#8--practice-questions)
9. [Interview Questions & Answers](#9--interview-questions--answers)

---

## 1. 🚀 Why Quick Sort?

| Algorithm      | Time Complexity | Space Complexity |
| -------------- | :-------------: | :--------------: |
| Selection Sort |      O(n²)      |       O(1)       |
| Bubble Sort    |      O(n²)      |       O(1)       |
| Insertion Sort |      O(n²)      |       O(1)       |
| Merge Sort     |   O(n log n)    |    **O(n)** ❌    |
| **Quick Sort** | **O(n log n)**  |    **O(1)** ✅    |

> Quick Sort achieves the same **O(n log n)** time as Merge Sort but **without the extra O(n) temporary array**. It only uses O(1) extra space (ignoring recursion stack space).

> ⚠️ **Does this mean Merge Sort is useless?** No! Merge Sort is essential for:
> - Sorting **linked lists** (O(1) space merge possible)
> - Problems like **count inversions**, **reverse pairs**, etc.
> - **Guaranteed** O(n log n) — Quick Sort can degrade to O(n²) in worst case

---

## 2. 🧠 Core Algorithm — Pick a Pivot

### Two Simple Rules

> **Rule 1:** Pick a **pivot** and place it in its **correct position** in the sorted array.
>
> **Rule 2:** Everything **smaller** goes to the **left**, everything **larger** goes to the **right**.

### What is a Pivot?

A pivot can be **any element** in the array:
- ✅ First element *(we'll use this)*
- ✅ Last element
- ✅ Median element
- ✅ Random element

It doesn't matter what you pick — as long as you place it at its correct position, the algorithm works.

### The Intuition

```
Why does this work?

If you pick EVERY element one by one and place each at its correct
position in the sorted array → the entire array becomes sorted.

It's that simple!
```

### Visual Overview

```
Original: [4, 6, 2, 5, 7, 9, 1, 3]

Step 1: Pick pivot = 4, place at correct position
        [2, 1, 3, 4, 6, 5, 7, 9]
                   ↑
              Pivot placed!
        Left: smaller    Right: larger

Step 2: Recursively do the same for left [2, 1, 3] and right [6, 5, 7, 9]

Step 3: Keep dividing until single elements → Array is sorted!

Final:  [1, 2, 3, 4, 5, 6, 7, 9] ✅
```

> 🔑 **Divide and Conquer:** We divide the problem at the pivot, then conquer (sort) each half independently.

---

## 3. 🔗 The Partition Algorithm (In Depth)

This is the **heart** of Quick Sort — how do we actually place the pivot at its correct position?

### The Two-Pointer Approach

```
Array: [4, 6, 2, 5, 7, 9, 1, 3]
        ↑                       ↑
       low                    high
      pivot=4

Step 1: Place i at low, j at high
        i →                   ← j

Step 2: Move i rightward → find first element GREATER than pivot
        Move j leftward  → find first element SMALLER than pivot

Step 3: If i < j → SWAP arr[i] and arr[j]
        Repeat Steps 2-3

Step 4: When j crosses i → STOP
        Swap pivot (arr[low]) with arr[j]
        j is now the partition index!
```

### Step-by-Step Walkthrough

```
Array: [4, 6, 2, 5, 7, 9, 1, 3]
Index:  0  1  2  3  4  5  6  7
Pivot = arr[0] = 4

Round 1:
  i moves right → finds 6 (> 4) at index 1. STOP.
  j moves left  → finds 3 (< 4) at index 7. STOP.
  i(1) < j(7)? YES → Swap arr[1] and arr[7]
  Array: [4, 3, 2, 5, 7, 9, 1, 6]

Round 2:
  i moves right → 3 ≤ 4 skip, 2 ≤ 4 skip, 5 > 4 STOP at index 3.
  j moves left  → 6 ≥ 4 skip, 1 < 4 STOP at index 6.
  i(3) < j(6)? YES → Swap arr[3] and arr[6]
  Array: [4, 3, 2, 1, 7, 9, 5, 6]

Round 3:
  i moves right → 1 ≤ 4 skip, 7 > 4 STOP at index 4.
  j moves left  → 5 ≥ 4 skip, 9 ≥ 4 skip, 7 ≥ 4 skip, 1 < 4 STOP at index 3.
  i(4) < j(3)? NO → j has CROSSED i. STOP!

Final Step:
  Swap pivot arr[0] with arr[j] = arr[3]
  Swap 4 and 1
  Array: [1, 3, 2, 4, 7, 9, 5, 6]
                   ↑
            Partition Index = 3
            4 is at its CORRECT position!
            Left: [1, 3, 2] — all < 4 ✅
            Right: [7, 9, 5, 6] — all > 4 ✅
```

### Why Swap with `j` (not `i`) at the End?

```
When j crosses i:
  - Everything at index ≤ j is SMALLER than or equal to pivot
  - Everything at index ≥ i is GREATER than or equal to pivot
  - j points to the LAST element in the "smaller" zone
  - Swapping pivot with arr[j] places pivot exactly between
    the smaller and larger groups → CORRECT POSITION!
```

### Handling Equal Elements

> When there are **duplicate** elements equal to the pivot:
> - `i` moves while `arr[i] <= pivot` → equal elements stay on the **left**
> - `j` moves while `arr[j] > pivot` → shifted to the **right** only if strictly greater
>
> This ensures equal elements are handled consistently.

---

## 4. 📝 Pseudo Code

### Quick Sort Function

```
quickSort(arr, low, high):
    if low < high:                              // More than 1 element
        partitionIndex = partition(arr, low, high)
        quickSort(arr, low, partitionIndex - 1)   // Sort left
        quickSort(arr, partitionIndex + 1, high)  // Sort right
```

### Partition Function

```
partition(arr, low, high):
    pivot = arr[low]        // Pick first element as pivot
    i = low                 // Left pointer
    j = high                // Right pointer

    while i < j:
        // Find first element GREATER than pivot (from left)
        while arr[i] <= pivot AND i <= high - 1:
            i++

        // Find first element SMALLER than pivot (from right)
        while arr[j] > pivot AND j >= low + 1:
            j--

        // If pointers haven't crossed, swap
        if i < j:
            swap(arr[i], arr[j])

    // Place pivot at its correct position
    swap(arr[low], arr[j])

    return j    // Partition index
```

### Boundary Checks Explained

| Check             | Why?                                                      |
| ----------------- | --------------------------------------------------------- |
| `i <= high-1`     | Prevents `i` from going beyond the array's right boundary |
| `j >= low+1`      | Prevents `j` from going beyond the array's left boundary  |
| `arr[i] <= pivot` | Uses `<=` so equal elements go to the **left** side       |
| `arr[j] > pivot`  | Uses `>` (strict) since equals are on the left            |

---

## 5. 🔄 Complete Dry Run

### Recursion Call Trace

```
Array: [4, 6, 2, 5, 7, 9, 1, 3]

quickSort(arr, 0, 7)
│   partition(arr, 0, 7) → pivot=4, returns index 3
│   Array after partition: [1, 3, 2, 4, 7, 9, 5, 6]
│
├── quickSort(arr, 0, 2)          ← Sort left [1, 3, 2]
│   │   partition(arr, 0, 2) → pivot=1, returns index 0
│   │   Array portion: [1, 3, 2]  (1 already at correct place)
│   │
│   ├── quickSort(arr, 0, -1)     ← low > high, RETURN
│   │
│   └── quickSort(arr, 1, 2)      ← Sort [3, 2]
│       │   partition(arr, 1, 2) → pivot=3, returns index 2
│       │   Array portion: [2, 3]
│       │
│       ├── quickSort(arr, 1, 1)   ← Single element, RETURN
│       │
│       └── quickSort(arr, 3, 2)   ← low > high, RETURN
│
├── quickSort(arr, 4, 7)          ← Sort right [7, 9, 5, 6]
│   │   partition(arr, 4, 7) → pivot=7, returns index 6
│   │   Array portion: [5, 6, 7, 9]
│   │
│   ├── quickSort(arr, 4, 5)      ← Sort [5, 6]
│   │   │   partition(arr, 4, 5) → pivot=5, returns index 4
│   │   │   Array portion: [5, 6]  (already sorted)
│   │   │
│   │   ├── quickSort(arr, 4, 3)   ← low > high, RETURN
│   │   └── quickSort(arr, 5, 5)   ← Single element, RETURN
│   │
│   └── quickSort(arr, 7, 7)      ← Single element [9], RETURN

Final Array: [1, 2, 3, 4, 5, 6, 7, 9] ✅
```

### Recursion Pattern

```
Same as Merge Sort:
  → Always go LEFT first
  → Then go RIGHT
  → But NO merge step needed! (partition does the work upfront)

Key difference from Merge Sort:
  Merge Sort:  Divide → Recurse → MERGE (work on the way UP)
  Quick Sort:  PARTITION (work on the way DOWN) → Recurse
```

---

## 6. 💻 C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

int partition(vector<int>& arr, int low, int high) {
    int pivot = arr[low];   // First element as pivot
    int i = low;
    int j = high;

    while (i < j) {
        // Find first element greater than pivot (from left)
        while (arr[i] <= pivot && i <= high - 1) {
            i++;
        }

        // Find first element smaller than pivot (from right)
        while (arr[j] > pivot && j >= low + 1) {
            j--;
        }

        // Swap if pointers haven't crossed
        if (i < j) {
            swap(arr[i], arr[j]);
        }
    }

    // Place pivot at its correct position
    swap(arr[low], arr[j]);

    return j;  // Partition index
}

void quickSort(vector<int>& arr, int low, int high) {
    if (low < high) {
        int pIndex = partition(arr, low, high);
        quickSort(arr, low, pIndex - 1);    // Sort left
        quickSort(arr, pIndex + 1, high);   // Sort right
    }
}

int main() {
    vector<int> arr = {4, 6, 2, 5, 7, 9, 1, 3};
    int n = arr.size();

    quickSort(arr, 0, n - 1);

    // Print sorted array
    for (int i = 0; i < n; i++) {
        cout << arr[i] << " ";
    }
    cout << endl;

    return 0;
}
```

### Key Implementation Details

| Detail                             | Why?                                                              |
| ---------------------------------- | ----------------------------------------------------------------- |
| `vector<int>&` (pass by reference) | Modifications reflect in the original array                       |
| `arr[i] <= pivot` (use `<=`)       | Equal elements go to the left side                                |
| `arr[j] > pivot` (strict `>`)      | Consistent with `<=` on the left                                  |
| `i <= high - 1`                    | Prevents `i` from going out of bounds on the right                |
| `j >= low + 1`                     | Prevents `j` from going out of bounds on the left                 |
| `swap(arr[low], arr[j])`           | `j` is the last position of the "smaller" zone — pivot goes there |

---

## 7. ⏱️ Time & Space Complexity

### Time Complexity Breakdown

```
Each partition call scans the subarray → O(n) work
Number of levels (recursive depth)   → O(log n) on average

Total (average) = O(n) × O(log n) = O(n log n)
```

### Best & Average Case — O(n log n)

```
When pivot splits the array roughly in half each time:

Level 0:  [n]                     → O(n) partition work
Level 1:  [n/2] [n/2]             → O(n) total
Level 2:  [n/4] [n/4] [n/4] [n/4] → O(n) total
  ...
Level k:  Single elements          → O(n) total

Height = log₂(n) levels → Total = O(n log n)
```

### Worst Case — O(n²) ⚠️

```
When pivot is always the SMALLEST or LARGEST element:
(Happens with already sorted/reverse-sorted arrays + first element pivot)

Array: [1, 2, 3, 4, 5]  →  pivot = 1 → partition: [] [1] [2,3,4,5]
       [2, 3, 4, 5]     →  pivot = 2 → partition: [] [2] [3,4,5]
       [3, 4, 5]         →  pivot = 3 → partition: [] [3] [4,5]
       [4, 5]            →  pivot = 4 → partition: [] [4] [5]

n + (n-1) + (n-2) + ... + 1 = n(n+1)/2 = O(n²)

The tree becomes a SKEWED line (height = n instead of log n)
```

> 💡 **How to avoid worst case?** Use **randomized pivot** selection or **median-of-three** strategy instead of always picking the first element.

### Complexity Summary

|    Case     | Time Complexity | Space Complexity |
| :---------: | :-------------: | :--------------: |
|  **Best**   |   O(n log n)    |      O(1)*       |
| **Average** |   O(n log n)    |      O(1)*       |
|  **Worst**  |   **O(n²)** ⚠️   |      O(1)*       |

> *\*O(1) auxiliary space — not counting the **O(log n)** recursion stack space (average) or **O(n)** stack space (worst case).*

### Quick Sort vs Merge Sort

| Aspect              |       Quick Sort        |       Merge Sort        |
| ------------------- | :---------------------: | :---------------------: |
| **Average Time**    |       O(n log n)        |       O(n log n)        |
| **Worst Time**      |         O(n²) ⚠️         |      O(n log n) ✅       |
| **Extra Space**     |         O(1) ✅          |         O(n) ❌          |
| **Stable?**         |          No ❌           |          Yes ✅          |
| **In-Place?**       |          Yes ✅          |          No ❌           |
| **Cache Friendly?** | Yes ✅ (better locality) |          No ❌           |
| **Best for**        |  Arrays (general use)   | Linked lists, stability |

---

## 8. 📝 Practice Questions

### Easy

1. **Implement Quick Sort** and print the array after each partition step.
2. Modify Quick Sort to sort in **descending order** *(this is the assignment from the lecture!)*.
3. Implement Quick Sort using the **last element** as pivot instead of the first.

### Medium

4. **Kth Smallest/Largest Element** — Use the partition logic (QuickSelect) to find the kth element in O(n) average time. *(LeetCode 215)*
5. **Sort Colors (Dutch National Flag)** — Sort an array of 0s, 1s, and 2s. *(LeetCode 75)*
6. Implement **3-Way Partition** (Lomuto's variant) to handle arrays with many duplicate elements efficiently.
7. Implement **Randomized Quick Sort** — pick a random pivot to avoid worst-case O(n²).
8. **Top K Frequent Elements** — Use QuickSelect-based approach. *(LeetCode 347)*

### Challenging

9. Implement **Iterative Quick Sort** without recursion (using an explicit stack).
10. Implement **In-Place Merge Sort** using Quick Sort's partition idea.
11. **Median of Two Sorted Arrays** — Understand how partition logic extends to this classic problem. *(LeetCode 4)*

---

## 9. 🎯 Interview Questions & Answers

### Q1: Explain the Quick Sort algorithm in simple terms.

> Quick Sort is a **Divide and Conquer** algorithm:
> 1. **Pick a pivot** element (can be first, last, random, or median)
> 2. **Partition** the array — place the pivot at its correct position, with smaller elements on the left and larger on the right
> 3. **Recursively** apply the same process to the left and right subarrays
>
> The key insight: if every element is placed at its correct position, the array is sorted.

---

### Q2: What is the role of the Partition function?

> The partition function is the **core** of Quick Sort. It:
> 1. Selects a pivot element
> 2. Rearranges the array so that all elements **≤ pivot** are on the left, and all elements **> pivot** are on the right
> 3. Places the pivot at its **exact correct position** in the sorted array
> 4. Returns the **partition index** — telling Quick Sort where to split for recursive calls
>
> After partition, the pivot **never moves again**. It's permanently in place.

---

### Q3: What is the worst case of Quick Sort and how do you avoid it?

> **Worst case: O(n²)** — occurs when the pivot is always the smallest or largest element, creating maximally unbalanced partitions.
>
> **Common triggers:**
> - Already sorted array with first-element pivot
> - Reverse-sorted array with first-element pivot
> - Array with all identical elements (without 3-way partition)
>
> **How to avoid:**
> 1. **Randomized pivot** — pick a random element as pivot
> 2. **Median-of-three** — pick median of first, middle, and last elements
> 3. **3-way partition** — handles duplicates efficiently (Dutch National Flag)
>
> ```cpp
> // Randomized pivot example:
> int randomIndex = low + rand() % (high - low + 1);
> swap(arr[low], arr[randomIndex]);  // Move random element to low
> int pivot = arr[low];              // Then proceed as usual
> ```

---

### Q4: Is Quick Sort stable? Why or why not?

> **No, Quick Sort is NOT stable** ❌
>
> During partitioning, elements can be swapped across long distances, disrupting the relative order of equal elements.
>
> **Example:**
> ```
> Array: [4a, 4b, 1]  (4a and 4b are equal, 'a' appears first)
> Pivot = 4a
> After partition: [1, 4b, 4a]  ← 4b now comes before 4a!
> ```
>
> If you need a stable O(n log n) sort → use **Merge Sort**.

---

### Q5: Quick Sort vs Merge Sort — when would you choose one over the other?

> **Choose Quick Sort when:**
> - Working with **arrays** (better cache locality)
> - **Memory is limited** (O(1) extra space vs O(n) for Merge Sort)
> - Average-case performance is acceptable (O(n²) worst case is rare with randomization)
>
> **Choose Merge Sort when:**
> - You need **guaranteed O(n log n)** in all cases
> - You need a **stable** sort
> - Working with **linked lists** (no random access needed, O(1) merge space)
> - Solving **inversion-count** type problems

---

### Q6: What is QuickSelect and how is it related to Quick Sort?

> **QuickSelect** uses the partition function from Quick Sort to find the **kth smallest element** in **O(n) average time** — without fully sorting the array.
>
> **How it works:**
> 1. Partition the array → pivot goes to position `p`
> 2. If `p == k` → found the kth element!
> 3. If `p > k` → search in the left subarray only
> 4. If `p < k` → search in the right subarray only
>
> Unlike Quick Sort which recurses on **both** sides, QuickSelect only recurses on **one** side → O(n) average.
>
> ```
> T(n) = T(n/2) + O(n)  →  O(n) by Master's Theorem
> ```

---

### Q7: Why do we swap `arr[low]` with `arr[j]` (not `arr[i]`) at the end?

> When the while loop ends (j crosses i):
> - `j` points to the **last element** that is **≤ pivot** (in the "smaller" zone)
> - `i` points to the **first element** that is **> pivot** (in the "larger" zone)
>
> Since pivot needs to go **between** smaller and larger zones, we swap it with `arr[j]` — the boundary of the smaller zone.
>
> Swapping with `arr[i]` would place pivot inside the "larger" zone, which is incorrect.

---

### Q8: What is the recurrence relation for Quick Sort?

> **Best/Average case:**
> ```
> T(n) = 2T(n/2) + O(n)
> By Master's Theorem → T(n) = O(n log n)
> ```
>
> **Worst case (skewed partition):**
> ```
> T(n) = T(n-1) + O(n)
> T(n) = O(n) + O(n-1) + O(n-2) + ... + O(1) = O(n²)
> ```

---

### Q9: What are different pivot selection strategies?

> | Strategy          | Description                          | Worst-Case Avoidance |
> |-------------------|--------------------------------------|:--------------------:|
> | **First element** | Always pick `arr[low]`               | ❌ Poor               |
> | **Last element**  | Always pick `arr[high]`              | ❌ Poor               |
> | **Random**        | Pick random index in `[low, high]`   | ✅ Good               |
> | **Median-of-3**   | Median of first, middle, last        | ✅ Very Good           |
> | **Median-of-medians** | True median finding (O(n))       | ✅ Guaranteed          |
>
> In practice, **randomized pivot** or **median-of-three** are most commonly used.

---

### Q10: Can Quick Sort be implemented iteratively?

> **Yes!** Using an explicit stack to simulate recursion:
>
> ```cpp
> void iterativeQuickSort(vector<int>& arr, int low, int high) {
>     stack<pair<int,int>> stk;
>     stk.push({low, high});
>
>     while (!stk.empty()) {
>         auto [l, h] = stk.top();
>         stk.pop();
>
>         if (l < h) {
>             int p = partition(arr, l, h);
>             stk.push({l, p - 1});    // Left subarray
>             stk.push({p + 1, h});    // Right subarray
>         }
>     }
> }
> ```
>
> This eliminates the O(log n) recursion stack space but adds O(log n) explicit stack space — so the total space is the same, but it avoids **stack overflow** for very large arrays.

---

### Q11: What is 3-Way Partition (Dutch National Flag) and when is it useful?

> Standard Quick Sort creates **2 partitions**: `< pivot` and `> pivot`. With many duplicate elements, this is inefficient.
>
> **3-Way Partition** creates **3 regions**: `< pivot`, `== pivot`, `> pivot`.
>
> ```
> Before: [4, 2, 4, 1, 4, 3, 4]
>
> After 3-way partition (pivot = 4):
>          [2, 1, 3]  [4, 4, 4, 4]  []
>           < pivot     == pivot     > pivot
> ```
>
> All duplicates of pivot are placed together and **never processed again**. This reduces time complexity from O(n²) to **O(n log n)** even for arrays with many duplicates.

---

### Q12: What is the space complexity — why do we say O(1)?

> Quick Sort uses **O(1) auxiliary space** — no temporary arrays are created. All swaps happen **in-place**.
>
> However, recursion uses the **call stack**:
> - **Average case:** O(log n) stack depth (balanced partitions)
> - **Worst case:** O(n) stack depth (skewed partitions)
>
> So the **total** space is:
> - Average: O(log n)
> - Worst: O(n)
>
> When interviewers say "O(1) space," they typically mean **no extra data structures** (ignoring recursion stack). Always clarify!

---

## 🧩 Quick Revision Summary

```
┌──────────────────────────────────────────────────────┐
│                    QUICK SORT                         │
│                                                       │
│  1. PICK a pivot (first element / random / median)    │
│                                                       │
│  2. PARTITION: Place pivot at correct position         │
│     • i → finds first element > pivot (from left)     │
│     • j → finds first element < pivot (from right)    │
│     • Swap arr[i] & arr[j] while i < j                │
│     • When j crosses i → swap pivot with arr[j]       │
│                                                       │
│  3. RECURSE: Sort left and right subarrays             │
│                                                       │
│  Time:  O(n log n) avg  |  O(n²) worst               │
│  Space: O(1) extra  |  O(log n) stack avg             │
│  Stable: NO ❌                                         │
│  In-place: YES ✅                                      │
└──────────────────────────────────────────────────────┘
```

### 🔑 Key Differences: Quick Sort vs Merge Sort

```
Merge Sort:  Split → Recurse → MERGE     (work on the way UP ↑)
Quick Sort:  PARTITION → Recurse          (work on the way DOWN ↓)
```

> 💬 *"Quick Sort does its work BEFORE the recursive calls (partition), while Merge Sort does its work AFTER (merge). Understanding this difference is key to mastering Divide and Conquer."*

---

### 📋 Assignment from the Lecture

> **Modify Quick Sort to sort in DESCENDING order and share your solution!**
>
> Hint: In the partition function, reverse the comparison operators:
> - `arr[i] <= pivot` → `arr[i] >= pivot`
> - `arr[j] > pivot` → `arr[j] < pivot`

---

*📌 Source: Striver's A2Z DSA Course — Step 2.2: Quick Sort*

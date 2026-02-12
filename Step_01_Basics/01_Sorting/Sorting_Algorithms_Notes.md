# 🔢 Sorting Algorithms — Striver's A2Z DSA Course (Step 2.1)

> **Lecture Summary:** This lecture covers three fundamental sorting algorithms — **Selection Sort**, **Bubble Sort**, and **Insertion Sort**. These are frequently asked in interviews and form the foundation for understanding more advanced sorting techniques.

---

## 📌 Table of Contents

1. [Selection Sort](#1--selection-sort)
2. [Bubble Sort](#2--bubble-sort)
3. [Insertion Sort](#3--insertion-sort)
4. [Comparison Table](#4--comparison-table)
5. [Practice Questions](#5--practice-questions)
6. [Interview Questions & Answers](#6--interview-questions--answers)

---

## 1. 🟡 Selection Sort

### 💡 Core Idea

> **"Select the minimum and swap it to the front."**

In each pass, find the **smallest element** from the unsorted portion and **swap** it with the first element of the unsorted portion.

### 🔍 Step-by-Step Dry Run

```
Array: [13, 46, 24, 52, 20, 9]

Step 1: Min = 9 (index 5)  → Swap with index 0 → [9, 46, 24, 52, 20, 13]
Step 2: Min = 13 (index 5) → Swap with index 1 → [9, 13, 24, 52, 20, 46]
Step 3: Min = 20 (index 4) → Swap with index 2 → [9, 13, 20, 52, 24, 46]
Step 4: Min = 24 (index 4) → Swap with index 3 → [9, 13, 20, 24, 52, 46]
Step 5: Min = 46 (index 5) → Swap with index 4 → [9, 13, 20, 24, 46, 52]

✅ Sorted!
```

> **Key Observation:** For an array of `n` elements, you only need `n - 1` passes. The last element is automatically in place.

### 🧠 Algorithm (Pseudo Code)

```
for i = 0 to n - 2:
    mini = i
    for j = i to n - 1:
        if arr[j] < arr[mini]:
            mini = j
    swap(arr[i], arr[mini])
```

### 💻 C++ Code

```cpp
void selectionSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int mini = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[mini]) {
                mini = j;
            }
        }
        // Swap
        int temp = arr[mini];
        arr[mini] = arr[i];
        arr[i] = temp;
    }
}
```

### ⏱️ Time & Space Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(n²)      |
| Average | O(n²)      |
| Worst   | O(n²)      |
| Space   | O(1)       |

> **Why O(n²)?** The inner loop runs `n + (n-1) + (n-2) + ... + 1 = n(n+1)/2 ≈ n²/2 → O(n²)`

> ⚠️ **Note:** Selection Sort has **no best-case optimization**. Even if the array is already sorted, it still checks every element.

---

## 2. 🔵 Bubble Sort

### 💡 Core Idea

> **"Push the maximum to the end by adjacent swapping."**

Repeatedly compare **adjacent elements** and swap them if they are in the wrong order. After each pass, the largest unsorted element "bubbles up" to its correct position.

### 🔍 Step-by-Step Dry Run

```
Array: [13, 46, 24, 52, 20, 9]

Pass 1: Compare adjacent pairs →
  13,46 ✓ | 46,24 ✗→swap | 46,52 ✓ | 52,20 ✗→swap | 52,9 ✗→swap
  Result: [13, 24, 46, 20, 9, 52]  ← 52 bubbled to the end

Pass 2: (ignore last element)
  13,24 ✓ | 24,46 ✓ | 46,20 ✗→swap | 46,9 ✗→swap
  Result: [13, 24, 20, 9, 46, 52]  ← 46 in place

Pass 3: (ignore last 2)
  13,24 ✓ | 24,20 ✗→swap | 24,9 ✗→swap
  Result: [13, 20, 9, 24, 46, 52]  ← 24 in place

Pass 4: (ignore last 3)
  13,20 ✓ | 20,9 ✗→swap
  Result: [13, 9, 20, 24, 46, 52]  ← 20 in place

Pass 5: (ignore last 4)
  13,9 ✗→swap
  Result: [9, 13, 20, 24, 46, 52]  ← 13 in place

✅ Sorted!
```

### 🧠 Algorithm (Pseudo Code)

```
for i = n-1 down to 1:
    didSwap = false
    for j = 0 to i-1:
        if arr[j] > arr[j+1]:
            swap(arr[j], arr[j+1])
            didSwap = true
    if didSwap == false:
        break   // Array is already sorted!
```

> 🔑 **Optimization:** If no swaps happen in an entire pass, the array is already sorted → **break early!**

### 💻 C++ Code

```cpp
void bubbleSort(int arr[], int n) {
    for (int i = n - 1; i >= 1; i--) {
        bool didSwap = false;
        for (int j = 0; j < i; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j + 1];
                arr[j + 1] = arr[j];
                arr[j] = temp;
                didSwap = true;
            }
        }
        if (!didSwap) break;
    }
}
```

### ⏱️ Time & Space Complexity

| Case    | Complexity                                           |
| ------- | ---------------------------------------------------- |
| Best    | **O(n)** ✨ (with optimization, already sorted array) |
| Average | O(n²)                                                |
| Worst   | O(n²)                                                |
| Space   | O(1)                                                 |

> **Best Case O(n):** When the array is already sorted, the `didSwap` flag remains `false` after the first pass, and we break out. Only one pass of `n` comparisons is made.

---

## 3. 🟢 Insertion Sort

### 💡 Core Idea

> **"Take an element and place it in its correct position."**

Pick each element one by one and **insert** it into its correct position in the already-sorted left portion of the array by shifting elements to the right.

### 🔍 Step-by-Step Dry Run

```
Array: [14, 9, 15, 12, 6, 8]

Step 1: Pick 9   → Compare with 14 → Swap → [9, 14, 15, 12, 6, 8]
Step 2: Pick 15  → 15 > 14? ✓ No swap → [9, 14, 15, 12, 6, 8]
Step 3: Pick 12  → 12 < 15? Swap → 12 < 14? Swap → 12 > 9? Stop
         → [9, 12, 14, 15, 6, 8]
Step 4: Pick 6   → Swap left, left, left, left → goes to index 0
         → [6, 9, 12, 14, 15, 8]
Step 5: Pick 8   → Swap left, left, left → stops before 6
         → [6, 8, 9, 12, 14, 15]

✅ Sorted!
```

### 🧩 How the "Shifting" Works

Think of it like **sorting a hand of playing cards**:
1. You pick up a new card (element at index `i`)
2. You compare it with cards already in your hand (elements `0` to `i-1`)
3. You shift larger cards to the right
4. You insert the new card in its correct spot

### 🧠 Algorithm (Pseudo Code)

```
for i = 1 to n - 1:
    j = i
    while j > 0 AND arr[j-1] > arr[j]:
        swap(arr[j-1], arr[j])
        j = j - 1
```

> 🔑 **Why `j > 0`?** Because when `j = 0`, accessing `arr[j-1]` = `arr[-1]` would be **out of bounds** → runtime error!

### 💻 C++ Code

```cpp
void insertionSort(int arr[], int n) {
    for (int i = 1; i < n; i++) {
        int j = i;
        while (j > 0 && arr[j - 1] > arr[j]) {
            int temp = arr[j - 1];
            arr[j - 1] = arr[j];
            arr[j] = temp;
            j--;
        }
    }
}
```

### ⏱️ Time & Space Complexity

| Case    | Complexity                                          |
| ------- | --------------------------------------------------- |
| Best    | **O(n)** ✨ (already sorted array — no swaps needed) |
| Average | O(n²)                                               |
| Worst   | O(n²) (reverse sorted array)                        |
| Space   | O(1)                                                |

> **Best Case O(n):** When array is already sorted, the `while` condition `arr[j-1] > arr[j]` is never true, so only the outer loop runs → O(n).

> **Worst Case O(n²):** Reverse-sorted array. Every element travels all the way to the beginning → `1 + 2 + 3 + ... + (n-1) = n(n-1)/2 → O(n²)`.

---

## 4. 📊 Comparison Table

| Property             |           Selection Sort            |    Bubble Sort     |       Insertion Sort        |
| -------------------- | :---------------------------------: | :----------------: | :-------------------------: |
| **Best Case**        |                O(n²)                |     **O(n)** ✨     |         **O(n)** ✨          |
| **Average Case**     |                O(n²)                |       O(n²)        |            O(n²)            |
| **Worst Case**       |                O(n²)                |       O(n²)        |            O(n²)            |
| **Space Complexity** |                O(1)                 |        O(1)        |            O(1)             |
| **Stable?**          |                ❌ No                 |       ✅ Yes        |            ✅ Yes            |
| **Adaptive?**        |                ❌ No                 | ✅ Yes (with flag)  |            ✅ Yes            |
| **In-Place?**        |                ✅ Yes                |       ✅ Yes        |            ✅ Yes            |
| **# of Swaps**       |           O(n) — minimal            |    O(n²) — many    |        O(n²) — many         |
| **Best Use Case**    | Small arrays, when swaps are costly | Nearly sorted data | Small or nearly sorted data |

### 🔑 Key Takeaways

- **Selection Sort** makes the fewest swaps (exactly `n-1`), useful when write operations are expensive.
- **Bubble Sort** can detect a sorted array early with the `didSwap` optimization.
- **Insertion Sort** is the best among these three for **nearly sorted** data and small arrays.
- All three are **O(n²)** in the worst/average case — not suitable for large datasets (use Merge Sort / Quick Sort instead).

---

## 5. 📝 Practice Questions

### Easy

1. **Sort an array** of `n` integers using Selection Sort. *(Basic implementation)*
2. **Sort an array** using Bubble Sort. Count the total number of swaps performed.
3. **Sort an array** using Insertion Sort and print the array after each pass.
4. Given a **sorted array**, verify that Bubble Sort terminates in O(n) using the `didSwap` flag.

### Medium

5. **Sort an array of strings** alphabetically using any of the three sorting algorithms.
6. Given an array, find the **minimum number of swaps** required to sort it. *(Hint: use Selection Sort logic)*
7. Implement **Descending Order** versions of all three sorts.
8. Modify Insertion Sort to sort in **descending order** and trace through `[3, 7, 1, 9, 2]`.
9. **Sort a linked list** using Insertion Sort.
10. Given an array of `0s`, `1s`, and `2s`, sort it using Bubble Sort. *(Then compare with the optimal Dutch National Flag approach)*

### Challenging

11. **Stability Check:** Given an array of pairs `(value, original_index)`, sort by value using each algorithm. Verify which ones maintain relative order of equal elements (stable).
12. Implement **Binary Insertion Sort** — use Binary Search to find the correct position in Insertion Sort instead of linear comparison.
13. **Count Inversions** in an array using a modified Bubble Sort. *(An inversion is a pair `(i, j)` where `i < j` but `arr[i] > arr[j]`)*

---

## 6. 🎯 Interview Questions & Answers

### Q1: What is the difference between Selection Sort and Bubble Sort?

> **Selection Sort** finds the minimum element from the unsorted portion and **swaps it once** to its correct position. It makes at most `n-1` swaps total.
>
> **Bubble Sort** repeatedly compares **adjacent elements** and swaps them if they're in the wrong order, "bubbling" the largest element to the end. It may perform up to `O(n²)` swaps.
>
> **Key Difference:** Selection Sort minimizes the number of swaps; Bubble Sort minimizes the number of passes for nearly sorted data (with the `didSwap` optimization).

---

### Q2: Which sorting algorithm is stable and which is not? Why does it matter?

> - **Stable:** Bubble Sort ✅, Insertion Sort ✅
> - **Unstable:** Selection Sort ❌
>
> A **stable** sort preserves the relative order of elements with equal keys. This matters when sorting by multiple criteria (e.g., sort students first by name, then by grade — a stable sort on grade preserves the alphabetical order within the same grade).
>
> **Why is Selection Sort unstable?** Example: `[4a, 4b, 1]` → After sorting, Selection Sort may swap `4a` with `1`, producing `[1, 4b, 4a]` — the relative order of `4a` and `4b` is reversed.

---

### Q3: When would you prefer Insertion Sort over other O(n²) algorithms?

> Insertion Sort is preferred when:
> 1. The array is **nearly sorted** — it runs in O(n) for already sorted data.
> 2. The array is **small** (e.g., n ≤ 50) — the overhead is negligible.
> 3. You need a **stable** sort.
> 4. Data arrives **incrementally** (online algorithm) — you can insert each new element into its correct position without re-sorting.
>
> Fun Fact: Many optimized implementations of Quick Sort and Merge Sort switch to Insertion Sort for small subarrays (e.g., Java's `Arrays.sort()` uses Insertion Sort for arrays of size ≤ 47).

---

### Q4: Can you optimize Bubble Sort? What is the best-case time complexity after optimization?

> **Yes!** By adding a `didSwap` boolean flag:
> - If **no swaps** occur during an entire pass, the array is already sorted → **break early**.
> - **Best-case** with this optimization: **O(n)** — only one pass is needed for an already sorted array.
>
> ```cpp
> for (int i = n-1; i >= 1; i--) {
>     bool didSwap = false;
>     for (int j = 0; j < i; j++) {
>         if (arr[j] > arr[j+1]) {
>             swap(arr[j], arr[j+1]);
>             didSwap = true;
>         }
>     }
>     if (!didSwap) break;  // ← This is the optimization
> }
> ```

---

### Q5: Why is the outer loop in Selection Sort `i < n - 1` and not `i < n`?

> Because when only **one element** remains (the last one), it is **automatically in its correct position** — there's nothing left to compare or swap with. So we only need `n - 1` passes.

---

### Q6: What is the space complexity of these algorithms? Are they in-place?

> All three — Selection Sort, Bubble Sort, and Insertion Sort — have **O(1) space complexity**. They are **in-place** algorithms, meaning they sort the array without requiring extra memory proportional to the input size. Only a constant amount of extra space (for variables like `temp`, `mini`, loop counters) is used.

---

### Q7: Why does Insertion Sort loop start from index 1 and not index 0?

> A **single element** (at index 0) is trivially sorted. There's nothing to its left to compare with. So we start from index 1 and try to place it in the correct position relative to the sorted portion `[0..0]`.

---

### Q8: In Insertion Sort, why do we check `j > 0` and not `j >= 0`?

> Because we compare `arr[j]` with `arr[j - 1]`. If `j` were `0`, then `j - 1 = -1`, which is an **invalid array index** → causes a **runtime error** (array out of bounds). So `j > 0` ensures `j - 1` is always a valid index (≥ 0).

---

### Q9: If all three have O(n²) worst-case, why bother learning them?

> 1. **Interview staples** — frequently asked to write from scratch.
> 2. **Foundation** — understanding these builds intuition for Merge Sort & Quick Sort.
> 3. **Practical use** — Insertion Sort is used inside production sorting algorithms for small subarrays.
> 4. **Concept building** — teaches loop invariants, in-place operations, stability, and complexity analysis.
> 5. **Selection Sort** minimizes writes — useful for memory with limited write cycles (e.g., flash memory).

---

### Q10: Explain the concept of "Adaptive" sorting. Which of these algorithms are adaptive?

> An **adaptive** sorting algorithm performs better (fewer operations) when the input is already partially sorted.
>
> - **Bubble Sort** (with `didSwap` flag): ✅ Adaptive — exits early if no swaps needed.
> - **Insertion Sort**: ✅ Adaptive — fewer shifts when data is nearly sorted.
> - **Selection Sort**: ❌ Not adaptive — always scans the entire unsorted portion regardless.

---

### Q11: How do you swap two numbers without a temporary variable?

> Using **XOR**:
> ```cpp
> a = a ^ b;  // a now holds a XOR b
> b = a ^ b;  // b gets original a
> a = a ^ b;  // a gets original b
> ```
>
> Or using **arithmetic** (beware of overflow):
> ```cpp
> a = a + b;
> b = a - b;
> a = a - b;
> ```
>
> ⚠️ **In interviews**, using a temp variable is cleaner and less error-prone. These tricks are good to know but rarely preferred in production code.

---

### Q12: Can Bubble Sort be used to count inversions in an array?

> **Yes!** Every swap in Bubble Sort fixes exactly one inversion. So the **total number of swaps** = **total number of inversions**.
>
> However, this approach is O(n²). A more efficient method uses **Modified Merge Sort** to count inversions in O(n log n).

---

## 🧩 Quick Revision Mnemonics

| Algorithm      | Remember As                                   |
| -------------- | --------------------------------------------- |
| Selection Sort | 🔍 **Select** the minimum → place it at front  |
| Bubble Sort    | 🫧 **Bubble** the maximum → push it to the end |
| Insertion Sort | 🃏 **Insert** like sorting playing cards       |

---

> 💬 *"Sorting is the foundation. Master these three, and the advanced ones (Merge Sort, Quick Sort, Heap Sort) will feel like natural extensions."*

---

*📌 Source: Striver's A2Z DSA Course — Step 2.1: Sorting Techniques*

# 📦 Arrays — Striver's A2Z DSA Course (Step 3.1 - Easy)

> **Lecture Summary:** This lecture kicks off **Step 3 — Arrays**. It covers array basics (declaration, indexing, memory) and solves the first **4 easy problems** using the **Brute → Better → Optimal** interview approach.

---

## 📌 Table of Contents

1. [Array Basics](#1--array-basics)
2. [Interview Strategy: Brute → Better → Optimal](#2--interview-strategy-brute--better--optimal)
3. [Problem 1: Largest Element in Array](#3--problem-1-largest-element-in-array)
4. [Problem 2: Second Largest Element](#4--problem-2-second-largest-element)
5. [Problem 3: Check if Array is Sorted](#5--problem-3-check-if-array-is-sorted)
6. [Problem 4: Remove Duplicates from Sorted Array](#6--problem-4-remove-duplicates-from-sorted-array)
7. [Practice Questions](#7--practice-questions)
8. [Interview Questions & Answers](#8--interview-questions--answers)

---

## 1. 📘 Array Basics

### What is an Array?

> An array is a **data structure** that stores elements of the **same data type** in **contiguous memory locations**.

```
All integers:    [3, 1, 4, 1, 5]     ✅
All characters:  ['a', 'b', 'c']      ✅
Mixed types:     [3, "hello", 'a']    ❌  (Not allowed!)
```

### Declaration

```cpp
// C++ — inside main (garbage values by default)
int arr[6];

// C++ — globally (initialized to 0)
int arr[6];

// With values
int arr[] = {2, 3, 1, 4, 7, 6};
```

### Indexing

```
Array:  [2, 3, 1, 4, 7, 6]
Index:   0  1  2  3  4  5

First element:  arr[0]          → 2
Last element:   arr[n-1]        → 6   (where n = size = 6)
```

> 🔑 Array indexing is **0-based** — goes from `0` to `n-1`.

### Accessing Elements

```cpp
for (int i = 0; i < n; i++) {
    cout << arr[i] << " ";    // Prints each element
}
```

### Memory Layout

```
Address:   X     X+1    X+2    X+3    X+4    X+5
Value:    [2]    [3]    [1]    [4]    [7]    [6]
Index:     0      1      2      3      4      5

• X is a random memory address (unpredictable)
• Each subsequent element is stored at the NEXT contiguous address
• This is why array access by index is O(1) — direct address calculation!
```

### Maximum Array Sizes

| Declaration        | Max Size |
| ------------------ | :------: |
| Inside `main()`    | **10⁶**  |
| Globally (outside) | **10⁷**  |

> ⚠️ Declaring large arrays inside `main()` can cause **stack overflow**. Declare globally for larger arrays.

---

## 2. 🎯 Interview Strategy: Brute → Better → Optimal

> **Don't jump to the optimal solution directly — even if you know it!**

```
Interview Flow:

1. Ask clarifying questions & understand test cases
2. Give the BRUTE FORCE solution (most naive approach)
3. Optimize → BETTER solution (if exists)
4. Optimize further → OPTIMAL solution
5. Code the optimal & analyze complexity

This shows you can THINK, not just memorize solutions.
```

| Level       | Description                                    |
| ----------- | ---------------------------------------------- |
| **Brute**   | First thing that comes to mind (e.g., sort it) |
| **Better**  | Optimize one aspect (e.g., reduce passes)      |
| **Optimal** | Best possible time/space complexity            |

> 💡 For harder problems you may have: Brute → Better → More Better → Optimal. **You drive the interview.**

---

## 3. 🟢 Problem 1: Largest Element in Array

> **Problem:** Given an array, find and return the largest element.
>
> 🔗 **Problem Link:** [Largest Element in Array — GeeksforGeeks](https://www.geeksforgeeks.org/problems/largest-element-in-array4009/0)

```
Input:  [3, 2, 1, 5, 2]
Output: 5
```

### Approach 1: Brute Force — Sort

```
Sort the array → Last element is the largest → arr[n-1]

[3, 2, 1, 5, 2]  →  sort  →  [1, 2, 2, 3, 5]  →  arr[4] = 5
```

```cpp
// Brute Force
sort(arr.begin(), arr.end());
return arr[n - 1];
```

|    Time    | Space |
| :--------: | :---: |
| O(n log n) | O(1)  |

---

### Approach 2: Optimal — Single Pass

```
Keep a variable 'largest', traverse the array,
update whenever you find something bigger.
```

```cpp
int largest = arr[0];
for (int i = 1; i < n; i++) {
    if (arr[i] > largest) {
        largest = arr[i];
    }
}
return largest;
```

**Dry Run:**

```
arr = [3, 2, 1, 5, 2],  largest = 3

i=1: 2 > 3? No
i=2: 1 > 3? No
i=3: 5 > 3? Yes → largest = 5
i=4: 2 > 5? No

Return 5 ✅
```

|    Time    | Space |
| :--------: | :---: |
| **O(n)** ✅ | O(1)  |

---

## 4. 🟡 Problem 2: Second Largest Element

> **Problem:** Given an array of unique non-negative integers, find the second largest and second smallest elements.
>
> 🔗 **Problem Link:** [Second Largest — Coding Ninjas](https://www.naukri.com/code360/problems/ninja-and-the-second-order-elements_6581960)

```
Input:  [1, 2, 4, 7, 7, 5]
Output: Second Largest = 5, Second Smallest = 2
```

### Approach 1: Brute Force — Sort + Traverse Back

```
Sort → Largest is arr[n-1]
Go backwards from n-2, skip duplicates → first different = second largest
```

```cpp
sort(arr.begin(), arr.end());
int largest = arr[n - 1];
for (int i = n - 2; i >= 0; i--) {
    if (arr[i] != largest) {
        return arr[i];  // Second largest
    }
}
return -1;  // No second largest exists
```

|       Time        | Space |
| :---------------: | :---: |
| O(n log n) + O(n) | O(1)  |

---

### Approach 2: Better — Two Passes

```
Pass 1: Find the largest element
Pass 2: Find the largest element that is NOT equal to the largest
```

```cpp
// Pass 1: Find largest
int largest = arr[0];
for (int i = 1; i < n; i++) {
    if (arr[i] > largest) largest = arr[i];
}

// Pass 2: Find second largest
int secondLargest = -1;
for (int i = 0; i < n; i++) {
    if (arr[i] != largest && arr[i] > secondLargest) {
        secondLargest = arr[i];
    }
}
return secondLargest;
```

|        Time        | Space |
| :----------------: | :---: |
| O(2n) — two passes | O(1)  |

---

### Approach 3: Optimal — Single Pass 🏆

> **Key Insight:** When a new `largest` is found, the **old largest becomes second largest**. Also check if an element is greater than `secondLargest` but less than `largest`.

```cpp
int secondLargest(vector<int>& arr, int n) {
    int largest = arr[0];
    int sLargest = -1;

    for (int i = 1; i < n; i++) {
        if (arr[i] > largest) {
            sLargest = largest;   // Old largest → second largest
            largest = arr[i];     // New largest
        }
        else if (arr[i] < largest && arr[i] > sLargest) {
            sLargest = arr[i];   // Better second largest found
        }
    }
    return sLargest;
}
```

**Dry Run:**

```
arr = [1, 2, 4, 7, 7, 5],  largest = 1,  sLargest = -1

i=1:  2 > 1? Yes  → sLargest = 1, largest = 2
i=2:  4 > 2? Yes  → sLargest = 2, largest = 4
i=3:  7 > 4? Yes  → sLargest = 4, largest = 7
i=4:  7 > 7? No.  7 < 7? No.  (Equal → skip! ⚠️)
i=5:  5 > 7? No.  5 < 7 && 5 > 4? Yes → sLargest = 5

Return 5 ✅
```

> ⚠️ **Crucial:** When elements are **equal** to largest, do NOT update anything. The `else if` condition `arr[i] < largest` handles this automatically.

**For Second Smallest** — same logic but reversed:

```cpp
int secondSmallest(vector<int>& arr, int n) {
    int smallest = arr[0];
    int sSmallest = INT_MAX;

    for (int i = 1; i < n; i++) {
        if (arr[i] < smallest) {
            sSmallest = smallest;
            smallest = arr[i];
        }
        else if (arr[i] > smallest && arr[i] < sSmallest) {
            sSmallest = arr[i];
        }
    }
    return sSmallest;
}
```

|           Time           | Space |
| :----------------------: | :---: |
| **O(n)** — single pass ✅ | O(1)  |

---

## 5. 🟢 Problem 3: Check if Array is Sorted

> **Problem:** Given an array, check if it is sorted in **non-descending** (ascending) order.
>
> 🔗 **Problem Link:** [Check if Array is Sorted — LeetCode 1752](https://leetcode.com/problems/check-if-array-is-sorted-and-rotated/)

```
Input:  [1, 2, 2, 3, 3, 4]  →  true ✅  (non-descending)
Input:  [1, 2, 1, 3, 4]      →  false ❌  (2 > 1 breaks order)
```

### Optimal — Single Pass

> Compare each element with its **previous** element. If any element is strictly less than its predecessor → NOT sorted.

```cpp
bool isSorted(vector<int>& arr, int n) {
    for (int i = 1; i < n; i++) {
        if (arr[i] < arr[i - 1]) {
            return false;   // Found a violation!
        }
    }
    return true;  // No violations → sorted
}
```

**Dry Run:**

```
arr = [1, 2, 2, 3, 3, 4]

i=1: 2 < 1? No ✅
i=2: 2 < 2? No ✅
i=3: 3 < 2? No ✅
i=4: 3 < 3? No ✅
i=5: 4 < 3? No ✅

Return true ✅

arr = [1, 2, 1, 3, 4]

i=1: 2 < 1? No ✅
i=2: 1 < 2? YES → Return false ❌
```

|   Time   | Space |
| :------: | :---: |
| **O(n)** | O(1)  |

> 💡 **Non-descending** means `arr[i] >= arr[i-1]` is allowed (equal elements are fine). Strictly ascending would disallow equals.

---

## 6. 🟡 Problem 4: Remove Duplicates from Sorted Array

> **Problem:** Given a **sorted** array, remove duplicates **in-place** and return the count of unique elements. The first `k` positions should contain the unique elements.
>
> 🔗 **Problem Link:** [Remove Duplicates from Sorted Array — LeetCode 26](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

```
Input:  [1, 1, 2, 2, 2, 3, 3]
Output: 3  (first 3 positions → [1, 2, 3, _, _, _, _])
```

### Approach 1: Brute Force — Using Set

```
Insert all elements into a Set (automatically removes duplicates).
Copy Set elements back to the array.
```

```cpp
int removeDuplicates(vector<int>& arr) {
    set<int> st;
    for (int i = 0; i < arr.size(); i++) {
        st.insert(arr[i]);    // Set ignores duplicates
    }

    int index = 0;
    for (auto it : st) {
        arr[index] = it;      // Copy back
        index++;
    }
    return index;   // Number of unique elements
}
```

|            Time            |        Space         |
| :------------------------: | :------------------: |
| O(n log n) — set insertion | O(n) — for the set ❌ |

---

### Approach 2: Optimal — Two Pointer 🏆

> **Key Insight:** Since the array is **sorted**, all duplicates are **adjacent**. Use two pointers:
> - `i` — points to the last unique element found
> - `j` — scans forward looking for the next different element

```
arr = [1, 1, 2, 2, 2, 3, 3]
       i  j

j=1: arr[1]=1 == arr[i]=1? Same → skip
j=2: arr[2]=2 != arr[i]=1? DIFFERENT → i++, arr[i]=2
       → arr = [1, 2, 2, 2, 2, 3, 3]
                   i     j
j=3: arr[3]=2 == arr[i]=2? Same → skip
j=4: arr[4]=2 == arr[i]=2? Same → skip
j=5: arr[5]=3 != arr[i]=2? DIFFERENT → i++, arr[i]=3
       → arr = [1, 2, 3, 2, 2, 3, 3]
                      i           j
j=6: arr[6]=3 == arr[i]=3? Same → skip

Loop ends. i = 2. Return i+1 = 3 ✅
First 3 positions: [1, 2, 3]
```

```cpp
int removeDuplicates(vector<int>& arr) {
    int i = 0;  // Points to last unique element
    for (int j = 1; j < arr.size(); j++) {
        if (arr[j] != arr[i]) {
            i++;
            arr[i] = arr[j];   // Place next unique element
        }
    }
    return i + 1;  // Count of unique elements
}
```

> 🔑 **Why does this work?** The array is sorted, so duplicates are always grouped together. Once `j` finds a new value, it must be the next unique element.

|           Time           |         Space         |
| :----------------------: | :-------------------: |
| **O(n)** — single pass ✅ | **O(1)** — in-place ✅ |

---

## 7. 📝 Practice Questions

### Easy

1. 🔗 [Largest Element in Array — GFG](https://www.geeksforgeeks.org/problems/largest-element-in-array4009/0) *(Covered in lecture)*
2. 🔗 [Second Largest — Coding Ninjas](https://www.naukri.com/code360/problems/ninja-and-the-second-order-elements_6581960) *(Covered in lecture)*
3. 🔗 [Check if Sorted and Rotated — LeetCode 1752](https://leetcode.com/problems/check-if-array-is-sorted-and-rotated/) *(Covered in lecture)*
4. 🔗 [Remove Duplicates from Sorted Array — LeetCode 26](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) *(Covered in lecture)*
5. 🔗 [Remove Element — LeetCode 27](https://leetcode.com/problems/remove-element/)
6. 🔗 [Search Insert Position — LeetCode 35](https://leetcode.com/problems/search-insert-position/)
7. 🔗 [Plus One — LeetCode 66](https://leetcode.com/problems/plus-one/)

### Medium

8. 🔗 [Remove Duplicates from Sorted Array II — LeetCode 80](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/) *(Allow at most 2 duplicates)*
9. 🔗 [Third Maximum Number — LeetCode 414](https://leetcode.com/problems/third-maximum-number/)
10. 🔗 [Contains Duplicate — LeetCode 217](https://leetcode.com/problems/contains-duplicate/)
11. 🔗 [Merge Sorted Array — LeetCode 88](https://leetcode.com/problems/merge-sorted-array/)
12. Find the **kth largest** element without sorting. *(Think QuickSelect)*

### Challenging

13. 🔗 [Kth Largest Element — LeetCode 215](https://leetcode.com/problems/kth-largest-element-in-an-array/)
14. 🔗 [Median of Two Sorted Arrays — LeetCode 4](https://leetcode.com/problems/median-of-two-sorted-arrays/)

---

## 8. 🎯 Interview Questions & Answers

### Q1: What is an array? What are its advantages and disadvantages?

> **Array** = A data structure storing elements of the **same type** in **contiguous memory** locations.
>
> **Advantages:**
> - **O(1) random access** — access any element by index instantly
> - **Cache friendly** — contiguous memory improves CPU cache performance
> - **Simple** — easy to declare and use
>
> **Disadvantages:**
> - **Fixed size** (in most languages) — can't grow/shrink dynamically
> - **Insertion/Deletion is O(n)** — need to shift elements
> - **Wasted space** — if you allocate more than needed

---

### Q2: What is the difference between an Array and a Vector (or ArrayList)?

> | Feature        | Array              | Vector / ArrayList  |
> |----------------|:------------------:|:-------------------:|
> | **Size**       | Fixed at creation   | Dynamic (resizable) |
> | **Memory**     | Stack or Global     | Heap (dynamic)      |
> | **Resize**     | Not possible        | Automatic (doubles)  |
> | **Functions**  | None built-in       | push_back, size, etc.|
> | **Performance**| Slightly faster     | Slight overhead      |
>
> In competitive programming and interviews, **vectors** are preferred due to flexibility.

---

### Q3: How would you find the largest element in an array? What's the time complexity?

> Traverse the array once, keeping track of the maximum value seen so far.
>
> ```cpp
> int largest = arr[0];
> for (int i = 1; i < n; i++) {
>     if (arr[i] > largest) largest = arr[i];
> }
> ```
>
> **Time: O(n)** — you must look at every element at least once.
> **Space: O(1)** — only one extra variable.
>
> This is **provably optimal** — you can't find the max without examining all elements.

---

### Q4: How do you find the second largest in a single pass?

> Track **both** `largest` and `secondLargest` simultaneously:
>
> - When you find a new largest → old largest becomes second largest
> - When you find something between largest and second largest → update second largest
> - When equal to largest → **skip** (this is the trap!)
>
> ```cpp
> if (arr[i] > largest) {
>     secondLargest = largest;
>     largest = arr[i];
> } else if (arr[i] < largest && arr[i] > secondLargest) {
>     secondLargest = arr[i];
> }
> ```
>
> **Time: O(n)** in a single pass.

---

### Q5: What does "in-place" modification mean?

> **In-place** means modifying the **original** data structure without creating a new copy.
>
> - **O(1) extra space** — no additional arrays/sets
> - Modify the input array directly
> - Common in problems like "Remove Duplicates," "Move Zeroes," etc.
>
> Example: In "Remove Duplicates," instead of creating a new array with unique elements, you rearrange the existing array.

---

### Q6: Explain the Two-Pointer technique with an example.

> **Two-pointer** uses two indices that move through the array based on conditions. It's powerful for **sorted arrays** and **in-place** modifications.
>
> **Example — Remove Duplicates:**
> ```
> Pointer i = last unique element
> Pointer j = scanning ahead for next unique
>
> [1, 1, 2, 2, 3]
>  i  j              → arr[j]=1 == arr[i]=1 → skip
>  i     j           → arr[j]=2 != arr[i]=1 → i++, arr[i]=2
>     i     j        → arr[j]=2 == arr[i]=2 → skip
>     i        j     → arr[j]=3 != arr[i]=2 → i++, arr[i]=3
>
> Result: [1, 2, 3, _, _], return 3
> ```
>
> **When to use:** Sorted arrays, pair finding, partitioning, sliding window variants.

---

### Q7: Why is Set-based approach O(n log n) while Two-Pointer is O(n)?

> **Set insertion** takes **O(log n)** per element (balanced BST internally).
> For `n` elements: **O(n log n)** + uses **O(n) extra space**.
>
> **Two-Pointer** does a single pass with simple comparisons:
> **O(n)** time + **O(1)** space.
>
> The two-pointer approach exploits the fact that the array is **already sorted**, so duplicates are adjacent and easy to detect.

---

### Q8: What's the difference between "sorted" and "non-descending"?

> | Term             | Meaning                              | Example           |
> |------------------|--------------------------------------|--------------------|
> | **Strictly ascending** | Each element > previous      | [1, 2, 3, 4]     |
> | **Non-descending**    | Each element ≥ previous       | [1, 2, **2**, 3, **3**] |
> | **Strictly descending** | Each element < previous     | [4, 3, 2, 1]     |
> | **Non-ascending**     | Each element ≤ previous       | [4, 3, **3**, 2, **2**] |
>
> Most sorting problems use **non-descending** (allows duplicates).

---

### Q9: What is the maximum array size you can declare? Why does location matter?

> | Where Declared     | Max Size | Memory Used |
> |--------------------|:--------:|:-----------:|
> | Inside `main()`    | ~10⁶     | Stack       |
> | Globally           | ~10⁷     | Data Segment|
>
> **Why?** The **stack** has limited memory (~1-8 MB typically). Large arrays inside `main()` can cause **stack overflow**. Global/static arrays use the **data segment**, which is larger.
>
> For even larger data → use **dynamic allocation** (`new`, `malloc`, or `vector`).

---

### Q10: If an array is stored in contiguous memory, why is access O(1)?

> The address of any element can be **computed directly**:
>
> ```
> address(arr[i]) = base_address + i × sizeof(element)
> ```
>
> **No traversal needed** — just arithmetic! This is why array access is **O(1)** regardless of size.
>
> Contrast with **linked lists**: to access the 5th element, you must traverse 4 nodes → **O(n)**.

---

## 🧩 Quick Revision Summary

```
┌───────────────────────────────────────────────────────────────┐
│                  ARRAYS EASY PROBLEMS                          │
│                                                                │
│  P1: Largest Element                                           │
│      → Single pass, track max                    O(n)          │
│                                                                │
│  P2: Second Largest                                            │
│      → Brute: Sort, go back             O(n log n)             │
│      → Better: Two passes                O(2n)                 │
│      → Optimal: Single pass, track both  O(n) ✅               │
│                                                                │
│  P3: Check if Sorted                                           │
│      → Compare each with previous        O(n)                  │
│                                                                │
│  P4: Remove Duplicates (Sorted Array)                          │
│      → Brute: Use Set               O(n log n), O(n) space     │
│      → Optimal: Two Pointer          O(n), O(1) space ✅       │
│                                                                │
│  KEY TECHNIQUE: Two-Pointer approach for sorted arrays!        │
└───────────────────────────────────────────────────────────────┘
```

> 💬 *"Always drive the interview — Brute → Better → Optimal. Even for easy problems, follow this pattern so it becomes second nature."*

---

*📌 Source: Striver's A2Z DSA Course — Step 3.1: Arrays (Easy Problems)*

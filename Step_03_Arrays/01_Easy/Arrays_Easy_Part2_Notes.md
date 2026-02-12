# 📦 Arrays Easy — Part 2 | Striver's A2Z DSA Course (Step 3.1)

> **Lecture Summary:** Continuation of Step 3.1 covering **5 more easy array problems** — rotations, moving zeros, linear search, and union/intersection of sorted arrays. Each problem follows the **Brute → Better → Optimal** interview approach.

---

## 📌 Table of Contents

1. [Problem 5: Left Rotate Array by One Place](#1--problem-5-left-rotate-array-by-one-place)
2. [Problem 6: Left Rotate Array by D Places](#2--problem-6-left-rotate-array-by-d-places)
3. [Problem 7: Move Zeros to End](#3--problem-7-move-zeros-to-end)
4. [Problem 8: Linear Search](#4--problem-8-linear-search)
5. [Problem 9: Union of Two Sorted Arrays](#5--problem-9-union-of-two-sorted-arrays)
6. [Problem 10: Intersection of Two Sorted Arrays](#6--problem-10-intersection-of-two-sorted-arrays)
7. [Practice Questions](#7--practice-questions)
8. [Interview Questions & Answers](#8--interview-questions--answers)

---

## 1. 🟢 Problem 5: Left Rotate Array by One Place

> **Problem:** Left rotate a given array by one position.
>
> 🔗 **Problem Link:** [Left Rotate Array by One — Coding Ninjas](https://www.naukri.com/code360/problems/left-rotate-an-array-by-one_5026278)

```
Input:  [1, 2, 3, 4, 5]
Output: [2, 3, 4, 5, 1]

What happened:
  • 1 (first) moved to the END
  • Everyone else shifted one place LEFT
```

### Optimal — Store First, Shift Left, Place at End

```
Step 1: Store arr[0] in temp         → temp = 1
Step 2: Shift every element left     → arr[i-1] = arr[i]
Step 3: Place temp at last index     → arr[n-1] = temp
```

```cpp
void leftRotateByOne(int arr[], int n) {
    int temp = arr[0];          // Step 1: Save first element
    for (int i = 1; i < n; i++) {
        arr[i - 1] = arr[i];   // Step 2: Shift left
    }
    arr[n - 1] = temp;         // Step 3: Place at end
}
```

**Dry Run:**

```
arr = [1, 2, 3, 4, 5],  temp = 1

i=1: arr[0] = arr[1] = 2  → [2, 2, 3, 4, 5]
i=2: arr[1] = arr[2] = 3  → [2, 3, 3, 4, 5]
i=3: arr[2] = arr[3] = 4  → [2, 3, 4, 4, 5]
i=4: arr[3] = arr[4] = 5  → [2, 3, 4, 5, 5]

arr[4] = temp = 1         → [2, 3, 4, 5, 1] ✅
```

|   Time   | Space |
| :------: | :---: |
| **O(n)** | O(1)  |

---

## 2. 🟡 Problem 6: Left Rotate Array by D Places

> **Problem:** Left rotate a given array by D positions.
>
> 🔗 **Problem Link:** [Rotate Array — LeetCode 189](https://leetcode.com/problems/rotate-array/)

```
Input:  arr = [1, 2, 3, 4, 5, 6, 7],  D = 3
Output: [4, 5, 6, 7, 1, 2, 3]

The first 3 elements moved to the end.
```

### ⚠️ First Thing: Handle D ≥ N

```
If D = 7 (size of array) → rotating by 7 = back to original!
If D = 8 → same as rotating by 1 (8 % 7 = 1)
If D = 15 → same as rotating by 1 (15 % 7 = 1)

ALWAYS DO: D = D % N  (before anything else!)
```

---

### Approach 1: Brute Force — Temp Array

```
Step 1: Store first D elements in a temp array
Step 2: Shift remaining (n-D) elements to the front
Step 3: Place temp elements at the back
```

```cpp
void leftRotate(int arr[], int n, int d) {
    d = d % n;   // Handle D >= N
    if (d == 0) return;

    // Step 1: Store first D elements
    int temp[d];
    for (int i = 0; i < d; i++) {
        temp[i] = arr[i];
    }

    // Step 2: Shift remaining elements left
    for (int i = d; i < n; i++) {
        arr[i - d] = arr[i];
    }

    // Step 3: Place temp at the back
    for (int i = n - d; i < n; i++) {
        arr[i] = temp[i - (n - d)];
    }
}
```

**Dry Run (D=3):**

```
arr = [1, 2, 3, 4, 5, 6, 7]

Step 1: temp = [1, 2, 3]

Step 2: Shift from index 3
  arr[0]=arr[3]=4, arr[1]=arr[4]=5, arr[2]=arr[5]=6, arr[3]=arr[6]=7
  arr = [4, 5, 6, 7, _, _, _]

Step 3: Place temp from index 4
  arr[4]=1, arr[5]=2, arr[6]=3
  arr = [4, 5, 6, 7, 1, 2, 3] ✅
```

|   Time   |         Space          |
| :------: | :--------------------: |
| O(n + d) | **O(d)** ❌ extra space |

---

### Approach 2: Optimal — Three Reversals 🏆

> **Key Insight:** Three reversals can achieve the rotation with **O(1) extra space**!

```
Array: [1, 2, 3, 4, 5, 6, 7],  D = 3

Step 1: Reverse first D elements      [3, 2, 1, 4, 5, 6, 7]
Step 2: Reverse remaining (N-D)       [3, 2, 1, 7, 6, 5, 4]
Step 3: Reverse entire array           [4, 5, 6, 7, 1, 2, 3] ✅
```

**Why does this work?**

```
Original:   [1, 2, 3 | 4, 5, 6, 7]
              D part    N-D part

After reversing each part separately:
            [3, 2, 1 | 7, 6, 5, 4]

After reversing the whole thing:
            [4, 5, 6, 7, 1, 2, 3]  ← Exactly left rotated by D!
```

```cpp
void leftRotate(vector<int>& arr, int d) {
    int n = arr.size();
    d = d % n;
    if (d == 0) return;

    reverse(arr.begin(), arr.begin() + d);       // Reverse [0, d-1]
    reverse(arr.begin() + d, arr.end());          // Reverse [d, n-1]
    reverse(arr.begin(), arr.end());              // Reverse entire array
}
```

> 📝 **Assignment:** Modify this for **right rotation** by D places!
>
> **Hint:** For right rotation, reverse order becomes: reverse last D → reverse first N-D → reverse all.

|       Time       |           Space           |
| :--------------: | :-----------------------: |
| **O(2n)** ≈ O(n) | **O(1)** ✅ No extra array |

---

## 3. 🟡 Problem 7: Move Zeros to End

> **Problem:** Move all zeros in the array to the end, while maintaining the relative order of non-zero elements. Do it **in-place**.
>
> 🔗 **Problem Link:** [Move Zeroes — LeetCode 283](https://leetcode.com/problems/move-zeroes/)

```
Input:  [1, 0, 2, 3, 2, 0, 0, 4, 5, 1]
Output: [1, 2, 3, 2, 4, 5, 1, 0, 0, 0]
```

### Approach 1: Brute Force — Temp Array

```
Step 1: Collect all non-zero elements into a temp array
Step 2: Place them back at the front of the original array
Step 3: Fill remaining positions with zeros
```

```cpp
void moveZeros(vector<int>& arr, int n) {
    vector<int> temp;

    // Step 1: Collect non-zeros
    for (int i = 0; i < n; i++) {
        if (arr[i] != 0) {
            temp.push_back(arr[i]);
        }
    }

    // Step 2: Place non-zeros at front
    for (int i = 0; i < temp.size(); i++) {
        arr[i] = temp[i];
    }

    // Step 3: Fill rest with zeros
    for (int i = temp.size(); i < n; i++) {
        arr[i] = 0;
    }
}
```

| Time  |          Space          |
| :---: | :---------------------: |
| O(2n) | **O(n)** ❌ (temp array) |

---

### Approach 2: Optimal — Two Pointer (Swap) 🏆

> **Key Insight:** Keep `j` pointing at the first zero. When `i` finds a non-zero, swap it with `j`'s zero. Both advance.

```
Step 1: Find the first zero (j points to it)
Step 2: From j+1 onwards, whenever you find a non-zero → swap with arr[j], advance j
```

**Dry Run:**

```
arr = [1, 0, 2, 3, 0, 0, 4, 5]
            j

j found at index 1 (first zero). Start i from j+1 = 2.

i=2: arr[2]=2 ≠ 0 → swap(arr[2], arr[1]) → [1, 2, 0, 3, 0, 0, 4, 5], j=2
i=3: arr[3]=3 ≠ 0 → swap(arr[3], arr[2]) → [1, 2, 3, 0, 0, 0, 4, 5], j=3
i=4: arr[4]=0       → skip
i=5: arr[5]=0       → skip
i=6: arr[6]=4 ≠ 0 → swap(arr[6], arr[3]) → [1, 2, 3, 4, 0, 0, 0, 5], j=4
i=7: arr[7]=5 ≠ 0 → swap(arr[7], arr[4]) → [1, 2, 3, 4, 5, 0, 0, 0], j=5

Done! ✅ All zeros at the end, order preserved.
```

```cpp
void moveZeroes(vector<int>& nums) {
    int j = -1;
    int n = nums.size();

    // Step 1: Find first zero
    for (int i = 0; i < n; i++) {
        if (nums[i] == 0) {
            j = i;
            break;
        }
    }

    if (j == -1) return;  // No zeros exist

    // Step 2: Swap non-zeros forward
    for (int i = j + 1; i < n; i++) {
        if (nums[i] != 0) {
            swap(nums[i], nums[j]);
            j++;
        }
    }
}
```

|           Time           |         Space         |
| :----------------------: | :-------------------: |
| **O(n)** — single pass ✅ | **O(1)** — in-place ✅ |

> 💡 `j` always points to the leftmost zero. Every time we swap a non-zero into `j`'s position, the next position (`j+1`) becomes the new leftmost zero.

---

## 4. 🟢 Problem 8: Linear Search

> **Problem:** Given an array and a target number, return the **index** of its first occurrence. Return **-1** if not found.
>
> 🔗 **Problem Link:** [Linear Search — GeeksforGeeks](https://www.geeksforgeeks.org/problems/who-will-win-1587115621/1)

```
Input:  arr = [6, 7, 8, 4, 1],  target = 4
Output: 3  (4 is at index 3)

Input:  arr = [6, 7, 8, 4, 1],  target = 10
Output: -1  (10 not found)
```

### Optimal — Simple Iteration

```cpp
int linearSearch(vector<int>& arr, int target) {
    for (int i = 0; i < arr.size(); i++) {
        if (arr[i] == target) {
            return i;    // First occurrence found
        }
    }
    return -1;  // Not found
}
```

|   Time   | Space |
| :------: | :---: |
| **O(n)** | O(1)  |

> **Variants the interviewer may ask:**
> - **Last occurrence** → traverse from the end
> - **All occurrences** → store all matching indices in a list
> - **Count of occurrences** → keep a counter

---

## 5. 🟡 Problem 9: Union of Two Sorted Arrays

> **Problem:** Given two **sorted** arrays (may have duplicates), return their **union** in sorted order with no duplicates.
>
> 🔗 **Problem Link:** [Union of Two Sorted Arrays — GeeksforGeeks](https://www.geeksforgeeks.org/problems/union-of-two-sorted-arrays-1587115621/1)

```
Input:  A = [1, 1, 2, 3, 4, 5],  B = [2, 3, 4, 4, 5, 6]
Output: [1, 2, 3, 4, 5, 6]

Union = All unique elements from BOTH arrays, in sorted order.
```

### Approach 1: Brute Force — Using Set

```
Insert all elements from both arrays into a Set.
Set automatically handles duplicates and maintains sorted order.
Copy Set to result array.
```

```cpp
vector<int> unionArray(vector<int>& a, vector<int>& b) {
    set<int> st;

    // Insert all from A
    for (int i = 0; i < a.size(); i++) st.insert(a[i]);

    // Insert all from B
    for (int i = 0; i < b.size(); i++) st.insert(b[i]);

    // Copy to result
    vector<int> result;
    for (auto it : st) result.push_back(it);

    return result;
}
```

|          Time          |         Space          |
| :--------------------: | :--------------------: |
| O(n₁ log n + n₂ log n) | O(n₁ + n₂) for the set |

> ⚠️ Use `set` (not `unordered_set`) — we need **sorted** order!

---

### Approach 2: Optimal — Two Pointer Merge 🏆

> **Key Insight:** Both arrays are already sorted! Use two pointers (like merge step of Merge Sort), but skip duplicates.

**Algorithm:**

```
1. Compare arr1[i] and arr2[j]
2. Take the SMALLER one (if not duplicate of last inserted)
3. Move the pointer of the array from which you took
4. If equal → take either, move both
5. When one array exhausts → process remaining of the other
```

**Dry Run:**

```
A = [1, 1, 2, 3, 4, 5]     B = [2, 3, 4, 4, 5, 6]
     i                           j                      union = []

A[0]=1 < B[0]=2 → take 1, i++                          union = [1]
A[1]=1 < B[0]=2 → 1 == back? skip, i++                 union = [1]
A[2]=2 == B[0]=2 → take 2, i++, j++                    union = [1,2]
A[3]=3 == B[1]=3 → take 3, i++, j++                    union = [1,2,3]
A[4]=4 == B[2]=4 → take 4, i++, j++                    union = [1,2,3,4]
                    B[3]=4 → 4 == back? skip (handled by comparison)
A[5]=5 == B[4]=5 → take 5, i++, j++                    union = [1,2,3,4,5]
i exhausted! Process remaining B:
B[5]=6 → 6 != back → take 6                            union = [1,2,3,4,5,6] ✅
```

```cpp
vector<int> unionSorted(vector<int>& a, vector<int>& b) {
    int n1 = a.size(), n2 = b.size();
    int i = 0, j = 0;
    vector<int> unionArr;

    while (i < n1 && j < n2) {
        if (a[i] <= b[j]) {
            // Take from A if not duplicate
            if (unionArr.empty() || unionArr.back() != a[i]) {
                unionArr.push_back(a[i]);
            }
            i++;
        } else {
            // Take from B if not duplicate
            if (unionArr.empty() || unionArr.back() != b[j]) {
                unionArr.push_back(b[j]);
            }
            j++;
        }
    }

    // Process remaining of A
    while (i < n1) {
        if (unionArr.back() != a[i]) {
            unionArr.push_back(a[i]);
        }
        i++;
    }

    // Process remaining of B
    while (j < n2) {
        if (unionArr.back() != b[j]) {
            unionArr.push_back(b[j]);
        }
        j++;
    }

    return unionArr;
}
```

|       Time       |                Space                 |
| :--------------: | :----------------------------------: |
| **O(n₁ + n₂)** ✅ | O(n₁ + n₂) for returning answer only |

---

## 6. 🟡 Problem 10: Intersection of Two Sorted Arrays

> **Problem:** Given two **sorted** arrays, return their **intersection** — elements present in **both** arrays (with matching frequency).
>
> 🔗 **Problem Link:** [Intersection of Two Arrays II — LeetCode 350](https://leetcode.com/problems/intersection-of-two-arrays-ii/)

```
Input:  A = [1, 2, 2, 3, 3, 4, 5, 6],  B = [2, 3, 3, 5, 6, 6, 7]
Output: [2, 3, 3, 5, 6]

Intersection = Elements that have a MATCHING PAIR in both arrays.
```

### Approach 1: Brute Force — Visited Array

```
For each element in A:
  Search for a matching, UN-VISITED element in B
  If found → add to answer, mark that B element as visited
```

```cpp
vector<int> intersection(vector<int>& a, vector<int>& b) {
    int n1 = a.size(), n2 = b.size();
    vector<int> visited(n2, 0);  // Track used elements in B
    vector<int> ans;

    for (int i = 0; i < n1; i++) {
        for (int j = 0; j < n2; j++) {
            if (a[i] == b[j] && visited[j] == 0) {
                ans.push_back(a[i]);
                visited[j] = 1;   // Mark as used
                break;            // Move to next element in A
            }
            // Optimization: if b[j] > a[i], no point going further (sorted!)
            if (b[j] > a[i]) break;
        }
    }
    return ans;
}
```

|         Time          |          Space          |
| :-------------------: | :---------------------: |
| O(n₁ × n₂) worst case | O(n₂) for visited array |

---

### Approach 2: Optimal — Two Pointer 🏆

> **Key Insight:** Since both are sorted, if elements match → it's an intersection. If not, advance the pointer with the **smaller** value.

**Algorithm:**

```
If A[i] < B[j]  → A[i] can't have a partner, advance i
If A[i] > B[j]  → B[j] can't have a partner, advance j
If A[i] == B[j] → MATCH! Take it, advance BOTH (they're committed)
```

**Dry Run:**

```
A = [1, 2, 2, 3, 3, 4, 5, 6]     B = [2, 3, 3, 5, 6, 6, 7]
     i                                  j

A[0]=1 < B[0]=2 → i++                    (1 has no partner)
A[1]=2 == B[0]=2 → take 2, i++, j++      ans = [2]
A[2]=2 < B[1]=3 → i++                    (this 2 has no partner left)
A[3]=3 == B[1]=3 → take 3, i++, j++      ans = [2, 3]
A[4]=3 == B[2]=3 → take 3, i++, j++      ans = [2, 3, 3]
A[5]=4 < B[3]=5 → i++                    (4 has no partner)
A[6]=5 == B[3]=5 → take 5, i++, j++      ans = [2, 3, 3, 5]
A[7]=6 == B[4]=6 → take 6, i++, j++      ans = [2, 3, 3, 5, 6]
i exhausted → STOP

Answer: [2, 3, 3, 5, 6] ✅
```

```cpp
vector<int> intersectionSorted(vector<int>& a, vector<int>& b) {
    int i = 0, j = 0;
    int n1 = a.size(), n2 = b.size();
    vector<int> ans;

    while (i < n1 && j < n2) {
        if (a[i] < b[j]) {
            i++;              // A[i] too small, no partner
        } else if (a[i] > b[j]) {
            j++;              // B[j] too small, no partner
        } else {
            ans.push_back(a[i]);  // Match found!
            i++;
            j++;              // Both committed, move on
        }
    }

    return ans;
}
```

|       Time       |                   Space                   |
| :--------------: | :---------------------------------------: |
| **O(n₁ + n₂)** ✅ | O(1) extra ✅ (answer space doesn't count) |

---

## 7. 📝 Practice Questions

### Easy

1. 🔗 [Rotate Array — LeetCode 189](https://leetcode.com/problems/rotate-array/) *(Right rotation by K — the assignment!)*
2. 🔗 [Move Zeroes — LeetCode 283](https://leetcode.com/problems/move-zeroes/) *(Covered in lecture)*
3. 🔗 [Remove Duplicates from Sorted Array — LeetCode 26](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
4. 🔗 [Remove Element — LeetCode 27](https://leetcode.com/problems/remove-element/) *(Similar to move zeros)*
5. 🔗 [Intersection of Two Arrays — LeetCode 349](https://leetcode.com/problems/intersection-of-two-arrays/) *(Unique intersection)*
6. 🔗 [Intersection of Two Arrays II — LeetCode 350](https://leetcode.com/problems/intersection-of-two-arrays-ii/) *(Covered in lecture)*

### Medium

7. 🔗 [Merge Sorted Array — LeetCode 88](https://leetcode.com/problems/merge-sorted-array/) *(Similar merge logic)*
8. 🔗 [Sort Colors — LeetCode 75](https://leetcode.com/problems/sort-colors/) *(Move 0s, 1s, 2s)*
9. 🔗 [Find All Numbers Disappeared — LeetCode 448](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/)
10. 🔗 [Rotate Image — LeetCode 48](https://leetcode.com/problems/rotate-image/) *(2D rotation)*

### Challenging

11. 🔗 [Median of Two Sorted Arrays — LeetCode 4](https://leetcode.com/problems/median-of-two-sorted-arrays/) *(Two sorted arrays advanced)*
12. Implement **right rotation** by D places using the three-reversal technique. *(Lecture assignment!)*

---

## 8. 🎯 Interview Questions & Answers

### Q1: Explain the Three-Reversal trick for array rotation.

> **Left rotate by D:**
> 1. Reverse first D elements: `[0, D-1]`
> 2. Reverse remaining elements: `[D, N-1]`
> 3. Reverse entire array: `[0, N-1]`
>
> **Right rotate by D:**
> 1. Reverse last D elements: `[N-D, N-1]`
> 2. Reverse first N-D elements: `[0, N-D-1]`
> 3. Reverse entire array: `[0, N-1]`
>
> **Why it works:** Reversing each half puts elements in the right relative order but flipped. The final full reversal flips them into the correct positions.
>
> **Time: O(n), Space: O(1)** — no extra array needed!

---

### Q2: Why do we do `D = D % N` before rotating?

> Rotating an array of size N by N positions brings it back to the **original** — it's a full cycle. So:
> - D = N → same array (0 effective rotations)
> - D = N+1 → same as 1 rotation
> - D = 2N+3 → same as 3 rotations
>
> `D % N` gives the **effective** number of rotations needed, avoiding unnecessary work.

---

### Q3: In "Move Zeros," why does the two-pointer approach maintain relative order?

> `j` always points to the **leftmost zero**. When a non-zero at `i` swaps with zero at `j`:
> - The non-zero moves forward (earlier in array) → its relative order vs other non-zeros is preserved because `i` processes elements **left to right**
> - The zero moves backward → zeros naturally accumulate at the end
>
> No non-zero element ever "jumps over" another non-zero → **order is preserved**.

---

### Q4: What is the difference between Union and Intersection?

> | Operation         | Definition                         | Duplicates          |
> |-------------------|-------------------------------------|---------------------|
> | **Union (A ∪ B)** | All elements from both arrays      | Only unique elements |
> | **Intersection (A ∩ B)** | Elements present in **both** | Counted by frequency |
>
> **Union** example: `{1,2,3} ∪ {2,3,4} = {1,2,3,4}`
>
> **Intersection** example: `{1,2,2,3} ∩ {2,2,3,4} = {2,2,3}`

---

### Q5: Why is the Two-Pointer approach O(n₁ + n₂) and not O(n₁ × n₂)?

> In the two-pointer approach, **each pointer only moves forward** — never backward. At each step, at least one pointer advances:
>
> - Pointer `i` can advance at most `n₁` times
> - Pointer `j` can advance at most `n₂` times
> - Total steps ≤ `n₁ + n₂`
>
> Compare with brute force: for **each** element in A, we scan **all** of B → O(n₁ × n₂).
>
> The sorted property is what makes two-pointer possible — if `A[i] < B[j]`, we **know** no element at `B[j:]` can match `A[i]`.

---

### Q6: Can you solve Union/Intersection for UNSORTED arrays?

> **Yes, but with trade-offs:**
>
> | Approach            | Time              | Space   |
> |---------------------|:-----------------:|:-------:|
> | Sort first + Two-ptr | O(n log n)       | O(1)    |
> | Hash Set / Hash Map  | O(n)             | O(n)    |
>
> For **unsorted** arrays, the Hash approach is typically preferred:
>
> ```cpp
> // Union of unsorted arrays
> unordered_set<int> st(a.begin(), a.end());
> st.insert(b.begin(), b.end());
>
> // Intersection of unsorted arrays
> unordered_map<int, int> freq;
> for (int x : a) freq[x]++;
> for (int x : b) {
>     if (freq[x] > 0) { ans.push_back(x); freq[x]--; }
> }
> ```

---

### Q7: What is the space complexity nuance the interviewer cares about?

> **Always clarify with the interviewer:**
>
> - **Algorithm space** — total space used (including the input array): **O(n)**
> - **Extra/Auxiliary space** — additional space beyond input: **O(1)**
>
> Example: For "Move Zeros" optimal solution:
> > *"The extra space I'm using is O(1) — I'm modifying the array in-place. However, the algorithm does use the given array of size n, so if you consider that, it's O(n) total."*
>
> This distinction shows maturity in your understanding and impresses interviewers.

---

### Q8: How do you handle edge cases for rotation problems?

> Key edge cases to mention in an interview:
>
> | Edge Case          | How to Handle                    |
> |--------------------|----------------------------------|
> | D = 0              | No rotation needed, return as-is |
> | D = N              | Full rotation = original array   |
> | D > N              | `D = D % N` first                |
> | Array size = 1     | No rotation possible             |
> | All same elements  | Rotation doesn't change anything |
>
> Always validate these before coding!

---

## 🧩 Quick Revision Summary

```
┌──────────────────────────────────────────────────────────────────┐
│               ARRAYS EASY — PART 2 SUMMARY                       │
│                                                                   │
│  P5: Left Rotate by 1                                             │
│      → Store first, shift left, place at end      O(n), O(1)     │
│                                                                   │
│  P6: Left Rotate by D                                             │
│      → D = D % N first!                                           │
│      → Brute: Temp array                    O(n), O(d) space      │
│      → Optimal: 3 Reversals                 O(n), O(1) ✅         │
│                                                                   │
│  P7: Move Zeros to End                                            │
│      → Two-pointer: j=first zero, swap non-zeros   O(n), O(1) ✅ │
│                                                                   │
│  P8: Linear Search → Just iterate               O(n), O(1)       │
│                                                                   │
│  P9: Union of Sorted Arrays                                       │
│      → Brute: Set               O(n log n), O(n) space            │
│      → Optimal: Two-pointer     O(n₁+n₂), O(1) extra ✅          │
│                                                                   │
│  P10: Intersection of Sorted Arrays                               │
│      → Brute: Visited array     O(n₁×n₂), O(n₂)                  │
│      → Optimal: Two-pointer     O(n₁+n₂), O(1) extra ✅          │
│                                                                   │
│  KEY TECHNIQUES: Two-Pointer, Three-Reversal, D%N trick           │
└──────────────────────────────────────────────────────────────────┘
```

> 💡 **Assignment from Lecture:** Implement **right rotation** by D places and share your solution!

---

*📌 Source: Striver's A2Z DSA Course — Step 3.1: Arrays Easy (Part 2)*

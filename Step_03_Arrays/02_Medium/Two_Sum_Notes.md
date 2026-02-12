# 🔢 Two Sum | Striver's A2Z DSA Course (Step 3.2 — Medium)

> **Lecture Summary:** First problem of Step 3.2 — the classic **Two Sum** problem. Covers two varieties (yes/no existence and return indices), three approaches (Brute → Hashing → Two Pointer), and when to use which.
>
> 🔗 **Problem Link:** [Two Sum — LeetCode 1](https://leetcode.com/problems/two-sum/)

---

## 📌 Table of Contents

1. [Problem Statement — Two Varieties](#1--problem-statement--two-varieties)
2. [Approach 1: Brute Force — O(n²)](#2--approach-1-brute-force--on²)
3. [Approach 2: Better — Hashing — O(n)](#3--approach-2-better--hashing--on)
4. [Approach 3: Optimal — Two Pointer — O(n log n)](#4--approach-3-optimal--two-pointer--on-log-n)
5. [Which Approach for Which Variety?](#5--which-approach-for-which-variety)
6. [Practice Questions](#6--practice-questions)
7. [Interview Questions & Answers](#7--interview-questions--answers)

---

## 1. 📘 Problem Statement — Two Varieties

> Given an array of integers and a target sum, determine if two elements exist whose sum equals the target.

```
Array: [2, 6, 5, 8, 11]    Target: 14
```

### Variety 1: Return YES or NO

```
"Does there exist two elements that sum to 14?"
→ YES (6 + 8 = 14)

Target = 15 → Try all pairs → NO (no two elements sum to 15)
```

### Variety 2: Return the INDICES

```
"Return the indices of the two elements that sum to 14"
→ [1, 3]  (arr[1]=6, arr[3]=8, and 6+8=14)
```

> 💡 Both varieties share the same core logic. Variety 2 just needs to track WHERE the elements are.

---

## 2. ❌ Approach 1: Brute Force — O(n²)

> **Idea:** For every element, check with every OTHER element whether they sum to the target.

```
arr = [2, 6, 5, 8, 11],  target = 14

i=0 (2): Check 2+6=8❌, 2+5=7❌, 2+8=10❌, 2+11=13❌
i=1 (6): Check 6+5=11❌, 6+8=14✅ → FOUND!

Return "YES" or indices [1, 3]
```

### Optimization: Avoid Duplicate Checks

```
When i=0, we already checked (2,6). So when i=1, don't recheck (6,2).
→ Start j from i+1 instead of 0!
```

```cpp
// Variety 1: Return YES/NO
string twoSum(vector<int>& arr, int target) {
    int n = arr.size();
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (arr[i] + arr[j] == target) {
                return "YES";
            }
        }
    }
    return "NO";
}

// Variety 2: Return indices
vector<int> twoSum(vector<int>& nums, int target) {
    int n = nums.size();
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (nums[i] + nums[j] == target) {
                return {i, j};
            }
        }
    }
    return {-1, -1};  // Not found
}
```

|    Time     | Space |
| :---------: | :---: |
| **O(n²)** ❌ | O(1)  |

---

## 3. 🏆 Approach 2: Better — Hashing — O(n)

> **Key Insight:** If I'm at element `8` and I need target `14`, then I need `14 - 8 = 6`. If `6` was already seen earlier, we have our pair!

### Thought Process

```
Standing at element 8:
  I need: target - current = 14 - 8 = 6
  Has 6 appeared before? → Look in HashMap!
  Yes → FOUND pair (6, 8)
  No  → Store current element in HashMap and move on
```

> Instead of checking every pair (O(n²)), we ask **one question** per element: "Does my complement exist in the map?" → O(1) lookup!

### Step-by-Step Dry Run

```
arr = [2, 6, 5, 8, 11],  target = 14
HashMap = {},  (stores element → index)

i=0, element=2:
  Need: 14 - 2 = 12. Is 12 in map? NO
  Store: {2: 0}

i=1, element=6:
  Need: 14 - 6 = 8. Is 8 in map? NO
  Store: {2: 0, 6: 1}

i=2, element=5:
  Need: 14 - 5 = 9. Is 9 in map? NO
  Store: {2: 0, 6: 1, 5: 2}

i=3, element=8:
  Need: 14 - 8 = 6. Is 6 in map? YES! At index 1 ✅
  Return: [1, 3]  (map[6]=1, current i=3)
```

### C++ Implementation

```cpp
// Variety 2: Return indices (most common on LeetCode)
vector<int> twoSum(vector<int>& nums, int target) {
    map<int, int> mp;  // element → index

    for (int i = 0; i < nums.size(); i++) {
        int need = target - nums[i];

        // Check if the complement exists in the map
        if (mp.find(need) != mp.end()) {
            return {mp[need], i};
        }

        // Store current element with its index
        mp[nums[i]] = i;
    }

    return {-1, -1};  // Should never reach here if guaranteed
}

// Variety 1: Return YES/NO
string twoSum(vector<int>& arr, int target) {
    map<int, int> mp;
    for (int i = 0; i < arr.size(); i++) {
        int need = target - arr[i];
        if (mp.find(need) != mp.end()) {
            return "YES";
        }
        mp[arr[i]] = i;
    }
    return "NO";
}
```

| Map Type        | Time (per lookup) |   Total Time   |      Worst Case       |
| --------------- | :---------------: | :------------: | :-------------------: |
| `unordered_map` |     O(1) avg      |  **O(n)** avg  | O(n²) with collisions |
| `map` (ordered) |     O(log n)      | **O(n log n)** | O(n log n) guaranteed |

|                 Space                  |
| :------------------------------------: |
| **O(n)** — storing elements in HashMap |

### Why This Is Optimal for Variety 2

```
Variety 2 needs ORIGINAL indices.
Sorting destroys the original indices.
To preserve indices with sorting → need extra data structure → same space.

Hashing directly gives us indices WITHOUT modifying the array.
→ Best approach for "return indices" variety.
```

---

## 4. ⚡ Approach 3: Optimal (No Extra Space) — Two Pointer — O(n log n)

> **Idea:** Sort the array. Use two pointers from both ends. If sum is too small, move left pointer right. If too big, move right pointer left.

### Why Sorting Helps

```
Sorted: [2, 5, 6, 8, 11]    Target: 14
         L              R

Sum = arr[L] + arr[R]
  • If sum < target → need BIGGER sum → move L right (get larger number)
  • If sum > target → need SMALLER sum → move R left (get smaller number)
  • If sum == target → FOUND! ✅

This works because the array is SORTED — moving left increases, moving right decreases.
```

### Detailed Dry Run

```
Sorted arr = [2, 5, 6, 8, 11],  target = 14

Step 1: L=0, R=4 → 2 + 11 = 13 < 14 → move L right
Step 2: L=1, R=4 → 5 + 11 = 16 > 14 → move R left
Step 3: L=1, R=3 → 5 + 8  = 13 < 14 → move L right
Step 4: L=2, R=3 → 6 + 8  = 14 == 14 → FOUND! ✅ Return "YES"
```

### C++ Implementation

```cpp
// Variety 1: Return YES/NO (BEST for this variety — no extra space!)
string twoSum(vector<int>& arr, int target) {
    sort(arr.begin(), arr.end());  // Sort first!

    int left = 0, right = arr.size() - 1;

    while (left < right) {
        int sum = arr[left] + arr[right];

        if (sum == target) {
            return "YES";
        } else if (sum < target) {
            left++;     // Need bigger sum
        } else {
            right--;    // Need smaller sum
        }
    }

    return "NO";
}
```

### ⚠️ Why NOT Optimal for Variety 2?

```
Problem: Sorting DESTROYS original indices!

Original: [2, 6, 5, 8, 11]   →  6 is at index 1, 8 is at index 3
Sorted:   [2, 5, 6, 8, 11]   →  6 is at index 2, 8 is at index 3 ← WRONG!

To preserve indices, you'd need to store (element, originalIndex) pairs:
  [(2,0), (5,2), (6,1), (8,3), (11,4)]

But this uses O(n) extra space anyway → no advantage over hashing!
```

|                    Time                     |            Space            |
| :-----------------------------------------: | :-------------------------: |
| **O(n log n)** for sorting + O(n) traversal | **O(1)** ✅ (no extra space) |

> The interviewer cares about this approach when they say: *"Can you solve it WITHOUT using a HashMap?"*

---

## 5. 📊 Which Approach for Which Variety?

| Variety                      | Best Approach         | Why?                                             |
| ---------------------------- | --------------------- | ------------------------------------------------ |
| **YES/NO** (no index needed) | **Two Pointer**       | O(n log n) time, O(1) space — no extra DS needed |
| **Return Indices**           | **Hashing (HashMap)** | O(n) time, preserves original indices natively   |

```
Decision Tree:

"Need original indices?"
├── YES → Use HashMap (Approach 2)
│         Time: O(n) avg, Space: O(n)
│
└── NO → "Can I modify the array?"
    ├── YES → Sort + Two Pointer (Approach 3)
    │         Time: O(n log n), Space: O(1)
    │
    └── NO → Use HashMap (Approach 2)
              Time: O(n) avg, Space: O(n)
```

> 💡 **Interview Pro Tip:** Present the hashing solution first (covers both varieties), then offer the two-pointer as a follow-up when asked *"Can you do it without extra space?"*

---

## 6. 📝 Practice Questions

### Easy

1. 🔗 [Two Sum — LeetCode 1](https://leetcode.com/problems/two-sum/) *(Covered in lecture)*
2. 🔗 [Two Sum II (Sorted Input) — LeetCode 167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) *(Direct two-pointer application!)*
3. 🔗 [Valid Anagram — LeetCode 242](https://leetcode.com/problems/valid-anagram/) *(Hashing practice)*

### Medium

4. 🔗 [3Sum — LeetCode 15](https://leetcode.com/problems/3sum/) *(Extension: find THREE numbers)*
5. 🔗 [4Sum — LeetCode 18](https://leetcode.com/problems/4sum/) *(Extension: find FOUR numbers)*
6. 🔗 [Two Sum IV (BST) — LeetCode 653](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/) *(Two Sum in a tree!)*
7. 🔗 [Subarray Sum Equals K — LeetCode 560](https://leetcode.com/problems/subarray-sum-equals-k/) *(Prefix sum hashing — related concept)*
8. 🔗 [Pairs with Given Sum — GeeksforGeeks](https://www.geeksforgeeks.org/problems/pair-with-given-sum-in-a-sorted-array4940/1) *(Count pairs)*

### Challenging

9. 🔗 [4Sum II — LeetCode 454](https://leetcode.com/problems/4sum-ii/) *(HashMap on four arrays)*
10. 🔗 [Max Number of K-Sum Pairs — LeetCode 1679](https://leetcode.com/problems/max-number-of-k-sum-pairs/) *(Count how many pairs you can remove)*

---

## 7. 🎯 Interview Questions & Answers

### Q1: Why does the Two-Pointer approach work only on sorted arrays?

> The approach relies on a **monotonic property**:
> - Moving `left` right → sum **increases** (sorted = next element is bigger)
> - Moving `right` left → sum **decreases** (sorted = previous element is smaller)
>
> This lets us make a confident decision at each step. On unsorted arrays, moving a pointer gives **no guarantee** about how the sum changes, so the logic breaks.

---

### Q2: What happens if there are duplicate elements?

> **HashMap Approach:** Works perfectly. Each element is stored as we iterate. If we need `7` and we see `7` later, it'll already be in the map with a DIFFERENT index.
>
> ```
> arr = [3, 3],  target = 6
> i=0: need 3, not in map. Store {3: 0}
> i=1: need 3, found in map at index 0! Return [0, 1] ✅
> ```
>
> **Two-Pointer:** Also works. After sorting, duplicates are adjacent. Both pointers move independently, so the same element at two positions is handled naturally.

---

### Q3: Can you solve Two Sum in O(n) time AND O(1) space?

> **No** — at least not in the general case:
>
> | Approach | Time | Space | Trade-off |
> |----------|:----:|:-----:|-----------|
> | HashMap  | O(n) | O(n)  | Fast but uses space |
> | Two Pointer | O(n log n) | O(1) | No space but slower |
>
> You either sacrifice **time** (sorting = O(n log n)) or **space** (HashMap = O(n)). There's no known O(n) time + O(1) space solution for the general Two Sum problem.
>
> This is a great answer for an interview — it shows you understand **time-space trade-offs**.

---

### Q4: Why do you store element AFTER checking the map, not before?

> To avoid matching an element with **itself**.
>
> ```
> arr = [3, 2, 4],  target = 6
>
> If we store first, then check:
>   i=0: Store {3: 0}. Need 3. Found 3 at index 0! → [0, 0] ❌ WRONG!
>
> If we check first, then store:
>   i=0: Need 3. Not in map. Store {3: 0}.
>   i=1: Need 4. Not in map. Store {2: 1}.
>   i=2: Need 2. Found 2 at index 1! → [1, 2] ✅ CORRECT!
> ```
>
> **Rule:** Always look before you store. This ensures you never pair an element with itself.

---

### Q5: `map` vs `unordered_map` — which should you use?

> | Type              | Avg Lookup | Worst Lookup | When to Use |
> |-------------------|:----------:|:------------:|-------------|
> | `unordered_map`   | **O(1)**   | O(n)         | Most cases — fastest avg |
> | `map` (ordered)   | O(log n)   | **O(log n)** | When you need guaranteed time |
>
> In interviews, mention both:
> > *"I'll use `unordered_map` for O(1) average lookup, giving O(n) total. But in the worst case with hash collisions, it could degrade to O(n²). If the interviewer prefers guaranteed performance, I can switch to `map` for O(n log n) total."*

---

### Q6: How would you extend Two Sum to Three Sum (3Sum)?

> **Strategy:** Fix one element, then run Two Sum on the rest!
>
> ```
> For each element a[i]:
>     New target = target - a[i]
>     Run Two Sum on remaining elements for new target
>
> Time: O(n²) — one loop × Two Sum's O(n)
> ```
>
> For [LeetCode 15 — 3Sum](https://leetcode.com/problems/3sum/), sort first + skip duplicates + two-pointer inner loop = O(n²).
>
> **Pattern:** K-Sum problems reduce to (K-1)-Sum recursively!
> - 4Sum = Fix one + 3Sum → O(n³)
> - 3Sum = Fix one + 2Sum → O(n²)
> - 2Sum = O(n)

---

### Q7: What if the problem says "the array is already sorted"?

> Then skip hashing entirely! Use **Two Pointer directly** — no sorting needed:
>
> - Time: **O(n)** (just the two-pointer pass)
> - Space: **O(1)**
>
> This is exactly [LeetCode 167 — Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/). The sorted input is a hint to use two pointers.

---

### Q8: The interviewer says "you're not allowed to use a Map." What do you do?

> Exactly the scenario from the lecture! Use the **Sort + Two Pointer** approach:
>
> 1. Sort the array — O(n log n)
> 2. Two pointers from both ends — O(n)
> 3. Total: O(n log n), Space: O(1)
>
> Trade-off: You lose the ability to return original indices (sorting changes positions), but for YES/NO this is perfect.
>
> If they ALSO need indices, you can store `(value, originalIndex)` pairs before sorting, but that uses O(n) space — so just mention the HashMap approach is better for that.

---

## 🧩 Quick Revision Summary

```
┌────────────────────────────────────────────────────────────────┐
│                    TWO SUM — SUMMARY                           │
│                                                                │
│  VARIETY 1 (YES/NO):      VARIETY 2 (Return Indices):         │
│  → Sort + Two Pointer     → HashMap (best!)                   │
│  → O(n log n), O(1)       → O(n) avg, O(n) space              │
│                                                                │
│  APPROACHES:                                                   │
│  ┌──────────────┬──────────────┬─────────┬──────────────────┐  │
│  │ Approach     │ Time         │ Space   │ Preserves Index? │  │
│  ├──────────────┼──────────────┼─────────┼──────────────────┤  │
│  │ Brute Force  │ O(n²)        │ O(1)    │ ✅ Yes           │  │
│  │ HashMap      │ O(n) avg     │ O(n)    │ ✅ Yes           │  │
│  │ Two Pointer  │ O(n log n)   │ O(1)    │ ❌ No (sorted)   │  │
│  └──────────────┴──────────────┴─────────┴──────────────────┘  │
│                                                                │
│  KEY TECHNIQUE:                                                │
│  • need = target - current                                     │
│  • Look for "need" in HashMap → O(1) check                    │
│  • Store AFTER checking → avoid self-pairing                   │
│  • Two Pointer: sum < target → L++, sum > target → R--        │
│                                                                │
│  INTERVIEW TIP: Present HashMap first, then offer              │
│  Two Pointer when asked "without extra space?"                 │
└────────────────────────────────────────────────────────────────┘
```

---

*📌 Source: Striver's A2Z DSA Course — Step 3.2: Arrays Medium (Problem 1)*

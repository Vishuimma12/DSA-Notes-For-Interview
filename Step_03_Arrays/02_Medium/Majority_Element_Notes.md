# 🗳️ Majority Element — Moore's Voting Algorithm | Step 3.2

> **Lecture Summary:** Find the element appearing **more than n/2 times** using the brilliant **Moore's Voting Algorithm** — a single-pass O(n) time, O(1) space solution based on a "cancellation" intuition.
>
> 🔗 **Problem Link:** [Majority Element — LeetCode 169](https://leetcode.com/problems/majority-element/)

---

## 📌 Table of Contents

1. [Problem Statement](#1--problem-statement)
2. [Approach 1: Brute Force — O(n²)](#2--approach-1-brute-force--on²)
3. [Approach 2: Better — Hashing — O(n log n)](#3--approach-2-better--hashing--on-log-n)
4. [Approach 3: Optimal — Moore's Voting Algorithm — O(n)](#4--approach-3-optimal--moores-voting-algorithm--on)
5. [Full Dry Run](#5--full-dry-run)
6. [C++ Implementation](#6--c-implementation)
7. [Practice Questions](#7--practice-questions)
8. [Interview Questions & Answers](#8--interview-questions--answers)

---

## 1. 📘 Problem Statement

> Given an array of n integers, find the element that appears **more than ⌊n/2⌋ times** (the majority element).

```
Input:  [2, 2, 3, 3, 1, 2, 2]
Output: 2

Why?  n = 7, so n/2 = 3 (floor).
      Element 2 appears 4 times → 4 > 3 ✅ → Majority!
```

> ⚠️ **"More than"** n/2, NOT "equal to." If n=8, an element must appear ≥ 5 times (more than 4).

### Key Observation

```
There can be AT MOST one majority element.

Why? If element A appears more than n/2 times,
     that's more than half the array.
     No room for another element to ALSO appear more than n/2 times.
     
     Example: n=10, majority needs > 5 occurrences.
     If A appears 6 times → only 4 spots left → B can't get > 5.
```

---

## 2. ❌ Approach 1: Brute Force — O(n²)

> **Idea:** Pick each element, scan the entire array to count it.

```cpp
int majorityElement(vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n; i++) {
        int count = 0;
        for (int j = 0; j < n; j++) {
            if (arr[j] == arr[i]) count++;
        }
        if (count > n / 2) return arr[i];
    }
    return -1;  // No majority
}
```

|    Time     | Space |
| :---------: | :---: |
| **O(n²)** ❌ | O(1)  |

---

## 3. ⚡ Approach 2: Better — Hashing — O(n log n)

> **Idea:** Use a HashMap to count occurrences in one pass. Then check who has count > n/2.

### Dry Run

```
arr = [2, 2, 3, 3, 1, 2, 2]

HashMap after iteration:
  { 2: 4, 3: 2, 1: 1 }

Check: 4 > 7/2 = 3? YES → return 2 ✅
```

```cpp
int majorityElement(vector<int>& nums) {
    map<int, int> mp;
    int n = nums.size();

    // Count occurrences
    for (int i = 0; i < n; i++) {
        mp[nums[i]]++;
    }

    // Find majority
    for (auto& it : mp) {
        if (it.second > n / 2) {
            return it.first;
        }
    }

    return -1;
}
```

| Map Type        |         Time          | Space |
| --------------- | :-------------------: | :---: |
| `map` (ordered) |    **O(n log n)**     | O(n)  |
| `unordered_map` | O(n) avg, O(n²) worst | O(n)  |

> **Interviewer:** *"Can you do it without extra space?"* → Moore's Voting Algorithm!

---

## 4. 🏆 Approach 3: Optimal — Moore's Voting Algorithm — O(n)

### The Cancellation Intuition 🎯

> **Core Idea:** If an element appears **more than half** the time, it **cannot** be fully canceled out by all other elements combined.

```
Think of it as a VOTE:

  Majority supporter (+1) vs Opposition (-1)

  If the majority element has MORE than n/2 votes,
  even if EVERY other element votes against it,
  it will still have votes remaining at the end!

  Example: arr = [5, 5, 5, 5, 7, 8, 9]
  5 has 4 votes. Opposition (7,8,9) has 3 votes.
  After cancellation: 5 still survives with 1 remaining! ✅
```

### The Algorithm — Two Variables

```
Variables:
  element → the current candidate for majority
  count   → NOT the frequency! It's the "survival score"

Rules:
  1. If count == 0 → pick current element as new candidate, count = 1
  2. If arr[i] == element → count++  (supporter found!)
  3. If arr[i] != element → count--  (opposition cancels one vote)
```

### Why Does This Work?

```
Scenario 1: Majority element exists (appears > n/2 times)
  → It has MORE supporters than all opposition combined
  → Even after all cancellations, it will be the LAST survivor
  → The element variable will hold it at the end ✅

Scenario 2: No majority element exists
  → The last survivor is RANDOM (whoever didn't get canceled last)
  → That's why we need a VERIFICATION step!
```

### Two-Phase Process

```
┌────────────────────────────────────────────────────────────┐
│ PHASE 1: Find the CANDIDATE using Moore's Voting          │
│   → The survivor after all cancellations                   │
│                                                            │
│ PHASE 2: VERIFY the candidate (only if problem says       │
│          majority may or may not exist)                     │
│   → Count actual occurrences of the candidate              │
│   → Check if count > n/2                                   │
│                                                            │
│ ⚠️ Skip Phase 2 if problem GUARANTEES a majority exists!  │
└────────────────────────────────────────────────────────────┘
```

---

## 5. 🔍 Full Dry Run

```
arr = [7, 7, 5, 7, 5, 1, 5, 7, 5, 5, 7, 7, 5, 5, 5, 5]
n = 16, need > 8 occurrences for majority

element = ?, count = 0
```

### Phase 1: Moore's Voting

```
i=0:  arr[0]=7, count=0 → NEW CANDIDATE! element=7, count=1
i=1:  arr[1]=7, 7==7    → count=2
i=2:  arr[2]=5, 5≠7     → count=1  (one 7 canceled by one 5)
i=3:  arr[3]=7, 7==7    → count=2
i=4:  arr[4]=5, 5≠7     → count=1
i=5:  arr[5]=1, 1≠7     → count=0  (all 7s canceled!)

  Section [7,7,5,7,5,1]: 7 appears 3 times, others 3 times → TIED → canceled

i=6:  arr[6]=5, count=0 → NEW CANDIDATE! element=5, count=1
i=7:  arr[7]=7, 7≠5     → count=0  (canceled again!)

  Section [5,7]: 5 once, 7 once → canceled

i=8:  arr[8]=5, count=0 → NEW CANDIDATE! element=5, count=1
i=9:  arr[9]=5, 5==5    → count=2
i=10: arr[10]=7, 7≠5    → count=1
i=11: arr[11]=7, 7≠5    → count=0

  Section [5,5,7,7]: 5 twice, 7 twice → canceled

i=12: arr[12]=5, count=0 → NEW CANDIDATE! element=5, count=1
i=13: arr[13]=5, 5==5   → count=2
i=14: arr[14]=5, 5==5   → count=3
i=15: arr[15]=5, 5==5   → count=4  ← SURVIVED!

PHASE 1 RESULT: element = 5, count = 4
(count=4 does NOT mean 5 appears 4 times — it's just the survival score)
```

### Phase 2: Verify

```
Count actual occurrences of 5:
[7, 7, 5, 7, 5, 1, 5, 7, 5, 5, 7, 7, 5, 5, 5, 5]
         ^     ^        ^     ^  ^        ^  ^  ^  ^

5 appears 9 times. 9 > 16/2 = 8? YES ✅

Answer: 5 is the majority element
```

### Visualizing The Cancellation

```
Section 1: [7, 7, 5, 7, 5, 1]  →  3 sevens vs 2 fives + 1 one = CANCELED
Section 2: [5, 7]                →  1 five vs 1 seven = CANCELED
Section 3: [5, 5, 7, 7]         →  2 fives vs 2 sevens = CANCELED
Section 4: [5, 5, 5, 5]         →  4 fives SURVIVE! ← element = 5

Out of 9 occurrences of 5:
  • 5 got canceled in sections 1, 2, 3
  • 4 survived in section 4
  • Since 5 appears MORE than half (9 > 8), it can't be fully canceled
```

---

## 6. 💻 C++ Implementation

```cpp
int majorityElement(vector<int>& nums) {
    int n = nums.size();
    int count = 0;
    int element;

    // PHASE 1: Moore's Voting — Find candidate
    for (int i = 0; i < n; i++) {
        if (count == 0) {
            element = nums[i];  // New candidate
            count = 1;
        }
        else if (nums[i] == element) {
            count++;            // Supporter found
        }
        else {
            count--;            // Opposition cancels
        }
    }

    // PHASE 2: Verify (only if majority MAY not exist)
    // LeetCode 169 guarantees majority exists → skip this
    // But in interviews, always include verification!
    int countVerify = 0;
    for (int i = 0; i < n; i++) {
        if (nums[i] == element) countVerify++;
    }

    if (countVerify > n / 2) return element;
    return -1;  // No majority element
}
```

> 💡 **LeetCode 169** guarantees a majority element always exists, so Phase 2 is optional there. But **always include it in interviews** to show thoroughness!

|                      Time                       |   Space    |
| :---------------------------------------------: | :--------: |
| **O(n)** — single pass (+ optional O(n) verify) | **O(1)** ✅ |

### Approach Comparison

| Approach           |   Time   |  Space   | Key Trade-off           |
| ------------------ | :------: | :------: | ----------------------- |
| Brute Force        |  O(n²)   |   O(1)   | Too slow                |
| Hashing            | O(n) avg | **O(n)** | Extra space for map     |
| **Moore's Voting** | **O(n)** | **O(1)** | Needs verification step |

---

## 7. 📝 Practice Questions

### Easy

1. 🔗 [Majority Element — LeetCode 169](https://leetcode.com/problems/majority-element/) *(Covered in lecture)*
2. 🔗 [Check If N and Its Double Exist — LeetCode 1346](https://leetcode.com/problems/check-if-n-and-its-double-exist/) *(Hashing practice)*

### Medium

3. 🔗 [Majority Element II — LeetCode 229](https://leetcode.com/problems/majority-element-ii/) *(Find elements appearing > n/3 times — extended Moore's!)*
4. 🔗 [Top K Frequent Elements — LeetCode 347](https://leetcode.com/problems/top-k-frequent-elements/) *(Frequency counting)*
5. 🔗 [Sort Characters By Frequency — LeetCode 451](https://leetcode.com/problems/sort-characters-by-frequency/)
6. 🔗 [Find All Numbers Disappeared — LeetCode 448](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/)

### Challenging

7. 🔗 [Online Majority Element In Subarray — LeetCode 1157](https://leetcode.com/problems/online-majority-element-in-subarray/) *(Majority in subarray queries)*
8. Extend Moore's Voting for **three** candidates (> n/3) — How many candidates can there be at most?
9. 🔗 [Degree of an Array — LeetCode 697](https://leetcode.com/problems/degree-of-an-array/)

---

## 8. 🎯 Interview Questions & Answers

### Q1: Explain Moore's Voting Algorithm in simple terms.

> Imagine an election where one candidate has **more than half** the votes. Every vote for that candidate is +1, every other vote is -1.
>
> We walk through the votes one by one:
> - **count = 0?** → Start backing a new candidate
> - **Same candidate?** → count++ (supporter!)
> - **Different candidate?** → count-- (opposition cancels one supporter)
>
> At the end, the surviving candidate is the majority — because if someone has MORE than half the support, they **cannot be fully canceled** by everyone else combined.
>
> After finding the candidate, we **verify** by counting their actual occurrences.

---

### Q2: Why is the verification step (Phase 2) necessary?

> Moore's Voting finds the **last surviving candidate**, which may not be the actual majority if no majority exists.
>
> ```
> arr = [1, 2, 3, 4, 5]  (no majority element)
>
> Phase 1: All elements cancel each other.
>   i=0: element=1, count=1
>   i=1: count=0, element=2, count=1
>   i=2: count=0, element=3, count=1
>   i=3: count=0, element=4, count=1
>   i=4: count=0, element=5, count=1
>
> Candidate: 5 (just the last one standing)
> Phase 2: 5 appears 1 time. 1 > 5/2=2? NO → No majority ✅
> ```
>
> Without verification, we'd incorrectly return 5 as the majority.

---

### Q3: What does the `count` variable represent? Is it the frequency?

> **No!** `count` is NOT the frequency of the element. It's the **"net survival score"** — how many unmatched/uncanceled supporters remain.
>
> ```
> arr = [5, 5, 5, 5, 7, 8, 9]
>
> After algorithm: element=5, count=1
> But 5 actually appears 4 times, not 1!
>
> count=1 because: 4 supporters - 3 opposition = 1 remaining
> ```
>
> That's exactly why Phase 2 is needed — to get the actual count.

---

### Q4: Prove that the majority element will always survive.

> **Proof by contradiction:**
>
> Let element M appear `k` times where `k > n/2`.
> Other elements appear `n - k` times where `n - k < n/2 < k`.
>
> In Moore's algorithm, each "cancellation" removes one M and one non-M.
> Maximum cancellations = `n - k` (limited by opposition count).
> Remaining M's after cancellation = `k - (n - k)` = `2k - n`.
>
> Since `k > n/2` → `2k > n` → `2k - n > 0`.
>
> Therefore, M will always have a **positive remainder** after all cancellations. It will be the final survivor. ∎

---

### Q5: How would you find elements appearing more than n/3 times?

> This is [LeetCode 229 — Majority Element II](https://leetcode.com/problems/majority-element-ii/).
>
> **Key Insight:** At most **2** elements can appear > n/3 times.
> (If 3 elements each had > n/3, total > n — impossible!)
>
> **Extended Moore's:** Use **two** candidates with two counts:
>
> ```cpp
> int el1, el2, cnt1 = 0, cnt2 = 0;
>
> for (int x : nums) {
>     if (cnt1 == 0 && x != el2) { el1 = x; cnt1 = 1; }
>     else if (cnt2 == 0 && x != el1) { el2 = x; cnt2 = 1; }
>     else if (x == el1) cnt1++;
>     else if (x == el2) cnt2++;
>     else { cnt1--; cnt2--; }  // Cancel both
> }
>
> // Verify both candidates
> ```
>
> **Pattern:** For > n/K → use K-1 candidates. But this extends only practically up to small K values.

---

### Q6: Can Moore's Voting be applied to find the most FREQUENT element (not necessarily > n/2)?

> **No.** Moore's Voting specifically requires the element to appear **more than n/2 times**. If no element has this property, the algorithm gives a meaningless result.
>
> For the **most frequent element** (could be any count):
> - Use **HashMap** → O(n) time, O(n) space
> - Use **Sorting** → O(n log n) time, O(1) space
> - Use **Bucket Sort** on frequencies → O(n) time, O(n) space

---

### Q7: What's the relationship between this algorithm and the "heavy hitter" problem?

> Moore's Voting is a special case of the **heavy hitter** / **frequent items** problem in streaming algorithms:
>
> | Problem | Threshold | Candidates | Algorithm |
> |---------|:---------:|:----------:|-----------|
> | Majority | > n/2 | At most 1 | Moore's Voting (1 counter) |
> | Majority II | > n/3 | At most 2 | Extended Moore's (2 counters) |
> | Majority K | > n/K | At most K-1 | Misra-Gries algorithm |
>
> This is a classic **streaming** algorithm — it processes data in a single pass with constant memory, making it useful for massive datasets that don't fit in memory.

---

### Q8: If the problem guarantees a majority exists, can we skip Phase 2?

> **Yes!** If the problem statement says *"a majority element always exists"* (like LeetCode 169), Phase 2 is unnecessary.
>
> But in an interview:
> - **Always ask:** *"Is a majority guaranteed to exist?"*
> - If yes → skip Phase 2 (saves one pass)
> - If no → include Phase 2 for correctness
>
> Mentioning this distinction shows the interviewer you think about **edge cases** and **problem constraints**.

---

## 🧩 Quick Revision Summary

```
┌──────────────────────────────────────────────────────────────────┐
│           MOORE'S VOTING ALGORITHM — SUMMARY                     │
│                                                                   │
│  PHASE 1: Find Candidate                                         │
│  ┌──────────────────────────────────────────────────────┐         │
│  │ count = 0, element = ?                               │         │
│  │                                                      │         │
│  │ For each arr[i]:                                     │         │
│  │   count == 0 → element = arr[i], count = 1           │         │
│  │   arr[i] == element → count++                        │         │
│  │   arr[i] != element → count--                        │         │
│  └──────────────────────────────────────────────────────┘         │
│                                                                   │
│  PHASE 2: Verify (if majority not guaranteed)                     │
│  → Count actual occurrences of candidate                          │
│  → Return if count > n/2, else return -1                          │
│                                                                   │
│  WHY IT WORKS:                                                    │
│  • Majority element has MORE than half the votes                  │
│  • Opposition has LESS than half                                  │
│  • After all cancellations, majority SURVIVES                     │
│  • count ≠ frequency! It's the "survival score"                   │
│                                                                   │
│  TIME: O(n)  │  SPACE: O(1)  │  PASSES: 1 (+ optional verify)    │
│                                                                   │
│  EXTENSION: > n/3 → use 2 candidates (LeetCode 229)              │
│             > n/K → use K-1 candidates (Misra-Gries)              │
└──────────────────────────────────────────────────────────────────┘
```

---

*📌 Source: Striver's A2Z DSA Course — Step 3.2: Arrays Medium (Problem 3)*

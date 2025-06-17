
## Time Complexity: Understanding When It's 2^n vs n^n**

When analyzing recursive functions, the key is to look at how many recursive calls are made at each level:

1. **Constant branching** - calls itself the same number of times at every level
2. **Fixed branching** - calls itself a specific, unchanging number of times
3. **Decreasing branching** - calls itself fewer times as recursion progresses

## Example 1: O(n^n) - Constant Branching

```javascript
function func(depth) {
    if (depth >= maxDepth) return;
    for (let i = 0; i < n.length; i++) {
        func(depth + 1)  // Always n recursive calls
    }
}
```

*(Note: This is different from `func(i + 1)` which would create shrinking recursion)*

In this case, we make the same constant n number of recursive calls at every level, resulting in **O(n^n)** time complexity.

## Example 2: O(3^n) - Fixed Branching

```javascript
function func(n) {
    if (n <= 0) return;
    func(n - 1);  // Call 1
    func(n - 1);  // Call 2  
    func(n - 1);  // Call 3
}
```

This makes exactly 3 recursive calls at each level, resulting in **O(3^n)** complexity.

## Example 3: O(2^n) - Decreasing Branching

```javascript
function func(index) {
    for (let i = index + 1; i < n.length; i++) {
        func(i)  // Fewer calls as index increases
    }
}
```

This creates **O(2^n)** complexity because the branching factor decreases each time (n-1, n-2, n-3...). The "2" comes from solving the recurrence relation T(n) = T(n-1) + T(n-2) + ..., which approximates exponential growth with base 2.

---

**Key takeaway:** Constant branching → n^n, Decreasing branching → 2^n (or similar small base)


---------------

# 1D Dynamic Programming Approaches

This guide outlines common techniques for solving one-dimensional dynamic programming problems, including standard DP, greedy strategies, and special greedy patterns like Kadane’s Algorithm.

---

## Type 1: Standard DP – Brute Force, Memoization, Bottom-Up

These problems typically involve overlapping subproblems and optimal substructure.

- **Brute Force**: Try all recursive paths.
- **Memoization (Top-Down DP)**: Use a cache (e.g. `Map` or array) to avoid recalculating subproblems.
- **Bottom-Up DP**: Create a `dp` array and fill it iteratively, usually from the base case up to the target.

### 🔹 Example – Coin Change (Minimum Coins)
- **Brute Force**: Try all combinations to reach the target.
- **Memoization**: Cache results for each amount to prevent recomputation.
- **Bottom-Up**: Build `dp[amount]` from `0` to the target using the smallest number of coins.

---

## Type 2: Greedy + Binary Search

Greedy solutions differ from DP by making **locally optimal** decisions at each step without trying all options.

✅ Use only if the problem satisfies the **greedy choice property** (local choices lead to global optimum).

### 🔹 Example – Longest Increasing Subsequence (LIS)
- **Brute Force**: Try all subsequences – `O(2^N)`.
- **Memoization**: Cache longest subsequence length per index – `O(N^2)`.
- **Greedy + Binary Search**:
    - Maintain an array (`tails`) representing the smallest possible tail of an increasing subsequence of each length.
    - Use binary search to replace or append.
    - Time complexity: `O(N log N)`.

---

## Type 2B: Kadane’s Algorithm (Greedy for Subarrays)

Kadane’s Algorithm is a greedy technique specifically for maximum subarray problems.

- Track `currentSum`: max subarray sum ending at current index.
- Track `globalMax`: max of all `currentSum` values so far.
- Decide at each step whether to extend the current subarray or start fresh.

### 🔹 Example – Maximum Subarray Sum
- Handles negative numbers by resetting the subarray when `currentSum` drops below zero.
- Runs in linear time: `O(N)`.

---

## Summary

| Type     | Strategy                            | Use Case                                | Time     |
|----------|-------------------------------------|------------------------------------------|----------|
| Type 1   | Brute → Memoization → Bottom-Up DP  | Overlapping subproblems                  | `O(N^2)` |
| Type 2   | Greedy + Binary Search              | Problems with greedy choice property     | `O(N log N)` |
| Type 2B  | Kadane’s Algorithm                  | Maximum subarray sum (special greedy)    | `O(N)`   |



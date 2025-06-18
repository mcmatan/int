# Dynamic Programming Patterns

## 1. Linear Recurrence DP (Fibonacci-style)

- **Method in list**: `fib`
- **Idea**: `dp[i]` depends on a fixed number of previous indices (often 1 or 2)

**Example Questions:**
- [Climbing Stairs (LeetCode 70)](https://leetcode.com/problems/climbing-stairs)
- [Min Cost Climbing Stairs (LeetCode 746)](https://leetcode.com/problems/min-cost-climbing-stairs)
- [House Robber I (198)](https://leetcode.com/problems/house-robber)  
- [House Robber II (213)](https://leetcode.com/problems/house-robber-ii)  
- [Decode Ways (91)](https://leetcode.com/problems/decode-ways)

---

## 2. Grid Path DP (2D Lattice)

- **Method in list**: `gridTraveler`
- **Idea**: States are grid cells `(r, c)`; moves usually go right or down

**Example Questions:**
- [Unique Paths (62)](https://leetcode.com/problems/unique-paths)
- [Unique Paths II (63)](https://leetcode.com/problems/unique-paths-ii)
- [Minimum Path Sum (64)](https://leetcode.com/problems/minimum-path-sum)
- [Number of Islands (200)](https://leetcode.com/problems/number-of-islands) – variant for counting connected regions

---

## 3. Unbounded Subset Sum / Coin Change DP

- **Methods in list**: `canSum`, `howSum`, `bestSum`
- **Idea**: Given a target, choose unlimited items from a set of numbers  
  - `canSum`: decision (yes/no)  
  - `howSum`: construct any one combination  
  - `bestSum`: construct the shortest combination  

**Example Questions:**
- [Coin Change (322)](https://leetcode.com/problems/coin-change)
- [Coin Change II (518)](https://leetcode.com/problems/coin-change-ii)
- [Combination Sum (39)](https://leetcode.com/problems/combination-sum)
- [Perfect Squares (279)](https://leetcode.com/problems/perfect-squares)

---

## 4. String Construction DP (Word Break Family)

- **Methods in list**: `canConstruct`, `countConstruct`, `allConstruct`
- **Idea**: Build a target string from a word bank with unlimited reuse  
  - `canConstruct`: decision  
  - `countConstruct`: count ways  
  - `allConstruct`: list all ways  

**Example Questions:**
- [Word Break (139)](https://leetcode.com/problems/word-break)
- [Word Break II (140)](https://leetcode.com/problems/word-break-ii)
- [Restore IP Addresses (93)](https://leetcode.com/problems/restore-ip-addresses)
- [Decode Ways II (639)](https://leetcode.com/problems/decode-ways-ii)

---

## 5. Bounded Subset DP / 0-1 Knapsack

- **Idea**: Choose a subset of items (each at most once) to satisfy a target (sum, weight, etc.)
- **Item reuse**: No
- **Core method**: Bottom-up DP table or top-down with memoization  
- **Memoization** is typically based on `(index, amount)` caching amount reached or amount remaining 



**Example Questions:**
- [0/1 Knapsack (GFG)](https://www.geeksforgeeks.org/0-1-knapsack-problem-dp-10)
- [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum)
- [Target Sum](https://leetcode.com/problems/target-sum)
- [Subset Sum (GFG)](https://www.geeksforgeeks.org/subset-sum-problem-dp-25)
- [Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii)

---
# 1. Climbing Stairs (爬樓梯)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) | Easy | Math, DP, Memoization |

## 題目描述 (Problem)

你正在爬樓梯。需要 `n` 階才能到達樓頂。
每次你可以爬 `1` 階或 `2` 階。
問有多少種不同的方法可以爬到樓頂？

### 例子 (Examples)
```cpp
Input: n = 2
Output: 2
// 1+1 或 2

Input: n = 3
Output: 3
// 1+1+1, 1+2, 2+1
```

---

## 🧠 思考模式 (Thinking Process)

### DP 五步驟

1.  **定義 `dp[i]`：** 爬到第 `i` 階的方法數。
2.  **Base Case：** `dp[1] = 1`, `dp[2] = 2`。
3.  **遞推關係：** 要到第 `i` 階，只能從 `i-1` 階跨 1 步，或從 `i-2` 階跨 2 步。
    *   `dp[i] = dp[i-1] + dp[i-2]`
4.  **遍歷順序：** 從 `3` 到 `n`。
5.  **回傳：** `dp[n]`。

---

## 💻 實作程式碼 (C++)

### 方法一：陣列 DP
```cpp
class Solution {
public:
    int climbStairs(int n) {
        if (n <= 2) return n;

        std::vector<int> dp(n + 1);
        dp[1] = 1;
        dp[2] = 2;

        for (int i = 3; i <= n; ++i) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n];
    }
};
```

### 方法二：空間優化 (只用兩個變數)
```cpp
class Solution {
public:
    int climbStairs(int n) {
        if (n <= 2) return n;

        int prev2 = 1; // dp[i-2]
        int prev1 = 2; // dp[i-1]

        for (int i = 3; i <= n; ++i) {
            int current = prev1 + prev2;
            prev2 = prev1;
            prev1 = current;
        }

        return prev1;
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n)$**
*   **空間複雜度：$O(1)$** (優化版)

---

## 🧪 自我檢測
*   [ ] 這題跟費波那契數列 (Fibonacci) 有什麼關係？ (一模一樣！只是 Base Case 的值可能不同)。

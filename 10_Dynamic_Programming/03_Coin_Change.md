# 3. Coin Change (零錢兌換)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [322. Coin Change](https://leetcode.com/problems/coin-change/) | Medium | Array, DP, BFS |

## 題目描述 (Problem)

給你不同面額的硬幣 `coins` 和一個總金額 `amount`。
計算湊出 `amount` 所需的 **最少硬幣數**。如果無法湊出，回傳 `-1`。
(每種硬幣數量無限)

### 例子 (Examples)
```cpp
Input: coins = [1, 2, 5], amount = 11
Output: 3
// 11 = 5 + 5 + 1

Input: coins = [2], amount = 3
Output: -1
// 無法湊出
```

---

## 🧠 思考模式 (Thinking Process)

### DP 五步驟

1.  **定義 `dp[i]`：** 湊出金額 `i` 所需的最少硬幣數。
2.  **Base Case：** `dp[0] = 0` (湊出 0 元需要 0 個硬幣)。
3.  **遞推關係：** 對於金額 `i`，考慮用每一種硬幣 `coin`：
    *   如果 `i >= coin`，我們可以先湊出 `i - coin`，然後加上這一枚 `coin`。
    *   `dp[i] = min(dp[i], dp[i - coin] + 1)`
4.  **遍歷順序：** 從 `1` 到 `amount`。
5.  **回傳：** 如果 `dp[amount]` 還是初始的「無限大」，回傳 `-1`。否則回傳 `dp[amount]`。

---

## 💻 實作程式碼 (C++)

```cpp
#include <vector>
#include <algorithm>
#include <climits>

class Solution {
public:
    int coinChange(std::vector<int>& coins, int amount) {
        // dp[i] = 湊出金額 i 需要的最少硬幣數
        // 初始化為 amount + 1 (代表「不可能」，比任何合法答案都大)
        std::vector<int> dp(amount + 1, amount + 1);
        
        // Base case
        dp[0] = 0;

        // 遍歷每個金額
        for (int i = 1; i <= amount; ++i) {
            // 嘗試每種硬幣
            for (int coin : coins) {
                if (i >= coin) {
                    dp[i] = std::min(dp[i], dp[i - coin] + 1);
                }
            }
        }

        // 檢查是否有解
        return dp[amount] > amount ? -1 : dp[amount];
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(amount \times n)$** - `n` 是硬幣種類數。
*   **空間複雜度：$O(amount)$** - DP 陣列。

---

## 🧪 自我檢測
*   [ ] 為什麼初始化用 `amount + 1` 而不是 `INT_MAX`？ (避免 `INT_MAX + 1` 溢位)。
*   [ ] 這題跟「完全背包問題」有什麼關係？ (這就是完全背包的一種變形！物品可以無限取，求最少數量而不是最大價值)。

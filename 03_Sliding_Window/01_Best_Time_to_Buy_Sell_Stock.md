# 1. Best Time to Buy and Sell Stock (買賣股票的最佳時機)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | Easy | Array, Sliding Window, DP |

## 題目描述 (Problem)

給你一個陣列 `prices`，其中 `prices[i]` 是某支股票在第 `i` 天的價格。
你只能選擇 **某一天** 買進，並在 **之後的某一天** 賣出。
計算你能獲得的 **最大利潤**。如果沒辦法獲利，就回傳 `0`。

### 例子 (Examples)
```cpp
Example 1:
Input: prices = [7, 1, 5, 3, 6, 4]
Output: 5
// 第 2 天買 (price = 1), 第 5 天賣 (price = 6), profit = 6-1 = 5.

Example 2:
Input: prices = [7, 6, 4, 3, 1]
Output: 0
// 股價一路跌，不買不賣，利潤 = 0.
```

---

## 🧠 思考模式 (Thinking Process)

### 1. 暴力解 (Brute Force)
每一天都當作「買入日」，然後去找後面所有日子裡「賣出價最高」的那天。
*   **缺點：** $O(n^2)$。LeetCode 會 TLE (Time Limit Exceeded) 超時。

### 2. 優化思路 (One Pass - 滑動視窗思維)
這題的關鍵在於：**你不需要真的管「哪一天賣」，你只需要記住「到目前為止，最低的買入價是多少」。**

*   想像你從第一天開始一路往後看價格。
*   你隨時維護兩個變數：
    1.  `minPrice`：**到目前為止，最低的買入價**。
    2.  `maxProfit`：**到目前為止，能賺到的最大利潤**。
*   每看到新一天的價格：
    1.  如果它比 `minPrice` 低 -> 更新 `minPrice`。
    2.  計算「今天賣」的利潤 (`今日價格 - minPrice`)，如果比 `maxProfit` 高 -> 更新 `maxProfit`。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **初始化：** `minPrice` = 無限大 (或第一天的價格)，`maxProfit` = 0。
2.  **遍歷：** 用迴圈掃過每一天的價格 `price`。
3.  **更新最低價：** 如果 `price < minPrice` -> `minPrice = price`。
4.  **計算利潤：** `profit = price - minPrice`。
5.  **更新最大利潤：** 如果 `profit > maxProfit` -> `maxProfit = profit`。
6.  **回傳：** `maxProfit`。

---

## 💻 實作程式碼 (C++)

```cpp
#include <vector>
#include <algorithm> // for std::min, std::max
#include <climits>   // for INT_MAX

class Solution {
public:
    int maxProfit(std::vector<int>& prices) {
        // 1. 初始化
        int minPrice = INT_MAX; // 目前最低買入價
        int maxProfit = 0;      // 目前最大利潤

        // 2. 遍歷每一天的價格
        for (int price : prices) {
            // 3. 更新最低買入價
            if (price < minPrice) {
                minPrice = price;
            }

            // 4. 計算「今天賣」的利潤
            int profit = price - minPrice;

            // 5. 更新最大利潤
            if (profit > maxProfit) {
                maxProfit = profit;
            }
        }

        // 6. 回傳最大利潤
        return maxProfit;
    }
};
```
> **小技巧：** 可以用 `std::min` 和 `std::max` 讓程式更簡潔：
> ```cpp
> minPrice = std::min(minPrice, price);
> maxProfit = std::max(maxProfit, price - minPrice);
> ```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 只掃描一次陣列。
*   **空間複雜度：$O(1)$** - 只用了兩個整數變數。

---

## 🧪 自我檢測
*   [ ] 如果 `prices` 是空的或只有一天，會怎樣？ (迴圈不跑或跑一次，`maxProfit` 維持 0)。
*   [ ] 理解為什麼這是「滑動視窗」思想？ (我們用 `minPrice` 代表「左指標看過的最佳點」，用 `price` 代表「右指標」，視窗從左滑到右)。

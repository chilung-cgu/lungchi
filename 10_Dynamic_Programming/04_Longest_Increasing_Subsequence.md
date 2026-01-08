# 4. Longest Increasing Subsequence (最長遞增子序列)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) | Medium | Array, Binary Search, DP |

## 題目描述 (Problem)

給你一個整數陣列 `nums`，找出其中 **最長嚴格遞增子序列** 的長度。
*   **子序列 (Subsequence)**：從原陣列中刪除一些元素（或不刪除）所得到的新陣列，**順序不變**。

### 例子 (Examples)
```cpp
Input: nums = [10, 9, 2, 5, 3, 7, 101, 18]
Output: 4
// LIS 是 [2, 3, 7, 101] 或 [2, 3, 7, 18] 或 [2, 5, 7, 101]...

Input: nums = [0, 1, 0, 3, 2, 3]
Output: 4
// LIS 是 [0, 1, 2, 3]
```

---

## 🧠 思考模式 (Thinking Process)

### DP 五步驟

1.  **定義 `dp[i]`：** 以 `nums[i]` **結尾** 的最長遞增子序列的長度。
2.  **Base Case：** 每個元素自己就是一個長度為 1 的 LIS -> `dp[i] = 1`。
3.  **遞推關係：** 對於 `nums[i]`，檢查所有在它前面的 `nums[j]` (`j < i`)：
    *   如果 `nums[j] < nums[i]`（`nums[i]` 可以接在 `nums[j]` 後面）：
    *   `dp[i] = max(dp[i], dp[j] + 1)`
4.  **遍歷順序：** 外層 `i` 從 `0` 到 `n-1`，內層 `j` 從 `0` 到 `i-1`。
5.  **回傳：** `dp` 陣列中的最大值。

---

## 💻 實作程式碼 (C++)

### 方法一：標準 DP ($O(n^2)$)
```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int lengthOfLIS(std::vector<int>& nums) {
        int n = nums.size();
        std::vector<int> dp(n, 1); // 每個元素至少自己是一個 LIS

        for (int i = 1; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                if (nums[j] < nums[i]) {
                    dp[i] = std::max(dp[i], dp[j] + 1);
                }
            }
        }

        return *std::max_element(dp.begin(), dp.end());
    }
};
```

### 方法二：Binary Search 優化 ($O(n \log n)$)
這是更進階的解法。維護一個 `tails` 陣列，`tails[i]` 是長度為 `i+1` 的 LIS 的最小結尾。
*   用二分搜尋找到 `nums[i]` 應該替換的位置。

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int lengthOfLIS(std::vector<int>& nums) {
        std::vector<int> tails;

        for (int num : nums) {
            // lower_bound: 找第一個 >= num 的位置
            auto it = std::lower_bound(tails.begin(), tails.end(), num);
            
            if (it == tails.end()) {
                tails.push_back(num); // num 比所有都大，延長 LIS
            } else {
                *it = num; // 替換，讓 LIS 的結尾更小（更有潛力）
            }
        }

        return tails.size();
    }
};
```

### 複雜度分析
*   **DP 解法：時間 $O(n^2)$，空間 $O(n)$**
*   **Binary Search 解法：時間 $O(n \log n)$，空間 $O(n)$**

---

## 🧪 自我檢測
*   [ ] `dp[i]` 為什麼定義為「以 `nums[i]` **結尾**」而不是「到 `nums[i]` 為止」？ (這樣定義才能方便地從前面的 `dp[j]` 轉移過來，因為我們知道 `j` 的結尾是什麼)。
*   [ ] 為什麼最後要取 `max_element` 而不是直接回傳 `dp[n-1]`？ (因為最長的 LIS 不一定以最後一個元素結尾)。

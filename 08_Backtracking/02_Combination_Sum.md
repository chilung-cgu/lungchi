# 2. Combination Sum (組合總和)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [39. Combination Sum](https://leetcode.com/problems/combination-sum/) | Medium | Array, Backtracking |

## 題目描述 (Problem)

給你一個不含重複元素的整數陣列 `candidates` 和一個目標值 `target`。
找出 `candidates` 中可以使數字和為 `target` 的所有 **唯一組合**。
**同一個數字可以被無限次選取。**

### 例子 (Examples)
```cpp
Input: candidates = [2, 3, 6, 7], target = 7
Output: [[2, 2, 3], [7]]

Input: candidates = [2, 3, 5], target = 8
Output: [[2, 2, 2, 2], [2, 3, 3], [3, 5]]
```

---

## 🧠 思考模式 (Thinking Process)

這是 Subsets 的變形，差別在於：
1.  我們有一個 **目標和**，要在達到目標時才加入結果。
2.  同一個元素 **可以重複選取**。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **定義 `backtrack(start, currentSum, path)`**。
2.  **Base Case：**
    *   如果 `currentSum == target` -> 找到一組解，加入結果，`return`。
    *   如果 `currentSum > target` -> 超過了，剪枝，`return`。
3.  **遍歷 `i` 從 `start` 到 `candidates.size()`：**
    *   **做選擇：** `path.push_back(candidates[i])`，`currentSum += candidates[i]`。
    *   **遞迴：** `backtrack(i, ...)` (注意這裡是 `i` 不是 `i+1`，因為可以重複選)。
    *   **撤銷選擇：** `path.pop_back()`，`currentSum -= candidates[i]`。

---

## 💻 實作程式碼 (C++)

```cpp
#include <vector>

class Solution {
public:
    std::vector<std::vector<int>> result;

    std::vector<std::vector<int>> combinationSum(std::vector<int>& candidates, int target) {
        std::vector<int> path;
        backtrack(candidates, target, 0, 0, path);
        return result;
    }

private:
    void backtrack(std::vector<int>& candidates, int target, int start, int currentSum, std::vector<int>& path) {
        // Base case: 超過目標，剪枝
        if (currentSum > target) {
            return;
        }

        // Base case: 達到目標
        if (currentSum == target) {
            result.push_back(path);
            return;
        }

        // 遍歷選擇
        for (int i = start; i < candidates.size(); ++i) {
            // 做選擇
            path.push_back(candidates[i]);

            // 遞迴 (i 不是 i+1，因為可以重複選)
            backtrack(candidates, target, i, currentSum + candidates[i], path);

            // 撤銷選擇
            path.pop_back();
        }
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n^{target/min})$** - 複雜度取決於遞迴樹的深度和分支。
*   **空間複雜度：$O(target/min)$** - 遞迴深度。

---

## 🧪 自我檢測
*   [ ] 為什麼遞迴時是 `i` 而不是 `i + 1`？ (因為題目說同一個數字可以重複選)。
*   [ ] 如果 `candidates` 本身有重複怎麼辦？ (這題保證沒有，但如果有就需要先排序再跳過重複，參考 Combination Sum II)。

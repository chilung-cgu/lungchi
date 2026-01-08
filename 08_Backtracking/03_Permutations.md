# 3. Permutations (全排列)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [46. Permutations](https://leetcode.com/problems/permutations/) | Medium | Array, Backtracking |

## 題目描述 (Problem)

給你一個不含重複數字的整數陣列 `nums`，回傳 **所有可能的排列**。

### 例子 (Examples)
```cpp
Input: nums = [1, 2, 3]
Output: [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]]
```

---

## 🧠 思考模式 (Thinking Process)

### 與子集 (Subsets) 的差別
*   **子集**：元素順序不重要，`[1,2]` 和 `[2,1]` 是同一個。
*   **排列**：元素順序很重要，`[1,2]` 和 `[2,1]` 是不同的。

### 策略
因為順序重要，我們每次選擇都要從**所有未使用**的元素中挑選（而不是只看後面的）。這需要一個 `used` 陣列來追蹤哪些數字已經被用過。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **定義 `backtrack(path, used)`**。
2.  **Base Case：** 如果 `path.size() == nums.size()` -> 一個完整排列，加入結果。
3.  **遍歷 `i` 從 `0` 到 `nums.size()` (注意，不是 start！)：**
    *   如果 `used[i] == true` -> 這個數字用過了，`continue`。
    *   **做選擇：** `path.push_back(nums[i])`，`used[i] = true`。
    *   **遞迴：** `backtrack(path, used)`。
    *   **撤銷選擇：** `path.pop_back()`，`used[i] = false`。

---

## 💻 實作程式碼 (C++)

```cpp
#include <vector>

class Solution {
public:
    std::vector<std::vector<int>> result;

    std::vector<std::vector<int>> permute(std::vector<int>& nums) {
        std::vector<int> path;
        std::vector<bool> used(nums.size(), false); // 記錄哪些數字已被使用
        backtrack(nums, path, used);
        return result;
    }

private:
    void backtrack(std::vector<int>& nums, std::vector<int>& path, std::vector<bool>& used) {
        // Base case: 排列完成
        if (path.size() == nums.size()) {
            result.push_back(path);
            return;
        }

        // 遍歷所有選擇 (注意：從 0 開始，不是 start)
        for (int i = 0; i < nums.size(); ++i) {
            // 剪枝：如果這個數字已經用過
            if (used[i]) {
                continue;
            }

            // 做選擇
            path.push_back(nums[i]);
            used[i] = true;

            // 遞迴
            backtrack(nums, path, used);

            // 撤銷選擇
            path.pop_back();
            used[i] = false;
        }
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n \times n!)$** - 共有 $n!$ 個排列，每個排列需要 $O(n)$ 時間複製。
*   **空間複雜度：$O(n)$** - 遞迴深度 + `used` 陣列。

---

## 🔍 子集 vs 組合 vs 排列 比較

| 問題類型 | 元素可否重複使用 | 順序是否重要 | 起始索引 |
| :--- | :---: | :---: | :--- |
| **Subsets** | 否 | 否 | `start = i + 1` |
| **Combination Sum** | 是 | 否 | `start = i` |
| **Permutations** | 否 | **是** | `start = 0` + `used[]` |

---

## 🧪 自我檢測
*   [ ] 為什麼排列問題的迴圈從 `i = 0` 開始？ (因為每個位置都可以選任何未使用的數)。
*   [ ] 為什麼需要 `used` 陣列？ (用來追蹤哪些數字已經在當前路徑中，避免重複選)。

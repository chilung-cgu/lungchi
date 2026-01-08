# 2. Top K Frequent Elements (前 K 高頻元素)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) | Medium | Array, Hash Table, Heap, Bucket Sort |

## 題目描述 (Problem)

給你一個整數陣列 `nums` 和一個整數 `k`，回傳出現頻率前 k 高的元素。

### 例子 (Examples)
```cpp
Input: nums = [1, 1, 1, 2, 2, 3], k = 2
Output: [1, 2]
// 1 出現 3 次，2 出現 2 次。

Input: nums = [1], k = 1
Output: [1]
```

---

## 🧠 思考模式 (Thinking Process)

### 兩階段
1.  **統計頻率：** 用 Hash Map 統計每個數字出現的次數。
2.  **找出前 k 高：** 用 **最小堆 (Min-Heap)** 維護前 k 高頻的元素 (跟上一題概念一樣)。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **統計頻率：** `unordered_map<int, int> count`。
2.  **建立最小堆：** 堆的元素是 `(frequency, number)` 的 pair，按 frequency 排序。
3.  **遍歷 count：**
    *   `heap.push({freq, num})`。
    *   如果 `heap.size() > k`，`heap.pop()`。
4.  **收集結果：** 把堆裡的 `num` 取出，放進結果陣列。

---

## 💻 實作程式碼 (C++)

```cpp
#include <vector>
#include <unordered_map>
#include <queue>

class Solution {
public:
    std::vector<int> topKFrequent(std::vector<int>& nums, int k) {
        // 1. 統計頻率
        std::unordered_map<int, int> count;
        for (int num : nums) {
            count[num]++;
        }

        // 2. 最小堆 (按頻率排序)
        // pair: {frequency, number}
        // 最小堆: greater<pair<...>> 讓頻率最小的在頂端
        std::priority_queue<
            std::pair<int, int>,
            std::vector<std::pair<int, int>>,
            std::greater<std::pair<int, int>>
        > minHeap;

        // 3. 遍歷頻率表
        for (auto& [num, freq] : count) {
            minHeap.push({freq, num});
            if (minHeap.size() > k) {
                minHeap.pop();
            }
        }

        // 4. 收集結果
        std::vector<int> result;
        while (!minHeap.empty()) {
            result.push_back(minHeap.top().second); // 取 number
            minHeap.pop();
        }

        return result;
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n \log k)$** - 統計 $O(n)$，堆操作 $O(m \log k)$，其中 $m$ 是不同元素的數量。
*   **空間複雜度：$O(n)$** - 頻率表。

---

## ✨ 補充：桶排序 (Bucket Sort) 解法 - $O(n)$

這是更高級的解法，面試時可以提出來加分。
*   頻率最高是 `n` (全部一樣)。建立 `n+1` 個桶，`bucket[i]` 存「出現 `i` 次」的數字。
*   從最高頻的桶往下掃，收集前 k 個。

```cpp
std::vector<int> topKFrequent_BucketSort(std::vector<int>& nums, int k) {
    std::unordered_map<int, int> count;
    for (int num : nums) count[num]++;
    
    std::vector<std::vector<int>> bucket(nums.size() + 1);
    for (auto& [num, freq] : count) {
        bucket[freq].push_back(num);
    }
    
    std::vector<int> result;
    for (int i = bucket.size() - 1; i >= 0 && result.size() < k; --i) {
        for (int num : bucket[i]) {
            result.push_back(num);
            if (result.size() == k) break;
        }
    }
    return result;
}
```

---

## 🧪 自我檢測
*   [ ] 為什麼堆裡存的是 `{freq, num}` 而不是 `{num, freq}`？ (因為 `pair` 預設按第一個元素排序，我們想按頻率排序)。

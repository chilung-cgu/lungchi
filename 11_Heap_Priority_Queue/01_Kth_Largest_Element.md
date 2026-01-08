# 1. Kth Largest Element in an Array (陣列中第 K 大的元素)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/) | Medium | Array, Heap, Quickselect |

## 題目描述 (Problem)

給你一個整數陣列 `nums` 和一個整數 `k`，回傳陣列中 **第 k 大** 的元素。
(注意不是第 k 個不重複的元素)

### 例子 (Examples)
```cpp
Input: nums = [3, 2, 1, 5, 6, 4], k = 2
Output: 5
// 排序後: [1, 2, 3, 4, 5, 6]，第 2 大是 5。

Input: nums = [3, 2, 3, 1, 2, 4, 5, 5, 6], k = 4
Output: 4
```

---

## 🧠 思考模式 (Thinking Process)

### 方法一：排序 (Sorting)
把陣列排序，然後回傳 `nums[n - k]`。
*   時間複雜度：$O(n \log n)$。

### 方法二：堆積 (Heap / Priority Queue)
維護一個 **大小為 k 的最小堆 (Min-Heap)**。
*   遍歷陣列，把每個元素放進堆裡。
*   如果堆的大小超過 `k`，就把最小的踢掉 (`pop`)。
*   最後，堆頂 (`top`) 就是第 k 大的元素。

**為什麼是最小堆？**
因為我們要的是「前 k 大」的元素。當堆滿了（大小為 k），如果新來的元素比堆頂大，它應該進來（把最小的踢掉）。最後堆頂就是「前 k 大裡面最小的那個」= 第 k 大。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **建立最小堆：** C++ 的 `priority_queue` 預設是最大堆，要用 `greater<int>` 變成最小堆。
2.  **遍歷 nums：**
    *   `heap.push(num)`。
    *   如果 `heap.size() > k`，`heap.pop()`。
3.  **回傳 `heap.top()`。**

---

## 💻 實作程式碼 (C++)

```cpp
#include <vector>
#include <queue> // for std::priority_queue

class Solution {
public:
    int findKthLargest(std::vector<int>& nums, int k) {
        // 1. 最小堆 (Min-Heap)
        // priority_queue<Type, Container, Comparator>
        // greater<int> 讓最小的在頂端
        std::priority_queue<int, std::vector<int>, std::greater<int>> minHeap;

        // 2. 遍歷
        for (int num : nums) {
            minHeap.push(num);

            // 維持堆的大小為 k
            if (minHeap.size() > k) {
                minHeap.pop(); // 踢掉最小的
            }
        }

        // 3. 堆頂就是第 k 大
        return minHeap.top();
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n \log k)$** - 對每個元素，堆的操作是 $O(\log k)$。
*   **空間複雜度：$O(k)$** - 堆的大小。

---

## 🧪 自我檢測
*   [ ] 為什麼用最小堆而不是最大堆？ (我們想保留「最大的 k 個」。用最小堆，堆頂是 k 個裡最小的，方便踢掉不夠大的)。
*   [ ] C++ `priority_queue` 的預設行為是什麼？ (預設是最大堆，最大的在頂端)。

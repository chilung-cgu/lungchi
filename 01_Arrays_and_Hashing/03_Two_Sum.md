# 3. Two Sum (兩數之和) (⭐經典必考)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [1. Two Sum](https://leetcode.com/problems/two-sum/) | Easy | Array, Hash Table |

## 題目描述 (Problem)

給你一個整數陣列 `nums` 和一個目標值 `target`。
請你在陣列中找出**兩個**數字，加起來剛好等於 `target`。
回傳這兩個數字的**索引 (Index)**。

(題目保證：剛好只會有一組解。你不能用同一個元素兩次。)

### 例子 (Examples)
```python
Example 1:
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
# 因為 nums[0] + nums[1] == 2 + 7 == 9

Example 2:
Input: nums = [3, 2, 4], target = 6
Output: [1, 2]
# 因為 nums[1] + nums[2] == 2 + 4 == 6
```

---

## 🧠 思考模式 (Thinking Process)

### 1. 暴力解 (Brute Force)
*   我拿第 1 個數字 `2`，然後去問後面的 `7, 11, 15`：「你是我的另一半嗎？」(另一半 = 9 - 2 = 7)。
    *   問到 `7`，Bingo！
*   如果沒問到，就換第 2 個數字 `7`，去問後面的 `11, 15`...
*   **缺點：** 又是巢狀迴圈，**$O(n^2)$**。太慢了。

### 2. 優化思路 (HashMap: 空間換時間)
這題的關鍵在於：**我不想「往後找」**。
我想做的是：**當我看到一個數字時，我能不能馬上知道「我需要的另一半」以前有沒有出現過？**

舉例：`nums = [2, 1, 5, 3]`, `target = 4`

1.  看到 `2`。我要找 `4-2=2`。記憶庫(Map)是空的。好吧，把 `2` 存進去：`{2: index_0}`。
2.  看到 `1`。我要找 `4-1=3`。記憶庫裡有 `3` 嗎？沒有。把 `1` 存進去：`{2: 0, 1: index_1}`。
3.  看到 `5`。我要找 `4-5=-1`。記憶庫有 `-1` 嗎？沒有。存進去...
4.  看到 `3`。我要找 `4-3=1`。記憶庫有 `1` 嗎？**有！** (在 index_1)。
    *   我現在是 `index_3`，記憶庫告訴我另一半在 `index_1`。
    *   答案就是 `[1, 3]`。

> **核心觀念：** 前面的人負責「儲存自己」，後面的人負責「回頭查表」。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **準備地圖：** 建立一個空的 Hash Map 叫 `prevMap`。用來存 `{ 數值 : 它的索引 }`。
2.  **遍歷陣列：** 用 `enumerate` 同時拿到 `索引 (i)` 和 `數值 (n)`。
3.  **計算另一半：** `diff = target - n`。
4.  **查表：**
    *   如果 `diff` 在 `prevMap` 裡面 -> 找到了！
        *   回傳 `[prevMap[diff], i]` (注意：先回傳舊的索引，再回傳現在的)。
    *   如果不在 -> 把現在這個數字存進去 `prevMap[n] = i`。

---

## 💻 實作程式碼 (Python)

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        # 1. 準備地圖 { value : index }
        prevMap = {}
        
        # 2. 遍歷 nums (拿到 index i 和 value n)
        for i, n in enumerate(nums):
            # 3. 計算我要找的另一半
            diff = target - n
            
            # 4. 查表：另一半之前出現過嗎？
            if diff in prevMap:
                # 找到了！回傳 [另一半的index, 這裡的index]
                return [prevMap[diff], i]
            
            # 沒找到，把自己存進去，等待有緣人
            prevMap[n] = i
            
        return [] # 題目保證有解，這行通常跑不到
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 我們只掃描了陣列一次。每次查表都是 $O(1)$。
*   **空間複雜度：$O(n)$** - 最壞情況下，我們會把把所有數字都存進 Map 裡。

---

## 🧪 自我檢測
*   [ ] 為什麼回傳的時候，`prevMap[diff]` 要放在前面？ (因為 `prevMap` 存的是**之前**看過的，也就是比較早的 index)。
*   [ ] 為什麼不用 `sort` (排序)？
    *   這題要回傳 **Index (索引)**。如果你把陣列排序了，原本的索引就亂掉了！(雖然可以用 Pair 存原本索引再來排，但那樣就是 $O(n \log n)$，比 Hash Map $O(n)$ 慢)。

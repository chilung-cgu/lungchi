# 1. Contains Duplicate (含有重複元素)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) | Easy | Array, Hash Table |

## 題目描述 (Problem)

給你一個整數陣列 `nums`。
- 如果任何一個數字在陣列中出現 **至少兩次**，回傳 `true`。
- 如果每個數字都 **獨一無二**，回傳 `false`。

### 例子 (Examples)
```python
Example 1:
Input: nums = [1, 2, 3, 1]
Output: true
# 解釋: 1 出現了兩次。

Example 2:
Input: nums = [1, 2, 3, 4]
Output: false
# 解釋: 每個數字都只出現一次。

Example 3:
Input: nums = [1, 1, 1, 3, 3, 4, 3, 2, 4, 2]
Output: true
```

---

## 🧠 思考模式 (Thinking Process)

### 1. 直觀想法 (Brute Force - 暴力解)
我們的直覺是：拿第 1 個數字，跟後面所有數字比。如果有相同的，就抓到了！
然後拿第 2 個數字，跟後面所有數字比...

*   **模擬：** `[1, 2, 3, 1]`
    *   拿第 1 個 `1`，看後面：是 `2`? 不對。是 `3`? 不對。是 `1`? **對！抓到了！** -> Return True。

*   **缺點：**
    *   如果陣列很長，比如有 10000 個數字。
    *   第 1 個要比 9999 次。
    *   第 2 個要比 9998 次。
    *   ...
    *   這就是我們剛學的 **$O(n^2)$** 龜速解法。在這個年代，這是會被面試官打槍的。

### 2. 優化思路 (Optimization - 以空間換時間)
我們能不能看過一個數字，就把它「記」在一個本子上？
下次看到新數字時，先查本子：「欸，這個數字我之前看過嗎？」

*   **這個「本子」是什麼？**
    *   在程式裡，查找速度最快的本子叫做 **Hash Set (哈希集合)**。
    *   在 Python 裡叫 `set`。
    *   Set 的特異功能是：**問它「這東西在不在裡面？」只需要 $O(1)$ 的瞬間時間。**

---

## 📝 微步驟計畫 (Micro-Steps)

請在寫程式前，先在腦中或紙上確認這三個步驟：

1.  **準備本子：** 建立一個空的 `seen` set。
2.  **一一檢查：** 寫個迴圈，把 `nums` 裡的數字一個一個拿出來 (叫它 `n`)。
3.  **查找與紀錄：**
    *   問 `seen`：`n` 在不在裡面？
    *   **在** -> 抓到了！立刻回傳 `True`。
    *   **不在** -> 把 `n` 寫進 `seen` 裡，繼續看下一個。
4.  **最後防線：** 如果迴圈跑完了都沒抓到重複 -> 代表大家都獨一無二 -> 回傳 `False`。

---

## 💻 實作程式碼 (Python)

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        # 1. 準備本子 (HashSet)
        seen = set()
        
        # 2. 一一檢查
        for n in nums:
            # 3. 查找: 如果這個數字已經在本子裡
            if n in seen:
                return True # 抓到了，有重複！
            
            # 沒看過，把它記本子裡
            seen.add(n)
            
        # 4. 最後防線：全部都看完了也都沒事
        return False
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 我們只需要把陣列從頭到尾看一遍。
*   **空間複雜度：$O(n)$** - 最壞情況下（都沒有重複），我們的 `seen` set 會存下所有的 `n` 個數字。

---

## 🔍 另一種Python流 (One-Liner)

Python 的 `set` 還有一個特性：它**自動會把重複的濾掉**。
所以如果 `len(set(nums))` (去重後的長度) 比 `len(nums)` (原本長度) 短，就代表原本一定有重複！

```python
class Solution:
    def containsDuplicate(self, nums: list[int]) -> bool:
        # 如果去重後的長度 < 原本長度，代表有重複元素被殺掉了
        return len(set(nums)) < len(nums)
```
> **注意：** 面試時建議先寫第一種解法，這顯示你懂演算法邏輯。第二種是 Python 特有的炫技，雖然簡潔，但比較看不出邏輯 (Under the hood)。第一種比較通用 (C++, Java, Go 都長那樣)。

---

## 🧪 自我檢測 (Verification)
*   [ ] 如果 `nums = []` (空陣列) 會怎樣？ -> 迴圈不執行，直接回傳 False。正確。
*   [ ] 如果 `nums = [1]` (只有一個) 會怎樣？ -> 迴圈跑一次，`seen` 變 `{1}`，回傳 False。正確（一個數字不可能重複）。
*   [ ] 真的理解了 Hash Set 為什麼是 $O(1)$ 嗎？ (就像鑰匙插進門鎖，配對是瞬間的)。

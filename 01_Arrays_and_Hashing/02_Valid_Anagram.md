# 2. Valid Anagram (有效的字母異位詞)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/) | Easy | Array, Hash Table, String |

## 題目描述 (Problem)

給你兩個字串 `s` 和 `t`。
請判斷 `t` 是否為 `s` 的 **字母異位詞 (Anagram)**、
*   **定義**：字母異位詞是指兩個字串用的「字母」和「數量」完全一樣，只是順序不同。
*   (也就是說：把 `s` 重新排列組合，能不能變成 `t`？)

### 例子 (Examples)
```python
Example 1:
Input: s = "anagram", t = "nagaram"
Output: true
# 兩邊都有 3個a, 1個n, 1個g, 1個r, 1個m.

Example 2:
Input: s = "rat", t = "car"
Output: false
# 'r', 'a' 都有，但 't' 和 'c' 對不上。
```

---

## 🧠 思考模式 (Thinking Process)

### 1. 也是直觀想法 (Sorting - 排序)
既然內容物要一樣，那如果我把它們都「重新整理」過，應該要變一模一樣吧？
`"rat"` -> 排序 -> `"art"`
`"car"` -> 排序 -> `"acr"`
`"art" != "acr"` -> False

*   **優點**：程式碼超短。
*   **缺點**：排序的時間複雜度是 **$O(n \log n)$**。這比 $O(n)$ 慢了一點點。雖然面試通常接受，但我們可以做得更好。

### 2. 優化思路 (Frequency Map - 計數器)
我們可以做一個「計數板」。
*   讀 `s` 的時候：看到一個字母，就在計數板上 +1。
*   讀 `t` 的時候：看到一個字母，就在計數板上 -1。
*   **最後檢查**：如果計數板上所有數字都剛好是 **0**，代表兩邊完全抵銷，不多也不少！

> 這個「計數板」可以用 **Hash Map** (Python dict) 來實作。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **快速過濾：** 先看 `len(s)` 是否等於 `len(t)`。如果不一樣長，直接回傳 `False` (完全不用比了)。
2.  **準備計數板：** 建立兩個 Hash Map (或者一個也可以，這邊為了簡單直觀，我們先用兩個，或者用 Python 的 Counter 神器)。
    *   *為了教學清楚，我們手動用一個 Map 來做加減法。*
    *   建立空字典 `countS`。
3.  **填寫計數板 (S)：** 遍歷 `s`，把每個字母出現次數記下來。
4.  **扣除計數板 (T)：** 遍歷 `t`，把對應字母的次數減掉 (或者檢查 `t` 的計數是否跟 `s` 一樣)。
5.  **比對：** 最後確認是否吻合。

---

## 💻 實作程式碼 (Python)

### 方法一：Hash Map (通用手刻版)
這是最標準的面試寫法，展示你懂 Hash Table 操作。

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        # 1. 長度不同，直接出局
        if len(s) != len(t):
            return False
            
        # 2. 準備兩個計數板 (字典)
        # 這裡我們展示 "比較兩個字典是否相等" 的邏輯，這最直觀
        countS, countT = {}, {}
        
        # 3. 統計 s 和 t
        for i in range(len(s)):
            # s[i] 是 s 目前的字母，t[i] 是 t 目前的字母
            # .get(key, 0) 的意思是：如果 key 存在就拿值，不存在就預設給 0
            countS[s[i]] = countS.get(s[i], 0) + 1
            countT[t[i]] = countT.get(t[i], 0) + 1
            
        # 4. 比較兩個計數板
        # 在 Python 裡，可以直接比較兩個字典的內容是否一樣
        return countS == countT
```

### 方法二：Array (陣列計數 - 進階優化)
如果題目說 **「只有小寫英文 a-z」**，我們可以用一個長度 26 的陣列來代替 Hash Map。陣列通常比 Map 更快一點 (因為不用算 Hash Function)。
*   `a` 對應索引 `0`, `b` 對應 `1`...
*   這需要用到 ASCII 碼轉換：`ord(char) - ord('a')`。

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t): return False
        
        # 準備一個長度 26 的陣列，全部填 0
        counter = [0] * 26
        
        for i in range(len(s)):
            # s 的字母讓計數器 +1
            counter[ord(s[i]) - ord('a')] += 1
            # t 的字母讓計數器 -1 (抵銷)
            counter[ord(t[i]) - ord('a')] -= 1
            
        # 最後檢查計數器是不是全部都歸零了
        for count in counter:
            if count != 0:
                return False
                
        return True
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 我們遍歷了字串一次。 (`s` 和 `t` 各一次，所以是 $2n$，但在 Big O 裡常數不計，所以是 $O(n)$)。
*   **空間複雜度：$O(1)$** - 蛤？為什麼是 $O(1)$？
    *   因為字母最多只有 26 個！不管字串長度是 1 萬還是 1 億，我們的 `count` 陣列永遠只有 26 格。這被稱為**固定空間**，所以視為 $O(1)$。如果是用 Hash Map 且支援 Unicode (所有字元)，那就是 $O(n)$。

---

## 🧪 自我檢測
*   [ ] 兩個字串長度不一樣時有擋掉嗎？
*   [ ] 理解為什麼用 `countS == countT` 就可以比對嗎？
*   [ ] 知道 `ord('a')` 是什麼意思嗎？ (它是字元轉數字的函式，方便我們把字母映射到 0~25 的索引)。

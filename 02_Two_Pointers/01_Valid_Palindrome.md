# 1. Valid Palindrome (驗證回文串)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) | Easy | Two Pointers, String |

## 題目描述 (Problem)

給你一個字串 `s`。
如果在將所有**大寫字母轉為小寫**、並**移除非字母數字字符** (比如空格、逗號) 之後，它正這讀和反著讀是一樣的，那麼它就是一個 **回文 (Palindrome)**。
是的話回傳 `true`，否則 `false`。

### 例子 (Examples)
```python
Example 1:
Input: s = "A man, a plan, a canal: Panama"
Output: true
# 解釋: 整理後變成 "amanaplanacanalpanama"。正反讀都一樣。

Example 2:
Input: s = "race a car"
Output: false
# 解釋: "raceacar" != "racaecar"
```

---

## 🧠 思考模式 (Thinking Process)

### 1. 直觀想法 (Reverse String)
把字串整理乾淨，然後跟「反轉後的自己」比較。如果相等就是回文。
*   `cleaned_s == cleaned_s[::-1]`
*   這招很有用，但在面試時，考官通常希望你**不要用額外的空間** (Space Complexity $O(1)$)。如果字串很長，複製一份反轉的字串會佔用記憶體。

### 2. 資料結構 (Two Pointers - 雙指標夾殺)
我們可以想像有兩根手指頭。
*   左手 (`L`) 指著字串最前面。
*   右手 (`R`) 指著字串最後面。
*   **比較：** 左手看到的字，跟右手看到的字，是不是一樣？
    *   **一樣：** 左手往右移一格，右手往左移一格 (縮小範圍)。
    *   **不一樣：** 立刻抓包！回傳 `False`。
*   **停止條件：** 當左手跟右手**相遇**或是**交錯**的時候，代表檢查完畢 -> 回傳 `True`。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **左邊界與右邊界：** 設定 L = 0, R = 最後一個字的索引。
2.  **雖然 L 還沒撞到 R (while L < R)：**
    *   **跳過垃圾：**
        *   如果 L 指的不是字母/數字 -> L 往右移 (`L+=1`)。
        *   如果 R 指的不是字母/數字 -> R 往左移 (`R-=1`)。
    *   **比對：**
        *   都乾淨了，把兩邊都轉小寫 (`.lower()`)。
        *   如果不一樣 -> 回傳 `False`。
    *   **縮圈：** `L += 1`, `R -= 1`。
3.  **成功通關：** 迴圈順利結束 -> 回傳 `True`。

---

## 💻 實作程式碼 (Python)

這題有兩種寫法，一種是從頭寫這個邏輯(Two Pointers)，一種是先寫好的 helper function 來清理字串。為了練習演算法，我們推薦練習 **方法二 (In-place Two Pointers)**。

### 方法一：偷吃步 (先整理字串)
這會用到額外空間 $O(n)$，但邏輯超簡單。
```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        new_s = ""
        for c in s:
            if c.isalnum(): # 檢查是不是字母或數字 (alpha-numeric)
                new_s += c.lower()
        return new_s == new_s[::-1]
```

### 方法二：正宗雙指標 (Two Pointers)
省空間 $O(1)$，面試官的最愛。

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        # 1. 左右護法
        L, R = 0, len(s) - 1
        
        while L < R:
            # 2a. 左邊如果不乾淨，往右跳 (注意 L < R 邊界，不然會噴出去)
            while L < R and not self.alphaNum(s[L]):
                L += 1
            
            # 2b. 右邊如果不乾淨，往左跳
            while L < R and not self.alphaNum(s[R]):
                R -= 1
            
            # 3. 比對 (記得轉小寫)
            if s[L].lower() != s[R].lower():
                return False
            
            # 4. 縮圈
            L, R = L + 1, R - 1
            
        return True

    # 小幫手：檢查是否為字母或數字
    # 也可以直接用 Python 的 c.isalnum()，這裡寫出來是為了讓你知道原理
    def alphaNum(self, c):
        return (ord('A') <= ord(c) <= ord('Z') or 
                ord('a') <= ord(c) <= ord('z') or 
                ord('0') <= ord(c) <= ord('9'))
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 左右指標加起來剛好走完一次字串。
*   **空間複雜度：$O(1)$** - 我們只有 L 和 R 兩個變數，沒有創造新字串。

---

## 🧪 自我檢測
*   [ ] 為什麼內部的 `while` 迴圈也要檢查 `L < R`？ (因為如果字串全是垃圾符號 `",,,,"`，L 會一路衝過頭撞到 R 的右邊，導致 error)。
*   [ ] 如果是空字串 `""` 會怎樣？ (L=0, R=-1，`while L < R` 一開始就是 False，直接回傳 True。符合定義)。

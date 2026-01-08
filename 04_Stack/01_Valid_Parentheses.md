# 1. Valid Parentheses (有效的括號)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/) | Easy | String, Stack |

## 題目描述 (Problem)

給你一個只包含 `'('`, `')'`, `'{'`, `'}'`, `'['`, `']'` 的字串 `s`，判斷它是否有效。

有效的定義：
1.  左括號必須由相同類型的右括號閉合。
2.  左括號必須按照正確的順序閉合。
3.  每個右括號都必須有對應的左括號。

### 例子 (Examples)
```cpp
Example 1:
Input: s = "()"
Output: true

Example 2:
Input: s = "()[]{}"
Output: true

Example 3:
Input: s = "(]"
Output: false

Example 4:
Input: s = "([)]"
Output: false // 順序不對

Example 5:
Input: s = "{[]}"
Output: true // 正確的巢狀
```

---

## 🧠 思考模式 (Thinking Process)

### 為什麼用 Stack (堆疊)？
這是一個 **「後進先出 (LIFO)」** 的問題。
*   最晚打開的括號，必須**最先**被關閉。
*   這完美契合 Stack 的特性。

### 演算法思路
1.  看到**左括號** (`(`, `{`, `[`) -> 推入 (push) Stack。
2.  看到**右括號** (`)`, `}`, `]`) -> 彈出 (pop) Stack 的頂端，看是不是配對的左括號。
    *   如果 Stack 是空的 (沒有東西可 pop) -> 配對失敗。
    *   如果彈出的左括號跟這個右括號不配 -> 配對失敗。
3.  最後，檢查 Stack 是不是空的。如果還有殘留的左括號沒被配對 -> 失敗。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **建立配對表：** 用 Map 存 `{ ')': '(', '}': '{', ']': '[' }`。
2.  **準備 Stack：** 用 `std::stack<char>`。
3.  **遍歷字串：** 對字串裡的每個字元 `c`：
    *   如果 `c` 是**右括號** (在 Map 的 key 裡找得到)：
        *   如果 Stack 是空的，回傳 `false`。
        *   `pop()` 出頂端元素，檢查是否等於 `map[c]` (對應的左括號)。不對就 `false`。
    *   如果 `c` 是**左括號**：
        *   `push(c)`。
4.  **最終檢查：** 回傳 `stack.empty()`。

---

## 💻 實作程式碼 (C++)

```cpp
#include <string>
#include <stack>
#include <unordered_map>

class Solution {
public:
    bool isValid(std::string s) {
        // 1. 建立配對表: 右括號 -> 對應的左括號
        std::unordered_map<char, char> closeToOpen = {
            {')', '('},
            {'}', '{'},
            {']', '['}
        };

        // 2. 準備 Stack
        std::stack<char> stk;

        // 3. 遍歷
        for (char c : s) {
            // 如果 c 是右括號
            if (closeToOpen.count(c)) {
                // Stack 有東西，且頂端是配對的左括號
                if (!stk.empty() && stk.top() == closeToOpen[c]) {
                    stk.pop(); // 配對成功，消掉
                } else {
                    return false; // 配對失敗
                }
            } else {
                // c 是左括號，推入 Stack
                stk.push(c);
            }
        }

        // 4. 最終檢查：Stack 必須是空的
        return stk.empty();
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 掃描一次字串。
*   **空間複雜度：$O(n)$** - 最壞情況 (如 `((((`)，所有字元都進 Stack。

---

## 🧪 自我檢測
*   [ ] 如果字串只有 `"]"` 會怎樣？ (進迴圈，發現是右括號，但 Stack 是空的，回傳 `false`。正確)。
*   [ ] 如果字串只有 `"["` 會怎樣？ (進迴圈，push 進 Stack。迴圈結束，Stack 不為空，回傳 `false`。正確)。

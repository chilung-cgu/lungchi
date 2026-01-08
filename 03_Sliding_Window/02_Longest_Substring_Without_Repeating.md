# 2. Longest Substring Without Repeating Characters (無重複字元的最長子串)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Medium | Hash Table, String, Sliding Window |

## 題目描述 (Problem)

給你一個字串 `s`，請你找出其中 **不含有重複字元** 的 **最長連續子字串** 的長度。

### 例子 (Examples)
```cpp
Example 1:
Input: s = "abcabcbb"
Output: 3
// "abc" 長度是 3。

Example 2:
Input: s = "bbbbb"
Output: 1
// "b" 長度是 1。

Example 3:
Input: s = "pwwkew"
Output: 3
// "wke" 長度是 3。注意答案必須是「連續子串」，不是「子序列」。
```

---

## 🧠 思考模式 (Thinking Process)

### 1. 暴力解
檢查所有可能的子字串，對每個子字串都確認有沒有重複字元。$O(n^3)$。太慢了。

### 2. 優化思路 (Sliding Window)
我們想像有一扇「窗戶」(由左邊界 `L` 和右邊界 `R` 組成)，這扇窗戶裡面的字元都不能重複。

*   **右邊界 `R` 往右移**，把新字元加入窗戶。
*   **如果發現重複**，把 **左邊界 `L` 往右縮**，直到窗戶裡沒有重複。
*   用一個 Set 來追蹤「窗戶裡目前有哪些字元」。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **準備：** `L = 0`，`maxLen = 0`，一個空的 `unordered_set<char> charSet`。
2.  **右指標遍歷：** 用 `R` 從 0 掃到 `s.length() - 1`。
3.  **處理重複：** 如果 `s[R]` 已經在 `charSet` 裡了：
    *   **縮窗**：把 `s[L]` 從 Set 移除，然後 `L++`。持續這個動作，直到 `s[R]` 不再重複。
4.  **加入新字元：** 把 `s[R]` 加入 `charSet`。
5.  **更新最大長度：** `maxLen = max(maxLen, R - L + 1)`。
6.  **回傳：** `maxLen`。

---

## 💻 實作程式碼 (C++)

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();

        if (n <= 1) return n;

        int maxLen = 0;
        int left = 0;
        unordered_map<char, int> prevIdx;

        for (int right = 0; right < n; right++) {
            char curChar = s[right];
            if (prevIdx.find(curChar) != prevIdx.end()) {
                // prevIdx[curChar] + 1
                // left = max(left, prevIdx[curChar]);
                left = max(left, prevIdx[curChar] + 1);
            }
            prevIdx[curChar] = right;
            maxLen = max(maxLen, right - left + 1);
        }
        return maxLen;
    }
};

```
與其在遇到重複時逐字移除字符，不如直接把左指針跳轉到正確的位置。
我們維護一個映射，記錄每個字符最後出現的索引。
當字符重複時，最早有效的起點移動到其上一個出現的下一個位置。
這讓我們能在一步中調整視窗，始終保持有效，讓該方法既快速又干淨。



```cpp
#include <string>
#include <unordered_set>
#include <algorithm> // for std::max

class Solution {
public:
    int lengthOfLongestSubstring(std::string s) {
        // 1. 初始化
        std::unordered_set<char> charSet; // 存放窗戶內的字元
        int L = 0;       // 窗戶左邊界
        int maxLen = 0;  // 最長不重複子串長度

        // 2. 右指標 R 遍歷整個字串
        for (int R = 0; R < s.length(); ++R) {
            
            // 3. 如果 s[R] 是重複的，縮窗 (移動 L)
            while (charSet.count(s[R])) { // count > 0 表示存在
                charSet.erase(s[L]); // 把左邊的字元踢出去
                L++;
            }

            // 4. 把 s[R] 加入集合
            charSet.insert(s[R]);

            // 5. 更新最大長度
            maxLen = std::max(maxLen, R - L + 1);
        }

        return maxLen;
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 左右指標各最多走 n 步，總共 2n 步。
*   **空間複雜度：$O(min(m, n))$** - `m` 是字元集大小 (ASCII 128 / Unicode 更大)，`n` 是字串長度。Set 最多存 min(m, n) 個字元。

---

## 🧪 自我檢測
*   [ ] 為什麼 `while` 而不是 `if`？ (因為可能需要連續移除多個左邊字元才能消除重複)。
*   [ ] 如果字串是空的 `""`，會怎樣？ (迴圈不跑，回傳 0)。

# 5. Word Break (單詞拆分)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [139. Word Break](https://leetcode.com/problems/word-break/) | Medium | Array, Hash Table, String, DP, Trie |

## 題目描述 (Problem Statement)

給你一個字串 `s` 和一個字串字典 `wordDict`。
判斷 `s` 是否可以被拆分成一個或多個在字典中出現的單詞。

**注意：** 字典中的單詞可以被重複使用。

### 例子

```python
Example 1:
Input: s = "leetcode", wordDict = ["leet","code"]
Output: true
解釋: "leetcode" 可以拆分成 "leet" + "code"

Example 2:
Input: s = "applepenapple", wordDict = ["apple","pen"]
Output: true
解釋: "applepenapple" = "apple" + "pen" + "apple"

Example 3:
Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: false
```

---

## 🔍 深度分析

### 問題約束
- `1 <= s.length <= 300`
- `1 <= wordDict.length <= 1000`
- `word` 長度在 `[1, 20]` 範圍
- `s` 和 `word` 只包含小寫英文字母
- `wordDict` 中所有單詞都是唯一的

### 邊界條件
1. **空字串：** `s = ""`，回傳 `true`（可以看作不需要拆分）
2. **字典為空：** `wordDict = []`，`s` 非空則回傳 `false`
3. **s 無法拆分：** 即使有部分匹配也不行

### 核心洞察

這是一個 **決策問題的動態規劃**。

**🤔 如何思考？**

對於字串 `s = "leetcode"`：
- 我可以在任意位置切一刀，分成 `prefix` 和 `suffix`
- 如果 `prefix` 在字典中，且 `suffix` 也能成功拆分 → s 可以拆分

這是一個 **子問題重疊** 的遞迴結構 → DP！

---

## 💡 思路演進

### 🐢 方法一：暴力遞迴（會 TLE）

```python
def wordBreak_bruteforce(s, wordDict):
    def canBreak(start):
        if start == len(s):
            return True
        
        # 嘗試所有可能的分割點
        for end in range(start + 1, len(s) + 1):
            word = s[start:end]
            if word in wordDict and canBreak(end):
                return True
        
        return False
    
    return canBreak(0)
```

**時間複雜度：** O(2^n)（每個位置都有「分」或「不分」兩種選擇）  
**為什麼慢？** 大量重複計算！例如 `canBreak(5)` 可能被計算多次。

### ✅ 方法二：DP（記憶化/Bottom-Up）

**DP 定義：**
- `dp[i]` = 字串 `s[0:i]` 能否被成功拆分

**遞推關係：**
- `dp[i] = true` 如果存在某個 `j < i` 使得：
  - `dp[j] == true` (前半段能拆分)
  - `s[j:i]` 在字典中（後半段是有效單詞）

**Base Case：**
- `dp[0] = true`（空字串可以拆分）

---

## 💻 實作程式碼

### Python 版本

```python
from typing import List

class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        # 將字典轉為 set，O(1) 查詢
        word_set = set(wordDict)
        n = len(s)
        
        # dp[i]: s[0:i] 能否被拆分
        dp = [False] * (n + 1)
        dp[0] = True  # 空字串可以拆分
        
        # 外層：枚舉結束位置 i
        for i in range(1, n + 1):
            # 內層：枚舉分割點 j
            for j in range(i):
                # 如果前半段 s[0:j] 可拆分，且後半段 s[j:i] 在字典中
                if dp[j] and s[j:i] in word_set:
                    dp[i] = True
                    break  # 找到一種拆分方式就夠了
        
        return dp[n]
```

### C++ 版本

```cpp
#include <vector>
#include <string>
#include <unordered_set>
using namespace std;

class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        // 將字典轉為 set，O(1) 查詢
        unordered_set<string> word_set(wordDict.begin(), wordDict.end());
        
        int n = s.length();
        
        // dp[i]: s[0:i] 能否被拆分
        vector<bool> dp(n + 1, false);
        dp[0] = true;  // 空字串可以拆分
        
        // 外層：枚舉結束位置 i
        for (int i = 1; i <= n; ++i) {
            // 內層：枚舉分割點 j
            for (int j = 0; j < i; ++j) {
                // 如果前半段 s[0:j] 可拆分，且後半段 s[j:i] 在字典中
                if (dp[j] && word_set.count(s.substr(j, i - j))) {
                    dp[i] = true;
                    break;  // 找到一種拆分方式就夠了
                }
            }
        }
        
        return dp[n];
    }
};
```

---

## 🎯 複雜度分析

### 時間複雜度：O(n² × m)
- **外層循環：** n（枚舉 `i`）
- **內層循環：** n（枚舉 `j`）
- **substring + set查詢：** O(m)，其中 m 是平均單詞長度
- 總和：O(n² × m)

### 空間複雜度：O(n + W)
- **dp陣列：** O(n)
- **word_set：** O(W)，W 是字典中所有單詞的字元總數

---

## 🚀 優化：減少 substring 開銷

在 C++ 中，`substr` 會複製字串，開銷較大。可以改用「從後往前枚舉單詞」：

```cpp
bool wordBreak(string s, vector<string>& wordDict) {
    unordered_set<string> word_set(wordDict.begin(), wordDict.end());
    int n = s.length();
    vector<bool> dp(n + 1, false);
    dp[0] = true;
    
    for (int i = 1; i <= n; ++i) {
        // 直接枚舉字典中的單詞
        for (const string& word : wordDict) {
            int len = word.length();
            if (i >= len && dp[i - len] &&
                s.substr(i - len, len) == word) {
                dp[i] = true;
                break;
            }
        }
    }
    
    return dp[n];
}
```

---

## 💡 頓悟時刻

> **蘇格拉底式提問：**  
> 「為什麼這題不能用貪心（每次找最長匹配）？」
>
> **反例：**  
> `s = "aaaaaaa", wordDict = ["aaaa", "aa"]`  
> 貪心會選 "aaaa" + 無法繼續 → False  
> 但正確答案是 "aa" + "aa" + "aa" + "a" 不行... 等等，這個例子其實也是 False。
>
> **更好的反例：**  
> `s = "catsanddog", wordDict = ["cat", "cats", "and", "sand", "dog"]`  
> 貪心選 "cats"（更長） → 剩下 "anddog" 無法拆分  
> 正確應該選 "cat" → "sand" → "dog"
>
> **答案：** 因為局部最優（選最長）不保證全局最優！DP 才能窮舉所有可能性。

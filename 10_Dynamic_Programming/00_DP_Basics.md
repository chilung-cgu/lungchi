# 0. 動態規劃基礎 (Dynamic Programming Basics)

動態規劃 (DP) 是許多人最害怕的主題。但別怕！我們會用「微步驟」的方式，一步一步拆解它。

---

## 🎯 什麼是動態規劃？

DP 的本質就是：**記住之前算過的答案，避免重複計算。**

它適合用來解決具有以下兩個特性的問題：
1.  **最優子結構 (Optimal Substructure)**：大問題的最佳解，可以由小問題的最佳解組合而成。
2.  **重疊子問題 (Overlapping Subproblems)**：在計算過程中，同樣的小問題會被計算很多次。

---

## 🧠 DP 心法：怎麼想？

DP 最難的是「找出遞推關係式 (Recurrence Relation)」。

**ADHD 友善的五步驟框架：**

1.  **定義狀態 `dp[i]`：** `dp[i]` 代表什麼？這通常是最難的一步。
    *   例：對於爬樓梯問題，`dp[i]` = 爬到第 `i` 階的方法數。
2.  **找出 Base Case：** 最小的、不用算的子問題是什麼？
    *   例：`dp[0] = 1`（站在原地，一種方法）, `dp[1] = 1`（一階，一種方法）。
3.  **找出遞推關係式：** `dp[i]` 跟前面的 `dp[?]` 有什麼關係？
    *   例：爬到第 `i` 階，可以從 `i-1` 跨一步，或從 `i-2` 跨兩步。所以 `dp[i] = dp[i-1] + dp[i-2]`。
4.  **決定遍歷順序：** 通常從 Base Case 開始，往目標方向跑。
5.  **回傳答案：** 通常是 `dp[n]` 或 `dp` 陣列的最後一個元素。

---

## 🔄 Top-Down vs Bottom-Up

| 方法 | 別名 | 實作方式 | 特色 |
| :--- | :--- | :--- | :--- |
| **Top-Down** | Memoization (記憶化) | 遞迴 + 快取 | 直觀，像在寫遞迴，但有時會 Stack Overflow |
| **Bottom-Up** | Tabulation (列表法) | 迴圈 + 陣列 | 通常效率更好，適合面試 |

這本書的教學會以 **Bottom-Up** 為主，因為它在面試中更常見、更穩定。

---

## 🎒 經典範例：爬樓梯 (Climbing Stairs)

**問題：** 你要爬 `n` 階樓梯，每次可以爬 1 階或 2 階。總共有多少種爬法？

| n | 爬法數 | 分解 |
| :---: | :---: | :--- |
| 1 | 1 | `[1]` |
| 2 | 2 | `[1,1]`, `[2]` |
| 3 | 3 | `[1,1,1]`, `[1,2]`, `[2,1]` |
| 4 | 5 | ... |

**遞推關係：** `dp[i] = dp[i-1] + dp[i-2]` (這就是費波那契數列！)

```cpp
int climbStairs(int n) {
    if (n <= 2) return n;
    
    // 只需要記住前兩個狀態
    int prev2 = 1; // dp[1]
    int prev1 = 2; // dp[2]
    
    for (int i = 3; i <= n; ++i) {
        int current = prev1 + prev2;
        prev2 = prev1;
        prev1 = current;
    }
    
    return prev1;
}
```

**空間優化：** 如果 `dp[i]` 只依賴 `dp[i-1]` 和 `dp[i-2]`，我們不需要整個陣列，只需要兩個變數！

---

## 🗺️ 常見 DP 類型

| 類型 | 經典題目 | 狀態定義 |
| :--- | :--- | :--- |
| **線性 DP** | Climbing Stairs, House Robber | `dp[i]` 表示到第 `i` 個位置的最佳值 |
| **0/1 背包** | Coin Change, Partition Equal | `dp[i][w]` 表示考慮前 `i` 個物品，容量 `w` 下的最佳值 |
| **區間 DP** | Longest Palindromic Substring | `dp[i][j]` 表示區間 `[i, j]` 的某屬性 |
| **樹形 DP** | House Robber III | 在樹上做 DP |

準備好了嗎？讓我們開始做題！

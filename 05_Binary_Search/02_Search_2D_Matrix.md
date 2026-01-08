# 2. Search a 2D Matrix (搜尋二維矩陣)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/) | Medium | Array, Binary Search, Matrix |

## 題目描述 (Problem)

給你一個 `m x n` 的二維矩陣 `matrix`，它有以下特性：
1.  每一行都從小排到大。
2.  每一行的第一個元素大於前一行的最後一個元素。

找出 `target` 是否存在於矩陣中。

### 例子 (Examples)
```
Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
Output: true

matrix:
[  1,  3,  5,  7 ]
[ 10, 11, 16, 20 ]
[ 23, 30, 34, 60 ]
```

---

## 🧠 思考模式 (Thinking Process)

### 關鍵洞察
這個矩陣如果攤開成一維陣列，它就是一個 **完全排序好的長陣列**！
`[1, 3, 5, 7, 10, 11, 16, 20, 23, 30, 34, 60]`

所以，這題本質上就是 **對一個長度為 m*n 的排序陣列做二分搜尋**。

### 座標轉換
*   總共有 `rows * cols` 個元素。
*   一維索引 `idx` 轉換成二維座標：
    *   `row = idx / cols`
    *   `col = idx % cols`

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **取得維度：** `rows = matrix.size()`, `cols = matrix[0].size()`。
2.  **邊界初始化：** `L = 0`, `R = rows * cols - 1`。
3.  **二分搜尋：**
    *   `M = L + (R - L) / 2`。
    *   將 `M` 轉換成二維座標 `(M / cols, M % cols)`。
    *   比對該位置的值與 `target`。
4.  **回傳結果。**

---

## 💻 實作程式碼 (C++)

```cpp
#include <vector>

class Solution {
public:
    bool searchMatrix(std::vector<std::vector<int>>& matrix, int target) {
        if (matrix.empty() || matrix[0].empty()) {
            return false;
        }

        int rows = matrix.size();
        int cols = matrix[0].size();

        int L = 0;
        int R = rows * cols - 1;

        while (L <= R) {
            int M = L + (R - L) / 2;
            
            // 座標轉換：一維 -> 二維
            int row = M / cols;
            int col = M % cols;
            int midVal = matrix[row][col];

            if (midVal == target) {
                return true;
            } else if (midVal < target) {
                L = M + 1;
            } else {
                R = M - 1;
            }
        }

        return false;
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(\log(m \times n))$** - 對 m*n 個元素做二分搜尋。
*   **空間複雜度：$O(1)$**。

---

## 🧪 自我檢測
*   [ ] 理解 `M / cols` 和 `M % cols` 是怎麼把一維索引變成二維座標？
    *   想像 `cols = 4`。
    *   `idx = 0, 1, 2, 3` -> row 0。
    *   `idx = 4, 5, 6, 7` -> row 1。
    *   `idx / 4` 得到 row，`idx % 4` 得到 col。

# 2. Maximum Depth of Binary Tree (二元樹的最大深度)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) | Easy | Tree, DFS, BFS |

## 題目描述 (Problem)

給你一棵二元樹的根節點 `root`，回傳這棵樹的 **最大深度**。
*   最大深度 = 從根節點到最遠的葉子節點，最長路徑上的 **節點數**。

### 例子 (Examples)
```
Input:
    3
   / \
  9  20
    /  \
   15   7

Output: 3
// 路徑 3 -> 20 -> 15 (或 3 -> 20 -> 7) 共 3 個節點。
```

---

## 🧠 思考模式 (Thinking Process)

### 遞迴的「信任」
假設 `maxDepth(root->left)` 會告訴我左子樹的最大深度，`maxDepth(root->right)` 會告訴我右子樹的最大深度。
那整棵樹的最大深度就是：`1 + max(左子樹深度, 右子樹深度)`。

那個 `1` 代表「當前節點本身」。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **Base Case：** 如果 `root` 是 `nullptr`，深度是 `0`。
2.  **遞迴取得深度：** `leftDepth = maxDepth(root->left)`，`rightDepth = maxDepth(root->right)`。
3.  **計算並回傳：** `return 1 + max(leftDepth, rightDepth)`。

---

## 💻 實作程式碼 (C++)

```cpp
#include <algorithm> // for std::max

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *l, TreeNode *r) : val(x), left(l), right(r) {}
};

class Solution {
public:
    int maxDepth(TreeNode* root) {
        // 1. Base Case
        if (root == nullptr) {
            return 0;
        }

        // 2. 遞迴取得左右子樹深度
        int leftDepth = maxDepth(root->left);
        int rightDepth = maxDepth(root->right);

        // 3. 回傳：自己(1) + 較深的那邊
        return 1 + std::max(leftDepth, rightDepth);
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 每個節點訪問一次。
*   **空間複雜度：$O(h)$** - 遞迴堆疊深度。

---

## 🧪 自我檢測
*   [ ] 如果只有一個根節點，答案是多少？ (左右子樹都是 `nullptr`，回傳 `0`。所以 `1 + max(0, 0) = 1`。正確)。

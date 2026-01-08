# 5. Binary Tree Level Order Traversal (二元樹的層序遍歷)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/) | Medium | Tree, BFS |

## 題目描述 (Problem)

給你一棵二元樹的根節點 `root`，回傳其 **層序遍歷** 的結果。
(即一層一層，從左到右訪問所有節點)

### 例子 (Examples)
```
Input:
    3
   / \
  9  20
    /  \
   15   7

Output: [[3], [9, 20], [15, 7]]
```

---

## 🧠 思考模式 (Thinking Process)

這是 **BFS (廣度優先搜尋)** 的經典應用。
*   DFS 用 **Stack (或遞迴)**。
*   BFS 用 **Queue**。

### 核心概念
1.  把根節點放進 Queue。
2.  當 Queue 不為空時，處理 **當前層**：
    *   記錄當前 Queue 的大小 (這就是當前層的節點數)。
    *   把這些節點一個一個取出，把它們的子節點放進 Queue。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **Edge Case：** 如果 `root == nullptr`，回傳空陣列。
2.  **初始化 Queue：** 把 `root` 放進去。
3.  **外層 while 迴圈 (處理每一層)：**
    *   `levelSize = queue.size()`：記錄當前層有多少節點。
    *   建立一個 `currentLevel` 陣列存這一層的值。
    *   **內層 for 迴圈 (處理 levelSize 個節點)：**
        *   從 Queue 取出一個節點。
        *   把它的值加入 `currentLevel`。
        *   如果它有左/右子節點，放進 Queue。
    *   把 `currentLevel` 加入結果。
4.  **回傳結果。**

---

## 💻 實作程式碼 (C++)

```cpp
#include <vector>
#include <queue>

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
    std::vector<std::vector<int>> levelOrder(TreeNode* root) {
        std::vector<std::vector<int>> result;
        
        // 1. Edge case
        if (root == nullptr) {
            return result;
        }

        // 2. 初始化 Queue
        std::queue<TreeNode*> q;
        q.push(root);

        // 3. BFS 主迴圈
        while (!q.empty()) {
            int levelSize = q.size(); // 當前層的節點數
            std::vector<int> currentLevel;

            for (int i = 0; i < levelSize; ++i) {
                TreeNode* node = q.front();
                q.pop();

                currentLevel.push_back(node->val);

                // 把子節點加入 Queue (為下一層做準備)
                if (node->left != nullptr) {
                    q.push(node->left);
                }
                if (node->right != nullptr) {
                    q.push(node->right);
                }
            }

            result.push_back(currentLevel);
        }

        return result;
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 每個節點訪問一次。
*   **空間複雜度：$O(n)$** - Queue 最多存一整層的節點 (最寬的一層)。

---

## 🧪 自我檢測
*   [ ] 為什麼要先記錄 `levelSize`？ (因為在迴圈中我們會往 Queue 加入下一層的節點，如果不記錄初始大小，迴圈會跑到下一層去)。
*   [ ] BFS 跟 DFS 的最大差別？ (BFS 用 Queue，一層一層處理；DFS 用 Stack/遞迴，一條路走到底)。

# 3. Same Tree (相同的樹)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [100. Same Tree](https://leetcode.com/problems/same-tree/) | Easy | Tree, DFS, BFS |

## 題目描述 (Problem)

給你兩棵二元樹的根節點 `p` 和 `q`，判斷它們是否 **結構相同** 且 **節點值相等**。

### 例子 (Examples)
```
Input: p = [1,2,3], q = [1,2,3]
Output: true

Input: p = [1,2], q = [1,null,2]
Output: false // 結構不同
```

---

## 🧠 思考模式 (Thinking Process)

同時遍歷兩棵樹，比較每個對應位置的節點。

### 遞迴邏輯
1.  如果 `p` 和 `q` 都是 `nullptr` -> 都是空的，相等。
2.  如果 `p` 或 `q` 其中一個是 `nullptr`，另一個不是 -> 結構不同，不相等。
3.  如果 `p->val != q->val` -> 值不同，不相等。
4.  遞迴比較左子樹和右子樹。

---

## 💻 實作程式碼 (C++)

```cpp
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
    bool isSameTree(TreeNode* p, TreeNode* q) {
        // 1. 都是空的
        if (p == nullptr && q == nullptr) {
            return true;
        }

        // 2. 其中一個是空的 (另一個不是)
        if (p == nullptr || q == nullptr) {
            return false;
        }

        // 3. 值不同
        if (p->val != q->val) {
            return false;
        }

        // 4. 遞迴比較左右子樹
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - `n` 是較小那棵樹的節點數 (最壞情況要比完整棵樹)。
*   **空間複雜度：$O(h)$** - 遞迴堆疊。

---

## 🧪 自我檢測
*   [ ] 第 2 和 第 3 點的順序能不能換？ (可以，但先檢查 `nullptr` 再檢查值，可以避免對 `nullptr` 取值導致 crash)。

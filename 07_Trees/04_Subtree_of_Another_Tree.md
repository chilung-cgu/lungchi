# 4. Subtree of Another Tree (另一棵樹的子樹)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/) | Easy | Tree, DFS |

## 題目描述 (Problem)

給你兩棵二元樹 `root` 和 `subRoot`。判斷 `root` 中是否包含一棵與 `subRoot` **相同** 的子樹。

### 例子 (Examples)
```
Input:
root:         subRoot:
    3            4
   / \          / \
  4   5        1   2
 / \
1   2

Output: true
// root 的左子樹 (以 4 為根) 跟 subRoot 完全一樣。
```

---

## 🧠 思考模式 (Thinking Process)

這題是 **Same Tree** 的延伸。
我們的策略是：遍歷 `root` 的每一個節點，問：「以這個節點為根的子樹，跟 `subRoot` 一樣嗎？」

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **Base Case：**
    *   如果 `subRoot == nullptr`：空樹是任何樹的子樹 -> `true`。
    *   如果 `root == nullptr` 且 `subRoot != nullptr`：找不到 -> `false`。
2.  **比較當前**：如果以 `root` 為根的樹跟 `subRoot` 一樣 (`isSameTree`) -> `true`。
3.  **遞迴搜尋**：去左子樹找 `isSubtree(root->left, subRoot)` **或** 去右子樹找 `isSubtree(root->right, subRoot)`。

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
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        // Base cases
        if (subRoot == nullptr) return true;  // 空子樹永遠是子樹
        if (root == nullptr) return false;    // root 空了但 subRoot 還在，找不到

        // 檢查以 root 為起點是否相同
        if (isSameTree(root, subRoot)) {
            return true;
        }

        // 遞迴去左右子樹找
        return isSubtree(root->left, subRoot) || isSubtree(root->right, subRoot);
    }

private:
    // 輔助函式：判斷兩棵樹是否相同 (跟上一題一樣)
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == nullptr && q == nullptr) return true;
        if (p == nullptr || q == nullptr) return false;
        if (p->val != q->val) return false;
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(m \times n)$** - `m` 是 `root` 節點數，`n` 是 `subRoot` 節點數。最壞情況每個節點都要比對整棵 `subRoot`。
*   **空間複雜度：$O(h)$** - 遞迴堆疊。

---

## 🧪 自我檢測
*   [ ] 為什麼這題的時間複雜度比較高？ (因為對 `root` 的每個節點，我們可能都需要做一次 $O(n)$ 的 `isSameTree` 比對)。

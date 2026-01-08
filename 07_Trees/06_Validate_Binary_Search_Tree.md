# 6. Validate Binary Search Tree (驗證二元搜尋樹)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) | Medium | Tree, DFS, BST |

## 題目描述

給你一棵二元樹的根節點 `root`，判斷它是否為 **有效的二元搜尋樹 (BST)**。

BST 的定義：
- 節點的左子樹只包含 **小於** 當前節點值的節點
- 節點的右子樹只包含 **大於** 當前節點值的節點
- 左右子樹本身也必須是 BST

### 例子

```
Example 1:
    2
   / \
  1   3
Output: true

Example 2:
    5
   / \
  1   4
     / \
    3   6
Output: false
解釋: 節點 4 的左子樹包含 3，但 3 < 5，違反了 BST 性質
```

---

## 🔍 深度分析

### 邊界條件
1. **空樹：** `root == null`，回傳 `true`
2. **單節點：** 總是有效的 BST
3. **重複值：** 題目定義 BST 用「嚴格小於/大於」，不允許相等

### 核心洞察

**🤔 陷阱：只檢查「左孩子 < root < 右孩子」夠嗎？**

```
    5
   / \
  1   6
     / \
    3   7
```
- 節點 6 滿足 `1 < 5 < 6`
- 但 6 的左孩子是 3，`3 < 5` 違反規則！

**正確想法：** 每個節點都有一個「合法值範圍」：
- 根節點：`(-∞, +∞)`
- 左子樹的節點：`(-∞, root.val)`
- 右子樹的節點：`(root.val, +∞)`

---

## 💡 思路演進

### 🐢 錯誤想法

```python
def isValidBST_wrong(root):
    if not root:
        return True
    if root.left and root.left.val >= root.val:
        return False
    if root.right and root.right.val <= root.val:
        return False
    return isValidBST_wrong(root.left) and isValidBST_wrong(root.right)
```

**問題：** 只檢查了父子關係，沒檢查祖先約束！

### ✅ 方法一：DFS + 範圍檢查

遞迴時傳遞 `(min_val, max_val)` 範圍：
- 左子樹：`(min_val, root.val)`
- 右子樹：`(root.val, max_val)`

---

## 💻 實作程式碼

### Python 版本

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        """
        使用 DFS 遞迴驗證，傳遞合法值範圍
        """
        def validate(node, min_val, max_val):
            # Base case: 空節點是有效的
            if not node:
                return True
            
            # 檢查當前節點是否在合法範圍內
            if node.val <= min_val or node.val >= max_val:
                return False
            
            # 遞迴檢查：
            # - 左子樹的所有值必須 < node.val
            # - 右子樹的所有值必須 > node.val
            return (validate(node.left, min_val, node.val) and
                    validate(node.right, node.val, max_val))
        
        # 使用正負無窮大作為初始範圍
        return validate(root, float('-inf'), float('inf'))
```

### C++ 版本

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
    bool isValidBST(TreeNode* root) {
        // 使用 long long 避免 INT_MIN/MAX 的邊界問題
        return validate(root, LONG_MIN, LONG_MAX);
    }
    
private:
    /**
     * DFS 驗證：檢查節點值是否在 (min_val, max_val) 範圍內
     * 
     * @param node 當前節點
     * @param min_val 當前節點允許的最小值（不包含）
     * @param max_val 當前節點允許的最大值（不包含）
     * @return true 如果子樹是有效BST
     */
    bool validate(TreeNode* node, long long min_val, long long max_val) {
        // Base case: 空節點是有效的
        if (node == nullptr) {
            return true;
        }
        
        // 檢查當前節點是否在合法範圍內
        if (node->val <= min_val || node->val >= max_val) {
            return false;
        }
        
        // 遞迴檢查左右子樹
        return validate(node->left, min_val, node->val) &&
               validate(node->right, node->val, max_val);
    }
};
```

---

## 🔄 方法二：中序遍歷 (Inorder Traversal)

**BST 的重要性質：** 中序遍歷會得到 **嚴格遞增** 的序列！

### Python (Inorder)

```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        self.prev = None  # 記錄前一個訪問的節點值
        
        def inorder(node):
            if not node:
                return True
            
            # 先遍歷左子樹
            if not inorder(node.left):
                return False
            
            # 檢查當前節點：必須 > prev
            if self.prev is not None and node.val <= self.prev:
                return False
            self.prev = node.val
            
            # 再遍歷右子樹
            return inorder(node.right)
        
        return inorder(root)
```

---

## 🎯 複雜度分析

### 時間複雜度：O(n)
- 每個節點訪問一次

### 空間複雜度：O(h)
- h 是樹高，遞迴堆疊深度
- 平衡樹：O(log n)
- 傾斜樹：O(n)

---

## 💡 頓悟時刻

> **蘇格拉底式提問：**  
> 「為什麼我們要用 `long long` 而不是 `int` 作為範圍？」
>
> **答案：** 考慮節點值剛好是 `INT_MIN` 或 `INT_MAX` 的情況！  
> 如果用 `int`，我們無法表示「比 INT_MIN 還小」或「比 INT_MAX 還大」的邊界。  
> 用 `long long` 可以安全地表示 `LONG_MIN` 和 `LONG_MAX`，避開這個邊界陷阱。
>
> **這是處理邊界值的經典技巧！**

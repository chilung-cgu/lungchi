# 1. Invert Binary Tree (翻轉二元樹)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/) | Easy | Tree, DFS, BFS |

## 題目描述 (Problem)

給你一棵二元樹的根節點 `root`，將這棵樹「左右翻轉」（鏡像），並回傳翻轉後的根節點。

### 例子 (Examples)
```
Input:
     4
   /   \
  2     7
 / \   / \
1   3 6   9

Output:
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

---

## 🧠 思考模式 (Thinking Process)

### 核心觀念
「翻轉」意味著：每個節點的左子樹和右子樹要交換。

### 遞迴的「信任」
假設 `invertTree(root->left)` 會把左子樹翻轉好，`invertTree(root->right)` 會把右子樹翻轉好。
那我只需要：
1.  翻轉左子樹。
2.  翻轉右子樹。
3.  把左右子樹交換。

就這樣！

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **Base Case：** 如果 `root` 是 `nullptr`，直接回傳 `nullptr`。
2.  **交換子節點：** `swap(root->left, root->right)`。
3.  **遞迴翻轉：** 對新的左右子樹分別遞迴呼叫。
4.  **回傳 `root`。**

---

## 💻 實作程式碼 (C++)

```cpp
#include <algorithm> // for std::swap

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
    TreeNode* invertTree(TreeNode* root) {
        // 1. Base Case
        if (root == nullptr) {
            return nullptr;
        }

        // 2. 交換左右子節點 (先交換，再遞迴)
        std::swap(root->left, root->right);

        // 3. 遞迴翻轉左右子樹
        invertTree(root->left);
        invertTree(root->right);

        // 4. 回傳
        return root;
    }
};
```

> 核心觀念：swap() 交換的是「指標 (Pointer)」，就是「整個 Subtree（子樹）」，不是「數值 (Value)」

### 複雜度分析
*   **時間複雜度：$O(n)$** - 每個節點都訪問一次。
*   **空間複雜度：$O(h)$** - `h` 是樹的高度 (遞迴呼叫堆疊)。最壞情況 (傾斜樹) 為 $O(n)$。

---

## 為什麼我們可以忽略那個 invertTree() Return Value

重點在於這個函式是**「原地修改」(In-Place Modification)**。

讓我們看看 `invertTree(root->left)` 發生了什麼事：

1. 我們把 `root->left` 這個指標傳進去。
2. 函式裡面會根據這個指標，把那顆子樹的內部結構翻轉過來。
3. 函式最後 `return root`（也就是回傳原本傳進去的那個指標）。

**關鍵點：**
雖然子樹的**內部結構**變了，但是該子樹**根節點的記憶體位址**沒有變。

舉例來說：

```cpp
// 假設 root->left 指向記憶體位址 0x100

// 寫法 A：接住回傳值
root->left = invertTree(root->left); 
// invertTree 處理完 0x100 的內容，回傳 0x100。
// 這行程式碼變成： root->left = 0x100; (把一樣的值指派回去，多此一舉)

// 寫法 B：忽略回傳值 (你的寫法)
invertTree(root->left);
// invertTree 處理完 0x100 的內容。
// root->left 依然指向 0x100，而且內容已經被改好了。

```

所以，因為 `root->left` 原本指著的人沒變（只是那個人換了衣服），我們不需要重新更新 `root->left` 這個指標。

### 什麼時候「不能」忽略？

如果你的函式會**產生一個全新的節點**，而不是修改舊的，那你就必須接住它。

例如，如果題目是「不要改動原本的樹，請建立一棵新的翻轉樹」：

```cpp
// 這種情況就絕對不能忽略！
TreeNode* newLeft = buildInvertedTree(root->left); 

```

---

## 迭代法 Iterative Approach

雖然遞迴 (Recursion) 解法很直觀，但在極端情況下（樹非常深時），可能會導致 **Stack Overflow (堆疊溢位)**。

在實務開發中，為了系統穩定性，我們常會使用 **迭代法 (Iterative)** 搭配 **佇列 (Queue)** 來模擬層序遍歷 (BFS)。這樣可以避免系統堆疊過深的問題。

以下是使用 `std::queue` 的迭代解法：

```cpp
#include <queue>
#include <algorithm> // for std::swap

class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) {
            return nullptr;
        }

        // 使用 Queue 進行廣度優先搜尋 (BFS)
        std::queue<TreeNode*> q;
        q.push(root);

        while (!q.empty()) {
            // 取出當前節點
            TreeNode* current = q.front();
            q.pop();

            // 核心邏輯：交換當前節點的左右子樹
            std::swap(current->left, current->right);

            // 如果左子節點存在，加入 Queue 待處理
            if (current->left != nullptr) {
                q.push(current->left);
            }

            // 如果右子節點存在，加入 Queue 待處理
            if (current->right != nullptr) {
                q.push(current->right);
            }
        }

        return root;
    }
};

```

---

## 🧪 自我檢測
*   [ ] 為什麼可以「先交換，再遞迴」？ (因為交換後，`root->left` 指向的是原本的右子樹，我們對它遞迴翻轉是正確的。順序不影響結果，因為我們對兩邊都遞迴了)。
*   [ ] 如果樹是空的 (`root == nullptr`)？ (直接回傳 `nullptr`，正確)。

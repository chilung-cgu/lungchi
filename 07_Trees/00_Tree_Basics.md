# 0. 樹的基礎概念 (Tree Basics)

在開始做題之前，先確保你理解「樹」是什麼。

---

## 🌲 什麼是樹 (Tree)？

樹是一種 **非線性** 的資料結構。跟陣列、鏈結串列不同，樹有「分支」。

```
        1         <- 根節點 (Root)
       / \
      2   3       <- 子節點 (Children)
     / \   \
    4   5   6     <- 葉子節點 (Leaf)，沒有子節點
```

### 關鍵術語
| 術語 | 解釋 |
| :--- | :--- |
| **Root (根)** | 最頂端的節點，沒有父節點。 |
| **Node (節點)** | 樹中的每一個元素。 |
| **Edge (邊)** | 節點之間的連線。 |
| **Parent (父節點)** | 某節點的上一層節點。 |
| **Child (子節點)** | 某節點的下一層節點。 |
| **Leaf (葉子)** | 沒有子節點的節點。 |
| **Height (高度)** | 從根到最遠的葉子的邊數。 |
| **Depth (深度)** | 從某節點到根的邊數。 |

---

## 🌳 二元樹 (Binary Tree)

**二元樹** 是我們最常遇到的樹類型。每個節點 **最多只有兩個子節點**（Left 和 Right）。

### C++ 節點定義
LeetCode 通常會給你這個結構：
```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *l, TreeNode *r) : val(x), left(l), right(r) {}
};
```

---

## 🔍 遍歷方式 (Traversal)

這是樹題目的核心。你必須熟練這三種遍歷（都是 DFS）：

| 名稱 | 順序 | 口訣 | 常見用途 |
| :--- | :--- | :--- | :--- |
| **Preorder (前序)** | 根 -> 左 -> 右 | **我**先看，再看左邊，再看右邊 | 複製樹、序列化 |
| **Inorder (中序)** | 左 -> 根 -> 右 | 左邊看完，**我**看，再看右邊 | **BST 會產生有序序列** |
| **Postorder (後序)** | 左 -> 右 -> 根 | 左右都看完，最後看**我** | 刪除樹、算高度 |

```
     1
    / \
   2   3
  / \
 4   5

Preorder:  1, 2, 4, 5, 3
Inorder:   4, 2, 5, 1, 3
Postorder: 4, 5, 2, 3, 1
```

### 遍歷程式碼框架 (遞迴版)
```cpp
void traverse(TreeNode* root) {
    if (root == nullptr) return;

    // Preorder: 在這裡處理 root
    traverse(root->left);
    // Inorder: 在這裡處理 root
    traverse(root->right);
    // Postorder: 在這裡處理 root
}
```

---

## 🚀 BFS vs DFS

| 方法 | 全名 | 實作工具 | 適用情境 |
| :--- | :--- | :--- | :--- |
| **DFS** | Depth-First Search (深度優先) | **遞迴** 或 **Stack** | 路徑問題、判斷樹的屬性 |
| **BFS** | Breadth-First Search (廣度優先) | **Queue** | 層級遍歷、最短路徑 |

---

## 🧠 ADHD 心法：樹的遞迴怎麼想？

1.  **信任遞迴**：不要試圖在腦中展開所有遞迴。只想「這一層」該做什麼。
2.  **三步驟**：
    *   **Base Case**：什麼時候該停？ (通常是 `root == nullptr`)
    *   **處理當前節點**：這個節點該做什麼？
    *   **遞迴呼叫**：把問題丟給左右子樹。
3.  **畫圖**：遇到樹的題目，一定要在紙上畫。

**準備好了嗎？讓我們開始做題！**

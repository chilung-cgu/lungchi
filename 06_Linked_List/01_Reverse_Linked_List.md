# 1. Reverse Linked List (反轉鏈結串列)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) | Easy | Linked List, Recursion |

## 題目描述 (Problem)

給你一個單向鏈結串列的頭節點 `head`，請將整個串列反轉，並回傳反轉後的頭節點。

### 例子 (Examples)
```
Example 1:
Input: head = [1, 2, 3, 4, 5]
Output: [5, 4, 3, 2, 1]

1 -> 2 -> 3 -> 4 -> 5 -> NULL
變成
5 -> 4 -> 3 -> 2 -> 1 -> NULL
```

---

## 🧠 思考模式 (Thinking Process)

### 核心操作：把箭頭反過來
鏈結串列的反轉，就是把每個節點的 `next` 指標，從「指向後面」改成「指向前面」。

### 迭代法 (Iterative) - 三指標法
我們需要三個指標：
1.  `prev`：上一個節點 (反轉後變成 `curr` 的 `next`)。
2.  `curr`：當前處理的節點。
3.  `next`：暫存下一個節點 (不然改 `curr->next` 後就斷線了)。

### 步驟模擬
`NULL <- 1    2 -> 3 -> 4 -> 5 -> NULL`
`       ^    ^`
`      prev curr`

1.  暫存 `next = curr->next` (指向 2)。
2.  把 `curr->next` 指向 `prev` (讓 1 指向 NULL)。
3.  `prev` 右移 (prev = curr，指向 1)。
4.  `curr` 右移 (curr = next，指向 2)。
5.  重複...

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **初始化：** `prev = NULL`, `curr = head`。
2.  **迴圈 `while (curr != NULL)`：**
    *   `nextTemp = curr->next` (暫存)。
    *   `curr->next = prev` (反轉！)。
    *   `prev = curr` (右移 prev)。
    *   `curr = nextTemp` (右移 curr)。
3.  **回傳 `prev`：** 當 `curr` 變成 NULL 時，`prev` 剛好指向原本的最後一個節點 (新的 head)。

---

## 💻 實作程式碼 (C++)

```cpp
// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *n) : val(x), next(n) {}
};

class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;

        while (curr != nullptr) {
            ListNode* nextTemp = curr->next; // 1. 暫存下一個
            curr->next = prev;               // 2. 反轉箭頭！
            prev = curr;                     // 3. prev 右移
            curr = nextTemp;                 // 4. curr 右移
        }

        // 迴圈結束，prev 是新的 head
        return prev;
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n)$** - 掃描一次串列。
*   **空間複雜度：$O(1)$** - 只用了幾個指標變數。

---

## ✨ 補充：遞迴解法 (Recursive)

這個解法比較抽象，但能幫你理解遞迴的「信任」。

**思路：** 假設從第二個節點開始的串列 `head->next` 已經被反轉了，我只要處理當前節點就好。

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        // Base case: 空串列或只有一個節點
        if (head == nullptr || head->next == nullptr) {
            return head;
        }

        // 遞迴：假設後面的都已經反轉好了，newHead 是新的頭
        ListNode* newHead = reverseList(head->next);

        // 把 head->next 的箭頭反指回 head
        head->next->next = head;
        // head 變成最後一個，指向 NULL
        head->next = nullptr;

        return newHead;
    }
};
```
*   **時間複雜度：$O(n)$**
*   **空間複雜度：$O(n)$** - 遞迴呼叫堆疊。

---

## 🧪 自我檢測
*   [ ] 試著在紙上畫 `1 -> 2 -> 3 -> NULL` 的反轉過程，用 prev/curr/next 三格標記它們的變化。
*   [ ] 如果 head 是 NULL，會怎樣？ (迴圈 `curr != nullptr` 為 false，不進入，回傳 `prev` 也是 nullptr。正確)。

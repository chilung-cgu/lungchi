# 2. Merge Two Sorted Lists (合併兩個有序鏈結串列)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/) | Easy | Linked List, Recursion |

## 題目描述 (Problem)

給你兩個 **已排序** 的鏈結串列 `list1` 和 `list2`。
將它們合併成 **一個** 新的、同樣是排序的鏈結串列，並回傳新串列的頭。

### 例子 (Examples)
```
Input: list1 = [1, 2, 4], list2 = [1, 3, 4]
Output: [1, 1, 2, 3, 4, 4]

1 -> 2 -> 4
1 -> 3 -> 4
合併成:
1 -> 1 -> 2 -> 3 -> 4 -> 4
```

---

## 🧠 思考模式 (Thinking Process)

### 類比：洗撲克牌
想像你有兩疊牌，每疊都從小排到大。你要把它們合成一疊。
*   你會比較兩疊最上面的牌，拿較小的那張放到新牌堆。
*   重複這個動作，直到至少一疊牌拿完。
*   最後把剩下的那疊整個接上去。

### 技巧：Dummy Head (假頭)
在處理鏈結串列時，一個常見的技巧是在結果串列的最前面加一個「假的頭節點」。
這樣可以避免處理「第一個節點是誰」的 edge case。最後回傳 `dummy->next` 即可。

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **建立假頭：** `dummy` 節點和 `tail` 指標 (用來追蹤結果串列的尾巴)。
2.  **迴圈 `while (list1 != NULL && list2 != NULL)`：**
    *   比較 `list1->val` 和 `list2->val`。
    *   把較小的那個接到 `tail->next`。
    *   `tail` 右移。
    *   被選中的那個串列的指標也右移。
3.  **處理剩餘：** 迴圈結束後，`list1` 或 `list2` 可能還有剩餘。把剩餘的直接接到 `tail->next`。
4.  **回傳 `dummy.next`：** 這就是合併後的真正頭。

---

## 💻 實作程式碼 (C++)

```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *n) : val(x), next(n) {}
};

class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        // 1. 建立假頭 (Dummy Head)
        // 把 dummy 宣告在 stack 上，不用 new，可避免 memory leak 的煩惱
        ListNode dummy;
        ListNode* tail = &dummy;

        // 2. 雙指標同步比較
        while (list1 != nullptr && list2 != nullptr) {
            if (list1->val <= list2->val) {
                tail->next = list1;
                list1 = list1->next;
            } else {
                tail->next = list2;
                list2 = list2->next;
            }
            tail = tail->next; // tail 右移
        }

        // 3. 處理剩餘部分
        // 其中一個串列還沒走完，直接接上
        if (list1 != nullptr) {
            tail->next = list1;
        } else {
            tail->next = list2;
        }

        // 4. 回傳真正的頭 (跳過 dummy)
        return dummy.next;
    }
};
```

### 複雜度分析
*   **時間複雜度：$O(n + m)$** - n 和 m 分別是兩個串列的長度。
*   **空間複雜度：$O(1)$** - 我們只是改變指標，沒有創造新節點。

---

## 🧪 自我檢測
*   [ ] 如果 `list1` 是空的呢？ (迴圈不進入，步驟 3 會把 `list2` 接上，回傳 `list2`)。
*   [ ] Dummy Head 的好處是什麼？ (不用特別處理「結果串列的第一個節點該是誰」，程式碼更乾淨)。

# 2. Min Stack (最小堆疊)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [155. Min Stack](https://leetcode.com/problems/min-stack/) | Medium | Stack, Design |

## 題目描述 (Problem)

設計一個支援以下操作的堆疊（Stack），且每個操作都必須在 **$O(1)$ 常數時間** 內完成：
*   `push(val)` - 將元素 val 推入 Stack。
*   `pop()` - 刪除 Stack 頂端的元素。
*   `top()` - 取得 Stack 頂端的元素。
*   `getMin()` - 取得 Stack 中 **最小** 的元素。

### 例子 (Examples)
```cpp
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2
```

---

## 🧠 思考模式 (Thinking Process)

### 問題核心
`push`, `pop`, `top` 對 Stack 來說都是 $O(1)$。
**難點在於 `getMin()`。** 如果我們每次都遍歷整個 Stack 找最小值，那就是 $O(n)$。

### 關鍵洞察
當一個元素被 push 進 Stack 時，**那個時刻的最小值**是確定的。
*   如果我們 push 一個新元素，新的最小值要嘛是新元素本身，要嘛是舊的最小值（取較小者）。
*   我們可以用 **兩個 Stack**：一個存原本的值，另一個 **同步存對應的最小值**。

### 雙 Stack 策略
*   `mainStack`: 正常的 Stack。
*   `minStack`: 每個位置存的是「到目前為止的最小值」。

| 操作 | mainStack | minStack (同步) | 說明 |
| :--- | :--- | :--- | :--- |
| push(-2) | [-2] | [-2] | 目前最小是 -2 |
| push(0) | [-2, 0] | [-2, -2] | 0 > -2，最小仍是 -2 |
| push(-3) | [-2, 0, -3] | [-2, -2, -3] | -3 < -2，最小更新為 -3 |
| pop() | [-2, 0] | [-2, -2] | 同步 pop |
| getMin() | | Return -2 from minStack.top() | |

---

## 📝 微步驟計畫 (Micro-Steps)

1.  **資料結構：** 兩個 `std::stack<int>`，分別叫 `stk` 和 `minStk`。
2.  **push(val)：**
    *   `stk.push(val)`。
    *   如果 `minStk` 是空的，或 `val <= minStk.top()`，則 `minStk.push(val)`。
    *   (或者，為了簡單起見，每次都 `minStk.push(min(val, current_min))`。)
3.  **pop()：**
    *   `stk.pop()`。
    *   `minStk.pop()`。 (保持同步)
4.  **top()：** `return stk.top();`
5.  **getMin()：** `return minStk.top();`

---

## 💻 實作程式碼 (C++)

v1. 
only push when val <= stackMin.top() or stackMin is empty
```cpp
class MinStack {
private:
    stack<int> stackData;   // 主堆疊，用於存儲所有元素
    stack<int> stackMin;    // 最小堆疊，用於存儲當前最小元素

public:
    /** initialize your data structure here. */
    MinStack() {
        // 無需初始化，stack 的默認構造函數已經處理
    }
    
    void push(int val) {
        stackData.push(val);
        // 如果 minStack 為空，或者新元素小於等於當前最小元素，推入 minStack
        if(stackMin.empty() || val <= stackMin.top()){
            stackMin.push(val);
        }
    }
    
    void pop() {
        if(stackData.empty()) return; // 根據約束條件，此情況不會發生
        int topVal = stackData.top();
        stackData.pop();
        // 如果彈出的元素等於 minStack 的頂部元素，則同時彈出 minStack
        if(topVal == stackMin.top()){
            stackMin.pop();
        }
    }
    
    int top() {
        if(stackData.empty()) return -1; // 根據約束條件，此情況不會發生
        return stackData.top();
    }
    
    int getMin() {
        if(stackMin.empty()) return -1; // 根據約束條件，此情況不會發生
        return stackMin.top();
    }
};
```

v2.
push every element into minStack
```cpp
#include <stack>
#include <algorithm> // for std::min

class MinStack {
private:
    std::stack<int> stk;    // 主 Stack
    std::stack<int> minStk; // 同步儲存最小值的 Stack

public:
    MinStack() {
        // 建構子，可以不做事
    }
    
    void push(int val) {
        stk.push(val);

        // 計算新的最小值：如果 minStk 是空的，val 就是最小；否則取 min
        int newMin = minStk.empty() ? val : std::min(val, minStk.top());
        minStk.push(newMin);
    }
    
    void pop() {
        stk.pop();
        minStk.pop(); // 同步 pop
    }
    
    int top() {
        return stk.top();
    }
    
    int getMin() {
        return minStk.top();
    }
};
```

### 複雜度分析
*   **時間複雜度：** 所有操作都是 **$O(1)$**。
*   **空間複雜度：$O(n)$** - 我們用了兩個 Stack，最壞情況會存 2n 個元素。

---

## 🧪 自我檢測
*   [ ] 為什麼 `minStk` 也要跟著 `pop`？ (因為當最小值被 pop 掉後，下一個最小值應該是「之前記錄的最小值」，那個值剛好就存在 minStk 的下一層)。
*   [ ] 能不能只用一個 Stack？ (可以，進階做法是把 `(val, current_min)` 的 pair 存在一起，或者用數學技巧。但雙 Stack 最直觀)。

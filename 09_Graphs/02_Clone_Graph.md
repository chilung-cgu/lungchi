# 2. Clone Graph (克隆圖)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [133. Clone Graph](https://leetcode.com/problems/clone-graph/) | Medium | Hash Table, DFS, BFS, Graph |

## 題目描述 (Problem Statement)

給你一個無向連通圖 (connected undirected graph) 中的一個節點的引用 (reference)。
請回傳該圖的 **深拷貝 (deep copy)**。

圖中的每個節點定義如下：

```cpp
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
```

### 例子

```
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]
Output: [[2,4],[1,3],[2,4],[1,3]]

解釋：
圖中有 4 個節點：
1 --- 2
|     |
|     |
4 --- 3
```

---

## 🔍 深度分析 (Deep Analysis)

### 問題約束 (Constraints)
- 節點數量在 `[0, 100]` 範圍內
- `1 <= Node.val <= 100`
- 節點值都是唯一的
- 圖是連通的且無自環 (self-loops)

### 邊界條件 (Edge Cases)
1. **空圖：** `node == null`，回傳 `null`
2. **單一節點：** 只有一個節點且沒有鄰居，回傳該節點的拷貝
3. **線性圖：** `1-2-3-4`，測試是否正確處理單向連接
4. **環形圖：** 所有節點形成一個環

### 核心洞察 (Key Insight)

這是一個 **圖的深度/廣度遍歷 + Hash Map** 問題。

**🤔 為什麼需要 Hash Map？**

因為圖可能有環 (cycles)！如果節點 A 和 B 互相指向對方，我們必須確保：
- 當我們克隆 A 時，B 還沒被創建
- 當我們回頭克隆 B 時，要用「已經創建的 A 的克隆體」

**Hash Map 的作用：** `original_node -> cloned_node`
- 避免重複創建同一個節點的克隆
- 讓我們能在遍歷時找到已創建的克隆節點

---

## 💡 思路演進 (Thought Process)

### 🐢 暴力想法（但行不通）

**錯誤想法：** 「我直接遍歷一遍，每遇到一個新節點就創建拷貝」

**問題在哪？**
- 圖有環！你會陷入無限循環。
- 當 A 指向 B，B 又指向 A 時，你會不斷創建新的 A 和 B。

### ✅ 正確思路：遍歷 + 記憶化 (Memoization)

1. 使用 Hash Map 追蹤「原節點 → 克隆節點」的映射
2. 遍歷圖（DFS 或 BFS）
3. 對於每個節點：
   - 如果已經克隆過，直接返回克隆體
   - 如果沒克隆過，創建新節點，加入 Map，然後遞迴處理鄰居

---

## 🚀 方法一：DFS (深度優先) + Hash Map

### 演算法步驟

1. 創建 `HashMap<Node*, Node*>` 存儲映射
2. 定義 DFS 函式 `clone(Node* node)`：
   - 如果 `node` 已在 Map 中，返回對應的克隆
   - 創建新節點 `copy`，加入 Map
   - 遞迴克隆所有鄰居，加入 `copy->neighbors`
   - 返回 `copy`

---

## 💻 實作程式碼 (Implementation)

### Python 版本

```python
class Node:
    def __init__(self, val = 0, neighbors = None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []

class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        # 邊界條件：空圖
        if not node:
            return None
        
        # Hash Map: {原節點 : 克隆節點}
        cloned = {}
        
        def dfs(curr_node: 'Node') -> 'Node':
            """
            DFS 遞迴克隆節點及其鄰居
            
            參數:
                curr_node: 當前要克隆的原始節點
            
            返回:
                克隆後的節點
            """
            # 如果已經克隆過，直接返回（避免無限循環）
            if curr_node in cloned:
                return cloned[curr_node]
            
            # 創建新節點（只有值，鄰居先空著）
            copy = Node(curr_node.val)
            
            # 立即加入 Map（這很關鍵！防止環導致的無限遞迴）
            cloned[curr_node] = copy
            
            # 遞迴克隆所有鄰居
            for neighbor in curr_node.neighbors:
                copy.neighbors.append(dfs(neighbor))
            
            return copy
        
        return dfs(node)
```

### C++ 版本

```cpp
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};

class Solution {
public:
    Node* cloneGraph(Node* node) {
        // 邊界條件：空圖
        if (node == nullptr) {
            return nullptr;
        }
        
        // Hash Map: {原節點 : 克隆節點}
        unordered_map<Node*, Node*> cloned;
        
        return dfs(node, cloned);
    }
    
private:
    /**
     * DFS 遞迴克隆節點及其鄰居
     * 
     * @param curr_node 當前要克隆的原始節點
     * @param cloned 記錄已克隆節點的 Map
     * @return 克隆後的節點
     */
    Node* dfs(Node* curr_node, unordered_map<Node*, Node*>& cloned) {
        // 如果已經克隆過，直接返回（避免無限循環）
        if (cloned.count(curr_node)) {
            return cloned[curr_node];
        }
        
        // 創建新節點（只有值，鄰居先空著）
        Node* copy = new Node(curr_node->val);
        
        // 立即加入 Map（這很關鍵！防止環導致的無限遞迴）
        cloned[curr_node] = copy;
        
        // 遞迴克隆所有鄰居
        for (Node* neighbor : curr_node->neighbors) {
            copy->neighbors.push_back(dfs(neighbor, cloned));
        }
        
        return copy;
    }
};
```

---

## 🎯 複雜度分析 (Complexity Analysis)

### 時間複雜度：O(V + E)
- **V (Vertices):** 節點數量，每個節點訪問一次
- **E (Edges):** 邊的數量，每條邊在處理鄰居列表時訪問一次
- 因為是連通圖，`E >= V - 1`，所以總體是 `O(V + E)`

### 空間複雜度：O(V)
- **Hash Map：** 最多存 V 個節點的映射
- **遞迴堆疊：** DFS 深度最壞為 V（鏈狀圖）
- **克隆圖本身：** O(V + E)（但這是輸出，通常不計入空間複雜度）

---

## 🔄 方法二：BFS (廣度優先) + Hash Map

與 DFS 思路完全一致，但用 Queue 代替遞迴。

### Python (BFS)

```python
from collections import deque

class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        if not node:
            return None
        
        cloned = {node: Node(node.val)}  # 先創建起始節點的克隆
        queue = deque([node])
        
        while queue:
            curr = queue.popleft()
            
            # 處理當前節點的所有鄰居
            for neighbor in curr.neighbors:
                if neighbor not in cloned:
                    # 第一次遇到這個鄰居，創建克隆並加入隊列
                    cloned[neighbor] = Node(neighbor.val)
                    queue.append(neighbor)
                
                # 將克隆的鄰居加入當前克隆節點的鄰居列表
                cloned[curr].neighbors.append(cloned[neighbor])
        
        return cloned[node]
```

---

## 🧪 自我檢測 (Self-Check Questions)

1. **為什麼要「立即」把新節點加入 Map，而不是等遞迴完成後？**
   - 因為圖有環！如果不立即加入，當遞迴回到自己時，會認為自己尚未克隆，導致無限遞迴。

2. **DFS 和 BFS 哪個更好？**
   - 時間和空間複雜度一樣。DFS 程式碼更簡潔，BFS 堆疊深度更可控。

3. **能不能不用 Hash Map？**
   - 不行。沒有 Map，你無法追蹤哪些節點已經克隆，也無法正確連接克隆後的鄰居。

---

## 💡 頓悟時刻 (Aha! Moment)

> **蘇格拉底式提問：**  
> 「為什麼我們要在創建節點後『立刻』加入 Map，而不是等鄰居都處理完再加？」
>
> **答：** 因為圖可能有環！  
> 想像 A 和 B 互相指向：當我們克隆 A 時，會遞迴克隆 B；克隆 B 時，又會回頭找 A。  
> 如果 A 還沒加入 Map，系統會認為 A 沒被克隆過，再創建一個新的 A，造成無限循環！  
> **「提前註冊」是打破循環依賴的關鍵技巧。**

這種「在完成前先註冊」的模式，在處理循環引用時非常常見（例如建構函式中的雙向連結）。

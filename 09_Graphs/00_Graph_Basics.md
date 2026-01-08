# 0. 圖的基礎概念 (Graph Basics)

在進入圖論題目之前，我們需要先建立對「圖 (Graph)」這個資料結構的直覺理解。

---

## 🗺️ 什麼是圖？

**圖是由節點 (Vertices/Nodes) 和邊 (Edges) 組成的資料結構。**

想像一下：
- 社群網路：人是節點，朋友關係是邊
- 地圖：城市是節點，道路是邊
- 網頁：網頁是節點，超連結是邊

```
    A --- B
    |     |
    |     |
    C --- D
```

### 關鍵術語

| 術語 | 定義 | 範例 |
|:---|:---|:---|
| **Vertex (頂點)** | 圖中的節點 | A, B, C, D |
| **Edge (邊)** | 連接兩個頂點 | A-B, A-C |
| **Degree (度)** | 連接到某頂點的邊數 | A 的度為 2 |
| **Path (路徑)** | 從一個頂點到另一個的邊序列 | A → B → D |
| **Cycle (環)** | 起點和終點相同的路徑 | A → B → D → C → A |
| **Connected (連通)** | 任兩點間都有路徑 | 上圖是連通圖 |

---

## 🔀 圖的類型

### 1. 有向圖 vs 無向圖

| 無向圖 (Undirected) | 有向圖 (Directed) |
|:---:|:---:|
| `A --- B` | `A --> B` |
| 邊沒有方向 | 邊有方向 |
| 朋友關係 | 追蹤/粉絲關係 |

### 2. 有權重圖 vs 無權重圖

```
無權重：A --- B
有權重：A --(5)-- B  (邊上有數字代表成本/距離)
```

---

## 🗃️ 圖的表示方式

在程式中，我們有三種主要方式儲存圖：

### 1. 鄰接矩陣 (Adjacency Matrix)

用二維陣列 `matrix[i][j]` 表示節點 `i` 到節點 `j` 是否有邊。

```python
# 對於上面的圖 A-B-C-D (編號為 0-1-2-3)
graph = [
    [0, 1, 1, 0],  # A 連到 B, C
    [1, 0, 0, 1],  # B 連到 A, D
    [1, 0, 0, 1],  # C 連到 A, D
    [0, 1, 1, 0]   # D 連到 B, C
]
```

**優點：** 查詢 `i` 到 `j` 是否有邊是 O(1)  
**缺點：** 空間複雜度 O(V²)，對稀疏圖浪費空間

### 2. 鄰接表 (Adjacency List) ⭐ 最常用

用 Hash Map 或陣列，key 是節點，value 是鄰居列表。

```python
# Python
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}
```

```cpp
// C++
std::unordered_map<char, std::vector<char>> graph = {
    {'A', {'B', 'C'}},
    {'B', {'A', 'D'}},
    {'C', {'A', 'D'}},
    {'D', {'B', 'C'}}
};
```

**優點：** 空間複雜度 O(V + E)，適合稀疏圖  
**缺點：** 查詢邊的存在需要 O(degree)

### 3. 邊列表 (Edge List)

直接列出所有的邊。

```python
edges = [('A', 'B'), ('A', 'C'), ('B', 'D'), ('C', 'D')]
```

---

## 🔍 圖的遍歷

### DFS (Depth-First Search) 深度優先搜尋

**特性：** 一條路走到底，再回溯。
**實作：** 遞迴 或 Stack
**用途：** 檢測環、路徑問題、拓撲排序

```python
def dfs(graph, node, visited):
    if node in visited:
        return
    
    visited.add(node)
    print(node)  # 處理節點
    
    for neighbor in graph[node]:
        dfs(graph, neighbor, visited)
```

### BFS (Breadth-First Search) 廣度優先搜尋

**特性：** 一層一層向外擴散。
**實作：** Queue
**用途：** 最短路徑（無權重圖）、層級問題

```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)
    
    while queue:
        node = queue.popleft()
        print(node)  # 處理節點
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

---

## 🧠 ADHD 心法：如何思考圖題？

1. **先畫圖！** 不要在腦中想像。用紙筆把節點和邊畫出來。
2. **確認圖的類型：** 有向？無向？有權重？這決定了你能用什麼演算法。
3. **選擇表示方式：** LeetCode 通常給你鄰接表或二維網格。
4. **DFS vs BFS？** 
   - 需要找「所有路徑」或「檢測環」→ DFS
   - 需要找「最短路徑」→ BFS
5. **記得 visited！** 避免無限循環（尤其是有環的圖）。

---

準備好了嗎？讓我們開始解圖論的題目！

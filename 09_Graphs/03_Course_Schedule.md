# 3. Course Schedule (課程表)

| LeetCode | 難度 | 標籤 |
| :--- | :--- | :--- |
| [207. Course Schedule](https://leetcode.com/problems/course-schedule/) | Medium | DFS, BFS, Graph, Topological Sort |

## 題目描述 (Problem Statement)

你總共要修 `numCourses` 門課，編號從 `0` 到 `numCourses - 1`。

給你一個陣列 `prerequisites`，其中 `prerequisites[i] = [ai, bi]` 表示：
- **如果要修課程 `ai`，你必須先修完課程 `bi`。**

請判斷你是否能完成所有課程。

### 例子

```cpp
Example 1:
Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
解釋: 先修課程 0，再修課程 1

Example 2:
Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
解釋: 要修 1 必須先修 0，要修 0 必須先修 1，形成循環依賴
```

---

## 🔍 深度分析 (Deep Analysis)

### 問題約束
- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= 5000`
- `prerequisites[i].length == 2`
- `0 <= ai, bi < numCourses`
- `prerequisites` 中所有課程對都是唯一的

### 邊界條件
1. **沒有先修條件：** `prerequisites = []`，回傳 `true`
2. **自我循環：** `[0, 0]`（題目保證不會出現，但思考這種情況能幫助理解）
3. **多個獨立的課程組：** 例如 A←B, C←D，互不相關

### 核心洞察 (Key Insight)

這是一個 **有向圖環檢測 (Cycle Detection)** 問題！

**圖的建模：**
- 每門課程是一個節點
- `prerequisites[i] = [a, b]` 代表有一條有向邊 `b → a`（b 是 a 的前置條件）

**問題轉化：**
> 「能否完成所有課程」= 「圖中是否存在環 (Cycle)」  
> - 如果有環 → 無法完成（互相依賴）  
> - 如果無環 → 可以完成（DAG - Directed Acyclic Graph）

---

## 💡 思路演進 (Thought Process)

### 🤔 什麼是環？為什麼有環就無法完成？

想像課程依賴：
```
課程 0 → 課程 1 → 課程 2
              ↑         |
              |_________|
```
要修 1 必須先修 0，要修 2 必須先修 1，但要修 1 又必須先修 2！  
這形成了一個 **死鎖 (Deadlock)**。

### 🐢 暴力想法（行不通）

「我嘗試按順序修課，如果遇到沒修的前置課程，就先去修那個」

**問題：** 這只是換個方式遍歷，沒有解決環的檢測問題。

### ✅ 正確想法：DFS + 狀態標記檢測環

我們需要在 DFS 遍歷時追蹤三種狀態：
1. **未訪問 (Unvisited)：** 還沒探索過
2. **訪問中 (Visiting)：** 當前遞迴路徑中，正在探索
3. **已完成 (Visited)：** 探索完畢，確認沒有環

**環的檢測邏輯：**
- 如果在 DFS 中遇到一個「訪問中」的節點，代表形成了環！

---

## 🚀 方法一：DFS + 三色標記法

### 演算法步驟

1. 建立鄰接表（課程 → 依賴它的課程列表）
2. 初始化狀態陣列 `state`：
   - `0` = 未訪問 (White)
   - `1` = 訪問中 (Gray)
   - `2` = 已完成 (Black)
3. 對每個課程執行 DFS：
   - 標記為「訪問中」
   - 遞迴訪問所有鄰居
   - 如果遇到「訪問中」的鄰居 → 有環，返回 `false`
   - 完成後標記為「已完成」

---

## 💻 實作程式碼 (Implementation)

### Python 版本

```python
from typing import List
from collections import defaultdict

class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        # 建立鄰接表：course -> 依賴它的課程列表
        graph = defaultdict(list)
        for course, prereq in prerequisites:
            graph[prereq].append(course)
        
        # 狀態陣列：0=未訪問, 1=訪問中, 2=已完成
        state = [0] * numCourses
        
        def has_cycle(course: int) -> bool:
            """
            DFS 檢測是否存在環
            
            參數:
                course: 當前檢查的課程
            
            返回:
                True 如果存在環，False 否則
            """
            # 如果當前課程在遞迴路徑中，形成環！
            if state[course] == 1:
                return True
            
            # 如果已經檢查過且確認無環，跳過
            if state[course] == 2:
                return False
            
            # 標記為「訪問中」（Gray）
            state[course] = 1
            
            # 遞迴檢查所有依賴當前課程的課程
            for next_course in graph[course]:
                if has_cycle(next_course):
                    return True
            
            # 標記為「已完成」（Black）
            state[course] = 2
            return False
        
        # 檢查所有課程（防止圖不連通的情況）
        for course in range(numCourses):
            if has_cycle(course):
                return False
        
        return True
```

### C++ 版本

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        // 建立鄰接表：course -> 依賴它的課程列表
        unordered_map<int, vector<int>> graph;
        for (const auto& prereq : prerequisites) {
            int course = prereq[0];
            int pre = prereq[1];
            graph[pre].push_back(course);
        }
        
        // 狀態陣列：0=未訪問, 1=訪問中, 2=已完成
        vector<int> state(numCourses, 0);
        
        // 檢查所有課程
        for (int course = 0; course < numCourses; ++course) {
            if (hasCycle(course, graph, state)) {
                return false;
            }
        }
        
        return true;
    }
    
private:
    /**
     * DFS 檢測是否存在環
     * 
     * @param course 當前檢查的課程
     * @param graph 課程依賴圖
     * @param state 節點狀態陣列
     * @return true 如果存在環，false 否則
     */
    bool hasCycle(int course, 
                   unordered_map<int, vector<int>>& graph, 
                   vector<int>& state) {
        // 如果當前課程在遞迴路徑中，形成環！
        if (state[course] == 1) {
            return true;
        }
        
        // 如果已經檢查過且確認無環，跳過
        if (state[course] == 2) {
            return false;
        }
        
        // 標記為「訪問中」（Gray）
        state[course] = 1;
        
        // 遞迴檢查所有依賴當前課程的課程
        if (graph.count(course)) {
            for (int next_course : graph[course]) {
                if (hasCycle(next_course, graph, state)) {
                    return true;
                }
            }
        }
        
        // 標記為「已完成」（Black）
        state[course] = 2;
        return false;
    }
};
```

---

## 🎯 複雜度分析 (Complexity Analysis)

### 時間複雜度：O(V + E)
- **V:** `numCourses` 個課程（節點）
- **E:** `prerequisites.length` 個依賴關係（邊）
- 每個節點和邊都訪問一次

### 空間複雜度：O(V + E)
- **鄰接表：** O(V + E)
- **狀態陣列：** O(V)
- **遞迴堆疊深度：** 最壞 O(V)

---

## 🔄 方法二：Kahn's Algorithm (拓撲排序 BFS)

這是另一種經典的環檢測方法，使用「入度 (In-degree)」概念。

### 核心想法

1. 計算每個節點的入度（有多少前置條件）
2. 把入度為 0 的節點（沒有前置條件）加入 Queue
3. BFS 處理：
   - 取出節點，讓所有依賴它的節點入度 `-1`
   - 如果某節點入度變成 `0`，加入 Queue
4. 最後檢查是否所有課程都被處理過

### Python (Kahn's Algorithm)

```python
from collections import deque, defaultdict

class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        # 建立鄰接表和入度陣列
        graph = defaultdict(list)
        in_degree = [0] * numCourses
        
        for course, prereq in prerequisites:
            graph[prereq].append(course)
            in_degree[course] += 1
        
        # Queue 初始化：放入所有入度為 0 的課程
        queue = deque([i for i in range(numCourses) if in_degree[i] == 0])
        
        processed = 0  # 已處理的課程數
        
        while queue:
            curr = queue.popleft()
            processed += 1
            
            # 處理所有依賴當前課程的課程
            for next_course in graph[curr]:
                in_degree[next_course] -= 1
                if in_degree[next_course] == 0:
                    queue.append(next_course)
        
        # 如果所有課程都處理過，代表無環
        return processed == numCourses
```

---

## 🧪 自我檢測

1. **為什麼需要三種狀態，不能只用「已訪問/未訪問」？**
   - 因為我們需要區分「當前路徑中」和「其他路徑中已訪問」。如果 A→B→C 和 A→D→C，第二次遇到 C 時不應該報環。

2. **DFS 和 BFS (Kahn) 哪個更好？**
   - DFS 更直覺，BFS 可以順便得到拓撲排序序列
   
3. **為什麼外層要對所有節點呼叫 DFS？**
   - 因為圖可能不連通！可能有多個獨立的課程組。

---

## 💡 頓悟時刻

> **蘇格拉底式提問：**  
> 「為什麼檢測環需要『訪問中』這個狀態，而不是只記錄『已訪問』？」
>
> **答：** 因為環的定義是「從某節點出發，經過一系列邊，能回到自己」。  
> 「訪問中」代表「我在當前這條探索路徑上」。  
> 如果我遇到另一個「訪問中」的節點，代表我繞了一圈回來了 → 環！  
> 而「已完成」的節點代表「這條路我走過了，確認沒環，可以安全跳過」。  
> **這種『路徑追蹤』vs『全域記憶』的區分，是圖論中最精妙的思維！**

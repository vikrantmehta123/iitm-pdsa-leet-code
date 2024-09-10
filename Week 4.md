# Leet Code Problems on Graph Algorithms

##### 1. [Find the Town Judge](#1-find-the-town-judge-1)
##### 2. [Course Schedule-I](#2-course-schedule-i)
##### 3. [Course Schedule-II](#3-course-schedule-ii-1)
##### 4. [Snakes and Ladders](#4-snakes-and-ladders-1)
##### 5. [Sort Items By Groups Respecting Dependencies](#5-sort-items-by-groups-respecting-dependencies-1)
##### 6. [Find If Path Exists](#6-find-if-path-exists-1)
##### 7. [Number of Provinces](#7-number-of-provinces-1)

## 1. [Find the Town Judge](https://leetcode.com/problems/find-the-town-judge/description/)

**🎯 Understanding the Test Cases:**

*Test Case 1:*

```n = 3, trust = [[1, 3], [2, 3]]```

*Explanation:*

There are 3 people in the town. Person 1 trusts Person 3, and Person 2 also trusts Person 3. Person 3 doesn't trust anyone, and both other people trust Person 3. This means Person 3 meets the conditions of being the town judge: trusted by everyone but trusts nobody. Hence, he is the town judge. 

*Test Case 2:*

```n = 3, trust = [[1, 3], [2, 3], [3, 1]]```

*Explanation:*

There are 3 people in the town. Person 1 trusts Person 3, and Person 2 also trusts Person 3. Person 3 trusts Person 1, which means Person 3 is not trusted by everyone (because Person 1 and Person 2 trust Person 3, but Person 3 also trusts Person 1). The town judge must trust nobody and be trusted by everyone else. Since Person 3 doesn't meet this condition (because they trust Person 1), there is no town judge. So we return -`.

### 1.1 By Counting Indegrees

**How Can We Think of Trust as a Graph?🤔**

We have an array `trusts`, where each entry `trust[i] = [a_i, b_i]` tells us that `a_i` trusts `b_i`. What if we turned this into a graph? 

- **Question:** If `a_i` trusts `b_i`, how could we represent this in a graph?  
If ```a_i``` trusts ```b_i```, then we can consider it an edge from ```a_i``` to ```b_i```. 

**Finding the Town Judge:**

The town judge is a person who:

1. *Trusts no one* $\implies$ *Outdegree = 0*
2. *Is trusted by everyone except himself* $\implies$ *Indegree = n - 1*

To solve this, we can use dictionaries:

1. *Indegree Dictionary* : Tracks how many people trust each person.
2. *Outdegree Dictionary* : Tracks how many people each person trusts.

- How can we use these dictionaries to find the town judge?  
We can check if someone has an outdegree of 0 and an indegree of `n-1`. Since the town judge is unique, whenever we find such person, we can return it.

Time complexity: $O(n)$:

#### 💻 Code Implementation:
```
class Solution:
    def findJudge(self, n: int, trust: List[List[int]]) -> int:        
        if n <= 1:
            return 1

        indegrees = { }
        outdegrees = { }

        # trust = [a_i, b_i]
        # a_i -> b_i
        for a, b in trust:
            if a not in indegrees: indegrees[a] = 0
            if b not in indegrees: indegrees[b] = 0
            if a not in outdegrees: outdegrees[a] = 0
            if b not in outdegrees: outdegrees[b] = 0

            outdegrees[a] += 1
            indegrees[b] += 1
        
        # If indegree == n - 1 and outdegree == 0, then we have found the town judge.
        townjudge = -1
        for person in indegrees:
            if outdegrees[person] == 0:
                if indegrees[person] >= n - 1:
                    townjudge = person 
                    break
        return townjudge        
```


## 2. [Course Schedule-I](https://leetcode.com/problems/course-schedule/description/)


**🎯 Understanding the Test Cases:**

*Test Case 1:*

```numCourses = 3, prerequisites = [[1,0], [2, 1]]```

*Explanation:*

There are a total of 3 courses to take. To take course $1$ you should have finished course $0$. To take the course $2$, you need to finish course $1$. If a student decides to pursue courses as follows: $0, 1, 2$, then all courses can be completed. So we return ```True```.

*Test Case 2:*

```numCourses = 2, prerequisites = [[1,0], [0, 1]]```

*Explanation:*

There are a total of $2$ courses to take. To take course $1$ you should have finished course $0$. To take the course $0$, you need to finish course $1$. It is not possible for a student to complete both courses. So we return ```False```. In essence, there is a cycle in the prerequisites.

### 2.1 Topological Sorting

🔍 Problem Understanding:

We have a list of prerequisites where each pair $prerequisites[i] = [a_i, b_i]$ means that you need to complete $b_i$ before taking $a_i$. In simpler terms, $a_i$ depends on $b_i$. How can we convert this into a graph? And, the big question: how do we find a sequence of courses that respects all the dependencies? 🤔 Which algorithm should we use?

We can use topological sorting to get this sequence. 

If $b_i$ needs to be completed before $a_i$, we add a directed edge from $b_i \rightarrow a_i$. Note the direction of the edge. For this problem, we are converting the `prerequisites` array into graph using adjacency matrix. Once we have converted this problem into a graph problem, we can use the topological sorting.

**Question:** When would we say that we have successfully found the sequence of courses satisfying the dependencies? 

When we the sequence includes all the courses, and no course is left. If a course is left out, there’s likely a cycle or unresolved dependency.

#### 💻 Code Implementation:
```
class Solution:
    def preprocessing(self, numCourses, prerequisites) -> list[list[int]]:
        adjacency_matrix = [ [0 for i in range(numCourses)] for i in range(numCourses) ]
        for a, b in prerequisites:
            adjacency_matrix[b][a] = 1
        return adjacency_matrix

    # Implementation of Topological sort for Adjacency matrix
    def toposort(self, AMat):
        #Initialization
        (rows,cols) = len(AMat), len(AMat[0])
        indegree = {}
        toposortlist = []
        
        #Compute indegree for each vertex
        for col in range(cols):
            indegree[col] = 0
            for row in range(rows):
                if AMat[row][col] == 1:
                    indegree[col] = indegree[col] + 1
        
        # Topological sort Computing process
        for i in range(rows):
            # Select the min level vertex for removing the graph which has indegree 0
            zero_indegree = [k for k in range(cols) if indegree[k] == 0]
            if not zero_indegree:return toposortlist #Handle the case where there are no more zero indegree vertices
            
            j = zero_indegree[0]
            # Store the removed vertex j in toposortlist and reduce the indegree by one 
            toposortlist.append(j)
            indegree[j] = indegree[j] - 1
            
            # Reduce the indegree of each adjacent of the removed vertex j by 1
            for k in range(cols):
                if AMat[j][k] == 1:
                    indegree[k] = indegree[k] - 1
                    
        return(toposortlist)

    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        adjacency_matrix = self.preprocessing(numCourses, prerequisites)
        toposortlist = self.toposort(adjacency_matrix)
        if len(toposortlist) == numCourses: return True
        return False
```

### 2.2 Topological Sort: Better Implementation

The approach used is similar to the above approach. However, we are using better data structures to increase our performance. Instead of keeping an adjacency matrix, we are using doubly ended queues and adjacency lists. 

Here's how:
- Keep a deque for vertices with zero degree queue such that the insert at end and delete from start operations are both constant time.
- Keep a dictionary of indegrees which counts the indegrees for each vertex

Time Complexity: $O(|V| + |E|)$

#### 💻 Code Implementation:

```
from collections import deque, Counter

class Solution:
    def canFinish(self, numCourse, prerequisites):
        AList = { i: [] for i in range(numCourse)}
        indegree = Counter()
        visited = { i:False for i in range(numCourse)}

        for course, prereq in prerequisites:
            AList[prereq].append(course)
            indegree[course] += 1
    
        # Initialization
        toposortlist = []
        zerodegreeq = deque()
        
        # Find the vertex with indegree 0 and added into the queue
        for u in range(numCourse):
            if indegree[u] == 0:
                zerodegreeq.append(u)
        
        # Topological sort Computing process
        while (zerodegreeq):
            # Remove one vertex from queue which have zero degree vertices
            curr_vertex = zerodegreeq.popleft()       
            # Store the removed vertex in toposortlist and reduce the indegree by one 
            toposortlist.append(curr_vertex)
            indegree[curr_vertex] = indegree[curr_vertex]-1
            
            # Repeat for each adjacent of the removed vertex
            for adj_vertex in AList[curr_vertex]:
                # Reduce the indegree of each adjacent of the removed vertex by 1
                indegree[adj_vertex] = indegree[adj_vertex] - 1
                # If after reducing the degree of adjacent, it becomes zero then insert it into the queue
                if indegree[adj_vertex] == 0:
                    zerodegreeq.append(adj_vertex)                
        
        return len(toposortlist) == numCourse
```

## 3. [Course Schedule-II](https://leetcode.com/problems/course-schedule-ii/description/)

This problem is an exact replica of the above problem, except that we have to return a sequence that satisfies the dependencies, instead of returning a boolean of whether or not we can create such a sequence. 

We can use the same approaches discussed in the above question. 

## 4. [Snakes and Ladders](https://leetcode.com/problems/snakes-and-ladders/description/)

**🎯 Understanding the Test Cases:**

Please refer to the *Problem Overview* where we have discussed the first test case.

### 4.1 Using BFS

📚 *Problem Overview:*

We have a board with $n^2$ cells, and from any cell, we can make up to 6 possible moves. Can we turn this into a graph? Yes! Here's how:

1. Start at cell 1.
2. We can roll a dice and move to next six cells. In the first test cases, the next possible moves are: 15, 3, 4, 5, 6, 7 (because cell 2 has a ladder to 15). If a cell has $-1$ value, then it's an empty cell. But if it has a different value, then it is either a snake or a ladder.
3. Each possible move from a cell is an edge in our graph.
4. Once we have the graph, We need to find the least number of moves required to go from cell 1 to cell $n^2$. This is a **shortest path** problem where there are no weights on the edges- we can use *Breadth-First Search (BFS)*. 


*Pseudocode:*
- We first need to convert the board into a graph. 
    - For every cell, check the next six cells. 
        - If the cell has a ladder or a snake, then we need to find out where the ladder ends which is given in cell: ```board[row][col]```
        - If the cell does not have a ladder or a snake, then ```board[row][col]``` will have the vaue $-1$. 
        - Add the edge as: ```[cell]``` to ```board[row][cell]``` or the next moves.

- Once we have the graph, we can run BFS on this graph, which will return the shortest path. 

#### 💻 Code Implementation:
```
from collections import deque
# Using BFS approch For Adjacency list, for path, maintaining the parent of each vertex
# Using BFS approch maintaing the adjacent level number from source vertrex
def BFSListPathLevel(AList,v):
    # Initialization
    (level,parent) = ({},{})
    for each_vertex in AList.keys():
        level[each_vertex] = -1
        parent[each_vertex] = -1
    
    # Create Queue object q
    q = deque()
    
    # Assigning the level 0 for start_vertex and insert it into the queue
    level[v] = 0
    q.append(v)
    
    # Repeat the following until the queue is empty
    while q:
        # Remove the one vertex from queue
        curr_vertex = q.popleft()
        # Visit the each adjacent of curr_vertex(if level value is -1) and insert into the queue
        for adj_vertex in AList[curr_vertex]:
            if (level[adj_vertex] == -1):
                # Assign the level value on each adjacent one more than the curr_vertex level
                level[adj_vertex] = level[curr_vertex] + 1
                # Assigne the curr_vertex as parent of adjacent vertex of curr_vertex
                parent[adj_vertex] = curr_vertex
                q.append(adj_vertex)
                
    return(level,parent)


class Solution:
    def get_indices(self, cell, n):
        """A helper function to convert the Boustrophedon style sequence into a cell's row and column index"""
        remainder = (n - 1) % 2
        row = (n - 1) - ((cell - 1) // n)
        if row % 2 == remainder:
            col = (cell % n) - 1 if cell % n != 0 else n - 1
        else:
            col = (n) - (cell % n) if cell % n !=0 else 0
        return row, col

    def preprocessing(self, board:List[List[int]]) -> list[list[int]]:
        """Converts the board into a graph"""
        n = len(board)
        AList = {i:set() for i in range(1, n*n + 1)}
        for i in range(1, n*n + 1):
            for j in range(i + 1, min(i + 7, n*n + 1)): 
                row, col = self.get_indices(j, n)
                val = board[row][col]
                if val == -1:
                    AList[i].add(j)
                else:
                    if val == i:
                        continue
                    AList[i].add(val)
        return AList            
            
    def snakesAndLadders(self, board: List[List[int]]) -> int:
        n = len(board)
        AList = self.preprocessing(board)
        level, parent = BFSListPathLevel(AList, 1)
        return level[n*n] # The last cell holds shortest path from start to the last cell
```


## 5. [Sort Items By Groups Respecting Dependencies](https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/description/)

### 5.1 Using Topological Sort

📚 **Problem Overview:**

We need to order a list of items so that all items in the same group are listed next to each other. Additionally, some items have dependencies, meaning one item must be completed before another. We can use topological sort to solve this problem.

🤔 **The Issue:**

If we just run topological sort on the `beforeItems` array, it might work for some test cases. But can you think of the test cases where this approach will fail?

*Imagine this situation:*
```
Group 0: Items [1, 2]
Group 1: Items [3, 4]
```
*Here’s the setup:*

Item 1 has no dependencies (`indegree[1] = 0`)\
Item 3 also has no dependencies (`indegree[3] = 0`)\
Item 2 and Item 4 each have one dependency (`indegree[2] = 1 and indegree[4] = 1`)\

*Now, assume:*

Item 2 must come before Item 4\
Item 1 must come before Item 2\
This means the correct order should be: 1 → 2 → 4.\

The problem arises when choosing between Item 1 and Item 3, as both have no dependencies. If we choose Item 3 first, we should list the other items from Group 1 next to it, leading to an order like this: 3, 4. This leaves us with Group 0 and the sequence 1, 2. The final order would be 3, 4, 1, 2, which breaks the dependency 1 → 2 → 4. (Draw these nodes and edges to get a clearer picture!)

💡 **The Solution:**

To solve this, we need to track the dependencies of both individual items and groups. Here’s how we can do that:

- Create Two Graphs:
    - Item Graph: Use the `beforeItems` array to build a graph showing how items depend on each other.
    - Group Graph: Also use the `beforeItems` array to build a graph showing how groups depend on each other.
    - We can use either the adjacency list or the adjacency matrix representation. Here, we are using the adjacency list representation.
- Run Nested Topological Sort:
    - Outer graph to perform topological sorting on the `group` graph.
    - Then, within each group, sort the items using topological sorting.

#### 💻 Code Implementation:
```
from collections import deque

class Solution:
    def get_group_wise_elements(self, group, m):
        """Helper function to quickly access all items belonging to a group"""
        group_wise_elements = { i:[] for i in range(m) }
        for i in range(len(group)):
            grp = group[i] if group[i] >= 0 else -i-1
            if grp not in group_wise_elements:
                group_wise_elements[grp] = []
            group_wise_elements[grp].append(i)
        return group_wise_elements

    def preprocessing(self, beforeItems, group, m, n, groupwise_items):
        """
        Given the beforeItems array, this function converts it into two graphs- Groups and Items. 
        Along with it, we are also returning the indegrees of each of the graphs' nodes so that we 
        can smoothly run topological sort. 
        """
        group_indegree, items_indegree = {}, {i:0 for i in range(n)}
        items_alist =  {i:[] for i in range(n)}
        group_alist = {}

        for key in groupwise_items:
            group_alist[key] = set()
            group_indegree[key] = 0
 
        for i in range(len(beforeItems)):
            before = beforeItems[i]
            for num in before:
                if num not in items_alist:
                    items_alist[num] = [ ]
                items_alist[num].append(i)
                items_indegree[i] += 1

                grp_num = group[num] if group[num] >= 0 else -num-1
                grp_i = group[i] if group[i] >= 0 else -i-1  
                if grp_num != grp_i:
                    if grp_num not in group_alist:
                        group_alist[grp_num] = set()
                    group_alist[grp_num].add(grp_i)
        
        for key in group_alist.keys():
            group_alist[key] = list(group_alist[key])

        for start_node in group_alist.keys():
            for end_node in group_alist[u]:
                if end_node not in group_indegree:
                    group_indegree[end_node] = 0
                group_indegree[end_node] += 1

        return items_alist, group_alist, items_indegree, group_indegree

    
    def lpath(self, items_alist, group_alist, items_indegree, group_indegree, groupwise_items, n, group):
        """The actual nested topological sorting algorithm"""
        output = [ ]

        grp_queue = deque()
        items_queue = deque()

        for grp in group_indegree:
            if group_indegree[grp] == 0:
                grp_queue.append(grp)

        # Outer topological sort is for groups
        while grp_queue:
            curr_grp = grp_queue.popleft()
            group_indegree[curr_grp] -= 1
            for adj_grp in group_alist[curr_grp]:
                # Reduce the indegree of each adjacent group of the removed vertex by 1
                group_indegree[adj_grp] -= 1

                # Add new zero degree groups to the group deque
                if group_indegree[adj_grp] == 0 :
                    grp_queue.append(adj_grp)

            # For popped group, find out zero degree items
            for i in groupwise_items[curr_grp]:
                if items_indegree[i] == 0 :
                    items_queue.append(i)

            # Topological sort is for the items
            while items_queue:

                # Remove one vertex from items queue which have zero degree items and reduce the indegree
                curr_vertex = items_queue.popleft()
                output.append(curr_vertex)
                items_indegree[curr_vertex] = items_indegree[curr_vertex] - 1
                
                # Repeat for each adjacent of the removed item 
                for adj_vertex in items_alist[curr_vertex]:
                    # Reduce the indegree of each adjacent of the removed item by 1
                    items_indegree[adj_vertex] = items_indegree[adj_vertex] - 1

                    # Add items to items deque if their indegree becomes zero
                    if items_indegree[adj_vertex] == 0 and group[adj_vertex] == curr_grp:
                        items_queue.append(adj_vertex)

        if len(output) != n: return []
        return output

    def sortItems(self, n: int, m: int, group: List[int], beforeItems: List[List[int]]) -> List[int]:
        group_wise_items = self.get_group_wise_elements(group, m)

        items_alist, group_alist, items_indegree, group_indegree = self.preprocessing(beforeItems,group, m, n, group_wise_items)
        
        return self.lpath(items_alist, group_alist, items_indegree, group_indegree, group_wise_items, n, group)
```


## 6. [Find If Path Exists](https://leetcode.com/problems/find-if-path-exists-in-graph/description/)

### 6.1 Using BFS

**🎯 Understanding the Test Cases:**

We have not discussed test cases as it is an easy problem.

📚 **Problem Overview:**

We need to figure out if we can go from a source node to a destination node. This is a reachability problem in a graph. We know we can use *BFS* or *DFS* on an adjacency list or a matrix for reachability problem. 

Can you convert the given `edges` list into an adjacency list or an adjacency matrix representation?
- We have a list of edges where each `edges[i] = [u, v]` shows an edge between nodes `u` and `v`.
- To create an adjacency list, we can do:
```
for u, v in edges:
    adj_list[u].append(v)
    adj_list[v].append(u)
```

#### 💻 Code Implementation:
Note: For a better, more neat code, we have used the ```deque``` data structure from the ```collections``` module in Python. We can use it to implement a ```queue``` used in BFS. It's a useful data structure to know.

```
from collections import deque

class Solution:
    def validPath(self, n: int, edges: List[List[int]], source: int, destination: int) -> bool:
        adj_list = { i:[ ] for i in range(n) }
        visited = set()

        # Create the adjacency list representation from Graph
        for u, v in edges:
            adj_list[u].append(v)
            adj_list[v].append(u)

        queue = deque([source])
        visited.add(source)
        while queue:
            vertex = queue.popleft()
            if vertex == destination:
                return True
            for neighbor in adj_list[vertex]:
                if neighbor not in visited:
                    queue.append(neighbor)
                    visited.add(neighbor)
        return False
```

## 7. [Number of Provinces](https://leetcode.com/problems/number-of-provinces/description/)


**🎯 Understanding the Test Cases:**

We have not discussed test cases as it is an easy problem.

**📚 Problem Overview:**

We are given a matrix `isConnected` where each entry `[i][j]` tells us if there is a connection (edge) between nodes `i` and `j`. We can think of this as an **adjacency matrix** for a graph.

A **province** is a group of nodes that are all connected. The task is to count how many such provinces exist.

🤔 Can you think of a similar problem that we saw in the lectures?

This is a **connected components** problem. We need to find how many separate groups of connected nodes exist in the graph.

**💡 The Solution:**

To solve this, we can use either **BFS** or **DFS**. For this explanation, we’ll go with **BFS** to explore each connected component one by one.

#### 💻 Code Implementation:
```
from collections import deque

class Solution:
    def get_neighbors(self, AMat, vertex):
        """Helper function to find out all neighbors of a node given the adjacency matrix."""
        neighbors = []
        for i in range(len(AMat)):
            if AMat[vertex][i] == 1:
                neighbors.append(i)
        return neighbors

    def BFS(self, AMat, source):
        """
        Runs BFS from a given source vertex and returns the set of vertices visited in that run of BFS.
        That is, each run of the BFS will give us one province, or one connected component.
        """
        visited = set()
        queue = deque([source])  # Initialize the queue with the source vertex
        visited.add(source)
        while queue:
            vertex = queue.popleft()
            neighbors = self.get_neighbors(AMat, vertex)  # Pass both AMat and vertex
            for neighbor in neighbors:
                if neighbor not in visited:
                    queue.append(neighbor)
                    visited.add(neighbor)
        return visited

    def findCircleNum(self, isConnected):

        # Initially, every vertex gets an invalid component number
        components = {i: -1 for i in range(len(isConnected))}

        # Keep track of the component number and the number of vertices already visited across BFS runs
        component_number = 0
        seen = 0
        
        while seen < len(isConnected):
            startvertex = min([i for i in range(len(isConnected)) if components[i] == -1])
            visited = self.BFS(isConnected, startvertex)

            # For every vertex that was visited in the current run of BFS, update its component number and update 
            # the number of vertices seen.
            for vertex in visited:
                components[vertex] = component_number
                seen += 1

            # Increment component number such that for the next run of BFS, 
            # a different component number will be assigned
            component_number += 1
            
        return component_number 
```
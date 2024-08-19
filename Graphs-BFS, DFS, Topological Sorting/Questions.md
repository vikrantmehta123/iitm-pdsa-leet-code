# Leet Code Problems on Graph Algorithms

##### 1. [Find the Town Judge](#1-find-the-town-judge-1)
##### 2. [Course Schedule-I](#2-course-schedule-i)
##### 3. [Course Schedule-II](#3-course-schedule-ii-1)
##### 4. [Snakes and Ladders](#4-snakes-and-ladders-1)
##### 5. [Sort Items By Groups Respecting Dependencies](#5-sort-items-by-groups-respecting-dependencies-1)
##### 6. [Find If Path Exists](#6-find-if-path-exists-1)
##### 7. [Number of Provinces](#7-number-of-provinces-1)

## 1. [Find the Town Judge](https://leetcode.com/problems/find-the-town-judge/description/)

### 1.1 By Counting Indegrees

We're given the array ```trusts```, where ```trust[i] = [a_i, b_i]``` means that ```a_i_``` trusts ```b_i_```. Can we convert this problem into a graph problem?

If ```a_i_``` trusts ```b_i_```, then we can consider it an edge from ```a_i_``` to ```b_i_```. 

The townjudge is a person who:
- Trusts no one $\implies outdegree(townjudge) = 0$ , and
- Everyone except him / herself trusts him / her $\implies indegree(townjudge) = n - 1$.

Keep two dictionaries:
- To count the indegrees of all the nodes
- To count the outdegrees of all the nodes

Then for every person, check if its outdegree is $0$ and indegree is $n-1$.

Complexity: $O(n)$, since the creating the dictionaries takes $O(n)$ and then iterating over people takes another $O(n)$.

#### Code:
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

### 2.1 Topological Sorting

The ```prerequisites[i] = [a_i, b_i]``` $\implies$ ```b_i``` has to come before ```a_i```. In other words, ```a_i``` is dependent on ```b_i```. Can you convert this problem into a graph problem? Further, which algorithm do we need to use to find a sequence that satisfies dependencies?

If ```b_i``` has to come before ```a_i```, then we add an edge from ```b_i``` to ```a_i```. Please note the direction of the edge. We can convert this into an adjacency matrix or an adjacency list. For this problem, we are converting the graph into an adjacency matrix. Once we have converted this problem into a graph problem, we can use the algorithm we use when we need to find a sequence that satisfies some dependencies- topological sorting.

When would you say that we have successfully found the sequence of courses satisfying the dependencies? 
When we the sequence that we get has all the courses- no course is left.  

#### Code:
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
        for c in range(cols):
            indegree[c] = 0
            for r in range(rows):
                if AMat[r][c] == 1:
                    indegree[c] = indegree[c] + 1
        
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

The approach used is similar to the above approach. However, we are utilizing better data structures to increase our performance. Instead of keeping an adjacency matrix, we are utilizing a combination of doubly ended queues and adjacency lists. 

Here's how:
- Keep a deque for vertices with zero degree queue such that the insert at end and delete from start operations are both constant time.
- Keep a dictionary of indegrees which counts the indegrees for each vertex

Complexity: $O(|V| + |E|)
#### Code:

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

### 4.1 Using BFS

We are given a board with $n^2$ cells. From any particular cell, there can be at maximum only six moves. Can we convert this structure into a graph?

For example, in the first test case, assume we are at cell $1$. 

The next possible moves are: $15, 3, 4, 5, 6, 7$ because $2$ has a ladder to $15$. 
From cell $1$, we can make each of the next possible moves as an edge.

The task is to find the least number of moves required to reach $n^2$ starting from $1$. Can you identify what type of a problem this is and which algorithm we can use for this problem?

It's a shortest path problem without edge weights. The algorithm that we can use for this problem is BFS. 

Pseudocode:
- We first need to convert the board into a graph. 
    - For every cell, check the next six cells. 
        - If the cell has a ladder or a snake, then we need to find out where the ladder ends which is given in cell: ```board[row][col]```
        - If the cell does not have a ladder or a snake, then ```board[row][col]``` will have the vaue $-1$. 
        - Add the edge as: ```[cell]``` to ```board[row][cell]``` or the next moves.

- Once we have the graph, we can run BFS on this graph, which will return the shortest path. 

#### Code:


## 5. [Sort Items By Groups Respecting Dependencies](https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/description/)

### 5.1 Using Topological Sort
We need to list a a sequential order of ```items``` such that the items belonging to the same group are listed next to each other. Each item can have a dependency such that the other item needs to be completed first- as given in the ```beforeItems``` array. It is easy to see that this is a topological ordering problem. 

Take a moment to think about the following:
Let's say we run topological sort directly on the beforeItems. We may pass some test cases. But what test cases might we fail?
Whenever we have multiple ```items``` that have zero indegree, then we have to make a choice as to which item should we take first. Let's take an example: 
```
group[0] = [1, 2]
group[1] = [3, 4]
indegree[1] = 0
indegree[3] = 0
indegree[2] = indegree[4] = 1 

Assume: 2 needs to be come before 4, and 1 needs to come before 2. 
1 -> 2 -> 4
```

Now, the issue will come when we are selecting between the ```items``` 1 and 3. If our algorithm chooses 3, then all the other elements belonging to group 1 should come next to 3- like: 3, 4-, and only then we'll be able to add elements from group 0- like: 3, 4, 1, 2. This violates the condition that ```1 -> 2 -> 4```.

To solve this problem, we need to keep a graph for the ```groups``` as well. Whenever we are choosing which ```item``` to select for the topological order, we need to first check the ```groups``` graph. 

Thus, we keep two graphs- one for ```items``` and one for ```groups```. Then we run a sort of nested topological sort- the outer one for the ```groups``` graph, and the inner one for the ```items``` graph. This will give us the correct solution that respects both the group dependencies and the item dependencies. 

#### Psuedocode:
1. Since we are going to need to list all elements belonging to a group side by side, we will keep a dictionary of the format: ```group:[all items of that group]``` for easy retrieval.

2. Use ```beforeItems``` array to construct both the graphs- ```groups``` and ```items```. You can use the either an adjacency matrix or an adjacency list representation for this. Here, we are using Adjacency List representation. 
    - If ```items[i]``` needs to come before ```items[j]```, then add edge from ```i``` to ```j``` for the ```items_graph```. 
    - Similarly, for the ```group_graph```, add the edge from ```group[i]``` to ```group[j]```, since the item in ```i```th group needs to come before the item in the ```j```th group. 

3. Once you have the graphs, run nested topological sort as mentioned above. 

#### Code:
```
# Implementation of Queue
class Queue:
    def __init__(self):
        self.queue = []
    def enqueue(self,v):
        self.queue.append(v)
    def isempty(self):
        return(self.queue == [])
    def dequeue(self):
        v = None
        if not self.isempty():
            v = self.queue[0]
            self.queue = self.queue[1:]
        return(v)    
    def __str__(self):
        return(str(self.queue))

class Solution:
    # Creates a dictionary that returns all elements of a particular group. Used in the main algorithm when we are listing all the items # that belong to a group side by side
    def get_group_wise_elements(self, group, m):
        group_wise_elements = { i:[] for i in range(m) }
        for i in range(len(group)):
            grp = group[i] if group[i] >= 0 else -i-1
            if grp not in group_wise_elements:
                group_wise_elements[grp] = []
            group_wise_elements[grp].append(i)
        return group_wise_elements

    # Convert the problem into two graphs- group_graph and items_graph. Along with it, return indegrees for each node in both the graphs. 
    def preprocessing(self, beforeItems, group, m, n, groupwise_items):
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

        for u in group_alist.keys():
            for v in group_alist[u]:
                if v not in group_indegree:
                    group_indegree[v] = 0
                group_indegree[v] += 1

        return items_alist, group_alist, items_indegree, group_indegree

    # Run the actual nested topological sorting algorithm
    def lpath(self, items_alist, group_alist, items_indegree, group_indegree, groupwise_items, n, group):
        output = [ ]

        grp_queue = Queue()
        items_queue = Queue()

        for grp in group_indegree:
            if group_indegree[grp] == 0:
                grp_queue.enqueue(grp)

        # Outer topological sort is for groups
        while not grp_queue.isempty():
            curr_grp = grp_queue.dequeue()
            group_indegree[curr_grp] -= 1
            for adj_grp in group_alist[curr_grp]:
                # Reduce the indegree of each adjacent group of the removed vertex by 1
                group_indegree[adj_grp] -= 1

                # If after reducing the degree of adjacent group, it becomes zero then insert it into the group queue
                if group_indegree[adj_grp] == 0 :
                    grp_queue.enqueue(adj_grp)

            for i in groupwise_items[curr_grp]:
                if items_indegree[i] == 0 :
                    items_queue.enqueue(i)

            # Inner topological sort is for the items
            while (not items_queue.isempty()):

                # Remove one vertex from items queue which have zero degree items and reduce the indegree by 1
                curr_vertex = items_queue.dequeue()
                output.append(curr_vertex)
                items_indegree[curr_vertex] = items_indegree[curr_vertex] - 1
                
                # Repeat for each adjacent of the removed item 
                for adj_vertex in items_alist[curr_vertex]:
                    # Reduce the indegree of each adjacent of the removed item by 1
                    items_indegree[adj_vertex] = items_indegree[adj_vertex] - 1

                    # If after reducing the degree of adjacent item, it becomes zero then insert it into the items queue
                    if items_indegree[adj_vertex] == 0 and group[adj_vertex] == curr_grp:
                        items_queue.enqueue(adj_vertex)

        if len(output) != n: return []
        return output

    def sortItems(self, n: int, m: int, group: List[int], beforeItems: List[List[int]]) -> List[int]:
        group_wise_items = self.get_group_wise_elements(group, m)

        items_alist, group_alist, items_indegree, group_indegree = self.preprocessing(beforeItems,group, m, n, group_wise_items)
        
        return self.lpath(items_alist, group_alist, items_indegree, group_indegree, group_wise_items, n, group)
```


## 6. [Find If Path Exists](https://leetcode.com/problems/find-if-path-exists-in-graph/description/)
### 6.1 Using BFS

What is the question asking you to do?
- We need to find out whether we can reach a particular destination from the given source node. That is, it's a reachability problem. 

Can you convert the given ```edges``` list into an adjacency list or an adjacency matrix representation?
- Each ```edges[i] = [u_i, v_i]``` represents a bidirectional edge, which means there is an edge from ```u``` to ```v```, and ```v``` to ```u```. 
- So we can iterate over the entire ```edges``` array, and add the two edges for every ```edges[i]```. Here's the code snippet for converting the ```edges``` array into an adjacency list:
```
for u, v in edges:
    adj_list[u].append(v)
    adj_list[v].append(u)
```

Once we have the adjacency list, which algorithm will give us whether we can reach ```destination``` from the ```source```?
- We can use either BFS or DFS. For this solution, we are using BFS. 

#### Code:
Note: For a better, more concise code, we have used the ```deque``` data structure from the ```collections``` module in Python. We can use it to implement a ```queue``` used in BFS. 

```
from collections import deque

class Solution:
    def validPath(self, n: int, edges: List[List[int]], source: int, destination: int) -> bool:
        adj_list = { i:[ ] for i in range(n) }
        visited = set()
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

### 7.1 Using BFS
We are given a matrix ```isConnected```, whose ```[i][j]```th entry tells us whether there is an undirected edge from ```i``` to ```j```. We can interpret this ```isConnected``` matrix as an adjacency matrix. 

A province is a set of vertices that are connected to each other. We are asked to find the number of provinces from the matrix. 

Can you identify the type of the problem?
- The problem is about finding the number of connected components from the graph. 

Can you identify which algorithm we can use to solve this problem?
- We can use BFS or DFS to solve this problem. For this solution, we will use BFS. 

#### Code:
```
from collections import deque

class Solution:
    def get_neighbors(self, AMat, vertex):
        """Given an adjacency matrix and a vertex, returns all neighbors for that vertex"""
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
            startv = min([i for i in range(len(isConnected)) if components[i] == -1])
            visited = self.BFS(isConnected, startv)

            # For every vertex that was visited in the current run of BFS, update its component number and update 
            # the number of vertices seen.
            for v in visited:
                components[v] = component_number
                seen += 1

            # Increment component number such that for the next run of BFS, 
            # a different component number will be assigned
            component_number += 1
            
        return component_number 
```
# Leet Code Problems on Graph Algorithms

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



## 6. [Word Ladder](https://leetcode.com/problems/word-ladder/description/)



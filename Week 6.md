# Leet Code Problems on Heaps and Trees

##### 1. [Validate Binary Search Tree](#1-validate-binary-search-tree-1)
##### 2. [Network Delay Time](#2-network-delay-time-1)
##### 3. [Maximum Spending After Buying Items](#3-maximum-spending-after-buying-items-1)
##### 4. [Average of Levels in Binary Tree](#4-average-of-levels-in-binary-tree-1)

## 1. [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/description/)

### 1.1 Using Inorder Traversal

**🎯 Understanding the Test Cases:**

*Test Case 1:*

`root = [ 2, 1, 3]`

*Explanation:*

This is the first test case from the problem description. A valid binary search tree is a one where every node's left child has value less than the node, and the right child has a value greater than the node. That is: $left < current < right$. We can see from the tree that this is indeed the case. So, it is a valid binary search tree. 

*Test Case 2:*

`root = [ 5, 1, 4, None, None, 3, 6]`

*Explanation:*

This is the second test case from the problem description. The node with value $4$ is the right child of the node with value $5$. This violates the conditions of a binary search tree. So, we return False. Note that this violation can occur in any internal node- not just the root node. 

**📚 Problem Overview:**

In a *binary search tree* (BST), the *inorder traversal* gives a *sorted list* of elements. If the BST is valid, then it's inorder traversal will produce a sorted list. If it is not valid, the inorder traversal will not produce a sorted list.

**💡 Approach:**

1. Do *inorder traversal* on the tree.
2. Check if the result is *sorted* by comparing each element with the previous one.

If the list is sorted, the tree is a valid BST. If not, it's invalid.

**⏳ Time Complexity:** $O(n)$

#### 💻 Code Implementation:
```
class Solution:
    def isValidBST(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """

        tree = self.inorder_traversal(root) # Do inorder traversal

        # Base Case: if tree has just one element, then it is a valid BST
        if len(tree) <= 1:
            return True

        # If the list is sorted, then the L[i - 1] will always be smaller than L[i]
        for i in range(1, len(tree)):
            if tree[i - 1] >= tree[i]:
                return False
        return True

    def inorder_traversal(self, root):
        if not root:
            return []
        return self.inorder_traversal(root.left) + [root.val] + self.inorder_traversal(root.right)     
```


## 2. [Network Delay Time](https://leetcode.com/problems/network-delay-time/description/)

### 2.1 Using Dijkstra

**🎯 Understanding the Test Cases:**

*Test Case 1:*

`times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2`

*Explanation:*

This is the first test case from the problem description. Starting node is given as $k=2$. From $2$, it will take 1 unit of time to reach both nodes 1 and 3. The signal can travel along both edges in parallel. So reaching both nodes only takes 1 unit of time. But we still have one more node left. After reaching node 3, it will take another 1 unit of time to reach node 4. Once the last node is reached, we return the time taken. In this case, it was 2. Can you relate this to the analogy discussed in one of the lectures?

**📚 Problem Overview:**

We have an array times where each element `times[i] = [u, v, w]` represents a directed edge from node `u` to node `v` with weight `w`. The edges are directed and weighted. 

Can you convert the `times` array into a graph?\
We can represent the `times` array as an adjacency list:

```
WList = { i:[] for i in range(1, n+1) }
for u, v, w in times:
    WList[u].append((v, w))
```

We need to find out the minimum time it will take for the signal to reach all nodes if started from the node `k`. Can you identify what type of a problem this is? 🤔 

This is a shortest path problem. Since the weights are non-negative, we can solve it using Dijkstra's algorithm. 
The minimum time for the signal to reach all nodes is the maximum shortest path found after running Dijkstra. In other words, it’s the time when the last node receives the signal.

#### 💻 Code Implementation:
```
import heapq

class Solution:
    def networkDelayTime(self, times: List[List[int]], n: int, k: int) -> int:
        # Convert the "times" array into an adjacency list
        WList = { i:[] for i in range(1, n+1) }
        visited = {i : False for i in range(1, n+1)}
        for u, v, w in times:
            WList[u].append((v, w))


        # Run Dijkstra on it
        dist = {node: float('inf') for node in range(1, n+1)}
        dist[k] = 0

        # Priority queue
        heap = [(0, k)]  # (distance, node)

        while heap:
            # By default, heapq.heappop(heap) returns the minimum element from heap
            current_dist, node = heapq.heappop(heap) 
            visited[node] = True

            if current_dist > dist[node]:
                continue

            for neighbor, weight in WList[node]:
                if visited[neighbor]:
                    continue
                distance = current_dist + weight
                if distance < dist[neighbor]:
                    dist[neighbor] = distance
                    heapq.heappush(heap, (distance, neighbor))

        # The minimum time taken for the signal to reach all the vertices, is the same as the last vertex to 
        # get "burnt" in Dijkstra's algorithm, which is the same as maximum of shortest distance values
        max_dist = max(dist.values())
        return max_dist if max_dist != float('inf') else -1 # Check if all nodes can be reached or not.
```


## 3 [Maximum Spending After Buying Items](https://leetcode.com/problems/maximum-spending-after-buying-items/description/)

**🎯 Understanding the Test Cases:**

The test cases given in the problem statement are detailed. So we have not discussed them here. One thing to note in the given test cases: The order in which possible values are getting removed from the matrix is from smallest to largest. Can you think of the reason why?

### 3.1 Brute Force

We are given an $m \times n$ matrix, and we can take out values only from the right end of each row. Further, the amount spent on a day is defined as ```day number * value of the product```. 

To maximize spending over ```m * n``` days, can you think of which element do we need to remove first?
It should be the smallest element that we can remove, because the spending can be maximized if larger values are taken near the end of ```m * n```th day. So, from each rows' last column, we need to pop the one that is the smallest. 

*Pseudocode:*
- Keep a variable for max spending.
- Iterate day counter $ m \times n$ times
    - Keep a minimum value variable. 
    - Find out the minimum last element for every row- since we can only take elements from the rightmost end.
    - Pop and multiply the minimum with the day counter and add this to the max spending. 

Time Complexity: $O(m^2n)$
#### 💻 Code Implementation:
```
class Solution:
    def maxSpending(self, values: List[List[int]]) -> int:
        m, n = len(values), len(values[0])  
        
        max_spending = 0

        # Iterate over all days
        for day in range(1, m*n + 1):
            # Find out the minimum val of the product that can be bought
            min_val, min_index = float('inf'), None
            for j in range(m):
                # Check if the row is not empty
                # And since only rightmost product can be bought, only compare L[j][-1]th element
                if values[j] and values[j][-1] < min_val: 
                    min_val = values[j][-1]
                    min_index = j

            # Pop the minimum value product and add the value to answer
            product_val = values[min_index].pop()
            max_spending += day * product_val
        return max_spending
```

### 3.2 Using Minheaps

We still need to find the minimum of the rightmost elements and add it to the counter. Can you think of a data structure that lets us efficiently remove the minimum element? Minheaps can be used. 

Let's see how: Let's assume we have the ```values``` array as follows:
$$ 
\begin{bmatrix}
4 & 3 \\
7 & 5
\end{bmatrix}
$$

The heap will consist of all the rightmost elements: $heap = [ 3, 5] $. From this minheap, we can remove the minimum easily. After ```delete_min``` operation, heap looks like: $ heap = [ 5] $ and the matrix ```values``` looks like this:

$$ 
\begin{bmatrix}
4 \\
7 & 5
\end{bmatrix}
$$

Now, for the first row, the rightmost element is $4$. We need to add it to the minheap for the next iteration for this approach to work well as this: $ heap = [4, 5]$. To do this well, we'll keep track of the row_index to note from which row was this minimum popped.

Here's the pseudocode:

- Keep the max spending variable
- Create the minheap for the rightmost elements. 
- Iterate day counter over $ m \times n$ times
- Remove the minimum from the Minheap. 
- From the row from which the minimum was popped, insert the new last element in the heap.


Time Complexity: $O(mn \cdot log(m))$

#### 💻 Code Implementation:
```
class Minheap:
    def __init__(self):
        self.A = []

    def min_heapify(self,k):
        l = 2 * k + 1
        r = 2 * k + 2
        smallest = k
        if l < len(self.A) and self.A[l][0] < self.A[smallest][0]:
            smallest = l
        if r < len(self.A) and self.A[r][0] < self.A[smallest][0]:
            smallest = r
        if smallest != k:
            self.A[k], self.A[smallest] = self.A[smallest], self.A[k]
            self.min_heapify(smallest)
        
    def delete_min(self):
        item = None
        if self.A != []:
            self.A[0],self.A[-1] = self.A[-1],self.A[0]
            item = self.A.pop()
            self.min_heapify(0)
        return item

    def insert_in_minheap(self,d):
        self.A.append(d)
        index = len(self.A)-1
        while index > 0:
            parent = (index-1)//2
            if self.A[index][0] < self.A[parent][0]:
                self.A[index],self.A[parent] = self.A[parent],self.A[index]
                index = parent
            else:
                break
            
class Solution:
    def maxSpending(self, values: List[List[int]]) -> int:
        m, n = len(values), len(values[0])  

        # Using Priority Queues- O(mn log(m))
        heap, max_spending = Minheap(), 0

        # Create a minimum heap from the last elements
        for i in range(m):
            heap.insert_in_minheap([values[i][-1], i]) # Every elem in heap is: [value, row_number]
        
        # Iterate over days
        for day in range(1, m*n+1):
            min_val, min_store_index = heap.delete_min() # Delete based on value
            values[min_store_index].pop() # Pop from values array
            max_spending += day * min_val
            if values[min_store_index]:
                heap.insert_in_minheap([values[min_store_index][-1], min_store_index])

        return max_spending
```


## 4. [Average of Levels in Binary Tree](https://leetcode.com/problems/average-of-levels-in-binary-tree/description/)

**🎯 Understanding the Test Cases:**

*Test Case 1:*

`root = [3,9,20,null,null,15,7]`

*Explanation:*

In the given figure, the first level has only one node. So the average of 1st level is: $3$. In the second level, there are two nodes: 9 and 20. So, the average of the second level is: $\dfrac{(9 + 20 )}{2}$. Similarly, the third level has two nodes: 15 and 7. So, the average of the third level is: $\dfrac{(15 + 7)}{2}$. In general, if there are $n$ nodes in a level, we need to return average as: $\dfrac{\text{sum of the n nodes' values}}{n}$

### Using BFS:

**📚 Problem Overview:**

To find the average of values for each level of the tree, we need to process the tree **level-by-level**. Remember, a tree is just a graph with **no cycles** , so we can apply **graph algorithms** to trees too!

When you hear "level-by-level," which algorithm comes to mind? 🤔

Yep, good old **BFS** (Breadth-First Search)! BFS helps us traverse the tree one level at a time, which is exactly what we need here. For each level, we collect all the nodes, then simply compute the **average** of their values.😊

#### 💻 Code Implementation:
```
from collections import deque

class Solution:
    def averageOfLevels(self, root: Optional[TreeNode]) -> List[float]:
        q = deque()
        q.append(root)

        answer = []
        
        while q:
            level_sum = 0 # The sum of the values of a given level
            level_nodes = len(q)  # Number of nodes at the current level

            # Since we use FIFO, this loop ensures that all nodes belonging to one level are popped
            for i in range(level_nodes):
                node = q.popleft()
                
                level_sum += node.val

                # Add left and right children to the queue if they exist. 
                # Nodes in binary tree have at most 2 neighbors only- the right and left child
                if node.right: 
                    q.append(node.right)
                if node.left: 
                    q.append(node.left)
                    
            # Calculate the average for the current level
            answer.append(level_sum / level_nodes)

        return answer
```
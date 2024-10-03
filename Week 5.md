# Leet Code Problems on Shortest Path and Minimum Cost Spanning Trees

##### 1. [Min Cost To Connect All Points](#1-min-cost-to-connect-all-points-1)
##### 2. [Path with Maximum Probability](#2-path-with-maximum-probability-1)
##### 3. [Cheapest Flight Within K Stops](#3-cheapest-flight-within-k-stops-1)


## 1. [Min Cost To Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/description/)

**🎯 Understanding the Test Cases:**

*Test Case 1:*

`points = [[0,0],[2,2],[3,10],[5,2],[7,0]]`

The first test case from the problem statement. 

*Explanation:*

There are many ways to connect all points. For example:
- Path: `[0,0] -> [7,0] -> [3,10] -> [2,2] -> [5,2]`
- Total Manhattan Distance: $7 + 14 + 9 + 3 = 33$ (not optimal)

But the *optimal* way connects them with a total distance of *20*, as shown in the problem description. 

Key idea: From any point, you can connect to any other point, and the edge cost is the Manhattan distance. The goal is to minimize the total cost (sum of all edges).

### 1.1 Minimum Cost Spanning Trees 

📚 **Problem Overview:**

We need to find the *minimum cost* to connect all points. This is a *Minimum Cost Spanning Tree (MCST)* problem! 

🛠️ How do we turn the points into a graph?

- Each point can be a node. 
- The edges are the Mahattan distances between every pair of points in the graph. That means it's a complete graph—each node is connected to every other one.

*Pseudocode for building this graph:*

- Run a nested loop:
    - For each point
        - For every other point, 
            - Compute the distance and store the edge in an adjacency matrix or an adjacency list

**💡 The Solution:**

1. Build the Graph
2. *Find the MCST:*
   - Use *Prim’s* or *Kruskal’s* algorithm to find the Minimum Cost Spanning Tree. 🌐 
   - Then, just sum up the edge weights of the Tree. That's our minimum cost! 🏆


#### 💻 Code Implementation:
```
def kruskal(WList):
    """Kruskal's as per the PDSA book"""
    (edges,component,TE) = ([],{},[])
    for u in WList.keys():
        edges.extend([(d,u,v) for (v,d) in WList[u]])
        component[u] = u

    edges.sort()

    for (d,u,v) in edges:
        if component[u] != component[v]:
            TE.append((u,v, d))
            c = component[u]
            for w in WList.keys():
                if component[w] == c:
                    component[w] = component[v]
    return TE

def compute_min_cost(TE) -> int:
    # Compute the Minimum Cost based on Tree Edges
    min_cost = 0
    for u, v, d in TE:
        min_cost += d
    return(min_cost)

class Solution:
    def minCostConnectPoints(self, points: List[List[int]]) -> int:
        # Init Adjacency Matrix
        AList = { i : [] for i in range(len(points))}

        # Each point can be connected to every other point and their manhattan distance can be computed.
        for i in range(len(points)):
            x1, y1 = points[i][0], points[i][1]

            for j in range(i + 1, len(points)):
                x2, y2 = points[j][0], points[j][1]
                dist = abs(x1 - x2) + abs(y1 - y2)

                # Distance from point i to j is same as from j to i. So init both entries
                AList[i].append((j, dist))
                AList[j].append((i, dist))

        tree_edges = kruskal(AList)
        minimum_cost = compute_min_cost(tree_edges)
        return minimum_cost
```

## 2. [Path with Maximum Probability](https://leetcode.com/problems/path-with-maximum-probability/description/)

### 2.1 Using Dijkstra

### 📚 Problem Overview:

We need to find the *maximum probability* of getting from a `start_node` to an `end_node` in a graph. Each edge in the graph has a *success probability*, and we want to maximize the total probability along the path. For example, if there's a path `A -> B -> C` with probabilities `A -> B = 0.5` and `B -> C = 0.5`, the total probability for the path `A -> C` is $0.5 \times 0.5 = 0.25$.

**💡 The Solution:**

It’s similar to finding the shortest path, but here we are finding the *path with the highest probability*!

- We create an adjacency list where each node is connected to its neighbors with the probability of success for each edge.

- *Max-Heap*: To keep track of the most promising path, we can use a *max-heap*. But since Python’s `heapq` is a *min-heap*, we store *negative probabilities* (because minimizing negatives is same as maximizing positives).

- We can run *Dijkstra’s algorithm*, but instead of minimizing distance, we maximize probability. We stop as soon as we reach the `end_node`.

If we never reach the end node, we return `0.0` because there's no valid path! 

#### 💻 Code Implementation:

```
class Solution:
    def maxProbability(self, n: int, edges: List[List[int]], succProb: List[float], start_node: int, end_node: int) -> float:
        # Create an adjacency list
        WList = defaultdict(list)
        for i, (start, end) in enumerate(edges):
            probab = succProb[i]
            WList[start].append((end, probab))
            WList[end].append((start, probab))

        # Max-heap (negative probabilities to simulate a max-heap)
        heap = [(-1.0, start_node)]  # (negative probability, node)
        dist = [-1.0] * n            # Store the maximum probability to reach each node
        dist[start_node] = 1.0        # Start node has a probability of 1

        while heap:
            # Pop the node with the maximum probability
            current_prob, node = heapq.heappop(heap)
            current_prob = -current_prob  # Convert back to positive

            # If we reached the end node, return the probability
            if node == end_node:
                return current_prob

            # Explore neighbors
            for neighbor, weight in WList[node]:
                new_prob = current_prob * weight
                # Only update if we found a higher probability path to neighbor
                if new_prob > dist[neighbor]:
                    dist[neighbor] = new_prob
                    heapq.heappush(heap, (-new_prob, neighbor))

        # If we never reach the end node
        return 0.0
```


## 3. [Cheapest Flight Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/description/)

### 3.1 Using Dijkstra

#### 💻 Code Implementation:
```
class Solution:
    def findCheapestPrice(self, n, flights, src, dst, k):
        # Create the adjacency list
        adj_list = {i: [] for i in range(n)}
        for flight in flights:
            from_city, to_city, price = flight
            adj_list[from_city].append((to_city, price))
        
        # Priority queue -> (current price, city, stops)
        pq = [(0, src, 0)]  # (price, city, stops)
        
        ## BFS with Heap

        # visited[city] stores min number of stops to reach the city
        visited = {}
        
        while pq:
            price, city, stops = heapq.heappop(pq)
            
            # If we reach the destination, return the price
            if city == dst:
                return price
            
            # If we have visited this city with fewer stops before, skip
            if city in visited and visited[city] < stops:
                continue
            
            # Store the minimum number of stops to this city
            visited[city] = stops
            
            # If we have more stops than k, continue to the next city
            if stops > k:
                continue
            
            # Add neighbors to explore
            for neighbor, cost in adj_list[city]:
                new_price = price + cost
                heapq.heappush(pq, (new_price, neighbor, stops + 1))
        
        # If no valid route found
        return -1
```

# Leet Code Problems on Shortest Path and Minimum Cost Spanning Trees

## 1. [Min Cost To Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/description/)

### 1.1 Minimum Cost Spanning Trees

- The question is asking for the minimum cost to connect all the points. Can you identify what type of a problem this is? It is a **MCST** problem!
- How can you convert the given points into a graph? Each point can represent a node in the graph. The edges are the distances between any two points in the graph. So, it will be a complete graph- where each node is connected to every other node with some cost.
- Run a nested loop:
    - For each point
        - For every other point, 
            - Compute the distance and store the edge in an adjacency matrix or an adjacency list

- Once we have the graph representation, we can run either or Prim's or Kruskal's algorithm on this graph to get the Minimum Cost Spanning Tree's edges. 
- To compute the minimum cost, iterate over the Tree edges and sum the edge weights

#### Code:
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



## 2. [Cheapest Flight Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/description/)


## 3. [Network Delay Time](https://leetcode.com/problems/network-delay-time/description/)

### 3.1 Using Dijkstra
We are given an array ```times``` where ```times[i] = [u_i, v_i, w_i]```, where ```u_i``` is the source node, ```v_i``` is the destination and ```w_i``` is the weight of that edge. It is clear that this is a directed graph. 

Can you convert the ```times``` array into a more graph suited representation like an adjacency list or an adjacency matrix?
- Here's the code snippet that can accomplish this:
```
WList = { i:[] for i in range(1, n+1) }
visited = {i : False for i in range(1, n+1)}
for u, v, w in times:
    WList[u].append((v, w))
```

The question is asking us to find out the minimum time it will take for the signal to reach all nodes if started from the node ```k```. Can you identify what type of a problem this is?
- It is a shortest path problem. Since time will be non-negative, we can use Dijkstra here. 

Observe the following:
- The minimum time taken to reach all vertices is the same as the minimum time it would take to "burn" the last reached vertex when using Dijkstra. This is the same as the maximum shortest path after running Dijkstra. 

#### Code:
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
            current_dist, node = heapq.heappop(heap) # By default, heapq.heappop(heap) returns the minimum element from heap
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

        # The minimum time taken for the signal to reach all the vertices, is the same as the last vertex to get "burnt" in Dijkstra's algorithm, which is the same as
        # maximum of shortest distance values
        max_dist = max(dist.values())
        return max_dist if max_dist != float('inf') else -1 # Check if all nodes can be reached or not.
```
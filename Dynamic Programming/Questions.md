# Leet Code Problems Based on Dynamic Programming

##### 1. [Climbing Stairs](#1-climbing-stairs-1)
##### 2. [House Robber](#2-house-robber-1)
##### 3. [Fibonacci Numbers](#3-fibonacci-numbers-1)
##### 4. [Triangle](#triangle-1)
##### 5. [Coin Change](#5-coin-change-1)

## 1. [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/description/)

### 1.1 Using Dynamic Programming
Can you find out the recursive approach to this problem? Try to think about the base cases and the the recursive relation. 

- When you are on the ```n```th step, there are zero ways to reach ```n```th step. If you are on the ```n-1```th step, there is only one way to reach ```n```th step. Lastly, if you are on the ```n-2```th step, there are two ways of reaching the ```n```th step. 

- Further, if you observe, the following relationship holds:
$$
ways(k) = ways(k - 1) + ways(k - 2)
$$

The number of ways to reach a stair $k$ is the sum of the number of ways to reach the stairs $k-1$ and $k-2$. 

Can you think of a solution that uses dynamic programming and solves this problem in linear time?

#### Code:
```
class Solution:
    def climbStairs(self, n: int) -> int:
        L = [0]*n # Initialize the dynamic programming array
        
        # Base Cases
        if n == 1: return 1 
        if n == 2: return 2
        
        L[0] = 1
        L[1] = 2

        # ways[i] = ways[i - 1] + ways[i - 2]
        for i in range(2, n):
            L[i] = L[i - 1] + L[i - 2]
        
        # Last element corresponds to the nth stair. So return that value.
        return L[-1]
```

## 2. [House Robber](https://leetcode.com/problems/house-robber/description/)
### 2.1 Using Dynamic Programming

We cannot break two adjacent houses. That is, ```nums[i]``` and ```nums[i+1]``` cannot be stolen together. So, for each house, we have to decide whether we should rob it or not. Now, let's think about how we can keep track of the maximum amount of money you can rob **up to** each house. We could use an array where each element represents the maximum amount of money that can be robbed up to that house. That is, ```maximum_amount[i]``` tells us the maximum amount of money that could be stolen up to the house ```i```. 

What might be the base cases for this ```maximum_amount```, abbreviated as ```m_a```, array?
- When it's the first house, then ```m_a[0]``` should the amount in the first house. 
- When it's the second house, then ```m_a[1]``` should be the maximum for ```nums[0]``` and ```nums[1]```, since they are adjacent and we cannot rob both. 

Can you come up with a recursive relation that solves this problem?
- From the third house onwards, we can either rob the house or choose to not rob it. If we rob it, we can't rob the previous house. If we don't rob it, we can rob the previous house. 
- In our ```m_a``` array, we can represent the relation as this:
    - $ m_a[i] = max(m_a[i-1], m_a[i-2] + nums[i])$.
    - If we don't rob the current house, we are taking the amount stolen up to the previous house. 
    - If we rob the current house, then we are taking the amount stolen up to the second previous house and adding the amount from the current house to it. 

- Whatever value we have accumulated till the last house will be the maximum amount that we can steal. 
- Time Complexity: $O(n)$

#### Code:
```
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) == 1:
            return nums[0]

        maximum_amount = [0] * len(nums)
        
        # Base cases
        maximum_amount[0] = nums[0]
        maximum_amount[1] = max(nums[0], nums[1])

        for i in range(2, len(nums)):
            maximum_amount[i] = max(maximum_amount[i-1], maximum_amount[i-2] + nums[i])
        
        return maximum_amount[-1]
```

## 3. [Fibonacci Numbers](https://leetcode.com/problems/fibonacci-number/description/)
### 3.1 Using Dynamic Programming

Let's begin by thinking about the problem. We need to calculate the Fibonacci number $F(n) $ for a given $n$. The Fibonacci sequence is defined as:

- $ F(0) = 0$
- $F(1) = 1$
- $F(n) = F(n-1) + F(n-2) \text{ for } n > 1$

Consider what this means. $F(n)$ is the sum of the two preceding numbers in the sequence. This suggests that if we know $F(n-1)$ and $F(n-2)$, we can find $F(n) $.

But how do we approach calculating $F(n)$?

First, think about the simplest cases, $F(0)$ and $F(1)$. These are our base cases because they are defined directly without requiring any further calculations.

Now, if $n = 2$, we use the formula $F(2) = F(1) + F(0)$. Since we already know $F(1) = 1 $ and $ F(0) = 0$, we can easily calculate $F(2) = 1 + 0 = 1 $.

What if $n = 3$? Again, using the formula, $F(3) = F(2) + F(1) $. From the previous steps, we know $ F(2) = 1 $ and $F(1) = 1 $, so $F(3) = 1 + 1 = 2 $.

Notice that each time we calculate a Fibonacci number, we rely on the results of previous calculations. 

Now, let’s consider a general approach. If we want to calculate $F(n) $, how can we systematically work our way up from the base cases to $F(n) $?

One way is to use **recursion**. We can define a function that calls itself with smaller values of $  n $ until it reaches the base cases. However, while recursion is elegant, it has a downside—repeatedly solving the same subproblems, which leads to inefficiency.

Can we improve this?

Yes, we can! Instead of recalculating the same Fibonacci numbers multiple times, we can **store** the results of each calculation and reuse them when needed.

So here's the approach that we will take:
- We will define an array named ```fibtable```, where ```fibtable[i]``` is the ```ith``` number in the fibonacci sequence. 
- Observe this relationship: ```fibtable[i] = fibtable[i - 1] + fibtable[i - 2]```
- Once we hardcode the base cases, we can iterate over the ```i``` upto ```n```, and update the ```fibtable[i]``` as per the above update condition. 


#### Code:
```
class Solution:
    def fib(self, n: int) -> int:
        # Initialize the fibtable array to be all zeros
        fibtable = [0] * (n + 1)

        # Base Case 1: Where n == 0
        if n == 0: return fibtable[0]

        # Base Case 2: Where n == 1
        fibtable[1] = 1

        for i in range(2, n + 1):
            #F(n) = F(n - 1) + F(n - 2)
            fibtable[i] = fibtable[i - 1] + fibtable[i - 2]
        
        # fibtable[i] is the ith fibonacci number. Thus, to get nth fibonacci number, return fibtable[n]
        return fibtable[n]
```

## 4. [Triangle](https://leetcode.com/problems/triangle/description/)

### 4.1 Using Dynamic Programming

The question is asking for us to find the minimum path sum from the top to the bottom of the triangle, where each step can be to only the ```ith``` or ```i + 1 th``` cell in the row below. 

Can you break this problem into further subproblems?
- If we start from the topmost cell of the triangle in the test case 1, the minimum path sum could be described using the following structure:
$MPS(2) = 2 + min(MPS(3), MPS(4))$, where $MPS(x) $ tells us the minimum path sum starting from a particular cell in the triangle. More generally, 
$MPS(triangle[row][col]) = triangle[row][col]+ min(MPS(triangle[row + 1][col]), MPS(triangle[row + 1][col + 1]))$

Can you identify the base cases here?
- The base cases are all the leaf nodes of the triangle where there are no more children. 

Since we are solving subproblems and then combining those subproblems to solve the original problem, we can use dynamic programming here. Further, using bottom-up approach here might be better than using the top-down approach. 

After processing the last row, that is- the row of the base cases, we can move one level up in the triangle. After processing that level, we get a minimum path of length 2 for each of the cells in the second last row. More generally, after processing a row, each element in that row would hold the minimum path sum from that element to the bottom of the triangle. After processing the entire triangle, the top element of the triangle would hold the minimum path sum from the top to the bottom of the triangle, which is the answer to the problem.

Here's how we can approach the pseudocode:
- Start with the second last row. ( We don't need to start from the last row, as the last row is the base case and it will return values just the same as what are there in the cells)
- Then for every cell of that row, we apply the following update rule:
$$MPS(triangle[row][col]) = triangle[row][col]+ min(MPS(triangle[row + 1][col]), MPS(triangle[row + 1][col + 1]))$$
- Repeat the same for every row till you reach the topmost row. 
- Return the value from the cell at the top- first cell of the first row. 

```
class Solution:
    def minimumTotal(self, triangle: List[List[int]]) -> int:
        # Start from the second-to-last row of the triangle
        for row in range(len(triangle) - 2, -1, -1):
            for col in range(len(triangle[row])):
                # Update the current element with the sum of the element itself
                # and the minimum of the two adjacent elements in the row below
                triangle[row][col] = triangle[row][col] +  min(triangle[row + 1][col], triangle[row + 1][col + 1])

        # The top element now contains the minimum path sum
        return triangle[0][0]
```

## 5. [Coin Change](https://leetcode.com/problems/coin-change/description/)
### 5.1 Using Dynamic Programming

The first thought here can be that we need to use the greedy strategy. But as we can see from the test cases, the greedy strategy won't always give us the optimal solution. 

Can you try to break this problem down into subproblems and identify if there are overlapping subproblems?
- We can observe that for a given amount, the minimum number of coins needed to get to that amount is one plus the minimum number of coins required to get $amount - coin$. Concisely, we can write this as:
    - $mincoins(amount) = mincoins(amount - coin) + 1$
- If you expand the subproblems for every coin in the given test case, you can observe that the subproblems overlap. 

How can you use dynamic programming to solve this problem?
- Since we know the subproblem structure, we can use memoization. 
- Keep an array of length $amount + 1$, where each index ```i``` stores the minimum number of coins required to get change for amount ```i```. Let this array be called ```dp```.
- Initially this array will be set to infinity. 
- For each element, we will update the value as: 
    - ```dp[i] = min(dp[i], dp[i - coin] + 1)```
    - We will do this for every amount in ```range(0, amount + 1)```, and we will do this for every ```coin``` in the ```coins``` array.

What can be the base cases here?
- To get to amount $0$, we need $0$ coins. Thus, ```dp[0] = 0```. 
- Further, for every coin in the given coins array, ```dp[coin] = 1```, as one coin will suffice to get to that amount. 

#### Code:
```
class Solution:
    def coinChange(self, coins, amount):
        # Initialize a dp array where dp[i] will hold the minimum number of coins required for amount i
        dp = [float('inf')] * (amount + 1)
        
        # Base case: No coins are needed to make amount 0
        dp[0] = 0

        for coin in coins:
            dp[coin] = 1
        
        # Fill the dp array
        for coin in coins:
            for x in range(coin, amount + 1):
                dp[x] = min(dp[x], dp[x - coin] + 1)
        
        # If dp[amount] is still inf, it means it's not possible to make that amount with given coins
        return dp[amount] if dp[amount] != float('inf') else -1
```
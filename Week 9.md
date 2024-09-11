# Leet Code Problems Based on Dynamic Programming

##### 1. [Climbing Stairs](#1-climbing-stairs-1)
##### 2. [House Robber](#2-house-robber-1)
##### 3. [Fibonacci Numbers](#3-fibonacci-numbers-1)
##### 4. [Triangle](#4-triangle-1)
##### 5. [Coin Change](#5-coin-change-1)
##### 6. [Best Time to Buy and Sell Stock III](#6-best-time-to-buy-and-sell-stock-iii-1)

## 1. [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/description/)

**📚 Problem Overview:**

We need to find the number of ways to reach the top of a staircase with `n` steps. At each step, you can either:
1. Take a *single step*, or
2. Take a *double step*.

**🎯 Understanding the Test Cases:**

*Test Case 1:*
`n = 3`

There are three ways to reach the third step:
- $0$ $\rightarrow$ $1$ $\rightarrow$ $2$ $\rightarrow$ $3$. In this way, we are taking one step every time. 
- $0$ $\rightarrow$ $1$ $\rightarrow$ $3$. Here, we jumped $2$ places from the $1$ st step. 
- $0$ $\rightarrow$ $2$ $\rightarrow$ $3$. Here, we jumped $2$ places from the $0$ th step, and then $1$ step from the $2$ nd step.

Since there are three ways we return 3.

**🤔 Key Observations:**

- *Base Cases*:
    - To reach step `0`, you need to take $0$ steps ( no need to take any step!).
    - To reach step `1`, there is only $1$ way. 
    - To reach step $2$, there are two ways: $ 0 \rightarrow 1 \rightarrow 2$ and $0 \rightarrow 2 $

- *Recursive Relationship:*
    - If you're on step `n`, you don't require further steps.
    - From step `n-1`, you have *1 way* to reach the top.
    - From step `n-2`, you have *2 ways* (you can jump directly to `n` or take two steps).
    - To reach step `i`, you can either come from step `i-1` or step `i-2`. So the relation is:
    $$ \text{ways}(i) = \text{ways}(i-1) + \text{ways}(i-2) $$


**💡 The Solution: Dynamic Programming** 

We can solve this problem in *linear time* using dynamic programming by storing the number of ways to reach each step and using the above relation to build up from the base cases.

- Use an array to store the number of ways to reach each step.
- Start from step `0` and build up using the relation:
  - `ways(i) = ways(i-1) + ways(i-2)`

Time Complexity: $O(n)$


#### 💻 Code Implementation:
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

**📚 Problem Overview:**

You need to rob houses along a street, but you can’t rob two *adjacent houses*. The goal is to figure out the *maximum amount of money* you can rob, without breaking the adjacency rule.

**🤔 Key Observations:**

- If you're at the *first house*, you can only rob that house. 
  - So, the maximum money you can rob up to house $1$ is just `nums[0]`.
  
- For the *second house*, you either rob the first or the second, but not both. 
  - So, the max money that you can steal up to house $2$ is $max(nums[0], nums[1])$.

- For any house from the *third house* onwards, you have two choices:
  - *Rob* this house and add its money to the max amount from two houses back (since you can’t rob the adjacent house).
  - *Skip* this house and keep the money robbed up to the previous house.

**💡 The Solution:**

We use a dynamic programming array `maximum_amount` where each `maximum_amount[i]` holds the maximum money you can rob *up to* house `i`:
- *Base cases*:
  - `maximum_amount[0] = nums[0]`
  - `maximum_amount[1] = max(nums[0], nums[1])`
  
- *Recursive relation:*
  - $maximum\_amount[i] = max(maximum\_amount[i-1], maximum\_amount[i-2] + nums[i])$
  
This ensures that for each house, you decide whether to rob it (and add the money from two houses ago) or skip it (and take the max up to the last house). The result will be the value in `maximum_amount[n-1]`, where `n` is the total number of houses.

**Time complexity**: $O(n)$

#### 💻 Code Implementation:
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

This problem is discussed in the lectures. The approach used here is exactly the same.

#### 💻 Code Implementation:
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

**📚 Problem Overview:**

We need to find the minimum path sum in a triangle from the top to the bottom. At each step, we can move to the element directly below or diagonally below-right. The task is to minimize the sum of the numbers along this path.

In other words, the question is asking for us to find the minimum path sum from the top to the bottom of the triangle, where each step can be to only the ```ith``` or ```i + 1 th``` cell in the row below. 

One way we can do this is by listing down all the paths and then take the minimum sum path from that. But it won't be efficient!

**🤔 Key Observations:**

- To get to a certain cell, you can only come from the cell directly above it or the one diagonally left above it.
- The problem can be broken into smaller subproblems. The minimum path sum at any cell depends on the minimum of the two cells below it.
- For a cell `triangle[row][col]`, the relation is: 
$$MPS(triangle[row][col]) = triangle[row][col] + \min(MPS(triangle[row + 1][col]), MPS(triangle[row + 1][col + 1]))$$
- The base cases are the last row of the triangle. There are no children below, so the values in the last row stay the same.

Since we are solving overlapping subproblems and then combining those subproblems to solve the original problem, we’ll use dynamic programming, starting from the bottom of the triangle and working our way up to the top.

**💡 The Solution Approach:**
- After processing the last row, that is- the row of the base cases, we can move one level up in the triangle. 
- After processing that level, we get a minimum path of length 2 for each of the cells in the second last row. More generally, after processing a row, each element in that row would hold the minimum path sum from that element to the bottom of the triangle. 
- After processing the entire triangle, the top element of the triangle would hold the minimum path sum from the top to the bottom of the triangle, which is the answer to the problem.

#### 💻 Code Implementation:
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

**📚 Problem Overview:**

We need to find the minimum number of coins required to make a given amount. We can use coins from the given list. If it’s impossible to make the amount, return $-1$.

The first thought here can be that we need to use the greedy strategy. But as we can see from the test cases, the greedy strategy won't always give us the optimal solution.

**🤔 Key Observations:**

- For any amount, the minimum number of coins to make that amount can be thought of as:
  $$ \text{mincoins}(amount) = \text{mincoins}(amount - coin) + 1 $$
- This means for any amount, we can take a coin and reduce the problem to finding the minimum coins needed for the remaining amount. 
- Can you figure out why the subproblems are overlapping?

**💡 The Solution Approach:**

Since we have overlapping subproblems, we use dynamic programming:
- Keep an array of length $amount + 1$, where each index ```i``` stores the minimum number of coins required to get change for amount ```i```. Let this array be called ```dp```.
1. Create a `dp` array of size `amount + 1`, where `dp[i]` stores the minimum number of coins to make amount `i`. Initially, set all values to infinity (`inf`), except `dp[0] = 0`, because we need 0 coins to make amount 0.
2. For each coin, update the `dp` array:
   - For every `amount` from 0 to the given amount, update the minimum coins:
   - $ \text{dp[i]} = \min(\text{dp[i]}, \text{dp[i - coin]} + 1) $

3. After processing all amounts, if `dp[amount]` is still `inf`, return $-1$ (because it’s impossible to make that amount). Otherwise, return `dp[amount]`.   

**Time Complexity:** $O(amount * coins)$

#### 💻 Code Implementation:
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

## 6. [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

### 6.1 Using Dynamic Programming

**📚 Problem Overview:**

We need to find the maximum profit we can earn by buying and selling a stock at most two times. We need to buy before we can sell the stock. Further, we are allowed to transact either once or twice but not more than twice. 

**🤔 Key Observations:**

Since we’re allowed to make *two transactions*, for any given day, the total profit is the sum of:

1. Maximum profit *before* that day.
2. Maximum profit *after* that day.

For example, let’s take the prices list: `[3, 3, 5, 0, 0, 3, 1, 4]`.

🔹 On *day 3* (price = 0):
   - Maximum Profit *before* day 3: The best time to buy and sell *before* day 3 is buying at 3 (day 0) and selling at 5 (day 2). So, *5 - 3 = 2*.
   - Maximum Profit *after* day 3: The best time to buy *after* day 3 is buying at 0 (day 3) and selling at 4 (day 7). So, *4 - 0 = 4*.

By adding them together, the total profit on day 3 would be **2 + 4 = 6**.

We repeat this process for each day to find the total maximum profit. The day with the highest total profit is where we should ideally transact to make the most money!

The key idea here is that we can compute the **before** and **after** arrays in **linear time**.

For the *after* array:
   - We start from the end of the prices list and keep track of the *largest number* we’ve seen so far. This largest number will be the *selling price* for any `prices[i]` we see along the way.
   - So, for each day, the profit is the difference between the *largest number* (sell price) and `prices[i]` (buy price).

We can do a similar thing for the *before* array, but instead of keeping track of the largest number, we will track *smallest* number.

#### 💻 Code Implementation:
```
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        after = [ 0 ] * n
        max_from_right = prices[-1]

        # Find out the maximum profit we can earn in one transaction after an index 'i'
        for i in range(n - 2, -1, -1):
            max_from_right = max(max_from_right, prices[i + 1])
            after[i] = max(max_from_right - prices[i], 0)
            after[i] = max(after[i], after[i + 1])

        # Find out the maximum profit we can earn in one transaction before an index 'i'
        before = [ 0 ] * n
        min_from_left = prices[0]
        for i in range(1, n):
            min_from_left = min(min_from_left, prices[i - 1])
            before[i] = max(prices[i] - min_from_left, 0)
            before[i] = max(before[i], before[i - 1])
        
        # Total Profit[i] = before[i] + after[i]
        # Maximum total profit = max(Total Profit)
        max_profit = 0 
        for i in range(n):
            max_profit = max(before[i] + after[i], max_profit)

        return max_profit
```

## 7. [Unique Paths-II](https://leetcode.com/problems/unique-paths-ii/description/)

### 7.1 Using Dynamic Programming

This is the grid paths with barriers problem discussed in the lecture. We will use the same idea and the code to solve this problem.

#### 💻 Code Implementation:
```
import numpy as np
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        x, y = len(obstacleGrid), len(obstacleGrid[0])

        M = np.zeros((x,y))

        # Handle corner case where the starting cell has a barrier
        if obstacleGrid[0][0] == 0: M[0, 0] = 1
        else: M[0, 0] = 0

        # Initialize the base cases
        for i in range(x):
            for j in range(y):
                if i == 0 and j > 0 and (obstacleGrid[i][j] == 0):
                    M[i,j] = M[i, j - 1]
                if j == 0 and i > 0 and (obstacleGrid[i][j] == 0):
                    M[i, j] = M[i - 1, j]
                
        # If there is obstacle, then there are 0 ways. Else we sum the left and bottom ways
        for i in range(1,x):
            for j in range(1,y):
                if obstacleGrid[i][j] == 1:
                    M[i,j] = 0
                else:
                    M[i,j] = M[i-1,j] + M[i,j-1]
        return int(M[x-1,y-1])
```
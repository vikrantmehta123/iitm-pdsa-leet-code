# Leet Code Problems Based on Dynamic Programming

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
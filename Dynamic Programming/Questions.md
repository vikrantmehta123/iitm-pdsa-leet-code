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
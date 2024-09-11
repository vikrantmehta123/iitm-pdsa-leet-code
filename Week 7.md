# Problems on Greedy Algorithms

##### 1. [N Meetings in One Room](#1-n-meetings-in-one-room-1)
##### 2. [Activity Selection](#2-activity-selection-1)
##### 3. [Minimum Number of Coins](#3-minimum-number-of-coins-1)
##### 4. [Jump Game](#4-jump-game-1)
##### 5. [Jump Game-II](#5-jump-game-ii-1)

## 1. [N Meetings in One Room](https://www.geeksforgeeks.org/problems/n-meetings-in-one-room-1587115620/1)

### 1.1 Using Greedy Approach

**📚 Problem Overview:**

We need to schedule the **maximum number of meetings** without any conflicts, given two arrays: `start` and `end`.

🤔 Can you find out what type of problem this is?\

It is a classic interval scheduling problem. 

From the lectures, we know we can solve such problems by sorting the schedules based on end times, and selecting the meetings that don't conflict.

Here's how we can solve this:
- Combine the `start` and `end` arrays into a single array of the form: $[(start_i, end_i)]$
- Sort the list based on the end times- earliest end times come first.
- Track of the end time of the last meeting.
- For each new meeting:
    - If it overlaps with the previous one (starts before the previous one ends), then skip it. 
    - Else, we increment the meeting count, and update the end time. 

#### 💻 Code Implementation:
```
class Solution:
    def maximumMeetings(self,n,start,end):
        # Combine two lists into one
        times = [ [start[i], end[i]] for i in range(n)]
        
        # Sort based on end times
        times.sort(key=lambda x:x[1])
        
        count = 0
        prev_endtime = None
        for start_time, end_time in times:
            # First meeting
            if not prev_endtime:
                prev_endtime = end_time
                count += 1
            else:
                # Check if the previous meet's endtime clashes with the start time of the current meet
                if prev_endtime < start_time:
                    prev_endtime = end_time
                    count += 1
        return count    
```

## 2. [Activity Selection](https://www.geeksforgeeks.org/problems/activity-selection-1587115620/1)

### 2.1 Greedy Approach
Same approach as the above question. 

#### 💻 Code Implementation:
```
class Solution:
    def activitySelection(self,n,start,end):
        # Rearrange the start and end arrays into a single array
        arr = [ [start[i], end[i]] for i in range(n) ]

        # Sort based on end times
        arr.sort(key=lambda x:x[1])

        count = 0
        prev_endtime = None
        for start_time, end_time in arr:
            # First activity
            if not prev_endtime:
                prev_endtime = end_time
                count += 1
            else:
                # Check if the previous activity's endtime clashes with the start time of the current activity
                if prev_endtime < start_time:
                    count += 1
                    prev_endtime = end_time
        return count
```

## 3. [Minimum Number of Coins](https://www.geeksforgeeks.org/problems/-minimum-number-of-coins4426/1)

**🎯 Understanding the Test Cases:**

*Test Case 1:*

`N = 43`

*Explanation:*

This is the first test case given in the question.

We need to find the minimum number of coins/notes to make ₹43. The best combination is:

- 2 notes of ₹20
- 1 coin of ₹2
- 1 coin of ₹1

So, we return `[20, 20, 2, 1]`.

Other combinations, like `[10, 10, 10, 10, 2, 1]`, are valid but require more coins/notes, so they aren’t optimal.

### 3.1 Using Greedy

Note: In general, we cannot say that greedy algorithm will produce correct output for such problems ( we need to use dynamic programming to solve these ). But in this problem, larger denominations can represent multiple smaller ones. For example, 3 coins of value 2 can be replaced by 1 coin of value 5 and 1 coin of value 1. So, we can use fewer large coins to replace many smaller ones.

Thus, we can use Greedy Approach:
- Start with the largest denomination.
- Subtract it from the amount until you can't anymore.
- Move to the next largest and repeat.
- Add each coin/note used to the result.

#### 💻 Code Implementation:
```
class Solution:
    def minPartition(self, N):
        denominations = [2000, 500, 200, 100, 50, 20, 10, 5, 2, 1]  # List of denominations in descending order
        result = []  # List to store the coins/notes used
        
        for coin in denominations:
            while N >= coin:  # Check how many times the coin/note can fit into the remaining amount
                N -= coin  # Subtract the coin/note value from target
                result.append(coin)  # Add the coin/note to the result list
        
        return result
```


## 4. [Jump Game](https://leetcode.com/problems/jump-game/description/)

**🎯 Understanding the Test Cases:**

*Test Case 1:*

`nums = [2,3,1,1,4]`

*Explanation:*


First, let's understand the input. $nums[0] = 2$ means that from the $0$ th index we can jump at most 2 steps. That is, we can jump to index 1 or 2. $nums[1] = 3$ means that from $1$ st index we can jump at most 3 steps. That is, we can jump to indices 2, 3, or 4 from the 1st index. We need to find out whether we can reach the last index- index 4- by making the jumps. 

- From index 0, you can jump up to 2 steps (to indices 1 or 2).
- From index 1, you can jump up to 3 steps (to indices 2, 3, or 4).

By jumping 1 step from index 0 to 1, and then 3 steps from index 1 to 4, you reach the last index. So, return True.

*Test Case 2:*

`nums = [3,2,1,0,4]`

*Explanation:*

From index 0, you can jump up to 3 steps (to index 3). But at index 3, you can't jump any further. No matter how you jump, you can't reach index 4.

But we can also take a jump of 2 steps ( or 1 steps ). Even then the maximum index that we can reach is the index 3. We cannot reach the last index- index 4. 

So, we return False.


### 4.1 Using Greedy Approach

**📚 Problem Overview:**

We need to check if we can reach the last index of the nums array. Each element in `nums[i]` tells us the maximum jump we can take from index `i`.

We can solve this problem using the Greedy Approach. 

**🤔 Greedy Approach:**

1. Use a `max_jump` variable to keep track of the **furthest index** we can reach so far.
2. Iterate Over `nums`:
   - At each step, update `max_jump` using: `max_jump = max(max_jump, nums[idx] + idx)`.
   - If `max_jump` reaches or exceeds the last index, return `True` (we can reach the end!).
3. If `idx > max_jump`, it means we are **stuck** and can't move forward, so return `False`.

#### 💻 Code Implementation:
```
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        max_jump = 0
        for idx in range(len(nums)):
            # idx <= max_jump -> case to check [1, 0, 2] like condition
            # nums[idx] + idx > max_jump to update the jump

            if idx <= max_jump and nums[idx] + idx > max_jump :
                max_jump = nums[idx] + idx
        
        # Check whether you can reach the last index or not.
        return max_jump >= len(nums) - 1
```


## 5 [Jump Game-II](https://leetcode.com/problems/jump-game-ii/)

### Using Greedy Approach
We can use similar approach as the above question.

#### 💻 Code Implementation:
```
class Solution:
    def jump(self, nums: List[int]) -> bool:
        n = len(nums)
        if n == 1:
            return 0
        
        jump_count = 0
        current_end = 0
        max_jump = 0
        
        for i in range(n - 1):
            max_jump = max(max_jump, i + nums[i])

            # When we reach the end of the current jump. For example, [3, 5, 1, 1, 1, 1]
            if i == current_end:
                jump_count += 1
                current_end = max_jump
                
                # If we can reach the end, break early
                if current_end >= n - 1:
                    break
        
        return jump_count
```
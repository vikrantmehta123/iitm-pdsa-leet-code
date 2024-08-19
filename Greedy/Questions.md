# Problems on Greedy Algorithms

##### 1. [N Meetings in One Room](#1-n-meetings-in-one-room-1)
##### 2. [Activity Selection](#2-activity-selection-1)
##### 3. [Minimum Number of Coins](#3-minimum-number-of-coins-1)
##### 4. [Jump Game](#4-jump-game-1)
##### 5. [Jump Game-II](#5-jump-game-ii-1)

## 1. [N Meetings in One Room](https://www.geeksforgeeks.org/problems/n-meetings-in-one-room-1587115620/1)

### 1.1 Using Greedy Approach
The question is asking us to find out the maximum number of meetings that could be scheduled without conflicts given two arrays- ```start``` and ```end```. 

Can you find out what type of problem this is?
- It is a classic interval scheduling problem. 

The way we go about solving such problems is by sorting the schedules based on their end times and then picking those meetings that don't conflict. 

Here's how we can approach the solution:
- Combine the ```start``` and ```end``` arrays into a single array of the form: ```[start_i, end_i]```
- Sort the list based on the times
- Keep track of the end time of the last meet.
    - If the start of the current meet overlaps with the previous end time, then we skip the meet. 
    - Else, we increment the meeting counter, and update the previous end time as the end time of the current meet. 

#### Code:
```
class Solution:
    def maximumMeetings(self,n,start,end):
        # code here
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

#### Code:
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
                if start_time > prev_endtime:
                    count += 1
                    prev_endtime = end_time
        return count
```

## 3. [Minimum Number of Coins](https://www.geeksforgeeks.org/problems/-minimum-number-of-coins4426/1)

### 3.1 Using Greedy

Note: In general, we cannot guarantee that greedy algorithm will produce correct output. However, in the case of this question, we can use greedy approach. Because the number of smaller denominations is bounded by the fact that we can use larger denominations to represent the smaller denominations. For example, the number of coins of denomination 2 cannot be more than 2, because if their number is 3, then we can replace it with one coin of denomination 5 and one coin of denomination 1. That is, with fewer coins of larger denomination, we can represent more coins of lower denomination. 

Thus, we can choose a greedy approach:
- Keep on subtracting the coin of largest denomination. Then move on to second largest, then third, and so on, till you reach 0. 
- Every time you subtract a coin / note, add the denomination to a results array.
#### Code:
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

### 4.1 Using Greedy Approach

The question is asking us to find out whether the last index can be reached or not using the given ```nums``` array. ```nums[i]``` corresponds to the maximum jump we can take from the ```ith``` index. We can solve this problem using the Greedy Approach. 

Here's how:
- We'll keep a ```max_jump``` variable, that acts like a flagpost. It tells what is the maximum index that we can reach based on the values of ```nums[i]``` we have seen so far. 
- Then we iterate over all the values of ```nums```.
    - Whenever this ```max_index``` goes beyond the length of the array, we will return ```True```. 
    - For every value in ```nums```, we update the ```max_jump``` as: ```max_jump = max(max_jump, nums[i] + i)```
    - But it is also possible that we cannot reach the last index. Consider the example ```nums = [1, 0, 2]```. In this example, once we reach the 1st index, there is no way to move forward. Can we capture this in a condition?
        - Whenever ```i > max_jump```, that means we have reached a place from where we cannot proceed further. Thus, we can return ```False``` here. 

#### Code:
```
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        max_jump = 0
        for i in range(len(nums)):
            # i <= max_jump -> case to check [1, 0, 2] like condition
            # nums[i] + i > max_jump to update the jump
            if i <= max_jump and nums[i] + i > max_jump :
                max_jump = nums[i] + i
        
        # Check whether you can reach the last index or not.
        return max_jump >= len(nums) - 1
```


## 5 [Jump Game-II](https://leetcode.com/problems/jump-game-ii/)

### Using Greedy Approach

#### Code:
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
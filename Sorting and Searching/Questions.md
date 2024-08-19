# Problems on Sorting and Searching

##### 1. [Longest Subsequence With Limited Sum](#1-longest-subsequence-with-limited-sum-1)
##### 2. [Top K Frequent Elements](#2-top-k-frequent-elements-1)
##### 3. [Sort Colors](#3-sort-colors-1)
##### 4. [Merge Intervals](#4-merge-intervals-1)

## 1. [Longest Subsequence With Limited Sum](https://leetcode.com/problems/longest-subsequence-with-limited-sum/description/)

### 1.1: Brute Force
- To find out the maximum number of elements less than or equal to some number, just add the smallest numbers. 
- Sort the list first.
- For every query in ```queries```, find out how many elements can be added by iterating over the array ```nums```.
- Complexity: $ O(mn) $
##### Code:
```
class Solution(object):
    def answerQueries(self, nums, queries):
        """
        :type nums: List[int]
        :type queries: List[int]
        :rtype: List[int]
        """
        answer = [ 0 for i in range(len(queries))]
        nums.sort() # Sort so that adding smallest elements is easier

        for i in range(len(queries)):
            query = queries[i]
            index, count, total = 0, 0, 0
            
            # For every query, keep adding the smallest element to get the maximum number of 
            # elements whose total is less than or equal to that query
            while total <= query and index < len(nums):
                if total + nums[index] > query:
                    answer[i] = count
                    break
                else:
                    total += nums[index]
                    index += 1
                    count += 1
            answer[i] = count
        return answer
```

### 1.2 Prefix Sum
- Sort the list first. 
- Keep a ```prefix_sum``` array that has this interpretation: ```i```th index stores the sum of first ```i``` smallest elements. Note that this array will be sorted.
- For each ```queries[i]```, use binary search on ```prefix_sum``` to find out the largest element less than equal to ```queries[i]```
- Complexity: $O(nlogn)$
##### Code:
```
class Solution:
    def answerQueries(self, nums: List[int], queries: List[int]) -> List[int]:
        answer = [0 for i in range(len(queries))] # O(n)
        nums.sort() # O(nlogn)
        prefix_sum = [ ] 

        # O(n)
        for i in range(len(nums)):
            prefix_sum.append(nums[0]) if i == 0 else prefix_sum.append(prefix_sum[i - 1] + nums[i])

        # O (m log n)
        for i in range(len(queries)): # O(m)
            query = queries[i]
            count = self.binarysearch(query, prefix_sum) #O(log n)
            answer[i] = count
        return answer


    def binarysearch( self,v, L):  #v = target element
        low, high = 0, len(L) - 1
        while low <= high: 
            mid = (low + high) // 2
            if L[mid ] < v:
                low = mid  + 1
            elif L[mid ] > v:
                high = mid  - 1
            else:
                return mid + 1 # return the next index than where the element was found
        return low # Returns index where the element should go if not found
```

## 2. [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/description/)
### 2.1 Using Dictionaries and Sorting
- Initialize a dictionary. For every element in ```nums```, you count occurrences of that element. We can do this in a single pass. Plus this has the added benefit of eliminating duplicates, which we will use later. 
- For every unique element from the ```nums``` array, create a new 2D array where each element is of the format: ```[element from nums, it's count]```
- Sort this array based on the counts in descending order. 
- Pick the first ```k``` elements.
- Complexity: $O(mlogm)$, where $m$ is the number of unique elements in ```nums```.
##### Code:
```
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        counts = { } # init counts dictionary

        # count the number of occurrences for each element
        for num in nums: # O(n)
            if num in counts:
                counts[num] += 1 
            else:
                counts[num] = 1
        
        # [ [elem1, it's count], [elem2, it's count], [elem3, it's count] ]
        L = [[x, counts[x]] for x in counts.keys()] # O(m), where m = number of unique elems in nums

        # Sort in descending order based on counts
        L.sort(key=lambda x:x[1], reverse=True) # NOT O(n log n), it's O(m log m)!

        output = [ ]

        # O(k)
        # Pick out the top k elements
        for i in range(k):
            output.append(L[i][0])

        return output
```

## 3. [Sort Colors](https://leetcode.com/problems/sort-colors/description/)
This problem is similar to what is discussed in one the practice programming assignments for this week, and we need to solve this problem in $O(n)$ complexity. 

### 3.1 Sort the ```nums``` in place
We can simply sort the array ```nums``` in place are return it. We can use ```nums.sort()``` for this. But the comlpexity for this will be: $O(n log n)$. Can we do better?

### 3.2 Take Count of Colors 
We know that there are only three distinct elements in the list. So can we count the occurrences for each distinct color element, and then replace the original array using these counts. 
For example, consider an array ```nums = [ 2, 0, 2, 1, 1, 0, 1]```. We can keep a dictionary for counts as follows: 
```
counts[0] = 2
counts[1] = 3
counts[2] = 2
```

Then for every color, we can replace ```nums[i]``` to ```nums[i + count]``` with the color.

##### Code:
```
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        counts = { 
            0 : 0, 
            1: 0, 
            2: 0
        }

        # O(n). Take counts for each color
        for i in range(len(nums)):
            counts[nums[i]] += 1

        index = 0

        # O(n). For each color, update the nums[i] to nums[i + count] to the color.
        for i in range(3):
            for count in range(counts[i]):
                nums[index] = i
                index += 1
```


## 4. [Merge Intervals](https://leetcode.com/problems/merge-intervals/description/)
### 4.1  Sorting intervals
- If we want to merge intervals, it would be easier if overlapping intervals come adjacently in the input list, which means... Sorting!
- Sort based on the start of the interval. 
- For every interval, check whether start of the interval falls within the end of the merged interval

- But we will need to consider the below corner cases:
    - One interval subsumes another, then what? This means that the second interval's end is smaller than the first. 
    Such as: [1, 10] and [2, 5]
    - How do you handle last interval?

We also need to keep track of the start and the end of the merged intervals.

##### Code:
```
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        if not intervals:
            return []
        
        # Sort intervals based on the start
        intervals.sort(key=lambda x: x[0])
        
        merged_intervals = []
        current_start, current_end = intervals[0]
        
        for interval in intervals[1:]:
            # Overlapping intervals, merge them
            if interval[0] <= current_end:
                current_end = max(current_end, interval[1]) # The max function is required to handle the 1st edge case
            else:
                # Non-overlapping interval found, add previous interval to result
                merged_intervals.append([current_start, current_end])

                # Update current interval
                current_start, current_end = interval
        
        # Add the last interval
        merged_intervals.append([current_start, current_end])
        
        return merged_intervals
```
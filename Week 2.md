# Problems on Sorting and Searching

##### 1. [Longest Subsequence With Limited Sum](#1-longest-subsequence-with-limited-sum-1)
##### 2. [Top K Frequent Elements](#2-top-k-frequent-elements-1)
##### 3. [Sort Colors](#3-sort-colors-1)
##### 4. [Merge Intervals](#4-merge-intervals-1)
##### 5. [Search in Rotated Sorted Array](#5-search-in-rotated-sorted-array-1)

## 1. [Longest Subsequence With Limited Sum](https://leetcode.com/problems/longest-subsequence-with-limited-sum/description/)

**🎯 Understanding the Test Cases:**

*Test Case 1:*

```nums = [4, 5, 2, 1], queries = [3, 10, 21]```

*Explanation:*

For `queries[0] = 3`: The longest subsequence we can take from nums is `[2, 1]` because $2 + 1 = 3$, which has 2 elements. So, the answer is 2. If we add any other element, the sum becomes greater than 3. Note that in a subsequence, you can drop certain elements but the relative order stays same. 

For `queries[1] = 10`: We can take `[4, 2, 1]`, with a sum of $7$, which has 3 elements. So, the answer is 3.

For `queries[2] = 21`: We can take the whole array `[4, 5, 2, 1]`, with a sum of 12, which has 4 elements. So, the answer is 4.

### 1.1: Brute Force
- To find out the maximum number of elements less than or equal to some number, just add the *smallest* numbers. 
- Sort the list first.
- For every query in ```queries```, find out how many elements can be added by iterating over the array ```nums```.
- Complexity: $O(m \cdot n)$
#### 💻 Code Implementation:
```
class Solution(object):
    def answerQueries(self, nums: List[int], queries: List[int]) -> List[int]:
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
- Keep a ```prefix_sum``` array that has this interpretation: `i`th index stores the sum of first `i` smallest elements. Note that this array will be sorted.
- For each ```queries[i]```, use binary search on `prefix_sum` to find out the largest element less than equal to ```queries[i]```
- Complexity: $O(n \cdot \log(n))$
#### 💻 Code Implementation:
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

**🎯 Understanding the Test Cases:**

*Test Case 1:*

`nums= [ 1, 1, 1, 2, 2, 3], k = 2`

*Explanation:*

In this test case, the number $1$ occurs thrice, the number $2$ occurs twice, and the number $3$ occurs once. Since we have to return top $2$ most frequent numbers, we return $1$ and $2$. Important to note here is that the question says nothing about whether the ```nums``` array is sorted or not. If it is not explicitly given in the question, we cannot assume it.

### 2.1 Using Dictionaries and Sorting
- Initialize a dictionary. For every element in ```nums```, you count occurrences of that element. We can do this in a single pass. Plus this has the added benefit of eliminating duplicates, which we will use later. A useful library class to explore here is: `from collections import Counter`
- For every unique element from the `nums` array, create a new 2D array where each element is of the format: `[element from nums, it's count]`
- Sort this array based on the counts in descending order. 
- Pick the first ```k``` elements.
- Complexity: $O(m \cdot \log(m))$, where $m$ is the number of unique elements in ```nums```.
#### 💻 Code Implementation:
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
        # Pick out the top k elements. Note that we don't need counts. So we return L[i][0]
        for i in range(k):
            output.append(L[i][0])

        return output
```

## 3. [Sort Colors](https://leetcode.com/problems/sort-colors/description/)
This problem is similar to what is discussed in one the practice programming assignments for this week, and we need to solve this problem in $O(n)$ complexity. 

### 3.1 Sort the ```nums``` in place
We can simply sort the array ```nums``` in place are return it. We can use ```nums.sort()``` for this. But the comlpexity for this will be: $O(n \cdot \log (n))$. Can we do better?

### 3.2 Take Count of Colors 
We know that there are only three distinct elements in the list. So can we count the occurrences for each distinct color element, and then replace the original array using these counts. 
For example, consider an array ```nums = [ 2, 0, 2, 1, 1, 0, 1]```. We can keep a dictionary for counts as follows: 
```
counts[0] = 2
counts[1] = 3
counts[2] = 2
```

Then for every color, we can replace ```nums[i]``` to ```nums[i + count]``` with the color.

Time Complexity: $O(n)$

#### 💻 Code Implementation:
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

**🎯 Understanding the Test Cases:**

*Test Case 1:*

```intervals = [[1,3], [2,6], [8,10], [15,18]]```

*Explanation:*

We start with the first interval ```[1, 3]```. The next interval ```[2, 6]``` overlaps with ```[1, 3]```, so we merge them into ```[1, 6]```. The next interval ```[8, 10]``` doesn’t overlap with ```[1, 6]```, so we keep it as is. The last interval ```[15, 18]``` also doesn’t overlap with any previous intervals, so we keep it as is.

*Test Case 2:*

```intervals = [[1, 4], [2, 3], [5, 10]]```

*Explanation:*

We start with the first interval ```[1, 4]```. The next interval ```[2, 3]``` overlaps with ```[1, 4]```. But note that the first interval subsumes this one. So the merged interval is: ```[1, 4]```

The last interval ```[5, 10]``` doesn't overlap with any previous intervals so we keep it as it is. 

### 4.1  Sorting intervals
- If we want to merge intervals, it would be easier if overlapping intervals come adjacently in the input list, which means... Sorting!
- Sort based on the start of the interval. 
- For every interval, check whether start of the interval falls within the end of the merged interval

- But we will need to consider the below corner cases:
    - One interval subsumes another, then what? This means that the second interval's end is smaller than the first. 
    Such as: [1, 10] and [2, 5]
    - How do you handle last interval?

We also need to keep track of the start and the end of the merged intervals.

Time Complexity: $O(n \cdot \log(n))$

#### 💻 Code Implementation:
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

## 5. [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/description/)

**🎯 Understanding the Test Cases:**

This is a simple search problem, so the test cases are not discussed.

### 5.1 Using Binary Search

📚 **Problem Overview:**

We need to find the element in $\log(n)$ time. When you see $\log(n)$, what do you think of? Of course, Binary search! This question has been asked in PYQs, so better to understand it well.

**💡 The Solution:**
1. At any `mid` index, there are two possibilities: either the *left half* of the array is sorted, or the *right half* is.
2. If the *left side is sorted*:
    - Check if the target within this range?
        - If yes, do a binary search on the left half. 
        - If no, explore the right half.
3. If the *right side is sorted*:
    - Again, check if the target is in this range.
        - If yes, perform binary search on the right half. 
        - If no, explore to the left side.

#### 💻 Code Implementation:
```
class Solution:
    def search(self, nums, target):
        left, right = 0, len(nums) - 1

        while left <= right:
            mid = (left + right) // 2
            
            # If the target is found
            if nums[mid] == target:
                return mid
            
            # Check if the left half is sorted
            if nums[left] <= nums[mid]:
                # If the target lies within the sorted left half
                if nums[left] <= target < nums[mid]:
                    right = mid - 1
                else:
                    left = mid + 1
            # Otherwise, the right half is sorted
            else:
                # If the target lies within the sorted right half
                if nums[mid] < target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1
        
        # If the target is not found
        return -1
```

## 6. [Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/description/)

**🎯 Understanding the Test Cases:**

*Test Case 1:*

`nums = [5,7,7,8,8,8,10], target = 8`

*Explanation:*

We need to find the starting and ending indices of the target number 8. In this case, 8 appears three times, so we return the range [3, 5], where index 3 is the first occurrence and index 5 is the last.

### 5.1 Using Binary Search

📚 **Problem Overview:**

Again, we need a $\log(n)$ solution. So, we think Binary Search to come up with a solution. 

**💡 The Solution:**

We know binary search is fast, but it stops when we find the target. What if we keep going to find both the first and last occurrences?

Here's how we can do it:

*Leftmost Occurrence:*
- Run a binary search, but when you find 8, don't stop! Keep searching the left half to find the first occurrence.

*Rightmost Occurrence:*
- Similar idea, but now after finding 8, explore the right half to find the last occurrence.

By running two binary searches, we find both boundaries of the target range in $O(\log n)$ time. 

#### 💻 Code Implementation:
```
class Solution:
    def searchRange(self, nums, target):
        def findLeft(nums, target):
            leftmost_occurrence = -1
            # Finds the left most index of target number
            left, right = 0, len(nums) - 1
            while left <= right:
                mid = (left + right) // 2
                if nums[mid] < target:
                    left = mid + 1
                elif nums[mid] == target:
                    leftmost_occurrence = mid
                    right = mid - 1
                else:
                    right = mid - 1
            return leftmost_occurrence
        
        def findRight(nums, target):
            # Finds rightmost index of target
            rightmost_occurrence = -1
            left, right = 0, len(nums) - 1
            while left <= right:
                mid = (left + right) // 2
                if nums[mid] < target:
                    left = mid + 1
                elif nums[mid] == target:
                    rightmost_occurrence = mid
                    left = mid + 1
                else:
                    right = mid - 1
            return rightmost_occurrence
        
        leftmost_occurrence = findLeft(nums, target)
        rightmost_occurrence = findRight(nums, target)
        
        # Check if the target is not found
        if leftmost_occurrence == -1:
            return [-1, -1]
        return [leftmost_occurrence, rightmost_occurrence]
```
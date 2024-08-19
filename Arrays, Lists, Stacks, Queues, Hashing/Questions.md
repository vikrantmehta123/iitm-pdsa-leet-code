# Leet Code Problems on Arrays, Lists, Stacks, Queues, and Hashing

##### 📋 Table of Contents:
##### 1. [Valid Parentheses](#1-valid-parentheses-1)
##### 2. [Longest Consecutive Sequence](#2-longest-consecutive-sequence-1)
##### 3. [Rotate List](#3-rotate-list-1)
##### 4. [Swap Nodes in Pairs](#4-swap-nodes-in-pairs-1)
##### 5. [Reverse Nodes in K Groups](#5-reverse-nodes-in-k-groups-1)
##### 6. [Middle of The Linked List](#6-middle-of-the-linked-list-1)
##### 7. [Delete the Middle Node](#7-delete-the-middle-node-1)
##### 8. [Delete nth Node From End](#8-delete-nth-node-from-end-1)
##### 9. [Merge Two Sorted Lists](#9-merge-two-sorted-lists-1)

## 1. [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/description/)

### 1.1 Using Stacks

🔍 Problem Understanding:

There are only three types of brackets: ```[], (), {}```.
We need to close the last opened bracket first. This implies a Last In, First Out (LIFO) system. Can you think of the appropriate data structure here?
Since LIFO behavior is required, a stack is perfect for tracking the parentheses. As we encounter opening parentheses, we add them to the stack. When we see a closing bracket, we remove (pop) the last added one.

🤔 Think about this:

Imagine you've processed the first two characters of the string and added them to the stack. Your stack might look like this: stack = ['(', '{']. Now, suppose the next character is }. How do you check if this closing bracket matches the last opened one?
We can maintain a mapping like this: \
```closing bracket : its corresponding opening bracket```. This allows us to verify that each closing bracket properly matches its corresponding opening one.

⚠️ Edge Cases:

What if there are opening brackets left in the stack, but no more characters in the string? Or vice versa—what if there are closing brackets in the string, but no opening ones left in the stack?

Time Complexity: $O(n)$
Space Complexity: $O(n)$

#### 💻 Code Implementation::
```
class Solution:
    def isValid(self, s: str) -> bool:
        # Keep the mapping as 'closing bracket' : 'Correspoding opening bracket'
        mapping = { 
            ')' : '(', 
            ']' : '[', 
            '}' : '{'
        }

        stack = [ ]

        # O(n)
        for char in s:
            if char not in mapping: # This means that the bracket is an open bracket
                stack.append(char)  # Simply add the open bracket to the stack     
            else:
                # Corner case- stack has no elements but the string has closing bracket
                if len(stack) == 0:
                    return False

                # Check if opening bracket in the stack matches with the closing bracket in the string
                if mapping[char] != stack.pop():
                    return False

        # if stack is empty after checking every char of string
        # that means that we found the closing bracket for every opening bracket. Thus, return True
        # And vice versa. Return False

        return not stack # Returns True if stack is empty, False otherwise
```

## 2. [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/description/)

### 2.1 Brute Force
- For every element, iterate over the array to check if the next element exists in the array. Keep doing this as long as the next element is found.
- Keep a counter for each sequence, and track the maximum.
- Time Complexity: $O(n^2)$. But we are told to find an $O(n)$ solution!

### 2.2 Improved Approach Using Sets

🧠 Deep Dive:

In the brute-force approach, we had to iterate through the entire unsorted array to check if the next element existed. Is there a way to optimize this check?
**Hint**: Consider using a data structure that allows constant-time lookups. 

We can use **set** data structure to serve this purpose!

How would you solve this using sets?
- For each number, while the next consecutive number exists, keep counting.
- However, if we use this method directly, some numbers may be checked more than once, leading to $O(n^2)$ time complexity. Can we do better?

Which numbers can start a consecutive sequence?
- Only those numbers ```n``` where ```n-1``` is not in the set. Only these numbers can be the starting points of the longest consecutive sequence. Can you see why?
- By finding these starting points, we can run our loop efficiently on only these elements.
- Time Complexity: $O(n)$
- Space Complexity: $O(n)$

#### Code:
```
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:    

        # Convert nums to a set
        num_set = set(nums)

        starting_points = set()
        
        # Only those elements can be starting points where n - 1 is not in num_set
        # O(n)
        for num in nums:
            if num - 1 not in num_set: 
                starting_points.add(num)

        max_length = 0
        # Overall O(n), because the inner while loop is just going to run once for other elements
        for current_num in starting_points:
                current_length = 1
                
                while current_num + 1 in num_set:
                    current_num += 1
                    current_length += 1
                
                max_length = max(max_length, current_length)
        
        return max_length
```

## 3. [Rotate List](https://leetcode.com/problems/rotate-list/description/)
### 3.1 🔄 Rotation Logic:
- If $k \gt n$, then the number of effective rotations is: $k = k \mod n$. 
- Think about this as a three step problem:
    1. First, traverse $k$ nodes. This ```n - k```th node becomes the ```newhead``` of the rotated list.
    2. Next, connect the original list's tail to its ```head```to create a cycle in the linked list.
    3. Finally, walk from the ```newhead``` for ```n``` nodes to find the ```newtail```, which will become the tail of the rotated list by marking its next pointer as None.

#### 💻 Code Implementation::
```
class Solution:
    # Gets the length of node: 'n'
    def get_length(self, head):
        current = head
        count = 0
        while current:
            current = current.next
            count += 1        
        return count

    def rotateRight(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        n = self.get_length(head)
        
        # Corner case
        if n <= 1:
            return head

        k = k % n # Number of effective rotations

        # In the original list, you need to walk these many steps to get to new head
        walk_these_many_steps = n - k 

        if k == 0 or walk_these_many_steps == 0:
            return head

        # Get the new head of the list that you will get after you rotate
        current = head
        count = 0
        newhead = None
        while current:
            if count == walk_these_many_steps:
                newhead = current
                break

            current = current.next
            count += 1

        # Get to the end of the list so that you can link tail with the head. 
        # But now your linked list is a cycle
        current = head
        count = 0
        while current:
            if not current.next:
                current.next = head
                break
            current = current.next

        # Now you need to make the new tail point to None to break the cycle
        current = newhead
        count = 0
        while current and count < n:
            current = current.next
            count += 1
            if count == n - 1:
                current.next = None

        return newhead
```

## 4. [Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/description/)

## 5. [Reverse Nodes in K Groups](https://leetcode.com/problems/reverse-nodes-in-k-group/description/)

### 5.1 Using Recursion
- We know how to reverse a linked list from the Graded Assignment question. Given k, we can reverse the first k nodes and return the head and tail of the reversed portion.
- For the next k nodes, you can pass the kth_node.next as the head and reverse the subsequent k nodes, returning their head and tail. This way, you reverse groups of k nodes. But how do you connect these groups?

🤔 Can you think of a recursive way?
- Remember that the last reversed ```k``` nodes will be the first to return!
- Let's take an example list: ```1 -> 2 -> 3 -> 4```, and ```k = 2```.
    - First, reverse ```1 -> 2``` as ```2 -> 1```, and return the head as ```2``` and tail as ```1```.
    - Then, reverse ```3 -> 4``` recursively as ```4 -> 3```, and return the head as ```4``` and tail as ```3```.
    - Now, Now, you just need to connect the tail of the first reversed group (1) to the head of the second reversed group (4). Voilà! You've connected the reversed groups.

#### Code:
```
class Solution:
    # Helper function: returns the number of nodes in the linked list
    def get_length(self, head):
        count = 0
        current = head
        while current:
            current = current.next
            count += 1
        return count

    def reverse(self, head, k, n):
        if not head:
            return head, None

        # If k > n, then remaining nodes should remain as they are
        if k > n:
            return head, None

        # Reverse the K nodes in groups
        current = head
        prev = None
        next_node = None
        count = 0
        while current and count <= k:
            # Counting because we want to mark the tail also
            if count < k:
                next_node = current.next
                current.next = prev       
                prev = current            
                current = next_node       

            count += 1
        
        curr_head = prev # The head of the reversed k nodes
        curr_tail = head # tail of the reversed k nodes

        # Recursively call reverse on the next k nodes
        next_head, next_tail = self.reverse(next_node, k, n - k)
        
        # Connect the current group's tail with the head of the next group
        curr_tail.next = next_head 

        return curr_head, curr_tail

    def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        n = self.get_length(head)
        head, tail = self.reverse(head, k, n)
        return head
```

## 6. [Middle of The Linked List](https://leetcode.com/problems/middle-of-the-linked-list/description/)
### 6.1 Using Iteration
How do we find the middle element in an array? We typically compute the middle index as: ```mid = len(L) // 2``` and then return ```L[mid]```.

Can you find out a similar approach in linked lists? Let's explore this:

1. First, iterate over the entire list to determine its length.
2. Next, compute the middle index. 
3. Finally, traverse the list up to the middle node and return it. 

- Time Complexity: $O(n)$
#### Code:
```
class Solution:
    def get_length(self, head):
        """
        Helper function- iterates over the entire linked list and returns the length of the list
        """
        counter = 0
        while head:
            counter += 1
            head = head.next
        return counter


    def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # Handle the base case
        if not head:
            return 

        # Compute the middle index 
        n = self.get_length(head)
        mid = n // 2

        # Iterate mid number of steps
        i = 0
        while i < mid: 
            head = head.next # note that we are updating the head. So after "mid" iterations, head = middle node
            i+=1
        
        # Since head = mid after iterating, we return head
        return head
```

## 7. [Delete the Middle Node](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list/description/)
### 7.1 Using Iteration

We’ve already discussed how to find the middle node in a linked list, right? So, if we know how to reach the middle node, can we figure out how to delete it? 🤔

To delete a node in a linked list, here’s what we need to do:

1. Stop at the node before the one we want to delete.
2. Redirect the pointer: Change the next pointer of the current node to skip the node we want to delete and point to the node after it. Essentially, ```current.next = current.next.next```.

⚠️ Edge Cases:
What if the linked list has only one node? In this scenario, we need to handle cases where ```current.next.next``` doesn’t exist.

- Time Complexity: $O(n)$

#### Code:
```
class Solution:
    def get_length(self, head):
        """
        Helper function- iterates over the entire linked list and returns the length of the list
        """
        counter = 0
        while head:
            counter += 1
            head = head.next
        return counter

    def deleteMiddle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        mid = self.get_length(head) // 2
        
        current = head # Iterate using current because we want to return the head

        i = 0
        # Stopping one node before the middle node
        while i < mid - 1:
            current = current.next
            i += 1
        
        # Change links of the node before to the next to next node by handling corner case
        if current.next and current.next.next:
            current.next = current.next.next
        else:
            current.next = None
        return head
```

## 8. [Delete nth Node From End](https://leetcode.com/problems/remove-nth-node-from-end-of-list/description/)
### 8.1 Using Iteration
This problem is very similar to the problem of deleting middle node.

Observe that deleting the ```nth``` node from the end is the same as deleting ```(length - n)th``` node from the start. 

We can use a similar approach as we used in the above question to figure out the solution to this. 


## 9. [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/description/)

This problem is very similar to the ```merge(A, B)``` operation from the merge sort. But instead of merging arrays, we’re merging linked lists! 🔗

#### Code:
```
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:

        # Dummy node to serve as a proxy for head of the list to return      
        dummy = ListNode()
        current = dummy

        # Iterate as long as neither list is exhausted
        while list1 and list2:
            if list1.val < list2.val:
                current.next = list1
                list1 = list1.next
            else:
                current.next = list2
                list2 = list2.next
            current = current.next

        # Attach the remaining nodes of list1 or list2
        if list1:
            current.next = list1
        elif list2:
            current.next = list2

        # The merged list is next to the dummy node
        return dummy.next
```
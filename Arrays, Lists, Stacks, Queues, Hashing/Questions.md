# Leet Code Problems on Arrays, Lists, Stacks, Queues, and Hashing

## 1. [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/description/)

### 1.1 Using Stacks

- There are only three types of brackets. ``` [], (), {}```
- We need to close the last opened bracket first $\implies$ a LIFO system. Can you think of the data structure to use here?
Since we need LIFO, we'll use stack here to keep track of the parentheses. As we see opening parentheses, we'll add them to the stack. If we see a closing brace, we will pop an element.
- Let's say we've gone through first two chars of the string and added those to the stack. Our stack looks like this: ```stack = [ '(', '{' ]```. Now let us assume that the next char is ```}```. We need a way to check whether this next char is the correct mapping for the last element of ```stack```, the one we will remove. Thus, we can keep a mapping: ```closing bracket : its corresponding opening bracket```.
- Can you think of a corner cases that our code might fail?
    - Consider the case where there are opening brackets in the stack but there are no more chars left in the string. Or the opposite case where there are no opening brackets in the stack, but there are more closing brackets in the string. 

#### Code:
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
- For every element, iterate over the array to check if the next element exists in the array. Keep doing this while such next element exists in the array.
- Keep counter for each, and take the maximum.
- Complexity: $O(n^2)$. But we need $O(n)$ solution.

### 2.2 Using Sets
- In the brute force approach, we had to iterate over the entire unsorted array to find out if the next element existed in the array. Can we convert this array into a data structure that will tell us whether the next element exists in constant time? We can use **sets** for this purpose. 
- Further, think about this: Let's say you use sets. How would you solve this?
    - For every number, while next number exists, keep searching and incrementing a count.
    - In this approach, each number is getting checked more than once. 
    - Complexity: O(n^2)
- Can we do better?
    - Yes! Think: which numbers can be the start of a consecutive sequence?
    - Those numbers ```n``` that don't have ```n-1``` in the set. Only these numbers can be starting points of a longest consecutive sequence. Can you think why?
    - So, we can first find out these starting points of a sequence, and then run the loop on these elements only. 
    - Complexity: O(n). If you observe carefully, each number is getting checked only once with this approach.

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
### 3.1 
- If $k \gt n$, then the number of rotations is: $k = k \mod n$. 
- Think about this as a three step problem:
    - First, you walk $k$ nodes. This ```n - k```th node would be the ```newhead``` of the rotated list.
    - Now, you need to connect the tail to the ```head``` of the original linked list. This will create a cycle in the linked list.
    - Now, from the ```newhead```, you need to walk ```n``` nodes and arrive at the ```newtail```. This is the tail of the rotated list. So we just mark the ```next``` pointer of this node as ```None```.

#### Code:
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
- We know how to reverse a linked list. So given ```k```, we can reverse the first ```k``` nodes using a counter and return the head and the tail of the reversed linked list.
- For the next ```k``` nodes, you can pass the ```kth_node.next``` as the head and reverse the next ```k``` nodes, and return the head and the tail of the reversed next ```k``` nodes. So, we have arrived at a situation where we can reverse groups of ```k``` nodes. But we still don't know how to connect these groups of reversed ```k``` nodes.
- Can you think of a recursive way to do this? Remember that the last reversed ```k``` nodes will be the first to return. 
- Let's take an example: 1 -> 2 -> 3 -> 4, and ```k = 2```.
    - First we reverse 1 -> 2 as 2 -> 1, and return the head as ```2``` and tail as ```1```.
    - Then we would recursively reverse 3 -> 4 as 4 -> 3, and return the head as ```4``` and tail as ```3```.
    - Now, we have to connect the tail of the 1st group with the head of the 2nd group. Then we have connected the groups that we reversed in isolation. 

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
How do we find the middle element in an array? We compute the middle as: ```mid = len(L) // 2``` and then return ```L[mid]```.

Can you find out a similar approach in linked lists?

First, we iterate over the entire list to get the length of the linked list. 
Then, we compute the middle index. 
Lastly, we can iterate ```mid``` number of nodes to reach the middle node and return it. 

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

From our discussion above, we know how to reach the middle node. 

Since we know how to reach the middle node, can you find out a way to delete that node?

- In a linked list, if we want to delete a node, we need to do the following:
    - Stop at the node before. 
    - Mark the node's next pointer as pointing to the next to next node. i.e. ```current.next = current.next.next```

- Corner Cases:
    - What if there is no node after the middle node? For instance, the linked list has only one node. 
    - In that case, we need to first check whether ```current.next.next``` pointer exists or not.

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
# Leet Code Problems Based on Divide and Conquer

1. [Merge K Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/description/)

📚 **Problem Overview:**

We need to merge several lists into one sorted list. We can use divide and conquer to do this faster!

**💡 The Solution:**

- If there are only two lists in the input, merge them. 
- Otherwise, split the input list into two halves.
    - Recursively call merge on the left half
    - Recursively call merge on the right half.
    - Merge the output from the two halves.

#### 💻 Code Implementation:

```
class Solution:
    def mergeTwoLists(self, list1, list2):
        """Code to merge two linked lists"""
        if not list1: return list2
        if not list2: return list1
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

    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        # Base Cases
        if not lists:
            return 
        if len(lists) == 1:
            return lists[0]
        if len(lists) == 2:
            return self.mergeTwoLists(lists[0], lists[1])

        # Split lists into two halves and recursively call merge on them
        mid = len(lists) // 2
        left_half = self.mergeKLists(lists[:mid])
        right_half = self.mergeKLists(lists[mid:])

        return self.mergeTwoLists(left_half, right_half)
```

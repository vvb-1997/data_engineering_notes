Write a method called ==has_loop== that is part of the linked list class.  
  
The method should be able to detect if there is a cycle or loop present in the linked list.  
  
You are required to use Floyd's cycle-finding algorithm (also known as the "tortoise and the hare" algorithm) to detect the loop.  
  
This algorithm uses two pointers: a slow pointer and a fast pointer. The slow pointer moves one step at a time, while the fast pointer moves two steps at a time. If there is a loop in the linked list, the two pointers will eventually meet at some point. If there is no loop, the fast pointer will reach the end of the list.  
  
The method should follow these guidelines:

1. Create two pointers, ==slow== and ==fast==, both initially pointing to the head of the linked list.
2. Traverse the list with the ==slow== pointer moving one step at a time, while the ==fast== pointer moves two steps at a time.
3. If there is a loop in the list, the ==fast== pointer will eventually meet the ==slow== pointer. If this occurs, the method should return ==True==.
4. If the ==fast== pointer reaches the end of the list or encounters a ==None== value, it means there is no loop in the list. In this case, the method should return ==False==.

If your Linked List contains a loop, it indicates a flaw in its implementation. This situation can manifest in several ways:

![[Pasted image 20240430193514.png]]
![[Pasted image 20240430193525.png]]
![[Pasted image 20240430193531.png]]

## Solution

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None
        
class LinkedList:
    def __init__(self, value):
        new_node = Node(value)
        self.head = new_node
        self.tail = new_node
        self.length = 1

    def append(self, value):
        new_node = Node(value)
        if self.length == 0:
            self.head = new_node
            self.tail = new_node
        else:
            self.tail.next = new_node
            self.tail = new_node
        self.length += 1
        return True

    def has_loop(self):
	    # 1. Initialize two pointers: 'slow' and 'fast', both starting from the head.
        fast = self.head
        slow = self.head

		# 2. Continue traversal as long as the 'fast' pointer and its next node aren't None. This ensures we don't run into errors trying to access non-existent nodes.
        while fast and fast.next:
        
	        # 2.1. Move 'slow' pointer one step ahead.
            slow = slow.next

			# 2.2. Move 'fast' pointer two steps ahead.
            fast = fast.next.next

			# 2.3. Check for cycle: If 'slow' and 'fast' meet, it means there's a cycle in the linked list.
            if fast == slow:

				# 2.3.1. If they meet, return True indicating the list has a loop.
                return True
        
        # 3. If we've gone through the entire list and the pointers never met, then the list doesn't have a loop.
        return False
    
my_linked_list_1 = LinkedList(1)
my_linked_list_1.append(2)
my_linked_list_1.append(3)
my_linked_list_1.append(4)
my_linked_list_1.tail.next = my_linked_list_1.head
print(my_linked_list_1.has_loop() ) # Returns True

my_linked_list_2 = LinkedList(1)
my_linked_list_2.append(2)
my_linked_list_2.append(3)
my_linked_list_2.append(4)
print(my_linked_list_2.has_loop() ) # Returns False
```

## Expected Output

```python
"""
    EXPECTED OUTPUT:
    ----------------
    True
    False
    
"""
```

## Detailed Explanation

Let's break down the ==has_loop== function to understand how it detects a cycle (or loop) in a linked list. This method makes use of Floyd's cycle-finding algorithm, often referred to as the "tortoise and the hare" technique.  
For the sake of visualization, imagine a linked list that has a loop in it, like this:

```python
1 → 2 → 3 →  4 
           ↗ ↓
         6 ← 5
```

In this example, nodes 4, 5, and 6 form a loop.

==Method Walkthrough:== ==has_loop==

```python
def has_loop(self):
```

1. ==Initialize two pointers, slow (the tortoise) and fast (the hare):==

```python
slow = self.head
fast = self.head
```

Both pointers start at the head of the linked list. The idea is to move the ==slow== pointer one step at a time while moving the ==fast== pointer two steps at a time.

2. ==Traverse the linked list:==

```python
while fast is not None and fast.next is not None:
```

This loop ensures we continue as long as ==fast== and the node after ==fast== (==fast.next==) are not None. This is similar to the logic we used in the ==find_middle_node== function and serves to prevent potential errors by ensuring we don't access nonexistent nodes.

3. ==Move the slow pointer one node and the fast pointer two nodes:==

```python
slow = slow.next
fast = fast.next.next
```
For each iteration of the loop, ==slow== moves one step, and ==fast== moves two steps.

4. ==Check if the slow pointer meets the fast pointer:==

```python
if slow == fast:
	return True
```

This is the crucial step. If there's a loop in the linked list, the fast pointer (which moves twice as quickly) will eventually catch up to the slow pointer inside the loop. When they meet (point to the same node), we've found a loop, and the function returns ==True==.

==Illustrative Steps for 1 → 2 → 3 → 4 → 5 → 6 (with loop from 6 to 4):==

- Both ==slow== and ==fast== start at node 1.
- After the first iteration, ==slow== is at node 2, and ==fast== is at node 3.
- In the next iteration, ==slow== moves to node 3, and ==fast== jumps to node 5.
- Following that, ==slow== reaches node 4, while ==fast== is now at node 6.
- As we continue, ==slow== will be at node 5, and ==fast==, having moved two steps, will loop back and land on node 4.
- Eventually, as we iterate further, both pointers will meet inside the loop, confirming its presence.

==5. Return== ==False== ==if no loop is found:==

```python
return False
```

If the loop completes without the two pointers meeting, it indicates there's no loop in the list, so the function returns ==False==.

==Conclusion:== The ==has_loop== method efficiently determines if a linked list contains a cycle or loop. By advancing one pointer at double the speed of the other, and checking if they ever meet, we can identify loops without needing to use any additional memory or mark visited nodes. If they meet, there's a loop. If they don't, the list has no loops.
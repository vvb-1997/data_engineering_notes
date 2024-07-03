Implement the ==find_middle_node== method for the LinkedList class.  
  
==Note: this== ==LinkedList== ==implementation does not have a== ==length== ==member variable.==  
  
If the linked list has an even number of nodes, return the first node of the second half of the list.  
  
Keep in mind the following requirements:

- The method should use a two-pointer approach, where one pointer (slow) moves one node at a time and the other pointer (fast) moves two nodes at a time.
- When the fast pointer reaches the end of the list or has no next node, the slow pointer should be at the middle node of the list.
- The method should return the middle node or the first node of the second half of the list if the list has an even number of nodes.
- The method should only traverse the linked list once.  In other words, you can only use one loop.

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
       
    def append(self, value):
        new_node = Node(value)
        if self.head == None:
            self.head = new_node
            self.tail = new_node
        else:
            self.tail.next = new_node
            self.tail = new_node
        return True
        
    def find_middle_node(self):
	    # 1. Initialize two pointers: 'slow' and 'fast', both starting from the head.
        fast = self.head
        slow = self.head

		# 2. Continue traversal as long as the 'fast' pointer and its next node aren't None. This ensures we don't run into errors trying to access non-existent nodes.
        while fast and fast.next:

			# 2.1. Move 'slow' pointer one step ahead.
			slow = slow.next

			# 2.2. Move 'fast' pointer two steps ahead.
            fast = fast.next.next
            
        return slow

my_linked_list = LinkedList(1)
my_linked_list.append(2)
my_linked_list.append(3)
my_linked_list.append(4)
my_linked_list.append(5)

print( my_linked_list.find_middle_node().value )
```

## Expected Output

``` python
"""
    EXPECTED OUTPUT:
    ----------------
    3
    
"""
```

## Detailed Explanation

For this example, let's consider a linked list with the nodes:  
==1 → 2 → 3 → 4 → 5==.

==Method Walkthrough:== ==find_middle_node==

1. def find_middle_node(self):

  

==1. Initialize two pointers, slow and fast:==

1. slow = self.head
2. fast = self.head

Here, both pointers start at the head of the linked list. The idea is that ==slow== will move one node at a time, while ==fast== will move two nodes. By the time ==fast== reaches the end, ==slow== will be at the middle.

  

==2. Traverse the linked list:==

1. while fast is not None and fast.next is not None:

This loop ensures that we continue as long as ==fast== and the node after ==fast== (==fast.next==) are not None. The two conditions are vital:

- The first, ==fast is not None==, ensures that we haven't already reached the end of the list.
    
- The second, ==fast.next is not None==, makes sure there's another node to move to, given that ==fast== will be jumping two nodes in the next step. This prevents potential errors.
    
- This will allow us to find the middle node when there is an even or odd number of nodes.
    

  

==3. Move the slow pointer one node and the fast pointer two nodes:==

1.     slow = slow.next
2.     fast = fast.next.next

  

For each iteration of the loop, the ==slow== pointer moves one step (or one node) and the ==fast== pointer moves two steps (or two nodes).

  

==Illustrative Steps for 1 → 2 → 3 → 4 → 5:==

- Both ==slow== and ==fast== start at node 1.
    
- After the first iteration, ==slow== moves to node 2, and ==fast== moves to node 3.
    
- In the second iteration, ==slow== moves to node 3, and ==fast== jumps to node 5.
    
- On the next attempt to iterate, since ==fast.next== is None (there's no node after node 5), the loop stops.
    
- The ==slow== pointer is now pointing to node 3, which is the middle of our list.
    

  

==4. Return the node the slow pointer is currently at:==

1. return slow

  

Once the loop exits, the ==slow== pointer is either pointing to the middle node of the list (for odd-length lists) or the first of the two middle nodes (for even-length lists).

  

==Conclusion:== The ==find_middle_node== method efficiently locates the middle of a linked list by using a two-pointer technique. The slow pointer moves at half the speed of the fast pointer. By the time the fast pointer has traveled the full length of the list, the slow pointer has traveled half the length, landing it in the middle. In our example, the middle of ==1 → 2 → 3 → 4 → 5== is node ==3==, and the method will return this node.
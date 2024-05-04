
Implement the ==partition_list== member function for the LinkedList class, which partitions the list such that all nodes with values less than x come before nodes with values greater than or equal to x.  
  
==Note:  This linked list class does NOT have a **tail** which will make this method easier to implement.==  
  
The original relative order of the nodes should be preserved.

==Details:==
The function ==partition_list== takes an integer ==x== as a parameter and modifies the current linked list in place according to the specified criteria. If the linked list is empty (i.e., ==head== is ==null==), the function should return immediately without making any changes.

### Example 1:

==Input:==
Linked List: ==3 -> 8 -> 5 -> 10 -> 2 -> 1== x: ==5==

==Process:==
- Values less than 5: ==3==, ==2==, ==1==
- Values greater than or equal to 5: ==8==, ==5==, ==10==

==Output:==
Linked List: ==3 -> 2 -> 1 -> 8 -> 5 -> 10==

### Example 2:  

==Input:==
Linked List: ==1 -> 4 -> 3 -> 2 -> 5 -> 2== x: ==3==

==Process:==
- Values less than 3: ==1==, ==2==, ==2==
- Values greater than or equal to 3: ==4==, ==3==, ==5==

==Output:==
Linked List: ==1 -> 2 -> 2 -> 4 -> 3 -> 5==

==Tips:==
1. While traversing the linked list, maintain two separate chains: one for values less than ==x== and one for values greater than or equal to ==x==.
2. Use dummy nodes to simplify the handling of the heads of these chains.
3. After processing the entire list, connect the two chains to get the desired arrangement.

==Note:==
1. The solution must maintain the relative order of nodes. For instance, in the first example, even though ==8== appears before ==5== in the original list, the partitioned list must still have ==8== before ==5== as their relative order remains unchanged.  
2. You must solve the problem ==WITHOUT MODIFYING THE VALUES== in the list's nodes (i.e., only the nodes' ==next== pointers may be changed.)

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
        self.length = 1

    def append(self, value):
        new_node = Node(value)
        if self.length == 0:
            self.head = new_node
        else:
            current_node = self.head
            while current_node.next is not None:
                current_node = current_node.next
            current_node.next = new_node
        self.length += 1 
    
    def print_list(self):
        temp = self.head
        while temp is not None:
            print(temp.value)
            temp = temp.next    
            
    def make_empty(self):
        self.head = None
        self.tail = None
        self.length = 0

	def partition_list(self, x):
	    # 1. Edge case: Check if the list is empty. If so, exit.
	    if not self.head:
	        return None
	 
	    # 2. Create two dummy nodes: 
	    # dummy1 for nodes with values less than x 
	    # and dummy2 for nodes with values greater or equal to x.
	    dummy1 = Node(0)
	    dummy2 = Node(0)
	 
	    # 3. Initialize two pointers (prev1 and prev2) at the dummy nodes.
	    # They will be used to build the two separate lists.
	    prev1 = dummy1
	    prev2 = dummy2
	 
	    # 4. Start iterating from the head of the original list.
	    current = self.head
	 
	    # 5. Traverse the entire list.
	    while current:
	        # 5.1. If the current node's value is less than x:
	        if current.value < x:
	            # 5.1.1. Attach it to the end of the list starting at dummy1.
	            prev1.next = current
	            # 5.1.2. Move the prev1 pointer forward.
	            prev1 = current
	        # 5.2. Otherwise:
	        else:
	            # 5.2.1. Attach it to the end of the list starting at dummy2.
	            prev2.next = current
	            # 5.2.2. Move the prev2 pointer forward.
	            prev2 = current
	        
	        # 5.3. Move to the next node in the original list.
	        current = current.next
	 
	    # 6. End the two lists. Set the next pointers of prev1 and prev2 to None.
	    prev1.next = None
	    prev2.next = None
	 
	    # 7. Link the end of the first list (the one that started at dummy1) 
	    # to the beginning of the second list (the one that started at dummy2).
	    prev1.next = dummy2.next
	 
	    # 8. Update the head of the linked list to point to the beginning 
	    # of the partitioned list.
	    self.head = dummy1.next

# Function to convert linked list to Python list
def linkedlist_to_list(head):
    result = []
    current = head
    while current:
        result.append(current.value)
        current = current.next
    return result

ll = LinkedList(3)
ll.append(1)
ll.append(4)
ll.append(2)
ll.append(5)
print("Before:", linkedlist_to_list(ll.head))
ll.partition_list(x)
print("After:", linkedlist_to_list(ll.head))
```
## Detailed Explanation

This function is designed to partition a linked list around a value ==x==, such that all nodes with values less than ==x== come before nodes with values greater than or equal to ==x==.  
  
Consider a sample linked list and value for partitioning:

```python
List: 5 → 3 → 8 → 2 → 10 → 4 → 1
Partition Value (x): 4
```
After partitioning, the linked list should look like:

```python
3 → 2 → 1 → 5 → 8 → 10 → 4
```

==Method Walkthrough:==

```python
def partition_list(self, x):
```

==Check if the list is empty:==

```python
if not self.head:
	return None
```

If the list is empty, there's nothing to partition, so we return ==None==.

==Initialize two dummy nodes and corresponding pointers:==

```python
dummy1 = Node(0)
dummy2 = Node(0)
prev1 = dummy1
prev2 = dummy2
```

We are going to use two separate linked lists: one for values less than ==x== (handled by ==dummy1== and ==prev1==) and another for values greater than or equal to ==x== (handled by ==dummy2== and ==prev2==).

==Start at the head of the original list:==

```python
current = self.head
```

This ==current== pointer will help us traverse the original linked list.

==Traverse the list and partition the nodes:==

```python
while current:
	if current.value < x:
		prev1.next = current
		prev1 = current
	else:
		prev2.next = current
		prev2 = current
	current = current.next
```

We use a loop to traverse the entire linked list. For each node, if its value is less than ==x==, we append it to the list headed by ==dummy1==. If it's greater than or equal to ==x==, we append it to the list headed by ==dummy2==.

==Terminate both lists:==

```python
prev1.next = None
prev2.next = None
```

Since we've possibly moved some nodes from the original list to our two new lists, we need to ensure that both new lists are properly terminated by setting their last node's ==next== pointers to ==None==.

==Connect the two lists:==

```python
prev1.next = dummy2.next
```

We attach the start of the second list (i.e., the list of values greater than or equal to ==x==) to the end of the first list.

==Update the head of the linked list:==

```python
self.head = dummy1.next
```

We update the head of the original list to point to the head of our partitioned list (i.e., the first node with a value less than ==x==).

==Conclusion:== The ==partition_list== method efficiently partitions a linked list around a value ==x== by using two temporary linked lists: one for nodes with values less than ==x== and another for nodes with values greater than or equal to ==x==. After partitioning, it connects these two lists together and updates the original list's head to reflect the newly partitioned list. In our example, the linked list gets rearranged to have all nodes less than 4 come before nodes with values greater than or equal to 4.
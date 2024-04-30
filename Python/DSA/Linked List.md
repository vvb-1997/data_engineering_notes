## What is Linked List in Python

[A linked list](https://www.geeksforgeeks.org/what-is-linked-list/) is a type of linear data structure similar to arrays. It is a collection of nodes that are linked with each other. A node contains two things first is data and second is a link that connects it with another node. Below is an example of a linked list with four nodes and each node contains character data and a link to another node. Our first node is where **head** points and we can access all the elements of the linked list using the **head**.

![[linked_list_diagram.png]]

# Big O 

![[Big O for linked list.png]]
# Constructor for linked list

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None
        
class LinkedList:
    def __init__(self, value):
        node = Node(value)
        self.head = node
        self.tail = node
        self.length = 1

my_linked_list = LinkedList(4)

print('Head:', my_linked_list.head.value)
print('Tail:', my_linked_list.tail.value)
print('Length:', my_linked_list.length)
```


# Operations on Linked List

- [[Print entire linked list]]
- [[Append new node at end of the linked list]]
- [[Pop element from the end]]
- [[Add element at the start (Prepend)]]
- [[Remove element from the start of linked list (pop first)]]
- [[Get nth element from linked list (0 based index)]]
- [[Set nth element from linked list (0 based index)]]
- [[Insert element at index in linked list]]
- [[Remove nth element from linked list]]
- [[Reverse a linked list]]

# Leetcode Questions

- [[Find Middle Node]]
- [[Has Loop]]
- 

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

    def print_list(self):
        temp = self.head
        while temp is not None:
            print(temp.value)
            temp = temp.next
        
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

    def prepend(self, value):
        new_node = Node(value)
        if self.length == 0:
            self.head = new_node
            self.tail = new_node
        else:
            new_node.next = self.head
            self.head = new_node
        self.length += 1
        return True

    def get(self, index):
        if index < 0 or index >= self.length:
            return None
        temp = self.head
        for _ in range(index):
            temp = temp.next
        return temp
        
    def set_value(self, index, value):
        temp = self.get(index)
        if temp:
            temp.value = value
            return True
        return False
    
    def insert(self, index, value):
        # for index out of bound for linked list
        if index < 0 or self.length < index:
            return False
        # for index equal to 0
        if index == 0:
            return self.prepend(value)
        # for index equal to length of linked list
        if index == self.length:
            return self.append(value)
        temp = self.get(index - 1)
        node = Node(value)
        node.next = temp.next
        temp.next = node
        self.length += 1
        return True

my_linked_list = LinkedList(1)
my_linked_list.append(3)

print('LL before insert():')
my_linked_list.print_list()

my_linked_list.insert(1,2)
print('\nLL after insert(2) in middle:')
my_linked_list.print_list()

my_linked_list.insert(0,0)
print('\nLL after insert(0) at beginning:')
my_linked_list.print_list()

my_linked_list.insert(4,4)
print('\nLL after insert(4) at end:')
my_linked_list.print_list()
```

# What is a List?
Lists are used to store multiple items in a single variable.
Lists are one of 4 built-in data types in Python used to store collections of data, the other 3 are [Tuple](https://www.w3schools.com/python/python_tuples.asp), [Set](https://www.w3schools.com/python/python_sets.asp), and [Dictionary](https://www.w3schools.com/python/python_dictionaries.asp), all with different qualities and usage.

Lists are created using square brackets:

```python
thislist = ["apple", "banana", "cherry"]  
print(thislist)
```

## List Items
List items are ordered, changeable, and allow duplicate values.
List items are indexed, the first item has index `[0]`, the second item has index `[1]` etc.
## Ordered
When we say that lists are ordered, it means that the items have a defined order, and that order will not change.
If you add new items to a list, the new items will be placed at the end of the list.
## Changeable
The list is changeable, meaning that we can change, add, and remove items in a list after it has been created.
## Allow Duplicates
Since lists are indexed, lists can have items with the same value:

```python
thislist = ["apple", "banana", "cherry", "apple", "cherry"]  
print(thislist)
```

## List Length
To determine how many items a list has, use the `len()` function:

```python
thislist = ["apple", "banana", "cherry"]  
print(len(thislist))
```
## List Items - Data Types
List items can be of any data type:

```python
list1 = ["apple", "banana", "cherry"]  
list2 = [1, 5, 7, 9, 3]  
list3 = [True, False, False]
```

A list can contain different data types:

```python
list1 = ["abc", 34, True, 40, "male"]
```

![[List elements types.png]]
## type()
From Python's perspective, lists are defined as objects with the data type 'list':

```python
mylist = ["apple", "banana", "cherry"]  
print(type(mylist))
>>> <class 'list'>
```
## The list() Constructor
It is also possible to use the list() constructor when creating a new list.

```python
thislist = list(("apple", "banana", "cherry")) # note the double round-brackets  
print(thislist)
```

# Operations

## Accessing the list

**Time Complexity** - O(1)
**Space Complexity** - O(1)

```python
shoppingList = ['Milk', 'Cheese' ,'Butter']

print(shoppingList[0])
>> 'Milk'
```

## `in` operator

**Time Complexity** - O(n)
**Space Complexity** - O(1)

```python
shoppingList = ['Milk', 'Cheese' ,'Butter']

print('Milk' in shoppingList)
>> True
```

## Negative index

**Time Complexity** - O(1)
**Space Complexity** - O(1)

```python
shoppingList = ['Milk', 'Cheese' ,'Butter']

print(shoppingList[-1])
>> 'Butter'
```

## Traversing the list

**Time Complexity** - O(n)
**Space Complexity** - O(1)

```python
shoppingList = ['Milk', 'Cheese' ,'Butter']

for element in shoppingList:
	print(element)
>> 'Milk'
>> 'Cheese'
>> 'Butter'

for i in range(len(shoppingList)):
	shoppingList[i] = shoppingList [i]+"+"
	print(shoppingList[i])
>> 'Milk+'
>> 'Cheese+'
>> 'Butter+'
```

## Update list

**Time Complexity** - O(1)
**Space Complexity** - O(1)

```python
myList= [1,2,3,4,5,6,7]
print(myList)

myList[2] = 33
myList[4] = 55
print(myList)
>> [1,2,3,4,5,6,7]
>> [1,2,33,4,55,6,7]
```

## Insert in list

### Inserting an element to the beginning of the list

**Time Complexity** - O(n)
**Space Complexity** - O(1)

```python
myList= [1,2,3,4,5,6,7]
print(myList)

myList.insert(0, 11)
print(myList)
>> [1,2,3,4,5,6,7]
>> [11,1,2,3,4,5,6,7]
```

### Inserting an element to the any given place in the list

**Time Complexity** - O(n)
**Space Complexity** - O(1)

```python
myList= [1,2,3,4,5,6,7]
print(myList)

myList.insert(4, 15)
print(myList)
>> [1,2,3,4,5,6,7]
>> [1,2,3,4,15,5,6,7]
```
### Inserting an element to the end of the list

**Time Complexity** - O(1)
**Space Complexity** - O(1)

```python
myList= [1,2,3,4,5,6,7]
print(myList)

myList.append(55)
print(myList)
>> [1,2,3,4,5,6,7]
>> [1,2,3,4,5,6,7,55]
```
### Inserting another list to the list

**Time Complexity** - O(n)
**Space Complexity** - O(n)

```python
myList= [1,2,3,4,5,6,7]
print(myList)

newList = [11,12,13,14]
myList.extend(newList)
print(myList)
>> [1,2,3,4,5,6,7]
>> [1,2,3,4,5,6,7,11,12,13,14]
```

## Slice in list

```python
myList = ['a', 'b', 'c', 'd', 'e', 'f']

print(myList[1:3])
>> ['b', 'c']

print(myList[:3])
>> ['a', 'b', 'c']

print(myList[1:])
>> ['b', 'c', 'd', 'e', 'f']

print(myList[:])
>> ['a', 'b', 'c', 'd', 'e', 'f']

# Update multiple elements using slice
myList[0:2] = ['x', 'y']
print(myList)
>> ['x', 'y', 'c', 'd', 'e', 'f']
```
## Delete from list

**Time Complexity** - O(n)
**Space Complexity** - O(1)

```python
myList = ['a', 'b', 'c', 'd', 'e', 'f']

# Deletes second element
print(myList.pop(1))
print(myList)
>> 'b'
>> ['a', 'c', 'd', 'e', 'f']

# Deletes last element
print(myList.pop())
print(myList)
>> 'f'
>> ['a', 'b', 'c', 'd', 'e']

del myList[3]
print(myList)
>> ['a', 'b', 'c', 'e', 'f']

del myList[0:2]
print(myList)
>> ['c', 'd', 'e', 'f']

# Remove element from list based on value
myList.remove('a')
print(myList)
>> ['b', 'c', 'd', 'e', 'f']
```

## Searching in list

**Time Complexity** - O(n)
**Space Complexity** - O(1)

```python
#  Searching for an element in the List
myList =  [10,20,30,40,50,60,70,80,90]

def searchinList(p_list, target):
    for i,value in enumerate(p_list):
        if value == target:
            return i
    return -1

print(searchinList(myList, 100))
>> -1

print(searchinList(myList, 10))
>> 0
```

# List Operations

## Concatenate of two list (`+` Operator)

```python
a = [1,2,3]
b = [4,5,6,7]
c = a + b

print(c)
>> [1,2,3,4,5,6,7]
```

## `*` Operator

```python
a = [0,1]
a = a * 4

print(a)
>> [0, 1, 0, 1, 0, 1, 0, 1]
```

## `len()` function

`len()` : returns count of elements in the List

```python
a = [0,1,2,3,4,5,6]

print(len(a))
>> 7
```

## `max()` function

`max()` : returns the item with the highest value in the List

```python
a = [0,1,2,3,4,5,6]

print(max(a))
>> 6
```

## `min()` function

`min()` : returns the item with the lowest value in the List

```python
a = [0,1,2,3,4,5,6]

print(min(a))
>> 0
```

## `sum()` function

`sum()` : returns the sum of all items in the List

```python
a = [0,1,2,3,4,5,6]

print(sum(a))
>> 21
```

## Average of numbers in list

```python
myList = []
while (True):
    inp = input('Enter a number: ') 
    if inp == 'done': break
    value = float(inp)
    myList.append(value)

average = sum(myList) / len(myList)
print('Average:', average)

>> Enter a number: 1
>> Enter a number: 2
>> Enter a number: 3
>> Enter a number: done
>> Average: 2.0
```

# Time and Space Complexity in Python Lists

| Operation                     | Time complexity | Space complexity |
| ----------------------------- | :-------------: | :--------------: |
| Creating a List               |      O(1)       |       O(1)       |
| Creating a List with elements |      O(n)       |       O(n)       |
| Inserting a value in a List   |      O(n)       |       O(1)       |
| Traversing a given List       |      O(n)       |       O(1)       |
| Accessing a given cell        |      O(1)       |       O(1)       |
| Searching a given value       |      O(n)       |       O(1)       |
| Deleting a given value        |      O(1)       |       O(1)       |

# List comprehension
 [[List comprehension]]

# Project

## Problem Statement

Find Number of Days Above Average Temperature

## Input
**How many day's temperature?** 2
**Day 1’s high temp:** 1
**Day 2’s high temp:** 2

## Output
Average = 1.5
1 day(s) above average

## Solution
```python
numDays = int(input("How many day's temperature?

total = 0
temp = []
for i in range(numDays):
	nextDay = int(input(f"Day {str(i+1)}'s high temp:"))
	temp.append(nextDay)
	total += temp[i]

avg = round (totat/numDays,2)
print(f"Average = {str(avg)}")

above = 0 
for i in temp:
	if i > avg:
		above += 1

print(f"{str(above)} day(s) above average")
```

# Coding questions

- [[Missing Number]]
- [[Two Sum]]
- [[Max Product of Two Integers]]
- [[Middle Function]]
- [[2D Lists]]
- [[Best Score]]
- [[Duplicate Number]]
- [[Pairs]]
- [[Contains Duplicate]]
- [[Rotate Matrix or Image]]
List comprehension offers a shorter syntax when you want to create a new list based on the values of an existing list.

Example:
Based on a list of fruits, you want a new list, containing only the fruits with the letter "a" in the name. Without list comprehension you will have to write a `for` statement with a conditional test inside:

```python
fruits = ["apple", "banana", "cherry", "kiwi", "mango"]  
newlist = []  
  
for x in fruits:  
	if "a" in x:  
		newlist.append(x)  
  
print(newlist)
```

With list comprehension you can do all that with only one line of code:

```python
fruits = ["apple", "banana", "cherry", "kiwi", "mango"]  
newlist = [x for x in fruits if "a" in x]  
print(newlist)
```
## The Syntax

```python
newlist = [_expression_ for _item_ in _iterable_ if _condition_ == True]
```

The return value is a new list, leaving the old list unchanged.
## Condition

The _condition_ is like a filter that only accepts the items that valuate to `True`.
### Example
Only accept items that are not "apple":

```python
newlist = [x for x in fruits if x != "apple"]
```

The condition if x != "apple"  will return `True` for all elements other than "apple", making the new list contain all fruits except "apple". The _condition_ is optional and can be omitted:

### Example
With no `if` statement:

```python
newlist = [x for x in fruits]
```
### Iterable

The _iterable_ can be any iterable object, like a list, tuple, set etc.
### Example
You can use the `range()` function to create an iterable:

```python
newlist = [x for x in range(10)]
```

Same example, but with a condition:
### Example
Accept only numbers lower than 5:

```python
newlist = [x for x in range(10) if x < 5]
```
### Expression

The _expression_ is the current item in the iteration, but it is also the outcome, which you can manipulate before it ends up like a list item in the new list:
### Example
Set the values in the new list to upper case:

```python
newlist = [x.upper() for x in fruits]
```

You can set the outcome to whatever you like:
### Example
Set all values in the new list to 'hello':

```python
newlist = ['hello' for x in fruits]
```

The _expression_ can also contain conditions, not like a filter, but as a way to manipulate the outcome:
### Example
Return "orange" instead of "banana":

```python
newlist = [x if x != "banana" else "orange" for x in fruits]
```

The _expression_ in the example above says:
_"Return the item if it is not banana, if it is banana return orange"._
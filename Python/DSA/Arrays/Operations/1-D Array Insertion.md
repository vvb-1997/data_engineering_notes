**Time Complexity** - O(n)
**Space Complexity** - O(1)

```python
import array

my_array1 = array.array( 'c' , ['a', 'b', 'c', 'd', 'e'])
print(my_array1)
my_array1.insert(5, 'f')
print(my_array1)
```

## Output

```python
array( 'c', ['a', 'b', 'c', 'd', 'e'])
array( 'c', ['a', 'b', 'c', 'd', 'e', 'f'])
```

![[1-D Array Insertion.png]]

# Insertion, when array if full

![[Insertion when 1-D Array full.png]]
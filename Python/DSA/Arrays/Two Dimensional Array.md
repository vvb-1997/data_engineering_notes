# Creating an array

When we create an array , we:
- Assign it to a variable
- Define the type of elements that it will store
- Define its size (the maximum numbers of elements)

![[create 2-D array.png]]

```python
import numpy as np
twoDArray = np.array([[11, 15, 10, 6], [10, 14, 11, 5], [12, 17, 12, 8], [15, 18, 14, 9]])

print(twoDArray)
```

**Time Complexity** - O(m\*n)
**Space Complexity** - O(m\*n)
# Operations for one dimensional array

- [[2-D Array Insertion]]
- [[2-D Array access an element]]
- [[2-D Array Traversal]]
- [[2-D Array searching for the element]]
- [[2-D Array deletion]]

## Time and Space Complexity for two dimensional array

| **Operation**                      | **Time Complexity** | **Space Complexity** |
| ---------------------------------- | :-----------------: | :------------------: |
| Creating an empty array            |        O(1)         |         O(1)         |
| Creating an array with elements    |       O(m\*n)       |       O(m\*n)        |
| Inserting a column/row in an array |       O(m\*n)       |       O(m\*n)        |
| Traversing a given array           |       O(m\*n)       |         O(1)         |
| Accessing a given cell             |        O(1)         |         O(1)         |
| Searching a given value            |       O(m\*n)       |         O(1)         |
| Deleting a given value             |       O(m\*n)       |       O(m\*n)        |

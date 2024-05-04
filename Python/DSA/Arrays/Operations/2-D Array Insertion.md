**Time Complexity** - O(m\*n)
**Space Complexity** - O(m\*n)

```python
import numpy as np
twoDArray = np.array([[11, 15, 10, 6], [10, 14, 11, 5], [12, 17, 12, 8], [15, 18, 14, 9]])
print(twoDArray)

#column insertion
newTwoDArray = np.insert(twoDArray, 0, [[1, 2, 3, 4]], axis = 1)
print(newTwoDArray)

#row insertion
newTwoDArray = np.insert(twoDArray, 0, [[1, 2, 3, 4]], axis = 0)
print(newTwoDArray)

#add row at the end
newTwoDArray = np.append(twoDArray, [[1, 2, 3, 4]], axis = 0)
print(newTwoDArray)

```

## Output

```python
[[11 15 10  6]
 [10 14 11  5]
 [12 17 12  8]
 [15 18 14  9]]

[[ 1 11 15 10  6]
 [ 2 10 14 11  5]
 [ 3 12 17 12  8]
 [ 4 15 18 14  9]]

[[ 1  2  3  4]
 [11 15 10  6]
 [10 14 11  5]
 [12 17 12  8]
 [15 18 14  9]]

[[11 15 10  6]
 [10 14 11  5]
 [12 17 12  8]
 [15 18 14  9]
 [ 1  2  3  4]]
```

![[2-D Array Column Insertion.png]]

![[2-D Array Row Insertion.png]]


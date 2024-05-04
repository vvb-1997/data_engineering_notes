**Time Complexity** - O(1)
**Space Complexity** - O(1)

```python
import numpy as np
twoDArray = np.array([[11, 15, 10, 6], [10, 14, 11, 5], [12, 17, 12, 8], [15, 18, 14, 9]])
print(twoDArray)

def accessElements(array, rowIndex, colIndex) :
	if rowIndex >= len(array) or colIndex >= len(array):
		print( 'Incorrect index')
	else:
		print (array[rowIndex][colIndex])

accessElements(twoDArray, 1, 2)
```

## Output

```python
[[11 15 10  6]
 [10 14 11  5]
 [12 17 12  8]
 [15 18 14  9]]

11
```

![[2-D Array access an element from array.png]]
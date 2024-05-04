**Time Complexity** - O($n^2$)
**Space Complexity** - O(1)

```python

import numpy as np
twoDArray = np.array([[11, 15, 10, 6], [10, 14, 11, 5], [12, 17, 12, 8], [15, 18, 14, 9]])
print(twoDArray)

def searchTDArray(array, value):
	for i in range(len(array)):
		for j in range(len(array[0])):
			if array[i][j] == value:
				return f'The return value is located at {str(i)} {str(j)}'
	return 'The element is not found'

print(searchTDArray(twoDArray, 14))
```
## Output

```python
[[11 15 10  6]
 [10 14 11  5]
 [12 17 12  8]
 [15 18 14  9]]

The return value is located at 1 1
```

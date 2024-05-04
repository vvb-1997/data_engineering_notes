**Time Complexity** - O($n^2$)
**Space Complexity** - O(1)

```python

import numpy as np
twoDArray = np.array([[11, 15, 10, 6], [10, 14, 11, 5], [12, 17, 12, 8], [15, 18, 14, 9]])
print(twoDArray)

def traverseTDArray(array):
	for i in range(len(array)):
		for j in range(len(array[0])):
			print(array[i][j])	

traverseTDArray(twoDArray)
```
## Output

```python
[[11 15 10  6]
 [10 14 11  5]
 [12 17 12  8]
 [15 18 14  9]]

11
15
10
6
10
14
11
5
12
17
12
8
15
18
14
9
```

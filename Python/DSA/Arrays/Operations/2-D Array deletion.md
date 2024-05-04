**Time Complexity** - O($n^2$)
**Space Complexity** - O($n^2$)

```python

import numpy as np
twoDArray = np.array([[11, 15, 10, 6], [10, 14, 11, 5], [12, 17, 12, 8], [15, 18, 14, 9]])
print(twoDArray)

#column deletion
newTwoDArray = np.delete(twoDArray, 0, axis = 1)
print(newTwoDArray)

#row deletion
newTwoDArray = np.delete(twoDArray, 0, axis = 0)
print(newTwoDArray)

```
## Output

```python
[[11 15 10  6]
 [10 14 11  5]
 [12 17 12  8]
 [15 18 14  9]]

[[15 10  6]
 [14 11  5]
 [17 12  8]
 [18 14  9]]

[[10 14 11  5]
 [12 17 12  8]
 [15 18 14  9]]
 ```

![[Python/DSA/Arrays/images/2-D Array column deletion.png]]

![[2-D Array row deletion.png]]
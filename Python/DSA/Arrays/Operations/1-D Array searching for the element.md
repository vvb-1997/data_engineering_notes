**Time Complexity** - O(n)
**Space Complexity** - O(1)

```python
import array
arr1 = array.array( 'i', [1,2,3,4,5])

def linear_search(arr, target):
	for i in range(len(arr)):
		if arr[i] == target:
			return i
	return —1

linear_search(arr1, 3)
```

## Output

```python
2
```


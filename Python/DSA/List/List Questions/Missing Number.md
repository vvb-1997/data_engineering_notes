# Problem Statement

Write a function to find the missing number in a given integer array of 1 to 100. The function takes to parameter the array and the number of elements that needs to be in array.  For example if we want to find missing number from 1 to 6 the second parameter will be 6.

**Example**

```python
missing_number([1, 2, 3, 4, 6], 6) # 5
```

# Explanation

You can use the arithmetic progression formula to find the sum of the numbers from 1 to n and then subtract the sum of the numbers in the array to get the missing number. Here's a function that does that:

```python
def missing_number(arr, n):
	# Calculate the sum of first n natural numbers
    total = n * (n + 1) // 2

    # Calculate the sum of numbers in the array
    sum_arr = sum(arr)

    # Find the missing number by subtracting sum_arr from total
    missing = total - sum_arr

    return missing

# Example
print(missing_number([1, 2, 3, 4, 6], 6))  # Output: 5
```

This function calculates the sum of the first n natural numbers using the arithmetic progression formula and then subtracts the sum of the numbers in the array to find the missing number. The time complexity of this function is O(n) because it iterates through the array once to calculate the sum of its elements.

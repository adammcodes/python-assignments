# NumPy Methods Notes for Beginner Practice

These notes summarize the main Python and NumPy ideas used in your assignment. They are ordered from the most basic concepts first, then build toward methods for finding minimums, maximums, sorting, and selecting top values.

---

## 1) Selecting rows and columns with bracket slicing

Before using NumPy methods well, it helps to understand how to select data from an array.

For a 2D NumPy array:

```python
arr[rows, columns]
```

A colon `:` means “take everything” along that dimension.

### Common patterns

```python
arr[:, 0]
```
Take all rows from column 0.

```python
arr[0, :]
```
Take all columns from row 0.

```python
arr[2:5, 0]
```
Take rows 2 through 4 from column 0.

```python
arr[:, 1:3]
```
Take all rows, and columns 1 through 2.

### Example

```python
import numpy as np

arr = np.array([
    [46, 1, 0],
    [19, 0, 1],
    [35, 1, 0]
])

print(arr[:, 0])
# [46 19 35]

print(arr[:, 1])
# [1 0 1]

print(arr[1])
# [19  0  1]
```

### Good rules of thumb

- `arr[:, 0]` means “all rows, first column.”
- `arr[0]` means “first row.”
- `arr[a:b]` includes `a` but does **not** include `b`.
- For a 2D array, selecting a single row like `arr[3]` usually returns a 1D array.
- Filtering rows with a mask like `arr[mask]` usually returns a 2D array.

---

## 2) Creating and using boolean masks

A **mask** is a boolean array of `True` and `False` values.

You use it to filter elements or rows.

### Basic idea

```python
mask = arr[:, 1] == 3
filtered = arr[mask]
```

This means:
- check whether column 1 equals 3
- keep only the rows where that is `True`

### Example

```python
arr = np.array([
    [15, 3, 2],
    [13, 3, 2],
    [ 9, 3, 2],
    [22, 1, 0]
])

mask = arr[:, 1] == 3
print(mask)
# [ True  True  True False]

print(arr[mask])
# [[15  3  2]
#  [13  3  2]
#  [ 9  3  2]]
```

### Combining conditions with `&` and `|`

Use:
- `&` for **and**
- `|` for **or**

Always wrap each condition in parentheses.

### AND example

```python
mask = (arr[:, 1] == 3) & (arr[:, 2] == 2)
filtered = arr[mask]
```

### OR example

```python
mask = (arr[:, 1] == 3) | (arr[:, 2] == 2)
filtered = arr[mask]
```

### Why parentheses matter

Correct:

```python
mask = (arr[:, 1] == 3) & (arr[:, 2] == 2)
```

Incorrect:

```python
mask = arr[:, 1] == 3 & arr[:, 2] == 2
```

### What `arr[mask]` returns

If `mask` is one boolean per row, then `arr[mask]` returns the rows where the mask is `True`.

Even if only one row matches, the result is still usually a 2D array with shape `(1, number_of_columns)`.

### Good rules of thumb

- Build the mask first, then apply it.
- Use masks when you want all rows that match a condition.
- Use `&` and `|`, not Python’s `and` and `or`, for NumPy arrays.
- Put each condition in parentheses.

---

## 3) Python `list.index()` vs NumPy `np.where()`

These are related ideas, but they are used differently.

### Python `list.index()`

Used with regular Python lists.

```python
fruits = ["apple", "banana", "orange"]
print(fruits.index("banana"))
# 1
```

### What it returns

- the index of the **first matching item**
- raises `ValueError` if the item is not found

### Good to know

- only works on Python lists
- only returns the first match

---

### NumPy `np.where()`

Used with NumPy arrays when you want the indexes where a condition is true.

```python
import numpy as np

arr = np.array([10, 20, 30, 20])
idx = np.where(arr == 20)[0]
print(idx)
# [1 3]
```

### What it returns

For a 1D array:
- an array of matching indexes

For a 2D array:
- separate arrays for row indexes and column indexes

```python
arr = np.array([
    [1, 2],
    [3, 2]
])

rows, cols = np.where(arr == 2)
print(rows)
# [0 1]
print(cols)
# [1 1]
```

### When to use which

Use `list.index()` when:
- you have a regular Python list
- you only want the first match

Use `np.where()` when:
- you have a NumPy array
- you want all matching indexes
- you are checking a condition, not just one exact item

### Good rules of thumb

- `list.index()` is simple but limited.
- `np.where()` is more flexible for arrays.
- For row filtering in NumPy, you often do not need `np.where()` at all; a mask like `arr[condition]` is often enough.

---

## 4) `np.argmin()` and `np.argmax()`

These are very common and very useful.

- `np.argmin()` returns the index of the smallest value
- `np.argmax()` returns the index of the largest value

They return the **position**, not the value itself.

### Example

```python
arr = np.array([15, 13, 9, 22, 0, 4, 6, 20])

print(np.min(arr))
# 0

print(np.argmin(arr))
# 4
```

The smallest value is `0`, and its index is `4`.

### Example with a 2D cricket-style array

```python
scores = np.array([
    [15, 3, 2],
    [13, 3, 2],
    [ 9, 3, 2],
    [22, 3, 2],
    [ 0, 3, 2]
])

idx = np.argmin(scores[:, 0])
print(idx)
# 4

print(scores[idx])
# [0 3 2]
```

### Why it is useful

Instead of:

```python
lowest_runs = np.min(scores[:, 0])
rows = scores[scores[:, 0] == lowest_runs]
```

You can directly get the row position:

```python
idx = np.argmin(scores[:, 0])
row = scores[idx]
```

### Tie behavior

If there is a tie:
- `np.argmin()` returns the first minimum
- `np.argmax()` returns the first maximum

### Good rules of thumb

- Use `argmin` and `argmax` when you want the row index of the smallest or largest value.
- Use `min` and `max` when you only want the value itself.
- For a 2D array, first select the column you care about, such as `arr[:, 0]`.

---

## 5) `np.sort()` and `np.argsort()`

These are used for sorting, but they do different things.

### `np.sort()`

Returns the sorted **values**.

```python
arr = np.array([40, 10, 30, 20])
print(np.sort(arr))
# [10 20 30 40]
```

### What it returns

- a sorted copy of the values
- not the indexes

### `np.argsort()`

Returns the indexes that would sort the array.

```python
arr = np.array([40, 10, 30, 20])
print(np.argsort(arr))
# [1 3 2 0]
```

Why?
- index 1 has value 10
- index 3 has value 20
- index 2 has value 30
- index 0 has value 40

### Why `argsort()` is powerful

You can use those indexes to sort another array or sort rows of a 2D array by a chosen column.

### Sort a 2D array by a column

```python
arr = np.array([
    [5, 10, 2],
    [9,  3, 7],
    [1,  8, 4],
    [6, 12, 9]
])

col = 1
idx = np.argsort(arr[:, col])
sorted_arr = arr[idx]

print(sorted_arr)
# [[ 9  3  7]
#  [ 1  8  4]
#  [ 5 10  2]
#  [ 6 12  9]]
```

### Descending order

Use `[::-1]` to reverse the sorted indexes.

```python
idx = np.argsort(arr[:, col])[::-1]
sorted_desc = arr[idx]
```

### Get the top X rows by a column

```python
X = 2
idx = np.argsort(arr[:, col])[::-1][:X]
top_rows = arr[idx]
```

### Good rules of thumb

- Use `np.sort()` when you only care about sorted values.
- Use `np.argsort()` when you want to sort rows or keep track of original positions.
- `argsort()` is usually the clearest way to fully sort by a column.

---

## 6) `np.argpartition()`

`np.argpartition()` is used when you want the top or bottom few values **without fully sorting everything**.

It is often faster than `argsort()` for large arrays.

### Basic idea

```python
idx = np.argpartition(arr[:, col], -X)[-X:]
```

This finds the indexes of the top `X` values in the chosen column.

### Example

```python
arr = np.array([
    [5, 10, 2],
    [9,  3, 7],
    [1,  8, 4],
    [6, 12, 9]
])

col = 1
X = 2

idx = np.argpartition(arr[:, col], -X)[-X:]
print(idx)
# might be [0 3]

print(arr[idx])
# rows with the top 2 values from column 1
```

### Important detail

`argpartition()` does **not fully sort** the results.

It only guarantees that the top `X` items are in the last `X` positions of the partitioned result.
The order within those top `X` items is not guaranteed.

### If you want the top X rows sorted too

```python
idx = np.argpartition(arr[:, col], -X)[-X:]
idx = idx[np.argsort(arr[idx, col])[::-1]]
top_rows = arr[idx]
```

### What it returns

- indexes
- not values
- not necessarily sorted indexes

### Why it is useful

Use it when:
- you only need the top few values
- you do not want to sort the whole array
- performance matters on larger data

### Good rules of thumb

- Use `argsort()` for clarity and full sorting.
- Use `argpartition()` for speed when you only need top/bottom `X` values.
- If presentation order matters, sort the result after using `argpartition()`.

---

## 7) Choosing between these methods

### Use masks when:
- you want all rows matching one or more conditions
- example: all rows where wickets == 3 and catches == 2

### Use `np.where()` when:
- you need the actual matching indexes
- especially useful in 1D arrays or when extracting row/column index pairs

### Use `np.argmin()` / `np.argmax()` when:
- you want the position of the single smallest or largest value
- example: the row with the fewest runs or most wickets

### Use `np.sort()` when:
- you only need the sorted values

### Use `np.argsort()` when:
- you want to sort rows by a column
- you want the top rows in sorted order

### Use `np.argpartition()` when:
- you only need the top or bottom `X`
- you do not need a full sort right away

---

## 8) Quick reference summary

### Select data

```python
arr[:, 0]      # first column
arr[3]         # fourth row
arr[2:5]       # rows 2 to 4
arr[:, 1:3]    # columns 1 to 2
```

### Filter with masks

```python
mask = arr[:, 1] == 3
arr[mask]

mask = (arr[:, 1] == 3) & (arr[:, 2] == 2)
arr[mask]
```

### Find indexes

```python
my_list.index(value)         # Python list, first match
np.where(arr == value)[0]    # NumPy array, all matches in 1D
```

### Smallest / largest

```python
np.argmin(arr[:, 0])
np.argmax(arr[:, 0])
```

### Sorting

```python
np.sort(arr[:, 0])
np.argsort(arr[:, 0])
arr[np.argsort(arr[:, 0])]
```

### Top X values

```python
idx = np.argsort(arr[:, 0])[::-1][:X]
top_rows = arr[idx]
```

### Faster top X without full sort

```python
idx = np.argpartition(arr[:, 0], -X)[-X:]
top_rows = arr[idx]
```

---

## 9) Final beginner advice

- First decide whether you want **values**, **indexes**, or **full rows**.
- Use masks for filtering by conditions.
- Use `argmin` and `argmax` for one best row.
- Use `argsort` when you want a clean full ranking.
- Use `argpartition` when you only care about the top few.
- When working with 2D arrays, be very clear about which column you are operating on.
- A lot of NumPy work follows this pattern:
  1. select the relevant column
  2. build a condition or compute indexes
  3. use those indexes or mask to get rows back


# Python Dictionary Notes

## 1. Combining dictionaries in Python

There are a few common ways to combine two dictionaries.

### Using `|` in Python 3.9+

```python
a = {"x": 1, "y": 2}
b = {"y": 99, "z": 3}

c = a | b
print(c)
# {'x': 1, 'y': 99, 'z': 3}
```

If the same key appears in both dictionaries, the value from the **second** dictionary is used.

### Using unpacking

```python
a = {"x": 1, "y": 2}
b = {"y": 99, "z": 3}

c = {**a, **b}
print(c)
# {'x': 1, 'y': 99, 'z': 3}
```

### Using `.update()`

```python
a = {"x": 1, "y": 2}
b = {"y": 99, "z": 3}

a.update(b)
print(a)
# {'x': 1, 'y': 99, 'z': 3}
```

The difference is that `.update()` changes the original dictionary in place.

---

## 2. Adding values from two dictionaries together by key

Sometimes you do not want to just replace values. Instead, you want to **add numbers together for matching keys**.

Example:

```python
total_views = {"Show 1": 100, "Show 2": 200}
netflix_last_week = {"Show 1": 5, "Show 3": 20}
```

We want to add the values from `netflix_last_week` into `total_views`.

A safe way to do that is:

```python
for show, views_last_week in netflix_last_week.items():
    total_views[show] = total_views.get(show, 0) + views_last_week

print(total_views)
# {'Show 1': 105, 'Show 2': 200, 'Show 3': 20}
```

### What this does

- If the show is already in `total_views`, Python uses its current value.
- If the show is missing, `.get(show, 0)` returns `0`.
- Then Python adds the new weekly views and stores the result back in the dictionary.

---

## 3. How augmented assignment works

Augmented assignment is shorthand for updating a variable using its current value.

Example:

```python
x = 100
x += 5
print(x)
# 105
```

This is shorthand for:

```python
x = x + 5
```

Other examples:

```python
x -= 5   # subtract
x *= 2   # multiply
x /= 4   # divide
x //= 4  # floor divide
x %= 3   # remainder
x **= 2  # power
```

### Augmented assignment with dictionaries

This can work when a dictionary already has the key:

```python
scores = {"math": 80}
scores["math"] += 5
print(scores)
# {'math': 85}
```

But this is **not safe** if the key might be missing.

---

## 4. Why `+=` can raise a `KeyError` in a loop

Consider this code:

```python
total_views = {"Show 1": 100, "Show 2": 200}
netflix_last_week = {"Show 1": 5, "Show 3": 20}

for show in netflix_last_week:
    views_last_week = netflix_last_week[show]
    total_views[show] += views_last_week
```

### What happens

- For `"Show 1"`, the key exists, so Python updates it.
- For `"Show 3"`, the key does **not** exist in `total_views`.
- Python tries to read `total_views["Show 3"]` before adding to it.
- That raises:

```python
KeyError: 'Show 3'
```

### Why it happens

This line:

```python
total_views[show] += views_last_week
```

is basically doing this:

```python
total_views[show] = total_views[show] + views_last_week
```

So Python must look up `total_views[show]` first. If the key is missing, it crashes with a `KeyError`.

### Safer version using `.get()`

```python
for show, views_last_week in netflix_last_week.items():
    total_views[show] = total_views.get(show, 0) + views_last_week
```

This is safer because `.get(show, 0)` returns `0` when the key does not exist.

---

## 5. How `.items()` works

A dictionary stores **key-value pairs**.

Example:

```python
netflix_last_week = {"Show 1": 5, "Show 3": 20}
```

When you call `.items()`, Python gives you each key-value pair as a tuple:

```python
("Show 1", 5)
("Show 3", 20)
```

So this loop:

```python
for item in netflix_last_week.items():
    print(item)
```

prints tuples like these pairs.

---

## 6. Unpacking with `.items()` in a loop

Because each item is a tuple like `(key, value)`, Python lets you **unpack** the tuple into two variables:

```python
for show, views_last_week in netflix_last_week.items():
    print(show, views_last_week)
```

### On each loop

First loop:

```python
show = "Show 1"
views_last_week = 5
```

Second loop:

```python
show = "Show 3"
views_last_week = 20
```

This is useful because you can work with both the key and the value directly, without writing extra dictionary lookups.

Instead of this:

```python
for show in netflix_last_week:
    views_last_week = netflix_last_week[show]
```

You can write this:

```python
for show, views_last_week in netflix_last_week.items():
```

That is shorter and often easier to read.

---

## 7. Putting it all together

This loop is a clean way to add values from one dictionary into another:

```python
total_views = {"Show 1": 100, "Show 2": 200}
netflix_last_week = {"Show 1": 5, "Show 3": 20}

for show, views_last_week in netflix_last_week.items():
    total_views[show] = total_views.get(show, 0) + views_last_week

print(total_views)
# {'Show 1': 105, 'Show 2': 200, 'Show 3': 20}
```

### Why this version is good

- `.items()` gives both key and value at the same time.
- unpacking makes the loop easier to read.
- `.get(show, 0)` prevents a `KeyError`.
- the result works whether the key already exists or not.

---

## Quick summary

- Use `a | b`, `{**a, **b}`, or `.update()` to combine dictionaries.
- If keys overlap, the later value replaces the earlier one.
- Use augmented assignment like `x += 5` as shorthand for `x = x + 5`.
- `dict[key] += value` can raise a `KeyError` if the key is missing.
- `dict.get(key, 0)` is safer when the key may not exist.
- `.items()` returns tuples of `(key, value)`.
- Unpacking those tuples in a loop makes it easy to work with both keys and values.


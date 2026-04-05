# Python Notes: Dictionaries, `min()` / `max()`, and `lambda`

## 1) Looping over dictionaries gives you keys

When you loop over a dictionary directly, Python gives you the **keys**, not the values.

```python
data = {"Abc": 123, "def": 456, "xyz": 50}

for key in data:
    print(key)
```

Output:

```python
Abc
def
xyz
```

That means this:

```python
for item in data:
    print(item)
```

is really giving you each dictionary key one at a time.

### Important idea

If you want:
- **keys** → loop over the dictionary directly
- **values** → use `.values()`
- **key-value pairs** → use `.items()`

Examples:

```python
data = {"Abc": 123, "def": 456, "xyz": 50}

for key in data:
    print("key:", key)

for value in data.values():
    print("value:", value)

for key, value in data.items():
    print(key, value)
```

---

## 2) The `dict.get` method

The `.get()` method lets you look up a value by its key.

```python
data = {"Abc": 123, "def": 456, "xyz": 50}

print(data.get("Abc"))   # 123
print(data.get("def"))   # 456
```

### Why use `.get()`?

It is a convenient way to retrieve a value from a dictionary.

This:

```python
data.get("Abc")
```

means:

- look for the key `"Abc"`
- return its value

So:

```python
data.get("Abc")
```

returns:

```python
123
```

### `.get()` can also provide a default

If a key might be missing, you can give a default value:

```python
print(data.get("missing", 0))   # 0
```

That means:

- try to get `"missing"`
- if it is not there, return `0`

---

## 3) How the `key` argument works in `max()` and `min()`

The `key` argument tells Python:

**"When deciding what is biggest or smallest, use this rule."**

### Example with a list of words

```python
words = ["apple", "banana", "kiwi", "strawberry"]

longest = max(words, key=len)
shortest = min(words, key=len)

print(longest)   # strawberry
print(shortest)  # kiwi
```

Here:
- `max(words)` by itself would compare alphabetically
- `max(words, key=len)` compares by **length**
- `min(words, key=len)` finds the shortest word

### What `key=` does

Python:
1. looks at each item
2. applies the key function to each item
3. compares the returned values
4. returns the **original item**

So:

```python
max(words, key=len)
```

compares:

- `len("apple")` → `5`
- `len("banana")` → `6`
- `len("kiwi")` → `4`
- `len("strawberry")` → `10`

and returns `"strawberry"`.

### Using `max()` and `min()` with a dictionary

Because looping over a dictionary gives keys, this works:

```python
data = {"Abc": 123, "def": 456, "xyz": 50}

highest_key = max(data, key=data.get)
lowest_key = min(data, key=data.get)

print(highest_key)   # def
print(lowest_key)    # xyz
```

### Why this works

`max(data, key=data.get)` means:

- loop over the keys: `"Abc"`, `"def"`, `"xyz"`
- for each key, use `data.get(key)` as the comparison value

So Python compares:

- `data.get("Abc")` → `123`
- `data.get("def")` → `456`
- `data.get("xyz")` → `50`

Since `456` is the largest, `max()` returns the key `"def"`.

Since `50` is the smallest, `min()` returns the key `"xyz"`.

### Useful pattern

```python
max(my_dict, key=my_dict.get)
min(my_dict, key=my_dict.get)
```

This is the common Python pattern for finding:
- the key with the highest value
- the key with the lowest value

### Getting the value too

```python
data = {"Abc": 123, "def": 456, "xyz": 50}

highest_key = max(data, key=data.get)
lowest_key = min(data, key=data.get)

print(highest_key, data[highest_key])   # def 456
print(lowest_key, data[lowest_key])     # xyz 50
```

---

## 4) The `lambda` function

A `lambda` is a small anonymous function.

- **small** = usually short
- **anonymous** = it does not have a name

Example:

```python
lambda x: x + 1
```

This means:

**"a function that takes `x` and returns `x + 1`"**

It is similar to:

```python
def add_one(x):
    return x + 1
```

### General syntax

```python
lambda parameter: expression
```

### Piece-by-piece explanation

#### `lambda`
This tells Python you are creating a function.

#### `parameter`
This is the input to the function.

#### `:`
This separates the input part from the value that will be returned.

#### `expression`
This is the result the function returns.

So:

```python
lambda x: x * 2
```

means:

- `lambda` → make a function
- `x` → one input value
- `:` → separator
- `x * 2` → return double the input

---

## `lambda` as the `key` argument

A `lambda` is often used when you want a quick comparison rule.

### Example with words

```python
words = ["cat", "elephant", "dog"]

result = max(words, key=lambda w: len(w))
print(result)   # elephant
```

### Piece-by-piece

```python
lambda w: len(w)
```

means:

- `lambda` → create a small function
- `w` → each word is passed in one at a time
- `:` → separates input from output
- `len(w)` → return the length of that word

So Python compares:

- `"cat"` → `3`
- `"elephant"` → `8`
- `"dog"` → `3`

and returns `"elephant"`.

### Equivalent named function

```python
def get_length(w):
    return len(w)

print(max(words, key=get_length))
```

This does the same thing as:

```python
print(max(words, key=lambda w: len(w)))
```

---

## `lambda` with dictionaries

You can also use `lambda` with dictionaries.

```python
data = {"Abc": 123, "def": 456, "xyz": 50}

highest_key = max(data, key=lambda k: data[k])
lowest_key = min(data, key=lambda k: data[k])

print(highest_key)   # def
print(lowest_key)    # xyz
```

### Piece-by-piece

```python
lambda k: data[k]
```

means:

- `lambda` → create a function
- `k` → one dictionary key at a time
- `:` → separator
- `data[k]` → return the value for that key

So Python compares:

- `data["Abc"]` → `123`
- `data["def"]` → `456`
- `data["xyz"]` → `50`

This gives the same result as:

```python
max(data, key=data.get)
min(data, key=data.get)
```

---

## Summary

### Looping over dictionaries
When you loop over a dictionary directly, Python gives you the **keys**.

```python
for key in data:
    print(key)
```

### `dict.get`
Use `.get()` to retrieve a value by key.

```python
data.get("Abc")
```

### `key=` in `max()` and `min()`
The `key` argument tells Python what value to use for comparison.

```python
max(words, key=len)
min(words, key=len)
```

For dictionaries:

```python
max(data, key=data.get)
min(data, key=data.get)
```

### `lambda`
A `lambda` is a short anonymous function.

```python
lambda x: x + 1
```

General form:

```python
lambda parameter: expression
```

Example as a `key` function:

```python
max(words, key=lambda w: len(w))
```

That means:

**"For each word `w`, use `len(w)` when comparing."**

---

## Final takeaway

A very useful pattern to remember is:

```python
data = {"Abc": 123, "def": 456, "xyz": 50}

print(max(data, key=data.get))   # def
print(min(data, key=data.get))   # xyz
```

This works because:
1. looping over the dictionary gives keys
2. `data.get(key)` gives the value for each key
3. `max()` and `min()` compare using those values

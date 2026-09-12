# Two Sum

**LeetCode:** #1
**Difficulty:** Easy
**Data Structure:** Array
**Supporting Data Structure:** Hash Map
**Technique:** Complement Lookup

[View Problem on LeetCode](https://leetcode.com/problems/two-sum/)

## Problem

Given an array of integers `nums` and an integer `target`, return the indices of the two numbers that add up to `target`.

Each input has exactly one solution, and the same element cannot be used twice.

### Example

```text
nums = [2, 7, 11, 15]
target = 9

Output = [0, 1]
```

Because:

```text
2 + 7 = 9
```

The output contains the indices of these two numbers.

---

## 1. Initial Approach

The first approach that comes to mind is to compare every number with every other number and check whether their sum equals the target.

```python
def twoSum(nums, target):
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] + nums[j] == target:
                return [i, j]
```

This works, but it checks many pairs that are not needed.

### Complexity

Time: `O(n²)`

Space: `O(1)`

The main issue is the repeated search for the second number.

---

## 2. Looking for a Better Approach

Suppose we have:

```text
nums = [2, 7, 11, 15]
target = 9
```

If the current number is `2`, what number do we need to reach `9`?

```text
9 - 2 = 7
```

So instead of searching for every possible pair, we can calculate the exact number we need.

For any current number:

```text
complement = target - current
```

The problem then becomes:

> Can I quickly check whether this complement already exists?

---

## 3. Using a Hash Map

A hash map allows us to store numbers that we have already seen and look them up efficiently.

We store:

```text
number -> index
```

For example:

```text
{
    2: 0,
    7: 1
}
```

The number is the key and the index is the value.

While iterating through the array:

1. Calculate the complement.
2. Check whether the complement is already in the hash map.
3. If it exists, return its stored index and the current index.
4. Otherwise, store the current number and its index.

---

## 4. Step-by-Step

For:

```text
nums = [2, 7, 11, 15]
target = 9
```

Start with an empty hash map:

```text
seen = {}
```

### First element

```text
num = 2
index = 0

complement = 9 - 2
           = 7
```

`7` is not in `seen`, so store `2`:

```text
seen = {
    2: 0
}
```

### Second element

```text
num = 7
index = 1

complement = 9 - 7
           = 2
```

`2` is already in `seen`.

The stored index of `2` is `0`, so the answer is:

```text
[0, 1]
```

---

## 5. Solution

```python
def twoSum(nums, target):
    seen = {}

    for i, num in enumerate(nums):
        complement = target - num

        if complement in seen:
            return [seen[complement], i]

        seen[num] = i
```

---

## 6. Why `enumerate()`?

The solution needs both the index and the value of each element.

Instead of:

```python
for i in range(len(nums)):
    num = nums[i]
```

we can use:

```python
for i, num in enumerate(nums):
```

`enumerate()` gives us both values directly.

For example:

```python
nums = [2, 7, 11]

for i, num in enumerate(nums):
    print(i, num)
```

produces:

```text
0 2
1 7
2 11
```

Using `enumerate()` does not change the time or space complexity.

---

## 7. Why Does `complement in seen` Check the Number?

The dictionary is structured as:

```text
number -> index
```

For example:

```python
seen = {
    2: 0,
    7: 1
}
```

In Python:

```python
2 in seen
```

checks whether `2` exists as a key.

If it does:

```python
seen[2]
```

returns the value associated with that key, which is the index.

This is why we store the number as the key and the index as the value.

---

## 8. Why Check Before Storing?

The complement must be checked before adding the current number.

Consider:

```text
nums = [3, 3]
target = 6
```

For the first `3`, the complement is also `3`.

We cannot use the same element twice, so the first `3` is stored without matching it with itself.

After processing it:

```text
seen = {
    3: 0
}
```

When we reach the second `3`, the complement is found in `seen`, giving:

```text
[0, 1]
```

---

## 9. Alternative Approach: Sorting + Two Pointers

Another possible solution is to sort the numbers and use two pointers.

The basic idea is:

```text
left ->                         <- right

[2, 3, 4, 7, 11, 15]
```

If the sum of the two values is:

* Less than the target: move `left` forward.
* Greater than the target: move `right` backward.
* Equal to the target: the pair has been found.

However, this approach has two problems here.

First, sorting takes `O(n log n)` time.

Second, the problem asks for the original indices. Sorting changes the positions of the elements, so we would have to preserve the original indices while sorting.

Because of this, the hash map approach is simpler and provides a better time complexity.

---

## 10. Complexity Comparison

| Approach               |           Time |                     Space |
| ---------------------- | -------------: | ------------------------: |
| Brute Force            |        `O(n²)` |                    `O(1)` |
| Sorting + Two Pointers |   `O(n log n)` | Depends on implementation |
| Hash Map               | `O(n)` average |                    `O(n)` |

The final solution takes `O(n)` average time because hash map lookups are `O(1)` on average.

---

## 11. Pattern Recognition

The main technique used here is complement lookup.

When the problem requires:

```text
a + b = target
```

we can rearrange it:

```text
b = target - a
```

For every element, we therefore know exactly what value we are looking for.

If the array is unsorted, a hash map lets us check whether that value has already appeared without scanning the entire array.

The general thought process is:

```text
Current value
      |
      v
Calculate what is needed
      |
      v
Can I find it efficiently?
      |
      v
Use a hash map for lookup
```

---

## 12. Edge Cases

### Duplicate values

```text
nums = [3, 3]
target = 6
```

The two different occurrences can be used.

### Negative numbers

```text
nums = [-3, 4, 2, 7]
target = 1
```

The same complement calculation works:

```text
1 - (-3) = 4
```

### Target involving zero

```text
nums = [0, 4, 3, 0]
target = 0
```

The same approach handles this without any special case.

---

## 13. Common Mistakes

### Using the same element twice

The current element should not be matched with itself.

Checking the complement before storing the current number prevents this.

### Storing the index as the key

This would make lookup inconvenient:

```python
seen[i] = num
```

We need to search for a number, so the number should be the key:

```python
seen[num] = i
```

### Using two pointers without considering the indices

Two pointers works naturally on sorted data, but sorting the original array changes the indices that the problem asks us to return.

### Searching the dictionary values

```python
complement in seen
```

checks the keys.

Since our keys are the numbers, this gives us the lookup we need.

---

## 14. What I Learned

The main improvement from brute force is changing the way we search for the second number.

Instead of checking every possible pair, calculate the number required to reach the target and use a hash map to check whether it has already been seen.

This turns the repeated search into a constant-time average lookup and reduces the overall time complexity from `O(n²)` to `O(n)`.

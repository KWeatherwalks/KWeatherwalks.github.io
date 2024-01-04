---
layout: post
permalink: "/blog/code-challenge/rock-cache"
title: Rock the Cache-bah
subtitle: Using Memoization in Python to Crack a Coding Challenge
thumbnail-img: "assets/blogresources/code-challenge/python_rock_guitar.png"
date: 2023-08-08 16:35
categories:
  - "code-challenge"
  - "write-in-public"
author: Kevin Weatherwalks
tags: writing-in-public/draft
---
{% include writing-in-public/draft.md %}

# Introduction



## Problem Statement

> Let's say that 
> number `a` feels _comfortable_ with number `b` 
> if `a ≠ b` and `b` lies in the segment `[a - s(a), a + s(a)]`, 
> where `s(x)` is the sum of `x`'s digits.  
> How many pairs `(a, b)` are there, 
> such that `a < b`, both `a` and `b` lie on the segment `[l, r]`, 
> and each number feels _comfortable_ with the other (so `a` feels _comfortable_ with `b` and `b` feels _comfortable_ with `a`)?  
> 
> Example  
> For `l = 10` and `r = 12`, the output should be  
> `solution(l, r) = 2`.  
> Here are all values of `s(x)` to consider:  
> • `s(10) = 1`, so `10` is _comfortable_ with `9` and `11`;  
> • `s(11) = 2`, so `11` is _comfortable_ with `9`, `10`, `12` and `13`;  
> • `s(12) = 3`, so `12` is _comfortable_ with `9`, `10`, `11`, `13`, `14` and `15`.  
> Thus, there are `2` pairs of numbers _comfortable_ with each other within the segment `[10; 12]`: `(10, 11)` and `(11, 12)`.  
> Input/Output  
> • **[execution time limit] 4 seconds (py3)**  
> • **[memory limit] 1 GB**  
> • **[input] integer l**  
> _Guaranteed constraints:_  
> `1 ≤ l ≤ r ≤ 1000`.  
> • **[input] integer r**  
> _Guaranteed constraints:_  
> `1 ≤ l ≤ r ≤ 1000`.  
> • **[output] integer**  
> The number of pairs satisfying all the above conditions.


# My Solution

## Initial Attempt

```python
def solution(l, r):
	pairs = []
	# loop over all possible a's
	for a in range(l, r+1):
		# calculate the sum of digits
		for b in range(l, r+1):
			if a != b and b in range(a-s(a),a+s(a)+1):
				pairs.append((a,b,))
	return len(list(filter(lambda x: (x[1],x[0]) in pairs, pairs)))//2
  

def s(x):
	return sum(map(lambda x: int(x), str(x)))
```

This solution will work if time is not a concern. This has $O(n^2)$  time complexity which is too much to pass the test.

## Round 2: List Comprehension

In general, list comprehensions tend to perform faster than nested loops. ^[Citation needed]

```python
def solution(l, r):
	pairs = [(a,b,) for a in range(l, r+1) for b in range(l,r+1) if (a!=b and b in range(a-s(a),a+s(a)+1))]

	return len(list(filter(lambda x: (x[1],x[0]) in pairs, pairs)))//2

def s(x):
	return sum(map(lambda x: int(x), str(x)))
```
But this doesn't pass any additional tests that didn't pass with the nested loop approach.

## Round 3: Precalculating...

I can save a little processing time by calculating the segment before the inner loop...
```python
def solution(l, r):
	pairs = []
	for a in range(l, r+1):
		segment = list(range(a - s(a), a + s(a) + 1))

		for b in range(l, r+1):
			if (a != b) and (b in segment):
				pairs.append((a,b,))

	return len(list(filter(lambda x: (x[1],x[0]) in pairs, pairs)))//2
```

And that does pass Test 5. But other tests are not passed.

## Round 4: Move

After rereading the problem statement, I saw the condition `a < b`. This made me think that I could reduce the search space by swapping the inner loop with the outer loop.
Thus, the solution that passes the sample tests...

```python
def solution(l, r):
	count = 0
	for b in range(l+1, r+1):
		for a in range(l,b):
			a_segment = range(a - s(a), a + s(a) + 1)
			b_segment = range(b - s(b), b + s(b) + 1)
			if (b in a_segment) and (a in b_segment):
				count += 1
	return count


def s(x):
	return sum(map(lambda x: int(x), str(x)))
```
Using the [DRY]() principle, and optimizing for use of list comprehensions...

```python
def solution(l, r):
	return sum([1 for b in range(l+1, r+1) for a in range(l,b) 
				if (a in segment(b)) and (b in segment(a))])

def segment(x):
	sum_of_digits = sum(map(lambda x: int(x), str(x)))
	return range(x - sum_of_digits, x + sum_of_digits + 1)
```

This solution sacrifices a little bit of readability in exchange for an increase in performance


## Round 5: The Gempire Strikes Back

Now here's a trick: [Memoization]()  
By caching previously calculated values, we can gain some speed enhancements on the `s` function. 

```python
def solution(l, r):
	count = 0
	for b in range(l+1, r+1):
		for a in range(l,b):
		a_segment = range(a - s(a), a + s(a) + 1)
		b_segment = range(b - s(b), b + s(b) + 1)
		if (b in a_segment) and (a in b_segment):
			count += 1
	return count

def memoize(func):
	memoized = {}
	def inner(number):
		if number not in memoized:
			memoized[number] = func(number)
		return memoized[number]
	return inner
  
@memoize
def s(x):
	return sum(map(lambda x: int(x), str(x)))
```

And we pass all tests!

## Further Optimization

However, there may still some space inefficiency here. Instead of using the `range` function, let's use a tuple indicating the bounds of the segment.

```python
def solution(l, r):
	count = 0
	for b in range(l+1, r+1):
		for a in range(l,b):
			if is_in_segment(b,calculate_segment(a)) and is_in_segment(a,calculate_segment(b)):
				count += 1
	return count
  
def memoize(func):
	memoized = {}
	def inner(number):
		if number not in memoized:
			memoized[number] = func(number)
		return memoized[number]
	return inner
  
@memoize
def calculate_segment(x):
	sum_of_digits = sum(map(lambda x: int(x), str(x)))
	return (x - sum_of_digits, x + sum_of_digits)
  
def is_in_segment(number, segment):
	return (number >= segment[0]) and (number <= segment[1])
```

> [!Idea] Range membership test may be faster than boundary check
> https://realpython.com/python-range-membership-test/

---

# Solution from [nixerasse](https://app.codesignal.com/profile/nixerasse) using the `itertools` module

```python
import itertools
def solution(L, R):
	def is_comfortable(a,b):
		s = sum(int(d) for d in str(a))
		return a - s <= b <= a + s
	cnt = 0
	for a, b in itertools.combinations(range(L, R + 1), 2):
		if is_comfortable(a, b) and is_comfortable(b, a):
			cnt += 1
	return cnt
```


# Links

- [Wikipedia - Space time tradeoff](https://en.wikipedia.org/wiki/Space%E2%80%93time_tradeoff)
- [Freecodecamp - Python Memoization Recursion for Loop](https://www.freecodecamp.org/news/python-memoization-recursion-for-loop/)
- [AskPython - Memoization in Python](https://www.askpython.com/python/examples/memoization-in-python)
- [Wikipedia - Don't Repeat Yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)
- [RealPython - Primer on Python Decorators](https://realpython.com/primer-on-python-decorators/)


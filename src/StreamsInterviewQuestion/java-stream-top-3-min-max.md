Excellent. This is a very common Java Stream interview question because it tests your understanding of:

* `sorted()`
* `Comparator`
* `limit()`
* Lazy evaluation
* Stream pipeline

Let's understand it step by step.

---

# Suggested Notes File

`java-stream-top-3-min-max.md`

---

# Problem Statement

Given

```java
List<Integer> list = Arrays.asList(
23,231,23,122,34,2,44,3435,1232,
434,234,2,24234,232,2423423
);
```

Need

```
Largest 3 numbers

2423423
24234
3435

------------------

Smallest 3 numbers

2
2
23
```

Notice

We are **not removing duplicates**.

That's why both `2`s appear.

---

# Maximum Numbers

Code

```java
list.stream()
    .sorted(Comparator.reverseOrder())
    .limit(3)
    .forEach(System.out::println);
```

Let's execute it.

---

## Step 1

```java
list.stream()
```

Creates

```
23

231

23

122

34

2

...

2423423
```

Nothing happens yet because Streams are lazy.

---

## Step 2

```java
.sorted(Comparator.reverseOrder())
```

### What is Comparator?

Remember

A Comparator answers one question:

> **How should two elements be compared?**

Normally

```java
Comparator.naturalOrder()
```

means

```
2

10

20

40
```

Ascending.

---

Reverse order means

```java
Comparator.reverseOrder()
```

```
40

20

10

2
```

Descending.

---

Our list becomes

```
2423423

24234

3435

1232

434

232

231

234

122

44

34

23

23

2

2
```

---

## Step 3

```java
.limit(3)
```

Very easy.

Java simply keeps the first three elements.

```
2423423

24234

3435
```

Everything else is ignored.

---

## Step 4

```java
.forEach(System.out::println)
```

Prints

```
2423423
24234
3435
```

Done.

---

# Minimum Numbers

```java
list.stream()
    .sorted(Comparator.naturalOrder())
    .limit(3)
```

Sorting

```
2

2

23

23

34

44

122

...
```

`limit(3)`

```
2

2

23
```

Printed.

---

# Why `limit()` after `sorted()`?

Suppose we write

```java
list.stream()
    .limit(3)
    .sorted()
```

Input

```
23
231
23
122
34
...
```

First

```
limit(3)
```

keeps

```
23

231

23
```

Then sort

```
23

23

231
```

This is **not** the smallest three numbers from the original list.

So order matters.

Correct

```
sorted()

↓

limit()
```

Wrong

```
limit()

↓

sorted()
```

---

# Stream Pipeline

```
List

↓

Stream

↓

sorted()

↓

limit(3)

↓

forEach()
```

---

# Complexity

Many interviewers ask:

> What is the time complexity?

Current solution

```java
sorted()
```

needs to sort the entire list.

Time complexity

```
O(n log n)
```

Even if we only need three numbers.

---

# Is there a better solution?

Yes.

Using a **PriorityQueue (Heap)**.

Time complexity

```
O(n log k)
```

where

```
k = 3
```

For millions of records, this is much faster.

That's what you'd often use in performance-sensitive code.

---

# What if duplicates should not be counted?

Suppose

```
2
2
23
23
34
```

Need

```
2

23

34
```

Simply add

```java
list.stream()
    .distinct()
    .sorted()
    .limit(3)
```

Pipeline

```
List

↓

distinct()

↓

sorted()

↓

limit()
```

---

# Can `max()` be used?

Many beginners ask:

Why not

```java
stream.max()
```

Because

```java
max()
```

returns

```
One element
```

not

```
Top 3
```

Similarly

```java
min()
```

returns only the smallest single value.

---

# Interview Questions

### Q1: Why use `Comparator.reverseOrder()`?

Because `sorted()` sorts in ascending order by default. `Comparator.reverseOrder()` changes the comparison so the largest elements come first.

---

### Q2: Why is `limit()` placed after `sorted()`?

Because we first need to determine the global ordering of the elements. Limiting before sorting would only sort the first few elements, not the entire collection.

---

### Q3: What is the complexity?

* Using `sorted()`: **O(n log n)**
* Using a heap of size 3: **O(n log 3)**, which is effectively **O(n)** because `log 3` is a constant.

---

# Production-Level Thinking

For interview coding rounds, the Stream solution is perfectly acceptable because it's concise and easy to read.

For very large datasets, if someone asks:

> **"How would you optimize this?"**

A good answer is:

> "Sorting the entire list is unnecessary when we only need the top three values. I'd maintain a min-heap of size three while scanning the list once, which reduces the complexity to O(n log 3)."

That answer demonstrates awareness of algorithmic trade-offs beyond simply using the Stream API.

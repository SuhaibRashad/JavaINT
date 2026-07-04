Excellent! This question is less about Streams and more about understanding the **logic of an anagram**. Once you understand the logic, the Stream code becomes very easy.

---

# Suggested Notes File

`java-stream-anagram.md`

---

# First, what is an anagram?

Two strings are anagrams if:

* They contain the **same characters**.
* Each character appears the **same number of times**.
* The order **doesn't matter**.

Example:

```text
listen

silent
```

Characters

```text
listen

↓

l i s t e n

silent

↓

s i l e n t
```

Both contain exactly the same letters.

So

```text
Anagram = true
```

---

## Another example

```text
cat

act
```

Same letters

```text
a
c
t
```

Result

```text
true
```

---

## Not an anagram

```text
cat

car
```

Because

```text
cat

↓

a
c
t

car

↓

a
c
r
```

Different characters.

Result

```text
false
```

---

# The trick used in this solution

Instead of counting characters,

the solution **sorts both strings**.

Example

```text
listen

↓

eilnst
```

Now

```text
silent

↓

eilnst
```

Both become identical.

Then Java simply checks

```java
equals()
```

That's the entire idea.

---

# Complete Code

```java
s1 = Stream.of(s1.split(""))
           .map(val -> val.toLowerCase())
           .sorted()
           .collect(Collectors.joining());
```

Let's understand every step.

---

# Step 1

```java
s1.split("")
```

Suppose

```java
s1 = "saurav";
```

Result

```text
["s","a","u","r","a","v"]
```

An array of strings.

---

# Step 2

```java
Stream.of(...)
```

Converts the array into a stream.

```text
s

↓

a

↓

u

↓

r

↓

a

↓

v
```

---

# Step 3

```java
.map(val -> val.toLowerCase())
```

Why?

Imagine

```text
Listen

silent
```

Without converting to lowercase

```text
L

≠

l
```

Java considers them different.

So we convert everything to lowercase.

Example

```text
S

↓

s

A

↓

a
```

Now comparisons become case-insensitive.

---

# Step 4

```java
.sorted()
```

This sorts alphabetically.

Current stream

```text
s

a

u

r

a

v
```

After sorting

```text
a

a

r

s

u

v
```

Notice

Duplicates are preserved.

We're only changing the order.

---

# Step 5

```java
.collect(Collectors.joining())
```

Until now we have

```text
a

a

r

s

u

v
```

Joining combines them into one string.

```text
"aarsuv"
```

Now

```java
s1
```

becomes

```text
"aarsuv"
```

The same process happens for

```java
s2
```

Suppose

```java
s2 = "vauras";
```

Split

```text
v

a

u

r

a

s
```

Sort

```text
a

a

r

s

u

v
```

Join

```text
"aarsuv"
```

---

# Final Step

Now compare

```java
return s1.equals(s2);
```

Which becomes

```java
"aarsuv".equals("aarsuv")
```

Result

```text
true
```

---

# Complete Flow

Suppose

```text
saurav

vauras
```

↓

Split

```text
["s","a","u","r","a","v"]

["v","a","u","r","a","s"]
```

↓

Lowercase

(No change)

↓

Sort

```text
a
a
r
s
u
v

a
a
r
s
u
v
```

↓

Join

```text
"aarsuv"

"aarsuv"
```

↓

Equals

```text
true
```

---

# Why `Collectors.joining()`?

Until now we have a stream:

```text
a

a

r

s

u

v
```

We need one string.

`joining()` concatenates all elements.

```java
.collect(Collectors.joining())
```

Result

```text
"aarsuv"
```

Without `joining()`, you'd still have a stream.

---

# One improvement

Before doing any work, check the lengths:

```java
if (s1.length() != s2.length()) {
    return false;
}
```

Why?

Example

```text
abc

abcd
```

Can they ever be anagrams?

No.

So we can return immediately.

This avoids unnecessary processing.

---

# Why does sorting work?

This is the key idea.

Take

```text
triangle

integral
```

Sort both:

```text
triangle

↓

aegilnrt

integral

↓

aegilnrt
```

Since both sorted strings are identical, they must contain exactly the same characters with the same frequencies.

---

# Time Complexity

Your solution:

```text
Split

↓

Sort

↓

Compare
```

Sorting dominates the cost.

```
O(n log n)
```

There is another approach using a frequency map (`groupingBy(..., counting())` or an `int[26]` for lowercase English letters), which can run in **O(n)** time because it counts characters instead of sorting.

For interviews, **both approaches are accepted**. The sorting approach is often easier to explain, while the frequency-count approach is more efficient.

---

## One small Stream improvement

Instead of:

```java
.map(val -> val.toLowerCase())
```

you'll often see the method reference:

```java
.map(String::toLowerCase)
```

These are equivalent:

```java
.map(val -> val.toLowerCase())
```

```java
.map(String::toLowerCase)
```

The second version is simply a shorter, more idiomatic way to write the same operation.

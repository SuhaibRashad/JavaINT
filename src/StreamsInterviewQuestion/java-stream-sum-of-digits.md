Excellent! This question teaches you another important Stream concept:

* `mapToInt()`
* Method Reference (`Integer::parseInt`)
* `sum()`

This is actually one of the simplest Stream pipelines.

---

# Suggested Notes File

`java-stream-sum-of-digits.md`

---

# Problem Statement

Given

```text
6786567
```

We want

```text
6 + 7 + 8 + 6 + 5 + 6 + 7

↓

45
```

---

# Complete Code

```java
public static int sum(int n){
    return Arrays.stream(String.valueOf(n).split(""))
                 .mapToInt(Integer::parseInt)
                 .sum();
}
```

Let's execute it step by step.

---

# Step 1

```java
String.valueOf(n)
```

Suppose

```java
int n = 6786567;
```

Java converts the integer into a string.

```text
6786567

↓

"6786567"
```

Why?

Because Streams cannot directly process the individual digits of an integer. We first convert it into a string so we can separate the digits.

---

# Step 2

```java
.split("")
```

Now Java splits the string.

```text
"6786567"

↓

["6","7","8","6","5","6","7"]
```

Notice these are **Strings**, not integers.

---

# Step 3

```java
Arrays.stream(...)
```

Converts the array into a stream.

```text
["6","7","8","6","5","6","7"]

↓

Stream<String>

6 → 7 → 8 → 6 → 5 → 6 → 7
```

---

# Step 4

```java
.mapToInt(Integer::parseInt)
```

This is the important line.

Each element is currently a String.

```text
"6"

"7"

"8"
```

We want integers.

So Java does

```java
Integer.parseInt("6")
```

Result

```text
6
```

Next

```java
Integer.parseInt("7")
```

Result

```text
7
```

After all elements

```text
6

7

8

6

5

6

7
```

Now the stream becomes an `IntStream`.

---

# What does `Integer::parseInt` mean?

This is called a **Method Reference**.

It is simply a shorter way of writing a lambda.

These two are exactly the same:

```java
.mapToInt(Integer::parseInt)
```

```java
.mapToInt(s -> Integer.parseInt(s))
```

Java sees each string and calls:

```java
Integer.parseInt(element)
```

For example:

```text
"6"

↓

Integer.parseInt("6")

↓

6
```

---

# Why `mapToInt()` and not `map()`?

Suppose we use

```java
.map(Integer::parseInt)
```

Then we'd get:

```text
Stream<Integer>
```

But `sum()` is available on an **IntStream**, not on a regular `Stream<Integer>`.

So:

```java
.mapToInt(Integer::parseInt)
```

creates an `IntStream`, allowing us to call:

```java
.sum()
```

directly.

---

# Step 5

```java
.sum()
```

Now Java has

```text
6

7

8

6

5

6

7
```

It simply adds them:

```text
6 + 7 = 13

13 + 8 = 21

21 + 6 = 27

27 + 5 = 32

32 + 6 = 38

38 + 7 = 45
```

Return

```text
45
```

---

# Complete Flow

```text
6786567

↓

String.valueOf()

↓

"6786567"

↓

split("")

↓

["6","7","8","6","5","6","7"]

↓

Arrays.stream()

↓

Stream<String>

↓

mapToInt(Integer::parseInt)

↓

IntStream

6
7
8
6
5
6
7

↓

sum()

↓

45
```

---

# Why `mapToInt()` instead of `map()`?

This is one of the most common beginner questions.

| `map()`             | `mapToInt()`                               |
| ------------------- | ------------------------------------------ |
| Returns `Stream<T>` | Returns `IntStream`                        |
| Used for objects    | Used for primitive `int` values            |
| No `sum()` method   | Has `sum()`, `average()`, `min()`, `max()` |

Example:

```java
Stream<String>
      ↓
.map(Integer::parseInt)
      ↓
Stream<Integer>
```

vs.

```java
Stream<String>
      ↓
.mapToInt(Integer::parseInt)
      ↓
IntStream
```

Since we need the sum, `IntStream` is the right choice.

---

# Can we solve it without converting to a String?

Yes.

Using mathematics:

```java
int sum = 0;

while (n > 0) {
    sum += n % 10;
    n /= 10;
}
```

This avoids creating strings and streams and is generally more efficient.

The Stream solution is mainly used to practice Stream operations and demonstrate functional programming.

---

## Interview Tip

Whenever you see:

```java
.mapToInt(...)
.sum()
```

think of it as two steps:

1. **Convert each element into an `int`.**
2. **Perform a numeric operation on the resulting `IntStream`.**

Other common numeric terminal operations are:

```java
.max()
.min()
.average()
.count()
.sum()
```

You'll encounter this pattern frequently in Java 8 Stream interview questions.

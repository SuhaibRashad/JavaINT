Excellent! This question combines several Stream operations you've already learned. The only new concept is **`IntStream.concat()`**.

The complete pipeline is:

```java
IntStream.concat(...)
         .distinct()
         .sorted()
         .toArray();
```

Each operation does one simple job.

---

# Suggested Notes File

`java-stream-merge-two-arrays.md`

---

# Problem Statement

Given

```text
Array 1

[6,0,4,5]

Array 2

[1,0,9,3]
```

Merge them into

```text
[0,1,3,4,5,6,9]
```

Requirements:

* Merge both arrays
* Remove duplicates
* Sort in ascending order

---

# Complete Code

```java
public static int[] merge(int[] arr1, int[] arr2){

    int[] merged =
            IntStream.concat(Arrays.stream(arr1),
                             Arrays.stream(arr2))
                     .distinct()
                     .sorted()
                     .toArray();

    return merged;
}
```

Let's understand every part.

---

# Step 1

```java
Arrays.stream(arr1)
```

Suppose

```java
arr1 = {6,0,4,5};
```

This creates an **IntStream**.

```
6

↓

0

↓

4

↓

5
```

Similarly

```java
Arrays.stream(arr2)
```

becomes

```
1

↓

0

↓

9

↓

3
```

Notice that because the input is an `int[]`, `Arrays.stream()` returns an **IntStream**, not a `Stream<Integer>`.

---

# Step 2

```java
IntStream.concat(stream1, stream2)
```

This is the only new concept.

## What does concat mean?

Concat simply means

> Join two streams one after another.

Imagine

First stream

```
6

0

4

5
```

Second stream

```
1

0

9

3
```

After

```java
IntStream.concat(...)
```

we get

```
6

0

4

5

1

0

9

3
```

Nothing is removed.

Nothing is sorted.

They are simply connected.

---

# Visual

```
Stream 1

6 → 0 → 4 → 5

+

Stream 2

1 → 0 → 9 → 3

↓

One Stream

6 → 0 → 4 → 5 → 1 → 0 → 9 → 3
```

---

# Step 3

```java
.distinct()
```

You've already learned this.

Current stream

```
6

0

4

5

1

0

9

3
```

Java keeps a hidden `HashSet`.

Let's execute it.

First

```
6

↓

Not seen

↓

Keep
```

Second

```
0

↓

Not seen

↓

Keep
```

Third

```
4

↓

Keep
```

Fourth

```
5

↓

Keep
```

Fifth

```
1

↓

Keep
```

Sixth

```
0

↓

Already exists

↓

Remove
```

Continue...

Final stream

```
6

0

4

5

1

9

3
```

---

# Step 4

```java
.sorted()
```

Now Java sorts the remaining numbers.

Current

```
6

0

4

5

1

9

3
```

After sorting

```
0

1

3

4

5

6

9
```

---

# Step 5

```java
.toArray()
```

Until now we have

```
IntStream
```

We want

```java
int[]
```

So Java converts the stream into an array.

Final result

```java
{0,1,3,4,5,6,9}
```

---

# Complete Flow

```
Array 1

6 0 4 5

+

Array 2

1 0 9 3

↓

Arrays.stream()

↓

Two IntStreams

↓

concat()

↓

6 0 4 5 1 0 9 3

↓

distinct()

↓

6 0 4 5 1 9 3

↓

sorted()

↓

0 1 3 4 5 6 9

↓

toArray()

↓

int[]
```

---

# Why use `IntStream.concat()`?

Because we are joining **two streams**, not two arrays.

Notice this carefully.

We are **not** writing

```java
IntStream.concat(arr1, arr2)
```

because `concat()` expects streams.

So first we convert:

```java
Arrays.stream(arr1)
```

↓

```
IntStream
```

and

```java
Arrays.stream(arr2)
```

↓

```
IntStream
```

Only then can we concatenate them.

---

# Why `toArray()`?

Until now, the result is still an `IntStream`.

```
0

↓

1

↓

3

↓

4
```

But the method signature says:

```java
public static int[] merge(...)
```

So we need an array.

That's why we call

```java
.toArray()
```

which converts the `IntStream` into an `int[]`.

---

# Can we merge without Streams?

Yes.

Using loops:

```java
int[] merged = new int[arr1.length + arr2.length];

// Copy arr1
// Copy arr2
// Remove duplicates
// Sort
```

This requires more code.

Streams let us express the same logic in a concise pipeline.

---

# Why is the order `distinct().sorted()`?

You could also write:

```java
.sorted()
.distinct()
```

and for this problem, you'd still get the same final result.

However,

```java
.distinct()
.sorted()
```

is usually a little more efficient because Java sorts fewer elements after duplicates have already been removed.

---

# Interview Tip

This question is really testing whether you know how to combine multiple Stream operations.

Think of the pipeline like this:

| Operation            | Purpose                      |
| -------------------- | ---------------------------- |
| `Arrays.stream()`    | Convert array to stream      |
| `IntStream.concat()` | Merge two streams            |
| `distinct()`         | Remove duplicates            |
| `sorted()`           | Sort elements                |
| `toArray()`          | Convert stream back to array |

A good way to explain it in an interview is:

> "First I convert both arrays into `IntStream`s. Then I concatenate them into one stream, remove duplicates using `distinct()`, sort the unique elements with `sorted()`, and finally convert the stream back into an `int[]` using `toArray()`."

That explanation demonstrates not only that you know the API, but also that you understand the purpose of each operation in the pipeline.

Excellent! This question introduces two terminal operations:

* `min()`
* `max()`

You'll notice they work almost exactly like `findFirst()` because they also return an **`Optional`**.

---

# Suggested Notes File

`java-stream-min-max.md`

---

# Problem Statement

Given

```text
[89,212,11,10,21,12,34,90]
```

Find

```text
Minimum = 10

Maximum = 212
```

---

# Complete Code

```java
Optional<Integer> minVal =
        list.stream().min(Comparator.naturalOrder());

Optional<Integer> maxVal =
        list.stream().max(Comparator.naturalOrder());
```

Let's understand every line.

---

# Step 1

```java
list.stream()
```

Creates the stream.

```
89 → 212 → 11 → 10 → 21 → 12 → 34 → 90
```

Nothing happens yet.

---

# Step 2

```java
.min(Comparator.naturalOrder())
```

This means

> Find the smallest element using natural ordering.

---

## What is Natural Order?

Natural order means

```
10

↓

11

↓

12

↓

21

↓

34

↓

89

↓

90

↓

212
```

Exactly how numbers are normally sorted.

`Comparator.naturalOrder()` simply tells Java:

> Compare numbers in ascending order.

---

# How does Java actually find the minimum?

Suppose the list is

```
89

212

11

10

21
```

Java keeps one variable internally.

Let's call it

```
currentMinimum
```

---

### First element

```
89
```

No minimum yet.

```
currentMinimum = 89
```

---

### Next

```
212
```

Compare

```
212 < 89 ?

No
```

Keep

```
currentMinimum = 89
```

---

### Next

```
11
```

Compare

```
11 < 89 ?

Yes
```

Update

```
currentMinimum = 11
```

---

### Next

```
10
```

Compare

```
10 < 11 ?

Yes
```

Update

```
currentMinimum = 10
```

---

### Next

```
21
```

Compare

```
21 < 10 ?

No
```

Keep

```
currentMinimum = 10
```

End of stream.

Return

```
10
```

---

# Visual

```
89

↓

Current Min = 89

↓

212

↓

Still 89

↓

11

↓

Current Min = 11

↓

10

↓

Current Min = 10

↓

21

↓

Still 10
```

---

# Step 3

```java
.max(Comparator.naturalOrder())
```

Exactly the opposite.

Java now keeps

```
currentMaximum
```

---

### First

```
89
```

```
currentMaximum = 89
```

---

### Next

```
212
```

```
212 > 89

↓

Update

212
```

---

### Next

```
11
```

```
11 > 212 ?

No
```

---

Continue...

Final

```
212
```

---

# Why does `min()` return Optional?

This is exactly like `findFirst()`.

Suppose

```java
List<Integer> list = new ArrayList<>();
```

Now

```java
list.stream().min(...)
```

What is the minimum?

There isn't one.

Instead of returning `null`, Java returns

```java
Optional<Integer>
```

which is either:

```
Optional[10]
```

or

```
Optional.empty()
```

---

# Why `.get()`?

Suppose

```java
Optional<Integer> minVal =
        Optional.of(10);
```

Inside

```
Optional

↓

10
```

Calling

```java
minVal.get();
```

extracts

```
10
```

---

# Complete Flow

```
List

↓

89
212
11
10
21
12
34
90

↓

stream()

↓

min()

↓

Optional(10)

↓

get()

↓

10
```

---

# Can we write this more simply?

Yes.

Since the stream contains **integers**, we can use an `IntStream`.

```java
int min = list.stream()
              .mapToInt(Integer::intValue)
              .min()
              .getAsInt();
```

Similarly

```java
int max = list.stream()
              .mapToInt(Integer::intValue)
              .max()
              .getAsInt();
```

Notice something new?

Instead of

```java
Optional<Integer>
```

you get

```java
OptionalInt
```

and instead of

```java
.get()
```

you call

```java
.getAsInt()
```

because `IntStream` works with primitive `int` values.

---

# Difference between `sorted().findFirst()` and `min()`

Many beginners think these are equivalent.

```java
list.stream()
    .sorted()
    .findFirst();
```

and

```java
list.stream()
    .min(Comparator.naturalOrder());
```

Both return the smallest value.

But **they do not work the same way**.

### `sorted().findFirst()`

Java first sorts the **entire list**:

```
89
212
11
10
21

↓

10
11
21
89
212
```

Then returns the first element.

Sorting takes more work.

---

### `min()`

Java **never sorts**.

It simply scans the list once:

```
89

↓

11

↓

10

↓

Done
```

Much more efficient.

That's why `min()` (and `max()`) is preferred when you only need the smallest or largest element.

---

# Interview Tip

These terminal operations all return an `Optional` (or a primitive variant like `OptionalInt`) because there may not be a result:

| Method        | Returns              |
| ------------- | -------------------- |
| `findFirst()` | First element        |
| `findAny()`   | Any matching element |
| `min()`       | Smallest element     |
| `max()`       | Largest element      |

All of them can return an empty `Optional` if the stream has no elements.

---

## One improvement to your code

Instead of:

```java
System.out.println(minVal.get());
System.out.println(maxVal.get());
```

a safer approach is:

```java
minVal.ifPresent(System.out::println);
maxVal.ifPresent(System.out::println);
```

or

```java
System.out.println(minVal.orElse(null));
System.out.println(maxVal.orElse(null));
```

This avoids calling `get()` on an empty `Optional`, which would throw a `NoSuchElementException`.

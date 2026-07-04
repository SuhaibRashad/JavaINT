Excellent! This question is actually a **combination of two questions you've already learned**:

1. **Count frequency** using `groupingBy(..., counting())`.
2. **Filter only duplicates** using `filter(entry -> entry.getValue() > 1)`.

So there is almost nothing new except understanding how we chain multiple stream operations.

---

# Suggested Notes File

`java-stream-duplicate-elements-with-count.md`

---

# Problem Statement

Given

```text
["Rahul", "Rohan", "Rahul", "Keshav", "Rohan"]
```

Expected Output

```text
{
 Rahul=2,
 Rohan=2
}
```

Notice

`Keshav` appears only once.

So it is **not** included.

---

# Complete Code

```java
public static Map<String, Long> findDuplicatesCount(List<String> names) {

    Map<String, Long> collect = names.stream()

            .collect(Collectors.groupingBy(
                    Function.identity(),
                    Collectors.counting()))

            .entrySet()

            .stream()

            .filter(entry -> entry.getValue() > 1)

            .collect(Collectors.toMap(
                    entry -> entry.getKey(),
                    entry -> entry.getValue()));

    return collect;
}
```

We'll understand it one step at a time.

---

# Step 1

```java
names.stream()
```

Creates the stream.

```
Rahul

↓

Rohan

↓

Rahul

↓

Keshav

↓

Rohan
```

---

# Step 2

```java
.collect(Collectors.groupingBy(
        Function.identity(),
        Collectors.counting()))
```

You already learned this in the **character frequency** question.

Java creates a frequency map.

Let's execute it manually.

---

### Rahul

Current Map

```
{}
```

Add Rahul

```
{
 Rahul=1
}
```

---

### Rohan

```
{
 Rahul=1,
 Rohan=1
}
```

---

### Rahul again

Already exists.

Increment count.

```
{
 Rahul=2,
 Rohan=1
}
```

---

### Keshav

```
{
 Rahul=2,
 Rohan=1,
 Keshav=1
}
```

---

### Rohan

Increment.

```
{
 Rahul=2,
 Rohan=2,
 Keshav=1
}
```

Done.

---

So after this line we have

```java
Map<String,Long>
```

containing

```text
{
 Rahul=2,
 Rohan=2,
 Keshav=1
}
```

---

# Step 3

```java
.entrySet()
```

You asked about this before 😊

A `Map` is converted into a **Set of Map.Entry** objects.

Before

```
Map

↓

Rahul -> 2

Rohan -> 2

Keshav -> 1
```

After

```
Entry

↓

(Rahul,2)

↓

(Rohan,2)

↓

(Keshav,1)
```

Each entry has

```java
entry.getKey()
```

and

```java
entry.getValue()
```

---

# Step 4

```java
.stream()
```

Why?

Because

`entrySet()` returns a **Set**, not a Stream.

To use `filter()`, we must convert the Set into a Stream.

So now we have

```
(Rahul,2)

↓

(Rohan,2)

↓

(Keshav,1)
```

---

# Step 5

```java
.filter(entry -> entry.getValue() > 1)
```

This is the new logic.

Remember

```java
entry.getValue()
```

means

```
Count
```

Let's execute it.

---

First Entry

```
Rahul=2
```

Check

```java
2 > 1
```

↓

```
true
```

Keep it.

---

Second

```
Rohan=2
```

Check

```java
2 > 1
```

↓

```
true
```

Keep.

---

Third

```
Keshav=1
```

Check

```java
1 > 1
```

↓

```
false
```

Remove.

---

Remaining stream

```
Rahul=2

↓

Rohan=2
```

---

# Step 6

Now we have a stream of entries.

We need a Map again.

So we write

```java
.collect(Collectors.toMap(
        entry -> entry.getKey(),
        entry -> entry.getValue()))
```

This means

For every entry

Take

```
Key

↓

Rahul
```

Take

```
Value

↓

2
```

Create

```
Rahul -> 2
```

Repeat

```
Rohan -> 2
```

Final Map

```text
{
 Rahul=2,
 Rohan=2
}
```

Done.

---

# Complete Flow

```
List

↓

Rahul
Rohan
Rahul
Keshav
Rohan

↓

groupingBy()

↓

Map

Rahul=2
Rohan=2
Keshav=1

↓

entrySet()

↓

Entries

(Rahul,2)
(Rohan,2)
(Keshav,1)

↓

filter()

↓

(Rahul,2)
(Rohan,2)

↓

toMap()

↓

Map

Rahul=2
Rohan=2
```

---

# Why can't we filter directly after `groupingBy()`?

This is a very common beginner question.

After

```java
.collect(groupingBy(...))
```

the stream has ended.

The result is now a **Map**.

You cannot write

```java
.collect(groupingBy(...))
.filter(...)
```

because a `Map` doesn't have a `filter()` method.

Instead:

```java
Map
```

↓

```java
entrySet()
```

↓

```java
Set<Map.Entry>
```

↓

```java
stream()
```

↓

```java
filter()
```

---

# Simplifying the last `toMap()`

You wrote:

```java
Collectors.toMap(
        entry -> entry.getKey(),
        entry -> entry.getValue())
```

A cleaner version uses method references:

```java
Collectors.toMap(
        Map.Entry::getKey,
        Map.Entry::getValue)
```

Both are exactly the same.

---

# Interview Tip

This question demonstrates a very common pattern in Java Streams:

```
Collection

↓

groupingBy()

↓

Map

↓

entrySet()

↓

stream()

↓

filter()

↓

collect()
```

You'll use this whenever you're asked to:

* Find duplicate elements.
* Find elements occurring more than *N* times.
* Find the most frequent element.
* Filter based on counts after grouping.

The key idea to remember is:

> **`groupingBy()` transforms a stream into a `Map`. If you want to process individual key-value pairs, convert the map to an `entrySet()`, stream over those entries, apply operations like `filter()`, and collect the result into the form you need.**

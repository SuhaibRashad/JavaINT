This is a good question because it teaches two new Stream operations:

* `skip()`
* `findFirst()`

It also shows one of the **limitations of Streams**: Streams are designed to process data from **start to end**, not backwards.

---

# Suggested Notes File

`java-stream-find-last-element.md`

---

# First, understand the problem

Given

```text
[One, Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten]
```

We want

```text
Ten
```

---

# Complete Code

```java
public static String lastElement(List<String> list){

    String result = list.stream()
                        .skip(list.size() - 1)
                        .findFirst()
                        .get();

    return result;
}
```

Let's execute it step by step.

---

# Step 1

```java
list.stream()
```

Creates a stream.

```
List

↓

One
Two
Three
Four
Five
Six
Seven
Eight
Nine
Ten

↓

Stream

One → Two → Three → Four → Five → Six → Seven → Eight → Nine → Ten
```

Nothing happens yet.

---

# Step 2

```java
list.size()
```

How many elements are there?

```
10
```

---

# Step 3

```java
list.size() - 1
```

```
10 - 1

↓

9
```

So Java executes

```java
.skip(9)
```

---

# What does skip() do?

This is the important part.

`skip(n)` means

> **Ignore the first n elements.**

Imagine

```
One
Two
Three
Four
Five
Six
Seven
Eight
Nine
Ten
```

Now

```java
.skip(9)
```

means

Ignore

```
❌ One

❌ Two

❌ Three

❌ Four

❌ Five

❌ Six

❌ Seven

❌ Eight

❌ Nine
```

What's left?

```
Ten
```

The stream now contains only

```
Ten
```

---

# Visual

Original Stream

```
One
Two
Three
Four
Five
Six
Seven
Eight
Nine
Ten
```

After

```java
.skip(9)
```

```
Ten
```

That's all.

---

# Step 4

Now we have

```
Stream

↓

Ten
```

Next comes

```java
.findFirst()
```

Question:

What's the first element?

```
Ten
```

So Java returns

```java
Optional<String>
```

containing

```
Ten
```

Notice

Not

```java
String
```

Instead

```java
Optional<String>
```

because Java cannot guarantee that a stream always has an element.

---

# Step 5

Then

```java
.get()
```

means

> "Give me the value inside the Optional."

So

```
Optional

↓

Ten

↓

get()

↓

Ten
```

---

# Complete Flow

```
Original List

↓

One
Two
Three
Four
Five
Six
Seven
Eight
Nine
Ten

↓

stream()

↓

skip(9)

↓

Ten

↓

findFirst()

↓

Optional(Ten)

↓

get()

↓

Ten
```

---

# Why use skip()?

Remember,

Streams move **forward only**.

There is no method like

```java
stream.last()
```

or

```java
stream.previous()
```

Java cannot jump directly to the end of the stream.

So we say:

```
Skip everything

↓

Keep only the last

↓

Take the first remaining element
```

That's the trick.

---

# What exactly does findFirst() return?

Many beginners think

```java
.findFirst()
```

returns

```java
String
```

It doesn't.

It returns

```java
Optional<String>
```

Why?

Suppose

```java
List<String> list = new ArrayList<>();
```

Then

```java
list.stream().findFirst();
```

What should Java return?

There is no first element.

Instead of returning `null`, Java returns an **empty Optional**.

```
Optional.empty()
```

This helps avoid `NullPointerException`.

---

# Why is get() dangerous?

Suppose

```java
List<String> list = new ArrayList<>();
```

Then

```java
list.stream()
    .skip(list.size() - 1)
```

becomes

```java
.skip(-1)
```

Actually, `skip(-1)` throws an `IllegalArgumentException` because `skip()` requires a non-negative number. Even if `skip()` weren't an issue, calling `.get()` on an empty `Optional` would throw a `NoSuchElementException`.

A safer version is:

```java
public static Optional<String> lastElement(List<String> list) {
    return list.stream()
               .skip(Math.max(0, list.size() - 1))
               .findFirst();
}
```

Or, if you want a `String`:

```java
return list.stream()
           .skip(Math.max(0, list.size() - 1))
           .findFirst()
           .orElse("List is empty");
```

---

# Is this the best way?

For interview practice, yes—it demonstrates `skip()` and `findFirst()`.

However, if you already have a `List`, the simplest and fastest approach is:

```java
String last = list.get(list.size() - 1);
```

Why?

Because a `List` supports random access by index.

```
List

↓

Directly access index 9

↓

Ten
```

No stream is needed.

---

# Interview Tip

There are three methods that are often confused:

| Method        | Meaning                                             |
| ------------- | --------------------------------------------------- |
| `skip(n)`     | Ignore the first `n` elements                       |
| `limit(n)`    | Keep only the first `n` elements                    |
| `findFirst()` | Return the first remaining element as an `Optional` |

Example:

```java
list.stream()
    .skip(3)
    .limit(2)
```

Given:

```
A B C D E F G
```

After `skip(3)`:

```
D E F G
```

After `limit(2)`:

```
D E
```

Understanding how each operation transforms the stream step by step is the key to reading Stream pipelines.

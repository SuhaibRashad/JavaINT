Excellent! This is one of the best beginner Stream questions because it introduces a very important idea:

> **Streams don't modify data. They transform each element and produce a new stream.**

This question also introduces:

* ✅ `Stream.of()`
* ✅ `map()`
* ✅ `StringBuffer.reverse()`
* ✅ `Collectors.joining(" ")`

Let's understand it from the beginning.

---

# Suggested Notes File

`java-stream-reverse-each-word.md`

---

# Problem Statement

Given

```text
java is a awesome programming language
```

Expected Output

```text
avaj si a emosewa gnimmargorp egaugnal
```

Notice carefully:

We are **not** reversing the entire sentence.

We are reversing **each individual word**.

---

Original

```text
java is a awesome
```

becomes

```text
avaj si a emosewa
```

---

# Complete Code

```java
public static String reverse(String str){

    String reversed =
            Stream.of(str.split(" "))
                  .map(word -> new StringBuffer(word).reverse())
                  .collect(Collectors.joining(" "));

    return reversed;
}
```

Let's understand every line.

---

# Step 1

```java
str.split(" ")
```

Suppose

```java
String str = "java is awesome";
```

After

```java
split(" ")
```

we get

```text
["java","is","awesome"]
```

Notice

This is an **array of Strings**.

---

# Step 2

```java
Stream.of(...)
```

Convert the array into a stream.

```text
java

↓

is

↓

awesome
```

Think of it as

```text
Stream<String>
```

---

# Step 3

Now comes the important line.

```java
.map(word -> new StringBuffer(word).reverse())
```

Let's break it into smaller parts.

---

## What does map() do?

Remember

`map()` means

> Transform every element into something else.

Current Stream

```text
java

↓

is

↓

awesome
```

Now Java processes **one word at a time**.

---

### First word

```text
java
```

Java executes

```java
new StringBuffer("java")
```

Creates

```text
StringBuffer

↓

java
```

---

Then

```java
.reverse()
```

becomes

```text
avaj
```

First transformed word

```text
avaj
```

---

### Second word

```text
is
```

Create

```java
new StringBuffer("is")
```

Reverse

```text
si
```

---

### Third

```text
awesome
```

Reverse

```text
emosewa
```

---

Now the stream becomes

```text
avaj

↓

si

↓

emosewa
```

Notice

The original stream

```text
java

↓

is

↓

awesome
```

has been transformed into

```text
avaj

↓

si

↓

emosewa
```

That's exactly what `map()` does.

---

# Why use StringBuffer?

Because

Strings are **immutable**.

You cannot do

```java
"java".reverse()
```

There is no such method.

Instead

Java provides

```java
StringBuffer
```

and

```java
StringBuilder
```

Both have

```java
reverse()
```

method.

Example

```java
new StringBuffer("java").reverse()
```

Result

```text
avaj
```

---

# What does reverse() return?

It returns the same `StringBuffer`.

Example

```java
StringBuffer sb = new StringBuffer("java");

System.out.println(sb.reverse());
```

Output

```text
avaj
```

Since `Collectors.joining()` accepts any `CharSequence`, a `StringBuffer` works directly.

---

# Step 4

Now we have

```text
avaj

↓

si

↓

a

↓

emosewa

↓

gnimmargorp

↓

egaugnal
```

Next

```java
.collect(Collectors.joining(" "))
```

What does joining do?

It joins all elements together.

The `" "` means

Insert a space between every element.

Without separator

```java
Collectors.joining()
```

Result

```text
avajsiemosewa
```

Everything sticks together.

With

```java
Collectors.joining(" ")
```

Result

```text
avaj si emosewa
```

Spaces are inserted.

---

# Complete Flow

Input

```text
java is awesome
```

↓

Split

```text
["java","is","awesome"]
```

↓

Stream

```text
java

↓

is

↓

awesome
```

↓

Map

```text
avaj

↓

si

↓

emosewa
```

↓

Joining

```text
avaj si emosewa
```

Done.

---

# Why use map()?

Remember this simple rule:

| Stream Method | Purpose                 |
| ------------- | ----------------------- |
| `filter()`    | Keep some elements      |
| `map()`       | Transform every element |
| `sorted()`    | Rearrange elements      |
| `distinct()`  | Remove duplicates       |

Here

Input

```text
java
```

Output

```text
avaj
```

One word becomes another word.

That's transformation.

Hence

```java
map()
```

---

# Why `Collectors.joining(" ")`?

Suppose after mapping we have

```text
avaj

si

a

emosewa
```

This is still a stream.

We need one String.

`joining(" ")` produces

```text
avaj si a emosewa
```

---

# Can we use StringBuilder instead?

Yes.

In fact, most modern Java code prefers `StringBuilder`.

```java
.map(word -> new StringBuilder(word).reverse())
```

Why?

Because

* `StringBuffer` is **thread-safe** (methods are synchronized).
* `StringBuilder` is **not thread-safe**, making it faster in single-threaded code.

Unless you specifically need thread safety, `StringBuilder` is usually the better choice.

---

# Interview Tip

A very common confusion is between these two problems:

### Reverse each word

Input

```text
Java is awesome
```

Output

```text
avaJ si emosewa
```

Each word is reversed individually, but the **order of words stays the same**.

---

### Reverse the order of words

Input

```text
Java is awesome
```

Output

```text
awesome is Java
```

Here, the words themselves are unchanged, but their **positions are reversed**.

These are different interview questions, so always clarify which one is being asked.

---

## One small improvement

Your lambda:

```java
.map(word -> new StringBuffer(word).reverse())
```

returns a `StringBuffer`.

Although `Collectors.joining()` accepts it because `StringBuffer` implements `CharSequence`, many developers prefer to make the conversion explicit:

```java
.map(word -> new StringBuilder(word).reverse().toString())
```

Now the stream remains a `Stream<String>`, which makes the code's intent a little clearer to anyone reading it.

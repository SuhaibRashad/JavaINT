This is a very good beginner Stream question because it introduces **`filter()`**, which is probably the **most used Stream operation** in real-world Java.

If you understand `filter()`, you've already learned about 40% of day-to-day Stream usage.

---

# Suggested Notes File

`java-stream-filter-starts-with-digit.md`

---

# Problem Statement

Given

```text
["One", "2wo", "3hree", "Four", "5ive", "6ix"]
```

Return only those strings that start with a digit.

Output

```text
["2wo", "3hree", "5ive", "6ix"]
```

---

# Complete Code

```java
public static List<String> startsWith(List<String> list){

    List<String> result = list.stream()
                              .filter(str -> Character.isDigit(str.charAt(0)))
                              .collect(Collectors.toList());

    return result;
}
```

Let's understand every line.

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
2wo
3hree
Four
5ive
6ix

↓

Stream

One → 2wo → 3hree → Four → 5ive → 6ix
```

---

# Step 2

```java
.filter(...)
```

This is the important part.

## What does filter mean?

Think of a security guard at the entrance of a building.

```
People arrive

↓

Security Guard

↓

Allowed People
```

Only people satisfying a condition are allowed inside.

The same thing happens here.

```
Strings

↓

filter()

↓

Only matching strings
```

---

# Step 3

Now look at the condition.

```java
str -> Character.isDigit(str.charAt(0))
```

Let's break this into smaller pieces.

---

## First

```java
str.charAt(0)
```

Suppose

```java
str = "2wo";
```

Then

```java
str.charAt(0)
```

returns

```text
'2'
```

If

```java
str = "One";
```

then

```java
str.charAt(0)
```

returns

```text
'O'
```

Remember:

`charAt(index)`

returns the character at that position.

```
"Java"

Index

0 1 2 3

J a v a
```

So

```java
charAt(0)
```

returns

```
J
```

---

# Step 4

Now

```java
Character.isDigit(...)
```

checks whether that character is a digit.

Examples

```java
Character.isDigit('5')
```

returns

```text
true
```

---

```java
Character.isDigit('J')
```

returns

```text
false
```

---

```java
Character.isDigit('8')
```

returns

```text
true
```

---

# Let's execute it

### First element

```text
One
```

Lambda

```java
str.charAt(0)
```

returns

```text
'O'
```

Now

```java
Character.isDigit('O')
```

↓

```text
false
```

`filter()` removes it.

---

### Second element

```text
2wo
```

First character

```text
'2'
```

Check

```java
Character.isDigit('2')
```

↓

```text
true
```

Keep it.

---

### Third

```text
3hree
```

First character

```text
'3'
```

↓

```text
true
```

Keep it.

---

### Fourth

```text
Four
```

First character

```text
'F'
```

↓

```text
false
```

Remove it.

---

Continue...

Final stream

```text
2wo

3hree

5ive

6ix
```

---

# Step 5

```java
.collect(Collectors.toList())
```

Collects the remaining strings into a list.

Final output

```text
[2wo, 3hree, 5ive, 6ix]
```

---

# Complete Flow

```
Original List

↓

One
2wo
3hree
Four
5ive
6ix

↓

stream()

↓

filter()

↓

2wo
3hree
5ive
6ix

↓

collect()

↓

List

[2wo, 3hree, 5ive, 6ix]
```

---

# What exactly is filter()?

Think of it like this.

A lambda used inside `filter()` **must return a boolean**.

```
true

↓

Keep element
```

```
false

↓

Remove element
```

That's all `filter()` does.

---

Suppose

```java
.filter(str -> str.length() > 4)
```

```
Java

length = 4

↓

false

↓

Remove
```

---

```
Programming

length = 11

↓

true

↓

Keep
```

The rule is always the same.

---

# Why use `Character.isDigit()` instead of comparing characters?

You could write:

```java
str.charAt(0) >= '0' && str.charAt(0) <= '9'
```

This works too.

But Java already provides:

```java
Character.isDigit(ch)
```

It is:

* Easier to read
* Less error-prone
* Handles digit characters according to Unicode, not just `'0'` to `'9'`

So it's generally the preferred approach.

---

# One thing to be careful about

Your code assumes every string has at least one character.

If the list contains:

```java
""
```

then:

```java
str.charAt(0)
```

will throw a `StringIndexOutOfBoundsException`.

A safer version is:

```java
list.stream()
    .filter(str -> !str.isEmpty())
    .filter(str -> Character.isDigit(str.charAt(0)))
    .collect(Collectors.toList());
```

or combine both conditions:

```java
list.stream()
    .filter(str -> !str.isEmpty() && Character.isDigit(str.charAt(0)))
    .collect(Collectors.toList());
```

---

# Interview Tip

`filter()` is used whenever you want to **keep only the elements that satisfy a condition**.

Some common examples are:

```java
// Even numbers
.filter(n -> n % 2 == 0)

// Strings starting with "A"
.filter(s -> s.startsWith("A"))

// Adults only
.filter(person -> person.getAge() >= 18)

// Non-null values
.filter(Objects::nonNull)
```

A simple way to remember it is:

> **`filter()` asks one question for every element: "Should I keep you?"**
> If the answer is `true`, the element stays. If the answer is `false`, it is discarded.

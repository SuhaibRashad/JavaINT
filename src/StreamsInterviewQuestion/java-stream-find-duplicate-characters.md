Excellent question. This is one of my favorite Stream interview questions because it teaches a concept that initially looks like magic:

```java
.filter(ch -> !seen.add(ch))
```

Most beginners don't understand **why `add()` is inside `filter()`**.

Once you understand this one line, you'll be able to solve many duplicate-related problems.

---

# Suggested Notes File

`java-stream-find-duplicate-characters.md`

---

# Problem Statement

Given

```text
java
```

Find duplicate characters.

Output

```text
a
```

Another example

```text
programming
```

Output

```text
r
g
m
```

---

# Your Solution

```java
public static Set<Character> findDuplicatesChars(String str) {

    Set<Character> seen = new HashSet<>();

    return str.chars()
            .mapToObj(c -> (char)c)
            .filter(ch -> !seen.add(ch))
            .collect(Collectors.toSet());
}
```

Let's understand every line.

---

# Step 1

```java
Set<Character> seen = new HashSet<>();
```

First understand this Set.

Initially

```text
seen

↓

{}
```

Empty.

Its job is simply

> Remember every character we've already seen.

---

# Step 2

```java
str.chars()
```

Suppose

```java
String str = "java";
```

`chars()` produces

```text
106

97

118

97
```

These are ASCII/Unicode values.

---

# Step 3

```java
.mapToObj(c -> (char)c)
```

Convert every integer into a character.

Now stream becomes

```text
j

↓

a

↓

v

↓

a
```

Much easier to work with.

---

# Step 4

Now comes the important part.

```java
.filter(ch -> !seen.add(ch))
```

Let's first understand

```java
seen.add(ch)
```

---

## What does Set.add() return?

Most beginners think

```java
set.add(x)
```

returns nothing.

Actually it returns a boolean.

Example

```java
Set<String> set = new HashSet<>();

set.add("Java");
```

Returns

```text
true
```

because

```text
Java

didn't exist
```

Now

```java
set.add("Java");
```

again returns

```text
false
```

because

```text
Java

already exists
```

This is the entire trick.

---

# Let's execute it manually

Input

```text
java
```

Initially

```text
seen

↓

{}
```

---

## First character

```text
j
```

Java executes

```java
seen.add('j')
```

Since

```text
j

doesn't exist
```

Set becomes

```text
{j}
```

Return value

```text
true
```

Now filter has

```java
!true
```

↓

```text
false
```

Meaning

```text
Remove it
```

Why?

Because it is NOT a duplicate.

---

## Second character

```text
a
```

Current Set

```text
{j}
```

Java executes

```java
seen.add('a')
```

Set becomes

```text
{j,a}
```

Returns

```text
true
```

Filter

```java
!true
```

↓

```text
false
```

Remove it.

Still not a duplicate.

---

## Third

```text
v
```

Current Set

```text
{j,a}
```

Add

```java
seen.add('v')
```

Returns

```text
true
```

Filter

```java
!true
```

↓

```text
false
```

Remove.

---

## Fourth

```text
a
```

Current Set

```text
{j,a,v}
```

Now

```java
seen.add('a')
```

Since

```text
a

already exists
```

Nothing changes.

Set remains

```text
{j,a,v}
```

Return value

```text
false
```

Now filter becomes

```java
!false
```

↓

```text
true
```

Keep it.

Why?

Because this is a duplicate.

---

# Visual

Input

```text
j

a

v

a
```

Seen Set

```text
{}

↓

{j}

↓

{j,a}

↓

{j,a,v}

↓

{j,a,v}
```

Filter Results

```text
j

↓

false

Remove

---------------

a

↓

false

Remove

---------------

v

↓

false

Remove

---------------

a

↓

true

Keep
```

Final Stream

```text
a
```

---

# Step 5

```java
.collect(Collectors.toSet())
```

Collect duplicates into a Set.

Output

```text
[a]
```

---

# Complete Flow

```text
String

↓

java

↓

chars()

↓

106
97
118
97

↓

mapToObj()

↓

j
a
v
a

↓

filter()

↓

a

↓

collect()

↓

Set

[a]
```

---

# Why use a Set?

Because

A Set automatically tells us whether something already exists.

Example

```java
HashSet<String> set = new HashSet<>();
```

First insertion

```java
set.add("Java")
```

Returns

```text
true
```

Second insertion

```java
set.add("Java")
```

Returns

```text
false
```

This boolean return value is exactly what makes the trick work.

---

# Why `!seen.add(ch)`?

Let's make it crystal clear.

Suppose

```java
seen.add(ch)
```

returns

```text
true
```

Meaning

```text
First occurrence
```

We don't want first occurrences.

So

```java
!true

↓

false
```

Filter removes it.

---

Suppose

```java
seen.add(ch)
```

returns

```text
false
```

Meaning

```text
Already exists

Duplicate
```

Now

```java
!false

↓

true
```

Filter keeps it.

That is the whole trick.

---

# One important thing about `Collectors.toSet()`

Suppose your input is

```text
programming
```

The duplicate stream before collecting is:

```text
r
g
m
m
```

Notice that `'m'` appears twice as a duplicate (the 2nd and 3rd occurrences).

When you collect using:

```java
.collect(Collectors.toSet())
```

the result becomes:

```text
[r, g, m]
```

because a `Set` stores only unique values.

---

# What if I want every duplicate occurrence?

If your expected output is:

```text
[r, g, m, m]
```

then collect into a `List` instead:

```java
.collect(Collectors.toList())
```

This preserves every duplicate occurrence.

---

# Comparison with your first solution

Your commented solution:

```java
Arrays.stream(str.split(""))
```

creates a `Stream<String>`.

Your second solution:

```java
str.chars()
```

creates an `IntStream`, which you convert to `Character`.

For problems involving individual characters, the second approach is generally preferred because you're working with actual characters instead of creating many one-character `String` objects.

---

# Interview Tip

This pattern is extremely common:

```java
Set<T> seen = new HashSet<>();

stream.filter(x -> !seen.add(x))
```

Think of it as:

```text
Have I seen this before?

↓

No

↓

Ignore

----------------

Yes

↓

Keep

↓

Duplicate
```

Once you understand that `Set.add()` returns a boolean, this entire pattern becomes very intuitive.

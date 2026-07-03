This is one of the easiest Stream questions, but it teaches an important concept: **`distinct()`**.

Once you understand what `distinct()` does internally, you'll never forget it.

---

# Suggested Notes File

`java-stream-distinct-remove-duplicates.md`

---

# Problem Statement

Given

```text
[Java, Ruby, C#, Java, Kotlin, Ruby, C++, COBOL, C#]
```

We want

```text
[Java, Ruby, C#, Kotlin, C++, COBOL]
```

Notice

* First `"Java"` stays ✅
* Second `"Java"` is removed ❌
* First `"Ruby"` stays ✅
* Second `"Ruby"` is removed ❌

---

# Complete Code

```java
public static List<String> removeDuplicates(List<String> list){

    List<String> unique = list.stream()
                              .distinct()
                              .collect(Collectors.toList());

    return unique;
}
```

Let's understand every line.

---

# Step 1

```java
list.stream()
```

Creates a stream.

```text
List

↓

Java
Ruby
C#
Java
Kotlin
Ruby
C++
COBOL
C#

↓

Stream

Java → Ruby → C# → Java → Kotlin → Ruby → C++ → COBOL → C#
```

Nothing changes yet.

---

# Step 2

```java
.distinct()
```

This is the new operation.

Question:

What does **distinct** mean?

It simply means

> **Keep only unique elements.**

---

## Let's execute it manually

Our stream is

```text
Java
Ruby
C#
Java
Kotlin
Ruby
C++
COBOL
C#
```

Java starts reading one element at a time.

---

### First element

```text
Java
```

Have we seen `"Java"` before?

```text
No
```

Keep it.

Current result

```text
Java
```

---

### Second element

```text
Ruby
```

Seen before?

```text
No
```

Keep it.

```text
Java
Ruby
```

---

### Third

```text
C#
```

Seen before?

```text
No
```

Keep it.

```text
Java
Ruby
C#
```

---

### Fourth

```text
Java
```

Seen before?

```text
Yes
```

Ignore it.

Result stays

```text
Java
Ruby
C#
```

---

### Fifth

```text
Kotlin
```

Not seen.

Keep it.

```text
Java
Ruby
C#
Kotlin
```

---

Continue...

Final result

```text
Java
Ruby
C#
Kotlin
C++
COBOL
```

---

# How does Java know it's a duplicate?

This is the interesting part.

Internally, `distinct()` keeps a **Set**.

Think of it like this:

```text
Incoming Stream

↓

Java

↓

Set

{Java}
```

Next

```text
Ruby

↓

Set

{Java,Ruby}
```

Next

```text
Java

↓

Already exists

↓

Ignore
```

That's why `distinct()` is efficient.

---

# Why a Set?

Remember what a `Set` does.

```java
Set<String> set = new HashSet<>();
```

A Set **never allows duplicates**.

```java
set.add("Java");
set.add("Ruby");
set.add("Java");
```

Final Set

```text
Java
Ruby
```

Exactly the same behavior as `distinct()`.

---

# Visual Flow

```text
List

↓

Java
Ruby
C#
Java
Ruby
Kotlin

↓

Stream

↓

distinct()

↓

Java
Ruby
C#
Kotlin

↓

collect()

↓

List
```

---

# Does `distinct()` preserve order?

Yes.

This is very important.

Input

```text
B
A
C
A
B
D
```

Output

```text
B
A
C
D
```

Notice

It keeps the **first occurrence**.

It does **not** sort the elements.

Many beginners think

```java
.distinct()
```

also sorts.

It doesn't.

Example

Input

```text
7
2
5
2
1
```

Output

```text
7
2
5
1
```

Not

```text
1
2
5
7
```

---

# How does it know two objects are equal?

For Strings

```java
"Java".equals("Java")
```

returns

```text
true
```

So duplicate detected.

---

Suppose you have

```java
class Employee{

    int id;
    String name;

}
```

Then

```java
employees.stream().distinct()
```

will work **properly only if** the `Employee` class correctly overrides:

```java
equals()

hashCode()
```

Otherwise, two employees with the same data may still be treated as different objects.

---

# Complete Flow

```text
Original List

↓

Java
Ruby
Java
Kotlin
Ruby
C#

↓

Stream

↓

distinct()

↓

Java
Ruby
Kotlin
C#

↓

collect()

↓

List
```

---

# Interview Tip

The operations below are often confused:

| Operation    | Purpose                                |
| ------------ | -------------------------------------- |
| `filter()`   | Keep elements that satisfy a condition |
| `distinct()` | Remove duplicates                      |
| `sorted()`   | Sort the elements                      |
| `limit(n)`   | Keep the first `n` elements            |
| `skip(n)`    | Ignore the first `n` elements          |

For example:

```java
list.stream()
    .filter(s -> s.length() > 3)
    .distinct()
    .sorted()
    .collect(Collectors.toList());
```

Think of it as a pipeline:

1. Keep strings longer than 3 characters.
2. Remove duplicates.
3. Sort the remaining strings.
4. Collect them into a list.

---

## One important thing to know

You might wonder:

> **How does `distinct()` decide whether two elements are duplicates?**

It uses the same mechanism as a `HashSet`:

* First, it checks the object's `hashCode()`.
* If the hash codes match, it then checks `equals()`.

That's why:

* `String`, `Integer`, `Double`, etc., work perfectly—they already implement `equals()` and `hashCode()`.
* For your own classes (like `Employee`, `Student`, etc.), you should override `equals()` and `hashCode()` if you want `distinct()` to identify logically equal objects as duplicates.

This becomes a very common interview topic when streams are used with custom objects.

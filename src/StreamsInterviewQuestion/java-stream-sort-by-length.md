Excellent. This question introduces one of the **most important comparator methods** you'll use in real projects:

```java
Comparator.comparing(...)
```

If you work in Spring Boot, you'll use `Comparator.comparing()` much more often than `Comparator.reverseOrder()` because most business objects (`Employee`, `User`, `Order`, etc.) need to be sorted by one of their properties.

---

# Suggested Notes File

`java-stream-sort-by-length.md`

---

# Problem Statement

Given

```text
["java","Bengaluru","Mumbai","Delhi","Kolkata","Chennai"]
```

Sort according to the **length** of each string.

Output

```text
[java, Delhi, Mumbai, Chennai, Kolkata, Bengaluru]
```

Let's verify the lengths:

| String    | Length |
| --------- | ------ |
| java      | 4      |
| Delhi     | 5      |
| Mumbai    | 6      |
| Chennai   | 8      |
| Kolkata   | 8      |
| Bengaluru | 10     |

---

# Complete Code

```java
public static List<String> sort(List<String> list){

    List<String> result = list.stream()
            .sorted(Comparator.comparing(String::length))
            .collect(Collectors.toList());

    return result;
}
```

Let's understand every part.

---

# Step 1

```java
list.stream()
```

Creates a stream.

```
java
↓

Bengaluru
↓

Mumbai
↓

Delhi
↓

Kolkata
↓

Chennai
```

---

# Step 2

```java
.sorted(...)
```

You already know this.

`sorted()` needs a way to compare two elements.

Previously we wrote

```java
.sorted()
```

or

```java
.sorted(Comparator.reverseOrder())
```

This time we write

```java
Comparator.comparing(...)
```

---

# What is Comparator.comparing()?

Think of it like this.

Java asks:

> "When comparing two strings, what should I compare?"

Normally Java compares the strings themselves alphabetically.

```
Apple

Banana

Cat
```

But now we want to compare something else.

We want to compare:

```
Length
```

So we tell Java:

```java
Comparator.comparing(String::length)
```

Meaning:

> Compare strings using their length.

---

# What does `String::length` mean?

This is a **method reference**.

It is equivalent to:

```java
str -> str.length()
```

Both are exactly the same.

```java
Comparator.comparing(String::length)
```

equals

```java
Comparator.comparing(str -> str.length())
```

---

# Let's execute it

Suppose Java compares

```
java

Mumbai
```

Java asks

```
What should I compare?
```

Comparator replies

```
Length
```

So Java computes

```
java

↓

4

--------------

Mumbai

↓

6
```

Now compare

```
4

vs

6
```

Since

```
4 < 6
```

Result

```
java comes first
```

---

Another comparison

```
Delhi

Chennai
```

Lengths

```
Delhi

↓

5

-----------

Chennai

↓

8
```

Result

```
Delhi first
```

---

Another comparison

```
Bengaluru

Kolkata
```

Lengths

```
10

vs

8
```

Result

```
Kolkata first
```

---

# Visual

Original

```
java

Bengaluru

Mumbai

Delhi

Kolkata

Chennai
```

Comparator converts every string into its length.

```
java

↓

4

-----------------

Bengaluru

↓

10

-----------------

Mumbai

↓

6

-----------------

Delhi

↓

5

-----------------

Kolkata

↓

8

-----------------

Chennai

↓

8
```

Java sorts using these numbers.

Final order

```
4

↓

5

↓

6

↓

8

↓

8

↓

10
```

Result

```
java

Delhi

Mumbai

Chennai

Kolkata

Bengaluru
```

---

# Step 3

```java
.collect(Collectors.toList())
```

Collect the sorted stream back into a list.

Done.

---

# Complete Flow

```
List

↓

Stream

↓

Comparator.comparing(String::length)

↓

Sorted Stream

↓

collect()

↓

List
```

---

# Why use `Comparator.comparing()`?

Suppose you have an `Employee` class:

```java
class Employee {

    String name;

    int salary;

    int age;

}
```

Sort by salary

```java
employees.stream()
         .sorted(Comparator.comparing(Employee::getSalary))
```

Sort by age

```java
employees.stream()
         .sorted(Comparator.comparing(Employee::getAge))
```

Sort by name

```java
employees.stream()
         .sorted(Comparator.comparing(Employee::getName))
```

Notice the pattern.

```
Comparator.comparing(property)
```

You're telling Java:

> "Use this property while comparing objects."

---

# What if I want descending order?

Very common interview question.

Simply add

```java
.reversed()
```

```java
list.stream()
    .sorted(
        Comparator.comparing(String::length)
                  .reversed()
    )
```

Output

```
Bengaluru

Chennai

Kolkata

Mumbai

Delhi

java
```

---

# What if two strings have the same length?

Example

```
java

code

book
```

All have length **4**.

The comparator says:

```
4

4

4
```

Since the lengths are equal, Java considers them equal for sorting purposes. Because Java's stream sort is **stable**, it preserves their original order.

Input:

```
java
code
book
```

Output:

```
java
code
book
```

The order doesn't change.

---

# Comparator.comparing() vs Comparator.naturalOrder()

| Comparator                  | Compares                 |
| --------------------------- | ------------------------ |
| `Comparator.naturalOrder()` | The object itself        |
| `Comparator.comparing(...)` | A property of the object |

Example:

```java
.sorted(Comparator.naturalOrder())
```

For strings:

```
Apple

Banana

Cat
```

Alphabetical order.

---

```java
.sorted(Comparator.comparing(String::length))
```

For the same strings:

```
Cat

Apple

Banana
```

Sorted by length.

---

# One improvement (Secondary Sorting)

Suppose you have:

```text
java
code
book
apple
```

Lengths:

```
java   -> 4
code   -> 4
book   -> 4
apple  -> 5
```

If you want strings of the same length to be sorted alphabetically, use **`thenComparing()`**:

```java
list.stream()
    .sorted(
        Comparator.comparing(String::length)
                  .thenComparing(Comparator.naturalOrder())
    )
    .collect(Collectors.toList());
```

This means:

1. Sort by length.
2. If the lengths are equal, sort alphabetically.

---

# Interview Tip

`Comparator.comparing()` is one of the most frequently used comparator methods in Java interviews and production code.

Some common patterns are:

```java
// Sort by name
Comparator.comparing(Employee::getName)

// Sort by salary
Comparator.comparing(Employee::getSalary)

// Sort by joining date
Comparator.comparing(Employee::getJoiningDate)

// Sort by string length
Comparator.comparing(String::length)
```

A simple way to remember it is:

> **`Comparator.comparing()` asks: "Which property should I use to compare these objects?"**

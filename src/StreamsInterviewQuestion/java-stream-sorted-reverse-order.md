Perfect. This question introduces another **very important Stream operation**:

* `sorted()`
* `Comparator.reverseOrder()`

The code is actually much simpler than the previous `groupingBy()` example.

---

# Suggested Notes File

`java-stream-sorted-reverse-order.md`

---

# Problem Statement

Given

```text
[12.45, 9.81, 45.9, 12.7, 89.90, 34.56, 11.3]
```

Output

```text
[89.90, 45.9, 34.56, 12.7, 12.45, 11.3, 9.81]
```

We want the numbers in **descending order**.

---

# Complete Code

```java
public static List<Double> sortReverse(List<Double> list){

    List<Double> ans = list.stream()
                           .sorted(Comparator.reverseOrder())
                           .collect(Collectors.toList());

    return ans;
}
```

Let's understand every line.

---

# Step 1

```java
list.stream()
```

Creates a stream.

Imagine

```
List

12.45
9.81
45.9
12.7
89.9
34.56
11.3

↓

Stream

12.45 → 9.81 → 45.9 → 12.7 → 89.9 → 34.56 → 11.3
```

Nothing is sorted yet.

---

# Step 2

```java
.sorted(...)
```

This is an **intermediate operation**.

Its job is simply

> "Arrange the elements."

Without any argument,

```java
list.stream()
    .sorted()
```

Java uses **natural ordering**.

Example

```
5
2
9
1
```

becomes

```
1
2
5
9
```

Ascending order.

---

# But here we passed something

```java
.sorted(Comparator.reverseOrder())
```

So Java asks

> "How should I compare two numbers?"

Instead of using the default comparison,

it uses

```java
Comparator.reverseOrder()
```

which means

```
Largest

↓

Smallest
```

---

# What is Comparator?

Think of a Comparator as a **Judge**.

Suppose Java has

```
45.9

12.7
```

Java asks the Comparator

```
Which one should come first?
```

Comparator replies

```
45.9
```

Next

```
89.9

45.9
```

Comparator says

```
89.9
```

Java keeps asking the Comparator until the entire list becomes sorted.

---

# What is reverseOrder()?

Normally

Comparator says

```
Smallest First

1

2

5

9
```

Reverse Comparator says

```
Largest First

9

5

2

1
```

That's all.

---

# Visual Flow

Original

```
12.45

9.81

45.9

12.7

89.9

34.56

11.3
```

↓

Comparator keeps comparing

```
89.9 > 45.9

45.9 > 34.56

34.56 > 12.7

...
```

↓

Final

```
89.9

45.9

34.56

12.7

12.45

11.3

9.81
```

---

# Step 3

```java
.collect(Collectors.toList())
```

Remember

Until now

```
Stream

↓

Sorted Stream
```

Now we want

```
List
```

So

```java
.collect(Collectors.toList())
```

collects everything back into a List.

---

# Final Flow

```
List

↓

stream()

↓

Stream

↓

sorted(reverseOrder())

↓

Sorted Stream

↓

collect(toList())

↓

List
```

---

# What exactly is Comparator.reverseOrder()?

This method returns a Comparator object.

Think of it like this:

```java
Comparator<Double> comparator = Comparator.reverseOrder();
```

Now imagine Java sorting these two values:

```text
45.9

12.7
```

It asks the comparator:

```
Which should come first?
```

The comparator answers:

```
45.9
```

It repeats this comparison many times internally until the entire list is ordered.

---

# Can we write it ourselves?

Absolutely.

Instead of

```java
.sorted(Comparator.reverseOrder())
```

you can write

```java
.sorted((a, b) -> b.compareTo(a))
```

Let's understand this.

Suppose

```
a = 12.7

b = 45.9
```

Normally

```java
a.compareTo(b)
```

means

```
12.7 compared to 45.9

↓

Negative number

↓

12.7 comes first
```

Ascending order.

Now reverse it.

```java
b.compareTo(a)
```

becomes

```
45.9 compared to 12.7

↓

Positive

↓

45.9 comes first
```

That's descending order.

So these are equivalent:

```java
.sorted(Comparator.reverseOrder())
```

```java
.sorted((a, b) -> b.compareTo(a))
```

The first one is cleaner and easier to read.

---

# Interview Tip

There are three common ways to use `sorted()`:

### 1. Natural order (ascending)

```java
stream.sorted()
```

Output:

```
1
2
3
4
```

---

### 2. Reverse order (descending)

```java
stream.sorted(Comparator.reverseOrder())
```

Output:

```
4
3
2
1
```

---

### 3. Custom sorting

Suppose you have:

```java
class Employee {
    String name;
    int salary;
}
```

Sort by salary:

```java
employees.stream()
         .sorted(Comparator.comparing(Employee::getSalary))
```

Or sort by salary in descending order:

```java
employees.stream()
         .sorted(
             Comparator.comparing(Employee::getSalary)
                       .reversed()
         )
```

This is one of the most common sorting patterns you'll see in real Spring Boot applications.

---

## One small point about your code

You wrote:

```java
List<Double> ans = list.stream()
                       .sorted(Comparator.reverseOrder())
                       .collect(Collectors.toList());

return ans;
```

This is perfectly correct, but since `ans` is only used to return the result, it can be shortened to:

```java
public static List<Double> sortReverse(List<Double> list) {
    return list.stream()
               .sorted(Comparator.reverseOrder())
               .collect(Collectors.toList());
}
```

Both versions behave exactly the same; the second is just a bit more concise.

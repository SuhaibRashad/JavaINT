Perfect. Now let's learn what I consider **the heart of Java Streams**.

# **How `map()` and `filter()` Actually Work Internally**

Most beginners memorize:

* `map()` → transforms
* `filter()` → filters

But an interviewer with 6+ years of experience may ask:

> **"What is the difference between map() and filter()? Explain internally."**

Let's understand it deeply.

---

# Imagine a Factory

Suppose you have a factory producing mobile phones.

```
Raw Material
      │
      ▼
Quality Check
      │
      ▼
Painting
      │
      ▼
Packaging
```

Each stage performs one task.

Streams work exactly like this.

---

Suppose

```java
List<Integer> list = Arrays.asList(1,2,3,4,5);
```

and

```java
list.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * 10)
    .forEach(System.out::println);
```

Pipeline

```
1
2
3
4
5

      │
      ▼
filter()

      │
      ▼
map()

      │
      ▼
forEach()
```

---

# Understanding filter()

Let's begin with

```java
.filter(n -> n % 2 == 0)
```

Think about the question Java asks.

```
Should I keep this element?
```

That's all.

Filter always answers only one thing.

```
YES

or

NO
```

---

For example

Input

```
1
```

Java asks

```
Keep it?
```

Lambda

```java
n -> n % 2 == 0
```

returns

```
false
```

Meaning

```
Throw it away.
```

---

Next

```
2
```

Java asks

```
Keep?
```

Lambda returns

```
true
```

Meaning

```
Pass it to next stage.
```

---

So filter **never changes data.**

It only decides

```
KEEP

or

REMOVE
```

---

# Understanding map()

Now comes

```java
.map(n -> n * 10)
```

Notice the question Java asks now.

```
What should this element become?
```

Not

```
Should I keep it?
```

Instead

```
Transform it.
```

---

Example

Input

```
2
```

Java asks

```
What should 2 become?
```

Lambda

```java
n -> n * 10
```

returns

```
20
```

So

```
2

↓

20
```

---

Another

```
4

↓

40
```

---

Notice

Filter

```
2

↓

2
```

Map

```
2

↓

20
```

Very different jobs.

---

# Visual Difference

Filter

```
Apple

↓

Keep?

↓

Yes

↓

Apple
```

Map

```
Apple

↓

Transform

↓

APPLE
```

---

Another example

```
Employee

↓

Employee Name
```

Here map converts

```
Employee
```

into

```
String
```

---

# This is Why map() Can Change Types

Suppose

```java
class Employee {

    String name;

    int salary;

}
```

Now

```java
employees.stream()
         .map(Employee::getName)
```

Input

```
Employee

Employee

Employee
```

Output

```
String

String

String
```

The type changed.

---

Filter never changes types.

```
Employee

↓

Employee
```

Still Employee.

---

Map can even become

```
Integer

↓

String

↓

Boolean

↓

Employee
```

Anything.

---

# Generic Signature

This is why the method signatures are different.

Filter

```java
Stream<T> filter(Predicate<T>)
```

Input

```
T
```

Output

```
T
```

Same type.

---

Map

```java
<R> Stream<R> map(Function<T,R>)
```

Input

```
T
```

Output

```
R
```

Possibly different type.

---

Example

```java
List<String> names =
Arrays.asList("Rahul","Rohan");
```

```
String

↓

Length

↓

Integer
```

Code

```java
names.stream()
     .map(String::length)
```

Output

```
5

5
```

String became Integer.

Filter can never do this.

---

# Internal Execution

Suppose

```java
list.stream()
    .filter(x -> x > 3)
    .map(x -> x * 100)
    .forEach(System.out::println);
```

Input

```
1
2
3
4
5
```

Execution

Element 1

```
1

↓

filter

False

↓

Discard
```

No map.

---

Element 2

```
2

↓

False

↓

Discard
```

---

Element 3

```
3

↓

False

↓

Discard
```

---

Element 4

```
4

↓

True

↓

map()

↓

400

↓

Print
```

---

Element 5

```
5

↓

True

↓

500

↓

Print
```

Notice

`map()` is **never called** for

```
1

2

3
```

because they failed the filter.

This is one reason streams are efficient.

---

# Real Project Example

Suppose

```java
class Employee{

    String name;

    int salary;

    boolean active;

}
```

Requirement

```
Find names of active employees.
```

Code

```java
employees.stream()
         .filter(Employee::isActive)
         .map(Employee::getName)
         .collect(Collectors.toList());
```

Think of the pipeline.

```
Employee

↓

filter()

↓

Active Employees

↓

map()

↓

Employee Names

↓

collect()

↓

List<String>
```

Notice how each stage has a single responsibility.

---

# Interview Question

**Q:** What is the difference between `map()` and `filter()`?

**Answer:**

* `filter()` is used to decide whether an element should continue in the stream. It takes a `Predicate<T>` and returns a `Stream<T>`, so the element type remains the same.
* `map()` is used to transform each element into another value. It takes a `Function<T, R>` and returns a `Stream<R>`, allowing the element type to change.

---

## Why interviewers love this question

If you answer only:

> "`filter()` filters and `map()` transforms."

that's a beginner answer.

A stronger answer is:

> "`filter()` evaluates a boolean condition for each element and either keeps or discards it, while `map()` transforms every element into a new value. `filter()` preserves the stream's element type because it returns `Stream<T>`, whereas `map()` can change the element type because it returns `Stream<R>`. During pipeline execution, `map()` is invoked only for elements that successfully pass through the preceding `filter()`."

---

This foundation will make the next advanced topics much easier:

1. `flatMap()` (the most asked Stream interview question after `map()`).
2. `reduce()` (very common in interviews).
3. Custom collectors.
4. How parallel streams execute the pipeline internally.

I recommend learning them in exactly that order.

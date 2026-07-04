Perfect. This is probably the **most important Stream concept**. Once you understand it, you'll stop memorizing Stream methods and start understanding how Streams actually work.

# Suggested Notes File

`java-stream-internal-working.md`

---

# Before Streams

Suppose we want to

1. Keep even numbers
2. Multiply by 10
3. Print

Without streams:

```java
List<Integer> list = Arrays.asList(1,2,3,4,5,6);

List<Integer> even = new ArrayList<>();

for(Integer n : list){
    if(n % 2 == 0){
        even.add(n);
    }
}

List<Integer> multiplied = new ArrayList<>();

for(Integer n : even){
    multiplied.add(n * 10);
}

for(Integer n : multiplied){
    System.out.println(n);
}
```

Notice something.

We created two extra lists.

```
Original List
      │
      ▼
Even List
      │
      ▼
Multiplied List
      │
      ▼
Print
```

Extra memory is used.

---

# With Streams

```java
list.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * 10)
    .forEach(System.out::println);
```

Looks like

```
List
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

Many beginners think Java first completes `filter()`, then `map()`, then `forEach()`.

**This is NOT what happens.**

---

# What beginners imagine

Input

```
1 2 3 4 5 6
```

They think Java does

### Step 1

```
filter()

↓

2 4 6
```

### Step 2

```
map()

↓

20 40 60
```

### Step 3

```
forEach()

↓

20
40
60
```

This seems logical.

But Java **doesn't work this way**.

---

# How Streams Actually Work

Java processes **one element at a time**.

Input

```
1
2
3
4
5
6
```

---

## First element

```
1
```

Filter

```
1 % 2 == 0

↓

false
```

Discard it.

Java immediately moves to the next element.

Nothing is stored.

---

## Second element

```
2
```

Filter

```
2 % 2 == 0

↓

true
```

Pass to `map()`

```
2 × 10

↓

20
```

Pass immediately to `forEach()`

```
Print

20
```

Done.

---

## Third element

```
3
```

Filter

```
false
```

Discard.

---

## Fourth element

```
4
```

Filter

```
true
```

Map

```
40
```

Print

```
40
```

---

Notice the flow.

```
2

↓

filter

↓

map

↓

forEach

↓

Printed
```

Then Java picks the next element.

It never waits for all elements.

---

# Visual Representation

Instead of this

```
All Elements

↓

Filter Everything

↓

Map Everything

↓

Print Everything
```

Java actually does

```
Element 1

↓

filter

↓

map

↓

forEach

----------------

Element 2

↓

filter

↓

map

↓

forEach

----------------

Element 3

↓

filter

↓

map

↓

forEach
```

This is called a **pipeline**.

---

# Why is this Faster?

Imagine

```
10 million numbers
```

If Java created a new collection after every operation

```
10 million

↓

Filter

↓

New List

↓

Map

↓

Another New List

↓

Sort

↓

Another New List
```

Huge memory usage.

Instead Java processes

```
One element

↓

Filter

↓

Map

↓

Collect
```

Then forgets about it.

Memory stays low.

---

# Lazy Evaluation

Another important concept.

Consider

```java
list.stream()
    .filter(n -> {
        System.out.println("Filtering " + n);
        return n % 2 == 0;
    });
```

What is printed?

Most beginners say

```
Filtering 1
Filtering 2
Filtering 3
```

Actually...

**Nothing is printed.**

Why?

Because no terminal operation exists.

---

Streams are **lazy**.

Creating this pipeline doesn't execute anything.

Think of it as a recipe.

```
Stream

↓

filter()

↓

map()
```

Java says

> "Okay, I know what you want to do."

But it doesn't start cooking yet.

---

# Terminal Operation

Now add

```java
.forEach(System.out::println);
```

Suddenly Java starts execution.

Because `forEach()` is a **terminal operation**.

---

# Intermediate Operations

These return another stream.

Examples

```java
filter()

map()

sorted()

distinct()

skip()

limit()

peek()
```

They don't execute.

They only describe the pipeline.

---

# Terminal Operations

These trigger execution.

Examples

```java
collect()

count()

findFirst()

findAny()

reduce()

min()

max()

forEach()

toArray()

anyMatch()

allMatch()

noneMatch()
```

Once a terminal operation is called, Java processes the stream.

---

# Think of a Water Pipeline

Imagine water flowing through pipes.

```
Tank

↓

Filter

↓

Pump

↓

Tap
```

No water comes out until you open the tap.

The tap is the **terminal operation**.

The filter and pump are just waiting.

---

# Another Example

```java
list.stream()
    .filter(n -> n > 5)
    .map(n -> n * 2)
    .findFirst();
```

Input

```
1 2 3 6 7 8
```

How many elements are processed?

Many beginners think all six.

Wrong.

Execution:

```
1

↓

Filter

False

Discard

------------

2

↓

False

------------

3

↓

False

------------

6

↓

True

↓

Map

12

↓

findFirst()

Found!

STOP
```

Java never even looks at

```
7

8
```

This is another advantage of lazy evaluation.

---

# Interview Question

**Q:** Why are Streams considered lazy?

**Answer:**

> Intermediate operations such as `filter()`, `map()`, and `sorted()` do not execute immediately. They only build a processing pipeline. The pipeline is executed only when a terminal operation like `collect()`, `forEach()`, or `findFirst()` is invoked. This allows Java to optimize execution, reduce memory usage, and avoid unnecessary processing.

---

This understanding is the foundation for advanced topics like `flatMap`, `peek`, `reduce`, collectors, and parallel streams. Once this model is clear, almost every Stream question becomes much easier to reason about.

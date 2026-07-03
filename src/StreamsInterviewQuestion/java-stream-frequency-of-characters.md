This is an excellent Stream question because it introduces **three very important concepts**:

1. `chars()`
2. `Function.identity()`
3. `groupingBy()`

If you understand these three, you'll be able to solve many interview questions.

---

# Suggested notes file

`java-stream-frequency-of-characters.md`

---

# First understand the problem

Given

```text
Java
```

We want

```text
J -> 1
a -> 2
v -> 1
```

Or for

```text
hello
```

We want

```text
h -> 1
e -> 1
l -> 2
o -> 1
```

---

# The complete code

```java
public static Map<Character,Long> getFreq(String str){

    Stream<Character> characterStream =
            str.chars().mapToObj(i -> (char) i);

    Map<Character, Long> result =
            characterStream.collect(
                    Collectors.groupingBy(
                            Function.identity(),
                            Collectors.counting()));

    return result;
}
```

Let's understand every single line.

---

# Step 1

```java
str.chars()
```

Suppose

```java
String str = "Java";
```

You may think

```java
str.chars()
```

returns

```text
J
a
v
a
```

❌ Wrong.

It actually returns the **Unicode (ASCII for English letters) values**.

```
J = 74
a = 97
v = 118
a = 97
```

So

```java
str.chars()
```

produces an `IntStream`.

```
74
97
118
97
```

Why `IntStream`?

Because Java stores each character as an integer Unicode code point.

---

# Step 2

```java
.mapToObj(i -> (char)i)
```

Now Java receives

```
74
97
118
97
```

One by one.

First

```
74
```

Lambda

```java
i -> (char)i
```

becomes

```java
(char)74
```

which is

```
J
```

Next

```
97
```

becomes

```
a
```

Next

```
118
```

becomes

```
v
```

Next

```
97
```

becomes

```
a
```

Now the stream becomes

```
J
a
v
a
```

Now we have

```java
Stream<Character>
```

instead of

```java
IntStream
```

---

# Why is it called mapToObj()?

Because

Before

```
IntStream
```

After

```
Stream<Character>
```

We're converting primitive integers into objects (`Character`).

That's exactly what `mapToObj()` means.

```
Primitive

↓

Object
```

---

# Step 3

Now comes the interesting part.

```java
.collect(...)
```

Remember from the previous question:

`collect()` gathers the final result.

Until now we only have

```
J
a
v
a
```

Now we want

```
J -> 1
a -> 2
v -> 1
```

---

# Step 4

```java
Collectors.groupingBy(...)
```

This collector creates groups.

Imagine students.

```
CSE

Rahul
Amit

ECE

John
Sara
```

Students are grouped by department.

Similarly

Characters

```
J

a

v

a
```

Java groups identical characters.

```
J

↓

J

----------------

a

↓

a
a

----------------

v

↓

v
```

Without counting yet.

---

# Step 5

Now comes the confusing part.

```java
Function.identity()
```

This scares almost every beginner.

Let's simplify it.

Suppose we have

```
J

a

v

a
```

Normally `groupingBy()` asks:

> "How should I group these elements?"

For example

```java
groupingBy(Person::getDepartment)
```

means

Group by department.

But here we don't want any property.

We want

```
J with J

a with a

v with v
```

Simply group each character by **itself**.

That is exactly what

```java
Function.identity()
```

means.

It is equivalent to

```java
c -> c
```

That's all!

Literally.

These are the same.

```java
Function.identity()
```

```java
c -> c
```

```java
x -> x
```

All return the element unchanged.

---

# Visual

Stream

```
J

a

v

a
```

Identity Function

```
J → J

a → a

v → v

a → a
```

Grouping becomes

```
J

↓

J

----------------

a

↓

a
a

----------------

v

↓

v
```

---

# Step 6

Now

```java
Collectors.counting()
```

asks

How many are there in each group?

Group

```
J
```

Count

```
1
```

Group

```
a
a
```

Count

```
2
```

Group

```
v
```

Count

```
1
```

Result

```
J → 1

a → 2

v → 1
```

---

# Putting it together

```java
Collectors.groupingBy(
        Function.identity(),
        Collectors.counting()
)
```

Means

```
Take each character

↓

Group identical characters

↓

Count how many are inside each group

↓

Return Map<Character,Long>
```

---

# Complete Flow

Suppose

```
Java
```

### Step 1

```
String

Java
```

↓

### Step 2

```
chars()

74
97
118
97
```

↓

### Step 3

```
mapToObj()

J
a
v
a
```

↓

### Step 4

```
groupingBy(identity())

J

↓

J

------------

a

↓

a
a

------------

v

↓

v
```

↓

### Step 5

```
counting()

J → 1

a → 2

v → 1
```

↓

Final

```java
{
J=1,
a=2,
v=1
}
```

---

# What does `groupingBy()` actually return?

A helpful way to think about it is that `groupingBy()` works in **two phases**:

### Phase 1: Group the elements

For `"Java"`:

```text
J -> [J]
a -> [a, a]
v -> [v]
```

If you wrote:

```java
Collectors.groupingBy(Function.identity())
```

the result would be:

```java
{
    J=[J],
    a=[a, a],
    v=[v]
}
```

### Phase 2: Apply another collector to each group

Here, the second collector is:

```java
Collectors.counting()
```

Instead of keeping the list, it counts the elements in each list.

So:

```text
J -> [J]      -> 1
a -> [a, a]   -> 2
v -> [v]      -> 1
```

Final result:

```java
{
    J=1,
    a=2,
    v=1
}
```

---

## Interview Tip

A very common pattern in Stream problems is:

```java
stream.collect(
    Collectors.groupingBy(
        classifier,
        downstreamCollector
    )
);
```

Think of it as:

* **Classifier** → "How should I group the elements?"
* **Downstream collector** → "What should I do with each group?"

Examples:

* `groupingBy(Function.identity(), counting())` → Count frequencies.
* `groupingBy(String::length)` → Group strings by their length.
* `groupingBy(Employee::getDepartment, mapping(Employee::getName, toList()))` → Group employees by department and collect their names.

Once you understand those two roles, `groupingBy()` becomes much easier to read.


# Follow Up
This is one of the best questions you could ask. It shows you're not just learning **Streams**, you're trying to understand **why the API was designed this way**.

The short answer is:

> **A `String` is NOT a collection of `Character` objects.**
> Therefore, it does **not** have a `stream()` method.

Let's understand why.

---

# Why can a List do this?

```java
List<String> list = List.of("A", "B", "C");

list.stream();
```

Why?

Because a `List` is a **Collection**.

```
Collection
     ↑
    List
     ↑
ArrayList
LinkedList
```

The `Collection` interface defines:

```java
Stream<E> stream();
```

Since `List` implements `Collection`, it automatically gets the `stream()` method.

---

# But what about String?

```java
String str = "Java";
```

Is `String` a `Collection<Character>`?

No.

The class hierarchy is simply:

```
Object
   ↑
String
```

It doesn't implement `Collection`.

So Java cannot provide:

```java
str.stream();   // ❌ Doesn't exist
```

---

# But isn't a String just a collection of characters?

Conceptually, yes.

Technically, no.

A `List<Character>` looks like this:

```
[
 'J',
 'a',
 'v',
 'a'
]
```

Every element is already a `Character` object.

A `String` is different.

Internally (simplified), Java stores characters in an array, not as a `List<Character>`.

In older Java versions:

```text
String

↓

char[]
```

Since Java 9, it uses a more optimized internal representation (`byte[]` with an encoding flag), but **it is still not a Collection**.

---

# So how do we stream over a String?

Java provides methods that expose its characters.

There are three common ones.

### 1. `chars()`

```java
str.chars()
```

Returns an `IntStream`.

```
"Java"

↓

74
97
118
97
```

Each value is the Unicode value of the character.

---

### 2. `codePoints()`

```java
str.codePoints()
```

Also returns an `IntStream`, but it correctly handles Unicode characters that require more than one `char` (such as many emojis and some less common scripts).

For normal English text:

```text
chars()

==

codePoints()
```

For emojis:

```
😀

chars()       ❌ splits it

codePoints()  ✅ keeps it as one code point
```

---

### 3. Convert to a character array

```java
char[] arr = str.toCharArray();

Arrays.stream(arr);   // ❌
```

Here you'll discover another interesting fact.

There is **no**:

```java
Arrays.stream(char[])
```

Java supports:

```java
Arrays.stream(int[])
Arrays.stream(long[])
Arrays.stream(double[])
```

But **not** `char[]`.

So if you use `toCharArray()`, you'd still have to convert it yourself.

---

# Why didn't Java just give String a stream() method?

Imagine if `String` implemented `Collection<Character>`.

Then it would need methods like:

```java
add()

remove()

clear()

iterator()
```

But a `String` is **immutable**.

You cannot do:

```java
str.add('A');
```

or

```java
str.remove(0);
```

That wouldn't make sense.

So Java designers intentionally kept `String` separate from the Collections Framework.

---

# Then why `chars()` instead of `stream()`?

Because `chars()` makes it explicit:

> "I want to stream over the characters of this string."

```
String

↓

chars()

↓

IntStream

↓

mapToObj()

↓

Stream<Character>
```

---

# Can we directly create a `Stream<Character>`?

Not directly using a built-in method.

That's why we write:

```java
Stream<Character> stream =
        str.chars()
           .mapToObj(c -> (char) c);
```

This converts:

```
IntStream

74
97
118
97

↓

Stream<Character>

J
a
v
a
```

---

# One design pattern you'll start noticing

Streams always begin from a **source**.

| Source | How to create a Stream                               |
| ------ | ---------------------------------------------------- |
| `List` | `list.stream()`                                      |
| `Set`  | `set.stream()`                                       |
| `Map`  | `map.entrySet().stream()` or `map.keySet().stream()` |
| Array  | `Arrays.stream(array)` (for supported array types)   |
| String | `str.chars()` or `str.codePoints()`                  |

Notice something? Every type has its own "entry point" into the Stream API.

---

## A question for you

Suppose you have:

```java
Map<String, Integer> map = Map.of(
    "A", 10,
    "B", 20
);
```

If you want to process **both the key and the value** using streams, which would you choose?

1. `map.keySet().stream()`
2. `map.values().stream()`
3. `map.entrySet().stream()`

Try answering before looking it up. Based on our previous discussion about `entrySet()`, you should be able to reason it out.

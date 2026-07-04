This is where interview experience matters.

The answer is:

> **You generally don't solve a "reverse list" problem using `parallelStream()` because reversing is an order-dependent operation, while parallel streams are designed for independent operations.**

So if an interviewer asks this, they are often testing whether you understand **when not to use parallel streams**.

Let's see why.

---

# Why Parallel Streams are a Bad Fit

Suppose we have:

```text
[1, 2, 3, 4, 5, 6]
```

A parallel stream may internally split it like this:

```text
Thread 1          Thread 2

1 2 3             4 5 6
```

Each thread works independently.

Now imagine asking:

> "Reverse the entire list."

Thread 1 doesn't know about Thread 2.

It can reverse its own part:

```text
3 2 1
```

Thread 2 can reverse its own part:

```text
6 5 4
```

But the final answer should be:

```text
6 5 4 3 2 1
```

Now Java has to coordinate both threads, merge the results, and preserve the correct order.

At that point, **parallelism gives little or no benefit**.

---

# Can we still use `parallelStream()`?

Yes, but not because it makes reversing faster.

One approach is:

```java
public static List<Integer> reverse(List<Integer> list) {

    return IntStream.range(0, list.size())
            .parallel()
            .mapToObj(i -> list.get(list.size() - 1 - i))
            .collect(Collectors.toList());
}
```

Let's understand it.

---

## Step 1

```java
IntStream.range(0, list.size())
```

Suppose

```java
List<Integer> list = Arrays.asList(10,20,30,40);
```

This creates

```text
0
1
2
3
```

These are **indices**, not the values.

---

## Step 2

```java
.parallel()
```

Now these indices can be processed by different threads.

Example

```text
Thread 1

0
1

------------

Thread 2

2
3
```

---

## Step 3

Now comes the trick.

```java
list.get(list.size() - 1 - i)
```

Let's execute it.

List

```text
Index

0  1  2  3

10 20 30 40
```

For

```text
i = 0
```

Java computes

```text
size - 1 - i

4 - 1 - 0

=

3
```

Fetch

```java
list.get(3)
```

↓

```text
40
```

---

For

```text
i = 1
```

Fetch

```java
list.get(2)
```

↓

```text
30
```

---

For

```text
i = 2
```

↓

```text
20
```

---

For

```text
i = 3
```

↓

```text
10
```

Result

```text
40
30
20
10
```

---

# Why does this work with parallel streams?

Notice something important.

Each thread only needs one piece of information:

```text
its own index
```

For example

```text
Thread A

i = 0

↓

Read index 3

↓

40
```

Thread B

```text
i = 2

↓

Read index 1

↓

20
```

No thread modifies the list.

Each thread simply reads one element.

That's why this approach is thread-safe.

---

# Is it faster?

Usually **no**.

For a small list like

```text
100 elements
```

parallel streams are actually slower because of:

* Creating tasks
* Splitting work
* Combining results
* Thread scheduling

The overhead outweighs any benefit.

---

# Interview Perspective

If an interviewer asks:

> **"Can you reverse a list using parallel streams?"**

A strong answer is:

> "Yes, it can be done using an index-based `IntStream.range(...).parallel()`, but I generally wouldn't use a parallel stream for reversing. Reversing is an order-dependent operation, so parallelism doesn't provide much benefit. I'd use `Collections.reverse()` or an index-based sequential stream unless there's a specific reason to parallelize."

This answer demonstrates maturity. Experienced Java developers don't use parallel streams just because they exist—they use them only when the workload is CPU-intensive, independent, and large enough to justify the overhead.

---

## A question for you

You've now learned:

* `stream()`
* `parallelStream()`
* `map()`
* `filter()`
* `sorted()`
* `distinct()`
* `groupingBy()`
* `Comparator`
* `Optional`

Before moving to the next interview question, I'd recommend spending one session understanding **how a Stream pipeline actually executes internally** (lazy evaluation, intermediate vs. terminal operations, why operations are chained, and how elements flow through the pipeline). That single topic makes all of these methods much easier to reason about in interviews and production code.

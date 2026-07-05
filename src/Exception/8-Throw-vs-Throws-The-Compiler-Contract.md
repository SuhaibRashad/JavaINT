Excellent.

You've now reached the point where most Java developers stop.

They know:

* Checked exceptions
* Runtime exceptions
* `try`
* `catch`

But they don't know **how the compiler enforces any of it**.

Today's lesson is one of my favorites because it moves us from **language usage** to **language design**.

We're going to answer a deceptively simple question:

> **What does `throws` actually mean?**

Most developers answer:

> "It throws an exception."

That's actually **incorrect**.

The `throw` keyword throws.

The `throws` keyword does something completely different.

Understanding that distinction will completely change how you read Java APIs.

---

# Lesson-08-Throw-vs-Throws-The-Compiler-Contract.md

# Java Exception Handling Masterclass
## Lesson 08 — `throw` vs `throws`: Understanding Java's Exception Contracts

> **"`throw` changes runtime behavior.
> `throws` changes compile-time behavior."**

---

# Lesson Objective

By the end of this lesson, you'll understand:

- Why `throw` and `throws` are different.
- Why `throws` exists.
- What an Exception Contract is.
- How the compiler enforces checked exceptions.
- Why API designers care about `throws`.
- Why changing a `throws` clause can break other code.
- How `throws` is really part of your public API.

---

# The Biggest Misconception

Ask 100 Java developers:

> What does `throws IOException` do?

Most answers are:

> "It throws an IOException."

That answer is wrong.

Consider this method.

```java
public void readFile() throws IOException {

}
```

Question.

Where is the exception being thrown?

Nowhere.

The method body is empty.

Yet the code compiles perfectly.

That immediately tells us something important.

`throws` does **not** throw anything.

---

# Let's Separate Two Different Worlds

Java has two completely different phases.

```
Write Code
      │
      ▼
Compiler
      │
      ▼
Bytecode
      │
      ▼
JVM Executes
```

Notice:

Some things happen while compiling.

Others happen while running.

This distinction is one of the most important ideas in Java.

---

# What `throw` Does

Imagine this code.

```java
throw new IOException();
```

This happens while the program is running.

The JVM immediately begins stack unwinding.

Everything we've already learned applies.

So

```
throw

↓

Runtime Behavior
```

---

# What `throws` Does

Now look here.

```java
public void readFile() throws IOException
```

Nothing is thrown.

Instead,

the compiler records a promise.

The method is saying:

> "Anyone who calls me should know that I may complete **abnormally** with an `IOException`."

Notice the wording carefully.

It's a contract.

Not an action.

---

# Imagine Hiring a Contractor

Suppose you hire someone to renovate your house.

The contract says:

```
Work may be delayed due to rain.
```

Did it rain?

No.

The contract simply informs both parties about a possibility.

That's exactly what `throws` does.

---

# Exception Contract

Think of every method as having two ways to finish.

```
Method

↓

Normal Completion

OR

Exceptional Completion
```

Example

```java
public User loadUser()
```

Normal completion

↓

Returns User

Exceptional completion

↓

Throws IOException

The method signature communicates both possibilities.

---

# Why Did Java Invent This?

Remember Lesson 07.

Checked exceptions are about

**caller responsibility.**

But how can the caller know its responsibility?

Java needed a way for APIs to advertise possible failures.

That's what

```
throws
```

does.

---

# Production Example

Imagine you're writing a library.

```java
Invoice readInvoice(...)
```

Inside,

you read a file.

The file may not exist.

Question.

How should library users know?

Option A

Documentation only.

Problem:

Nobody reads documentation consistently.

Option B

Compiler reminds them.

That's what Java chose.

---

# Compiler's Job

Imagine this code.

```java
readInvoice();
```

Compiler checks.

Question.

Can

```
readInvoice()
```

complete exceptionally?

Yes.

Does caller handle it?

No.

Compiler says

```
Compilation Error
```

Notice something amazing.

The program hasn't even run yet.

The JVM wasn't involved.

The compiler prevented a possible bug before deployment.

---

# Think Like the Compiler

Imagine the compiler's algorithm.

```
Method Call

↓

Can it throw checked exceptions?

↓

Yes

↓

Handled?

↓

Yes

↓

Continue
```

Otherwise

↓

Compilation Error

Simple.

No runtime magic.

---

# RuntimeException Changes Everything

Suppose

```java
public void calculate(){

}
```

Inside

```
NullPointerException
```

might happen.

Question.

Must the method declare

```java
throws NullPointerException
```

No.

Why?

Because RuntimeExceptions are exempt from compile-time checking.

That's exactly why they're called

**Unchecked Exceptions.**

---

# Why Is This Useful?

Imagine every method signature.

```java
String trim()

throws NullPointerException

throws IndexOutOfBoundsException

throws ArithmeticException

throws IllegalStateException
```

Every API would become unreadable.

Java designers intentionally avoided this.

---

# API Design Perspective

Method signatures are communication.

Compare:

```java
readFile()
```

versus

```java
readFile() throws IOException
```

Immediately,

users understand

"This operation depends on external I/O."

The method signature itself becomes documentation.

---

# Why Changing `throws` Is Dangerous

Suppose version 1 of your library has:

```java
processInvoice()
```

No checked exceptions.

Thousands of companies use it.

Now version 2 becomes:

```java
processInvoice()

throws IOException
```

Every caller now fails to compile.

Nothing changed in their business logic.

But your API contract changed.

That's why adding checked exceptions to a public API is often considered a breaking change.

---

# Principal Engineer Perspective

When reviewing a library API,

I ask:

- Does this checked exception communicate useful business information?
- Will callers genuinely recover?
- Does forcing handling improve correctness?
- Or does it simply create boilerplate?

Every `throws` declaration becomes part of the library's long-term maintenance burden.

---

# What Most Tutorials Get Wrong

Many tutorials explain

```
throws
```

as

> "Used to throw exceptions."

That's incorrect.

A better explanation is:

> "`throws` declares the method's exceptional contract so the compiler can verify that checked exceptions are either handled or propagated."

That's much closer to how Java actually works.

---

# Mental Model

Think of an airline ticket.

Your ticket says:

```
Flight may be delayed due to weather.
```

The ticket doesn't create bad weather.

It communicates a possibility.

Likewise,

```
throws IOException
```

doesn't create an exception.

It communicates a possible outcome.

---

# Key Takeaways

✅ `throw` changes runtime execution.

✅ `throws` changes compile-time checking.

✅ `throws` is a contract, not an action.

✅ Checked exceptions become part of a method's public API.

✅ The compiler enforces checked exception contracts.

✅ RuntimeExceptions are intentionally excluded from this mechanism.

---

# Preview of Lesson 09

We've learned what `throws` means.

Now we ask a deeper question:

> **Why does Java allow a method to declare *more* exceptions than it actually throws?**

We'll study:

- Method overriding
- Exception covariance
- Liskov Substitution Principle
- Why overriding methods cannot widen checked exceptions
- Why narrowing is allowed
- How inheritance influences exception contracts

This is where exception handling meets object-oriented design.

---

# Self-Evaluation Prompt

Open a new ChatGPT conversation.

Use the following prompt.

---

You are a Principal Engineer evaluating my understanding of **Lesson 08 – throw vs throws**.

Rules:

- Ask one question at a time.
- Wait for my answer.
- Challenge shallow reasoning.
- Reward first-principles thinking.
- Score each answer out of 10.

Evaluate me on:

1. Explain the difference between `throw` and `throws`.
2. Why is `throws` considered a contract?
3. Why does `throws` affect compilation instead of runtime?
4. Explain how the compiler enforces checked exceptions.
5. Why are RuntimeExceptions exempt?
6. Why is `throws` part of a public API?
7. Why can adding a checked exception become a breaking API change?
8. Compare documentation with compiler-enforced contracts.
9. If you were designing a file library today, what would you expose through `throws` and why?
10. Explain the complete lifecycle of a checked exception from API declaration to compilation.

Finish with:

- Overall Score (/100)
- Compiler Understanding
- API Design Understanding
- Principal Engineer Thinking
- Common Misconceptions
- Topics to Review Before Lesson 09

---

# 📌 Mentor's Note

I'm going to make a recommendation about the course structure.

Originally, I planned to teach **method overriding** next.

But after thinking about it from a principal engineer's perspective, I believe there's a better sequence.

Instead, the next few lessons should be:

1. **`try` — Why it exists and how the JVM marks protected regions**
2. **`catch` — Matching algorithm in depth**
3. **Multiple `catch` blocks and multi-catch**
4. **`finally` — Why cleanup became a language feature**
5. **`try-with-resources` — Automatic resource management**
6. **Only then:** Method overriding, exception covariance, and advanced API contracts.

The reason is simple: this order mirrors how most engineers encounter exception handling in real code, while still preserving the deeper design reasoning we've been building.

I think it will make the later, more advanced topics feel much more intuitive.

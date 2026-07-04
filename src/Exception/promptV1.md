# Master Prompt: Learn Java & Spring Boot Exception Handling from Scratch to Principal Engineer Level

I want you to become my mentor and teach me **Exception Handling in Java and Spring Boot** from **absolute beginner to principal engineer level**.

Do **NOT** teach this like a textbook or documentation.

Teach me like a senior engineer who has built and maintained large-scale production systems for years.

My goal is **not** to memorize Java syntax.

My goal is to understand **why every exception handling feature exists, what production problem it solves, what alternatives existed before it, what trade-offs it introduces, and how experienced engineers design failure handling in real-world systems.**

Assume I know nothing unless we have already covered it.

Build every lesson from first principles.

---

# Core Teaching Philosophy

For every concept, always answer these questions before showing code.

1. What problem existed before this concept?
2. Why was that problem difficult in real software?
3. What solutions did developers use before this feature existed?
4. Why were those solutions insufficient?
5. Why was this Java or Spring feature introduced?
6. How does it work internally?
7. What are its advantages?
8. What trade-offs does it introduce?
9. When should it NOT be used?
10. What mistakes do developers commonly make?
11. What are the production consequences of those mistakes?
12. How would a principal engineer think about this differently?

Never introduce a feature without first explaining the problem it solves.

---

# Teaching Style

Teach interactively.

Do not dump an entire chapter at once.

Teach one concept at a time.

After each major concept:

* ask conceptual questions
* ask scenario-based questions
* ask debugging questions
* ask interview questions
* ask production design questions

Wait for my answers before continuing whenever appropriate.

If I struggle, guide me with hints instead of immediately giving the answer.

---

# Learning Depth

Go from beginner to advanced.

Cover:

* Java language concepts
* JVM behavior
* Spring Boot internals
* Framework design decisions
* Enterprise architecture
* Microservices
* Distributed systems
* Cloud-native applications
* Observability
* Resilience
* API design
* Security
* Performance
* Production debugging
* Code review practices
* Principal engineer decision making

Do not stop at syntax.

Teach the engineering behind the syntax.

---

# Internal Implementation

Whenever possible explain:

* JVM internals
* Stack unwinding
* Exception propagation
* Stack traces
* Cause chaining
* Suppressed exceptions
* Bytecode behavior when useful
* Spring DispatcherServlet flow
* ExceptionResolver chain
* Spring Boot auto-configuration
* Transaction rollback mechanics
* Spring Security exception flow
* Reactive exception propagation
* Virtual Threads considerations
* Performance implications

---

# Real Production Scenarios

For every topic include realistic production incidents.

Examples:

* database connection failures
* payment gateway failures
* Kafka consumer failures
* Redis outages
* thread interruption
* timeout handling
* circuit breaker behavior
* retry storms
* cascading failures
* dead-letter queues
* API failures
* distributed tracing
* transaction rollback issues
* memory leaks caused by poor cleanup
* swallowed exceptions
* duplicate logging
* incorrect retry policies
* security information leakage
* exception masking
* root cause loss

For every scenario explain:

* what happened
* why it happened
* how engineers diagnosed it
* root cause
* fix
* prevention
* lessons learned

---

# Spring Boot Coverage

Cover everything related to exception handling in Spring Boot, including but not limited to:

* Exception flow through DispatcherServlet
* @ExceptionHandler
* @ControllerAdvice
* @RestControllerAdvice
* HandlerExceptionResolver
* ResponseEntityExceptionHandler
* Bean Validation
* @Valid
* ConstraintViolationException
* MethodArgumentNotValidException
* BindingResult
* Spring Security exception handling
* AuthenticationException
* AccessDeniedException
* AuthenticationEntryPoint
* AccessDeniedHandler
* Spring Data exception translation
* DataAccessException
* @Repository
* Transaction rollback behavior
* @Transactional
* RollbackFor
* NoRollbackFor
* Async exception handling
* CompletableFuture
* @Async
* Scheduled jobs
* WebFlux exception handling
* Global API error design
* RFC 9457 Problem Details
* Error codes
* Correlation IDs
* Trace IDs
* Structured logging
* OpenTelemetry
* Metrics
* Distributed tracing

Explain why each feature exists instead of only explaining how to use it.

---

# Exception Design

Teach how experienced engineers design exception hierarchies.

Discuss:

* checked vs unchecked exceptions
* exception translation
* wrapping exceptions
* preserving root causes
* custom exception design
* business exceptions
* infrastructure exceptions
* domain exceptions
* validation exceptions
* retryable vs non-retryable failures
* recoverable vs unrecoverable failures

Explain when each approach is appropriate.

---

# Principal Engineer Perspective

For every lesson include a section called:

"Principal Engineer Perspective"

Explain:

* how this concept affects maintainability
* scalability
* observability
* debugging
* developer experience
* API contracts
* resilience
* long-term evolution of a codebase
* organizational standards

Teach me how experienced engineers make design decisions instead of simply following best practices.

---

# Modern Java

Whenever relevant, include modern Java features and explain whether they improve exception handling.

Examples:

* Records
* Pattern Matching
* Sealed Classes
* Virtual Threads
* Structured Concurrency
* Modern switch expressions
* Helpful NullPointerExceptions
* Recent JDK improvements

Mention version-specific behavior where applicable.

---

# Code Quality

Every code sample should progress through these stages:

1. naive implementation
2. improved implementation
3. production-quality implementation
4. enterprise-scale implementation

Explain why each version is better than the previous one.

Follow clean coding principles.

---

# Visual Learning

Whenever useful include:

* ASCII diagrams
* execution flow diagrams
* call stack diagrams
* request lifecycle diagrams
* sequence diagrams
* exception propagation diagrams

Make complex concepts easy to visualize.

---

# Common Mistakes

For every topic include:

* beginner mistakes
* intermediate mistakes
* senior engineer mistakes
* code review comments
* hidden bugs
* performance issues
* security risks

Explain why each mistake is dangerous.

---

# Interview Preparation

After every lesson provide questions in increasing difficulty:

Level 1 — Beginner

Level 2 — Intermediate

Level 3 — Senior Engineer

Level 4 — Staff Engineer

Level 5 — Principal Engineer

Include follow-up discussion questions instead of only factual questions.

---

# End of Every Lesson

End every lesson with:

* key takeaways
* mental model
* decision checklist
* production checklist
* interview recap
* coding exercise
* debugging exercise
* architecture exercise
* reflection questions
* preview of the next lesson

---

# Learning Rules

Never skip steps.

Never assume prior knowledge.

Never teach syntax before motivation.

Always explain the "why" before the "how."

Always compare alternatives.

Always discuss trade-offs.

Always connect the topic to real production systems.

Always explain how large technology companies would approach the problem.

Keep building on previous lessons so that by the end I can confidently design and review exception handling strategies for large Java and Spring Boot codebases as a principal engineer, rather than merely writing try-catch blocks.

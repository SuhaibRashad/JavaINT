Your understanding is mostly on the right track. Below is a corrected and production-oriented version of your notes, with a few important distinctions about FutureTask, wildcards, thread execution, and the CompletableFuture methods.

Java Future and CompletableFuture
1. Submitting tasks to an ExecutorService

ExecutorService.submit() has three commonly used overloads:

Future<?> submit(Runnable task);

<T> Future<T> submit(Runnable task, T result);

<T> Future<T> submit(Callable<T> task);


The method you choose determines:

Whether the task returns a value
What type of Future you receive
What Future.get() returns
2. Runnable versus Callable
Runnable

A Runnable:

Does not return a value
Cannot directly throw checked exceptions
Defines a run() method
Runnable task = () -> {
    System.out.println("Executing task");
};


Its functional method is:

void run();

Callable<T>

A Callable<T>:

Returns a value of type T
Can throw checked exceptions
Defines a call() method
Callable<Integer> task = () -> {
    return 100;
};


Its functional method is:

T call() throws Exception;

3. The three forms of submit()
3.1 submit(Runnable)
ExecutorService executor = Executors.newFixedThreadPool(2);

Future<?> future = executor.submit(() -> {
    System.out.println("Running task");
});


Because the Runnable does not return a result:

Object result = future.get();
System.out.println(result); // null


The future represents the task's completion, cancellation, or failure, but the successful result is always null.

3.2 submit(Runnable, T result)
Future<String> future = executor.submit(
    () -> System.out.println("Processing"),
    "COMPLETED"
);

String result = future.get();
System.out.println(result); // COMPLETED


The Runnable itself does not calculate or return "COMPLETED".

The executor returns the predefined value after the Runnable finishes successfully.

Conceptually:

runTheRunnable();
return predefinedResult;

3.3 submit(Callable<T>)
Future<Integer> future = executor.submit(() -> {
    return 10 + 20;
});

Integer result = future.get();
System.out.println(result); // 30


Here, the result is calculated by the task itself.

4. What happens internally during submit()?

A simplified mental model is:

Runnable or Callable
        ↓
Wrapped in a FutureTask
        ↓
FutureTask submitted to ThreadPoolExecutor
        ↓
Worker thread executes FutureTask.run()
        ↓
FutureTask records result, exception, or cancellation
        ↓
Waiting threads are notified


For example, an implementation can conceptually perform:

RunnableFuture<T> futureTask = new FutureTask<>(callable);
execute(futureTask);
return futureTask;

Important correction

It is not quite accurate to say:

The thread pool updates the FutureTask state based on the thread's state.

The worker thread executes FutureTask.run(), but FutureTask manages its own lifecycle state.

Conceptually, its states include:

NEW
  ↓
COMPLETING
  ↓
NORMAL or EXCEPTIONAL


Cancellation can lead to states conceptually representing:

CANCELLED
INTERRUPTING
INTERRUPTED


The thread's lifecycle and the FutureTask's lifecycle are different.

For example:

A worker thread may remain alive after completing the task.
The FutureTask may be completed while the worker thread starts another task.
Cancelling the future does not necessarily mean the executor's worker thread is permanently terminated.
5. Relationship between Future and FutureTask

Future is an interface:

public interface Future<V>


It provides methods such as:

boolean cancel(boolean mayInterruptIfRunning);
boolean isCancelled();
boolean isDone();
V get();
V get(long timeout, TimeUnit unit);


FutureTask<V> is a concrete class that implements both Runnable and Future<V>:

public class FutureTask<V>
        implements RunnableFuture<V>


RunnableFuture<V> extends both:

Runnable
Future<V>


Therefore, a FutureTask has two important roles:

Executable task, because it is a Runnable
Result and state holder, because it is a Future

A useful mental model is:

FutureTask = executable computation + completion state + result/exception

6. What does Future<?> mean?

The ? is an unbounded wildcard.

Future<?>


It means:

A Future containing some type, but the exact type is unknown or irrelevant at this point.

For example:

Future<?> future = executor.submit(() -> {
    System.out.println("No result");
});


Because submit(Runnable) represents a task without a useful result, its return type is Future<?>.

When calling get():

Object result = future.get();


The only type-safe assumption Java can make is that the result is an Object. For submit(Runnable), the successful result will specifically be null.

Future<?> does not mean any value can be assigned

This is valid:

Future<String> stringFuture = executor.submit(() -> "hello");
Future<?> unknownFuture = stringFuture;


But once it is stored as Future<?>, the specific result type is no longer known:

Object value = unknownFuture.get();


This is not type-safe:

String value = unknownFuture.get(); // Compilation error


A cast would be required, though it should only be used if the type is genuinely known:

String value = (String) unknownFuture.get();

7. Limitations of Future

Future is useful for representing a pending computation, but it has limited composition capabilities.

For example:

Future<Integer> future = executor.submit(() -> 100);

Integer result = future.get();


get() is a blocking call. The calling thread waits until:

The task completes
The task fails
The task is cancelled
A specified timeout expires

A plain Future does not naturally support:

Chaining transformations
Combining multiple asynchronous computations
Registering completion callbacks
Declarative exception handling
Building asynchronous pipelines

This is where CompletableFuture becomes useful.

8. CompletableFuture

CompletableFuture<T> implements two important interfaces:

Future<T>
CompletionStage<T>


The Future<T> side gives it completion-related operations such as get().

The CompletionStage<T> side gives it asynchronous composition operations such as:

thenApply
thenCompose
thenCombine
thenAccept
exceptionally

It can also be completed manually:

CompletableFuture<String> future = new CompletableFuture<>();

future.complete("SUCCESS");

9. supplyAsync()

supplyAsync() starts an asynchronous computation that returns a value.

CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 100);


It accepts a Supplier<T>:

@FunctionalInterface
public interface Supplier<T> {
    T get();
}

Without an explicit executor
CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 100);


It normally uses ForkJoinPool.commonPool().

With an explicit executor
ExecutorService executor = Executors.newFixedThreadPool(4);

CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(
        () -> 100,
        executor
    );


In production code, passing an explicit executor is often preferable because it gives you control over:

Pool size
Thread naming
Queueing behavior
Workload isolation
Shutdown
Monitoring
Rejection policy

You should avoid putting long-running blocking I/O operations into the common fork-join pool because unrelated asynchronous operations may share that pool.

10. runAsync()

If the asynchronous operation does not return a value, use runAsync().

CompletableFuture<Void> future =
    CompletableFuture.runAsync(() -> {
        System.out.println("Performing background work");
    });


It accepts a Runnable and returns:

CompletableFuture<Void>


A simple distinction is:

runAsync()    → Runnable → no result
supplyAsync() → Supplier → produces a result

11. thenApply() versus thenApplyAsync()

Both methods transform the result of the previous stage.

CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 10)
        .thenApply(value -> value * 2);


The result is:

10 → 20


The function passed to thenApply receives an input and returns an output:

Function<T, U>


Conceptually:

T → U

thenApply()
.thenApply(value -> value * 2)


The non-async version may execute:

In the thread completing the previous stage, or
In the calling thread if the previous stage has already completed

Therefore, you should not rely on thenApply() always using the exact same thread as supplyAsync().

thenApplyAsync()
.thenApplyAsync(value -> value * 2)


This schedules the transformation for asynchronous execution.

Without an explicit executor, it normally uses the default asynchronous executor.

With an explicit executor:

.thenApplyAsync(value -> value * 2, executor)

Important correction

thenApplyAsync() does not guarantee a newly created thread.

It means the work is submitted for asynchronous execution. An existing worker thread from the relevant pool will usually execute it.

A better distinction is:

thenApply      → continuation may execute inline
thenApplyAsync → continuation is scheduled asynchronously

12. thenCompose() versus thenApply()

thenCompose() is used when the transformation itself starts another asynchronous operation.

Consider:

CompletableFuture<User> findUser(int userId)


and:

CompletableFuture<List<Order>> findOrders(User user)


You can compose them:

CompletableFuture<List<Order>> ordersFuture =
    findUser(10)
        .thenCompose(user -> findOrders(user));


Execution dependency:

Find user
    ↓
Use that user to find orders
    ↓
Produce List<Order>


The generic signature is conceptually:

<U> CompletableFuture<U> thenCompose(
    Function<? super T, ? extends CompletionStage<U>> function
);


The function:

Accepts the previous result, or one of its supertypes
Returns a CompletionStage<U> or one of its subtypes
Why not use thenApply()?

Using thenApply() would produce a nested future:

CompletableFuture<CompletableFuture<List<Order>>> nested =
    findUser(10)
        .thenApply(user -> findOrders(user));


Using thenCompose() flattens it:

CompletableFuture<List<Order>> flattened =
    findUser(10)
        .thenCompose(user -> findOrders(user));


This is similar to the difference between:

map     → thenApply
flatMap → thenCompose

About ordering

thenCompose() maintains dependency ordering, not necessarily thread ordering.

Task 2 cannot begin through this chain until Task 1 produces the value required by Task 2. However, Task 1 and Task 2 may execute on different threads.

13. Understanding ? super T and ? extends CompletionStage<U>

Consider:

Function<? super T, ? extends CompletionStage<U>>

? super T

The function may accept:

T
A superclass of T
An implemented superinterface of T

For example, if T is Integer, a function accepting Number can also process it.

Function<Number, CompletableFuture<String>> function =
    number -> CompletableFuture.completedFuture(number.toString());

? extends CompletionStage<U>

The function may return:

CompletionStage<U>
CompletableFuture<U>
Another subtype of CompletionStage<U>

A useful rule is:

Consumer/input position  → ? super T
Producer/output position → ? extends T


This is often remembered as PECS:

Producer Extends, Consumer Super

14. thenAccept() and thenAcceptAsync()

thenAccept() consumes the previous result without returning another business value.

CompletableFuture<Void> future =
    CompletableFuture.supplyAsync(() -> "Order created")
        .thenAccept(message -> System.out.println(message));


It accepts a:

Consumer<T>


A Consumer<T> receives a value and returns nothing:

void accept(T value);


Therefore, thenAccept() returns:

CompletableFuture<Void>

Is it necessarily the end of the chain?

Not technically.

Because it returns a CompletableFuture<Void>, more stages can still be attached:

CompletableFuture<Void> future =
    CompletableFuture.supplyAsync(() -> "Order created")
        .thenAccept(System.out::println)
        .thenRun(() -> System.out.println("Notification completed"));


So it is better to say:

thenAccept() is a value-consuming stage, commonly used near the end of a pipeline.

The async execution distinction is the same:

thenAccept      → may execute inline
thenAcceptAsync → scheduled asynchronously

15. thenRun() and thenRunAsync()

thenRun() is useful when the next action does not need the previous result.

CompletableFuture<Void> future =
    CompletableFuture.supplyAsync(() -> "Result")
        .thenRun(() -> System.out.println("Task completed"));


Difference:

thenAccept(value -> ...) → receives the previous result
thenRun(() -> ...)       → does not receive the previous result

16. thenCombine() and thenCombineAsync()

thenCombine() combines the results of two independent completion stages.

CompletableFuture<String> userFuture =
    CompletableFuture.supplyAsync(() -> "Suhaib");

CompletableFuture<Integer> scoreFuture =
    CompletableFuture.supplyAsync(() -> 95);

CompletableFuture<String> combined =
    userFuture.thenCombine(
        scoreFuture,
        (name, score) -> name + " scored " + score
    );


The combining function is a BiFunction:

BiFunction<T, U, V>


Conceptually:

First future produces T
Second future produces U
               ↓
        BiFunction<T, U, V>
               ↓
        Combined result V


The two futures can return different types.

CompletableFuture<String>
CompletableFuture<Integer>


The combined future may return a third type:

CompletableFuture<Result>

Dependency behavior

The two stages can run independently:

Task 1 ──────┐
             ├── combine results
Task 2 ──────┘


The combination function runs only after both futures complete successfully.

As before:

thenCombine      → combining function may execute inline
thenCombineAsync → combining function is scheduled asynchronously

17. Sequential versus parallel composition
Sequential dependency with thenCompose()

Use this when Task 2 requires Task 1's output:

findUser(userId)
    .thenCompose(user -> findOrders(user));

Task 1 → Task 2

Independent parallel tasks with thenCombine()

Use this when the tasks can run independently:

loadCustomer()
    .thenCombine(loadExchangeRate(), this::createResponse);

Task 1 ─┐
        ├→ combine
Task 2 ─┘


This distinction is more important than simply thinking in terms of ordering.

18. Exception handling

A practical CompletableFuture pipeline also needs exception handling.

exceptionally()

Provides a fallback value when an earlier stage fails:

CompletableFuture<String> future =
    CompletableFuture.supplyAsync(() -> loadData())
        .exceptionally(exception -> {
            System.err.println(exception.getMessage());
            return "fallback";
        });

handle()

Processes both success and failure:

CompletableFuture<String> future =
    CompletableFuture.supplyAsync(() -> loadData())
        .handle((result, exception) -> {
            if (exception != null) {
                return "fallback";
            }

            return result.toUpperCase();
        });

whenComplete()

Observes the result or exception without normally transforming it:

CompletableFuture<String> future =
    CompletableFuture.supplyAsync(() -> loadData())
        .whenComplete((result, exception) -> {
            if (exception != null) {
                System.err.println("Failed: " + exception.getMessage());
            } else {
                System.out.println("Completed: " + result);
            }
        });


Simple distinction:

exceptionally → recover from failure
handle        → transform success or failure
whenComplete  → observe success or failure

19. Complete example with a controlled executor
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class CompletableFutureExample {

    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(4);

        try {
            CompletableFuture<String> userFuture =
                CompletableFuture.supplyAsync(
                    () -> loadUser(),
                    executor
                );

            CompletableFuture<Integer> scoreFuture =
                CompletableFuture.supplyAsync(
                    () -> loadScore(),
                    executor
                );

            CompletableFuture<String> resultFuture =
                userFuture
                    .thenCombineAsync(
                        scoreFuture,
                        (user, score) -> user + " scored " + score,
                        executor
                    )
                    .thenApply(message -> message.toUpperCase())
                    .exceptionally(exception -> {
                        System.err.println(
                            "Pipeline failed: " + exception.getMessage()
                        );
                        return "RESULT UNAVAILABLE";
                    });

            String result = resultFuture.join();
            System.out.println(result);
        } finally {
            executor.shutdown();
        }
    }

    private static String loadUser() {
        return "Suhaib";
    }

    private static int loadScore() {
        return 95;
    }
}

20. get() versus join()

Both wait for completion.

get()
String result = future.get();


It throws checked exceptions:

InterruptedException
ExecutionException

join()
String result = future.join();


It throws an unchecked:

CompletionException


A common practical guideline is:

Use get() when working with the general Future API or when checked exception handling is desired.
Use join() when working inside CompletableFuture pipelines or where an unchecked completion exception is more convenient.

Both methods block the calling thread.

21. Quick method selection guide
Need to start a task with no result?
    → runAsync()

Need to start a task that returns a result?
    → supplyAsync()

Need to synchronously transform a previous value?
    → thenApply()

Need to start another async operation using the previous value?
    → thenCompose()

Need to consume the previous value without returning a value?
    → thenAccept()

Need to execute an action without using the previous value?
    → thenRun()

Need to combine two independent async results?
    → thenCombine()

Need to recover from an exception?
    → exceptionally()

Need to process both success and failure?
    → handle()

Need to observe completion without changing the result?
    → whenComplete()

Key corrections to your original understanding
Technical accuracy
FutureTask manages the task's completion state, not ThreadPoolExecutor based on the worker thread's state.
CompletableFuture implements both Future and CompletionStage.
thenApplyAsync() does not guarantee a new thread. It schedules work through an executor.
thenApply() does not guarantee that it will always run on the same thread as the preceding stage.
thenAccept() is not strictly a termination operation because another stage can still follow it.
thenCompose() provides dependent async composition and flattens nested futures.
thenCombine() combines independent results after both stages complete.
Clarity

The key model is:

Future
    → observe or wait for one computation

CompletableFuture
    → construct, transform, combine, recover, and observe
      asynchronous computations


The most useful method distinction is:

thenApply   = T → U
thenCompose = T → CompletableFuture<U>
thenAccept  = T → void
thenRun     = () → void
thenCombine = (T, U) → V

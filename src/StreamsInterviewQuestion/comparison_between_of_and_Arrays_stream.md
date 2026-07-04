
## Deep Dive: Arrays.stream() vs Stream.of()
In our code, we can convert the str.split(" ") array into a Stream using two different methods:

// Option A
Stream.of(str.split(" "))
// Option B
Arrays.stream(str.split(" "))

Both options produce the exact same output for this problem, but Option B (Arrays.stream) is highly preferred in professional code. Here is why.
------------------------------
## 1. How they handle Object Arrays (Like String[])
When you pass a String[] array:

* Arrays.stream(array) is explicitly designed for arrays. It takes the array and builds a stream from its elements.
* Stream.of(T... values) uses Java's varargs feature. Under the hood, Java treats varargs as an array. Therefore, Stream.of(array) actually just calls Arrays.stream(array) internally.

For strings, Stream.of() is just a shortcut wrapper around Arrays.stream().
------------------------------
## 2. The Dangerous Trap: Primitive Arrays (int[], long[])
The real danger comes if you use these methods on primitive arrays. This is a very common Java interview trick question.
Suppose you have an array of integers:

int[] numbers = {1, 2, 3};

## Using Arrays.stream()

IntStream s = Arrays.stream(numbers);
System.out.println(s.count());


* Output: 3
* Why: It correctly opens the array and creates a stream of 3 individual numbers.

## Using Stream.of()

Stream<int[]> s = Stream.of(numbers);
System.out.println(s.count());


* Output: 1
* Why: Stream.of(T... values) expects an array of Objects. Because int is a primitive type (not an Object), Java does not see {1, 2, 3} as three separate objects. Instead, it treats the entire array as a single Object. You get a stream containing exactly one element: the int[] array itself.

------------------------------
## Summary Rule for Notes

* Use Stream.of() when you want to pass individual elements directly: Stream.of("a", "b", "c").
* Use Arrays.stream() when you are converting an existing array into a stream: Arrays.stream(myArray).

------------------------------
Would you like to continue building this file by adding the companion section for reversing the order of the words themselves?


# Java Advanced: Functional, Asynchronous and Reactive Programming

Summary:

1. Functional Java: lambda expression, functional interfaces, streams

2. Executor framework and Fork Join pool

3. NIO - non-blocking input/output

4. Asynchronous Java (Completable Future)

5. Reactive Streams (Java 9)

6. RxJava 2

7. R2DBC (reactive JDBC replacement)

8. Spring WebFlux (Reactor)

9. Reactive Spring Data JPA

## 1. Lambda Expression, Functional Interfaces and Method References

## 1.1. A Lambda Expression

Let’s use an anonymous class

```java
FileFilter fileFilter = new FileFilter() {
  @Override
  public boolean accept(File file)
  {
    return file.getName().endsWith(".java");
  }
};
```

We take the parameters and return:

```java
FileFilter filter = (File file) -> file.getName().endsWith(".java");
```

This is a lambda expression.

## 1.2. Several Ways of Writing a Lambda Expression

The simplest way:

```java
FileFilter filter = (File file) -> file.getName().endsWith(".java");
```

If you have more than one line of code:

```java
Runnable r = () -> {
  for (int i = 0; i < 5; i++) {
     System.out.println("Hello world!");
  }
};
```

If you have more than one argument:

```java
Comparator<String> c = (String s1, String s2) -> Integer.compare(s1.length(), s2.length());
```

## 1.3. Functional Interface

A functional interface is one interface with only one abstract method (methods from class Object do not count)

```java
public interface Runnable {
  run();
}

public interface Comparator<T> {
  int compareTo(T t1, T t2);
}

public interface FileFilter {
  boolean accept(File pathname);
}
```

A functional interface can be annotated:

```java
@FunctionalInterface
public interface MyFunctionalInterface {
  someMethod();
  /**
  * Some more documentation
  */  equals(Object o);
};
```

The annotation is here just for convenience, as the compiler will tell me whether the interface is functional or not

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/17bd8b54-bbd0-40c4-a522-ee01c684015b)

```java
Predicate<Integer> isAdult = age -> age >= 18:
isAdult.test(10);
Consumer<String> printer = p -> System.out.println("Printed: "+p);
printer.accept("hi");
Supplier<String> sayHi = () -> "hi";
sayHi.get(); //hi
```

## Omitting Parameter

```java
Comparator<String> c = (String s1, String s2) -> Integer.compare(s1.length(), s2.length());
```

```java
Comparator<String> c = (s1, s2) -> Integer.compare(s1.length(), s2.length());
```

### 1.3.1. Supplier

A Supplier provides a result of a given type. It does not accept any arguments

Definition of java.util.function.Supplier

```java
package java.util.function;

@FunctionalInterface
public interface Supplier<T> {
  T get();
}
```

Use of a Supplier:

```java
Supplier<Integer> supplier = () -> 100;
System.out.println(supplier.get());
```

Another sample:

```java
import java.util.function.Supplier;

public class SupplierExample {
    public static void main(String[] args) {
        Supplier<String> stringSupplier = () -> "Hello World";
        System.out.println(stringSupplier.get());
    }
}
```

### 1.3.2. Predicate

A Predicate takes one argument and returns a boolean

```java
package java.util.function;

@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
}
```

```java
import java.util.function.Predicate;

public class PredicateExample {
    public static void main(String[] args) {
        Predicate<Integer> isPositive = x -> x > 0;
        System.out.println(isPositive.test(5));  // true
        System.out.println(isPositive.test(-5)); // false
    }
}
```

### 1.3.3. Function

A Function takes one argument and produces a result

```java
package java.util.function;

@FunctionalInterface
public interface Function<T, R> {
  R apply(T t);
}
```

```java
import java.util.function.Function;

public class FunctionExample {
    public static void main(String[] args) {
        Function<Integer, String> convert = x -> "Number: " + x;
        System.out.println(convert.apply(5));
    }
}
```

### 1.3.4. Consumer / BiConsumer

A Consumer performs an operation on a single argument, whereas a BiConsumer takes two arguments

```java
package java.util.function;

@FunctionalInterface
public interface Consumer<T> {
  void accept(T t);
}
```

```java
import java.util.function.Consumer;
import java.util.function.BiConsumer;

public class ConsumerExample {
    public static void main(String[] args) {
        Consumer<String> printer = System.out::println;
        printer.accept("Hello Consumer!");

        BiConsumer<Integer, Integer> adder = (a, b) -> System.out.println("Sum: " + (a + b));
        adder.accept(5, 3);
    }
}
```

### 1.3.5. BiPredicate

A BiPredicate takes two arguments and returns a boolean

```java
@FunctionalInterface
public interface BiPredicate<T, U> {
    boolean test(T t, U u);
}
```

```java
import java.util.function.BiPredicate;

public class BiPredicateExample {
    public static void main(String[] args) {
        BiPredicate<Integer, Integer> compare = (a, b) -> a > b;
        System.out.println(compare.test(5, 3));  // true
        System.out.println(compare.test(2, 3));  // false
    }
}
```

### 1.3.6. Unary Operator

A UnaryOperator takes one argument and returns a result of the same type

```java
@FunctionalInterface
public interface UnaryOperator<T> extends Function<T, T> {
    // Inherits the "apply" method from Function
}
```

```java
import java.util.function.UnaryOperator;

public class UnaryOperatorExample {
    public static void main(String[] args) {
        UnaryOperator<Integer> square = x -> x * x;
        System.out.println(square.apply(5));
    }
}
```

## 1.4. Method References

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/74c2e424-4298-441d-bead-aad422b585b0)

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/20579c83-8af3-4221-9007-f11d20d34fdc)

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/0e334b77-d695-4547-840d-65b5cddadb8c)

## Reference to an Instance Method of an Arbitrary Object of a Particular Type

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/115367ee-7036-4357-9ec5-3ddf8ca6094f)

## Reference to Constructor

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/65dc8eaa-e0b6-4b64-9206-61d61b060429)

## 1.5. Data Streams

What is a **Stream**?

•	An object on which one can define operations

•	An object that does not hold any data

•	An object that should not change the data it processes

•	An object able to process data in « one pass »

•	An object optimized from the algorithm point of view, and able to process data in parallel

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/9a493946-01a3-48fd-a9ca-c2618734d01f)

## How to Create a Stream?

Using static method Stream.of():

```java
Stream.of(1,2,3);
```

From array:

```java
String[] arr = {"one","two","three"};
stream = Stream.of(arr);
```		

From collection:

```java
List<Person> persons;
Stream<Person> stream = persons.stream();
```

Using generate():

```java
Stream<String> stream = Stream.generate(() -> "test").limit(10);
```

## 1.6. Map, Filter, Reduce

### 1.6.1. Map

The map operation applies a function to each element in a stream and collects the results into a new stream

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class StreamMapExample {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("hello", "world", "java", "stream");
        List<String> upperCaseWords = words.stream()
                                           .map(String::toUpperCase)
                                           .collect(Collectors.toList());
        System.out.println(upperCaseWords);
    }
}
```

## 1.6.2. Filter

The filter operation uses a predicate to test each element in the stream and includes only those that pass the test

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class StreamFilterExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
        List<Integer> evenNumbers = numbers.stream()
                                           .filter(n -> n % 2 == 0)
                                           .collect(Collectors.toList());
        System.out.println(evenNumbers);
    }
}
```

## 1.6.3. Reduce

The reduce operation combines all elements in the stream to produce a single summary result

This operation takes two parameters: an initial value and a binary operator

```java
import java.util.Arrays;
import java.util.List;

public class StreamReduceExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
        int sum = numbers.stream()
                         .reduce(0, Integer::sum);
        System.out.println("Sum of numbers: " + sum);
    }
}
```

## 1.7. foreach (consumer)

forEach(Consumer consumer) iterates over all stream elements and applies the consumer

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}
```

Consumer<T> is a functional interface. It can be implemented by a lambda expression

```java
Consumer<T> c = p -> System.out.println(p);
 Consumer<T> c = System.out::println;
 Stream.of(1,2,3).forEach(c);
```

## 1.8. Consumer Chaining

```java
List<String> list = new ArrayList<>();
Consumer<String> c1 = s -> list.add(s);
Consumer<String> c2 = s -> System.out.println(s);

List<String> list = new ArrayList<>();
Consumer<String> c1 = list::add;
Consumer<String> c2 = System.out::println;
Consumer<String> c3 = c1.andThen(c2);
Stream.of(1,2,3).forEach(c3);
```




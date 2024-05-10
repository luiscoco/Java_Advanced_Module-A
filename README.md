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

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/0d359f74-4c5f-45d3-94e7-87bdfb03fac7)

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

This **lambda function** s -> s.toLowerCase():

```java
Function<String, String> f = s -> s.toLowerCase();
```
Can be written like **method reference** String::toLowerCase:

```java
Function<String, String> f = String::toLowerCase;
f.apply("Hi") // hi
```

This **lambda expression** s -> System.out.println(s):

```java
Consumer<String> c = s -> System.out.println(s);
```
Can be written like **method reference** System.out::println:

```java
Consumer<String> c = System.out::println;
```

This is the sample for using the above code:

```java
import java.util.List;
import java.util.function.Consumer;

public class Main {
    public static void main(String[] args) {
        // Create a list of strings
        List<String> names = List.of("Alice", "Bob", "Charlie", "David");

        // Create a Consumer instance using a method reference
        Consumer<String> printer = System.out::println;

        // Use the Consumer to print each name in the list
        names.forEach(printer);
    }
}
```

This **lambda expression** (i1, i2) -> Integer.compare(i1, i2):

```java
Comparator<Integer> c = (i1, i2) -> Integer.compare(i1, i2);
```

Can be written like **method reference** Integer::compare:

```java
Comparator<Integer> c = Integer::compare;
```

This is a sample for the Comparator code:

```java
import java.util.List;
import java.util.Collections;
import java.util.Comparator;

public class Main {
    public static void main(String[] args) {
        // Create a list of integers
        List<Integer> numbers = List.of(10, 5, 20, 1, 15);

        // Create a Comparator instance using a method reference
        Comparator<Integer> comparator = Integer::compare;

        // Sort the list using the Comparator
        List<Integer> sortedNumbers = new ArrayList<>(numbers); // Creating a modifiable list
        Collections.sort(sortedNumbers, comparator);

        // Print the sorted list
        System.out.println(sortedNumbers);
    }
}
```

**Another Method reference sample**

However, this method to compare the birth dates of two Person instances already exists as Person.compareByAge

You can invoke this method with a **lambda expression**:

```java
Arrays.sort(rosterAsArray, (a, b) -> Person.compareByAge(a, b));
```

```java
Arrays.sort(rosterAsArray, (Person a, Person b) ->
{
    return a.getBirthday().compareTo(b.getBirthday());
});
```

Because this lambda expression invokes an existing method, you can use a **method reference** instead of a **lambda expression**:

```java
Arrays.sort(rosterAsArray, Person::compareByAge);
```

The **method reference Person::compareByAge** is semantically the **same as the lambda expression (a, b) -> Person.compareByAge(a, b)**

This table categorizes and exemplifies the different **types of method references** in Java,

which are used to simplify the syntax of lambda expressions when a method already exists to perform the operation

![image](https://github.com/luiscoco/Java_Advanced/assets/32194879/2602e7eb-8a20-4763-b25c-2de77547b72b)

Here are simple Java examples for each **type of method reference** described:

**Reference to a Static Method**

```java
import java.util.function.Function;

public class Utils {
    public static String toUpperCase(String input) {
        return input.toUpperCase();
    }

    public static void main(String[] args) {
        Function<String, String> upperFunc = Utils::toUpperCase;
        System.out.println(upperFunc.apply("hello"));
    }
}
```

**Reference to an Instance Method of a Particular Object**

```java
import java.util.function.Supplier;

public class Example {
    private String message = "Hello, World!";

    public String getMessage() {
        return message;
    }

    public static void main(String[] args) {
        Example example = new Example();
        Supplier<String> messageSupplier = example::getMessage;
        System.out.println(messageSupplier.get());
    }
}
```

**Reference to an Instance Method of an Arbitrary Object of a Particular Type**

```java
import java.util.List;
import java.util.function.Consumer;

public class Main {
    public static void main(String[] args) {
        List<String> names = List.of("Alice", "Bob", "Charlie");
        Consumer<String> action = String::toLowerCase;
        names.forEach(name -> System.out.println(action.accept(name)));
    }
}
```

**Note**: This example demonstrates invoking the toLowerCase method on each string element of the list individually. It prints the name in lower case.

**Reference to a Constructor**

```java
import java.util.function.Function;
import java.util.ArrayList;

public class Main {
    public static void main(String[] args) {
        Function<Integer, ArrayList<String>> listCreator = ArrayList::new;
        ArrayList<String> strings = listCreator.apply(10); // Initializes an ArrayList with initial capacity of 10
        System.out.println(strings);
    }
}
```

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

### 1.6.2. Filter

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

### 1.6.3. Reduce

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

## 1.9. Predicates Combination

```java
Predicate<String> p1 = s -> s.length() < 20;
Predicate<String> p2 = s -> s.length() > 10;

Predicate<String> p3 = p1.and(p2);
```

## 1.10. Predicate.isEqual()

```java
Predicate<String> id = Predicate.isEqual(target);
```

## 1.11. Predicates

```java
List<String> list = new ArrayList<>();
Stream<Person> stream = list.stream();
Stream<Person> filtered = stream.filter(person -> person.getAge() > 20);
```

```java
Predicate<Person> p = person -> person.getAge() > 20;
```

```java
Predicate<Integer>	p1	=	i	->	i	> 20;
Predicate<Integer>	p2	=	i	->	i	< 30;
Predicate<Integer>	p3	=	i	->	i	== 0;


Predicate<Integer>	p = p1.and(p2).or(p3); // (p1 && p2) || p3	
Predicate<Integer>	p = p3.or(p1).and(p2); // p3 || (p1 && p2)	=>	(p3 OR p1) && p2
```

### Example using Predicate<Person>

First, let's create a Person class and then use the provided ```Predicate<Person>``` to filter out persons over the age of 20

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Collectors;

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "Person{" +
               "name='" + name + '\'' +
               ", age=" + age +
               '}';
    }
}

public class PredicatePersonExample {
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("John", 22),
            new Person("Sarah", 20),
            new Person("Jane", 19),
            new Person("Greg", 32)
        );

        Predicate<Person> p = person -> person.getAge() > 20;
        List<Person> filteredPeople = people.stream()
                                            .filter(p)
                                            .collect(Collectors.toList());
        System.out.println(filteredPeople);
    }
}
```

### Example using Predicate<Integer>

Now let's apply the Predicate<Integer> to filter a list of integers based on complex logical conditions

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Collectors;

public class PredicateIntegerExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(0, 10, 20, 25, 30, 35);

        Predicate<Integer> p1 = i -> i > 20;
        Predicate<Integer> p2 = i -> i < 30;
        Predicate<Integer> p3 = i -> i == 0;

        Predicate<Integer> p = p1.and(p2).or(p3);  // (p1 && p2) || p3
        List<Integer> filteredNumbers = numbers.stream()
                                               .filter(p)
                                               .collect(Collectors.toList());
        System.out.println("Filtered using (p1 && p2) || p3: " + filteredNumbers);

        Predicate<Integer> pAlternative = p3.or(p1).and(p2);  // p3 || (p1 && p2) => (p3 OR p1) && p2
        List<Integer> filteredNumbersAlt = numbers.stream()
                                                  .filter(pAlternative)
                                                  .collect(Collectors.toList());
        System.out.println("Filtered using p3 || (p1 && p2): " + filteredNumbersAlt);
    }
}
```

In these examples:

The PredicatePersonExample filters persons based on their age being greater than 20

The PredicateIntegerExample demonstrates how to chain and combine predicates to create more complex conditions for filtering integers, which include checking whether numbers are between 20 and 30 or equal to 0

### Another Predicate sample

```java
Predicate<String> p = Predicate.isEqual("two") ;
Stream<String> stream1 = Stream.of("one", "two", "three");
Stream<String> stream2 = stream1.filter(p.negate()) ;
```

**First: Creating a Predicate**

```java
Predicate<String> p = Predicate.isEqual("two");
```

This line creates a Predicate<String> instance named p

The Predicate.isEqual("two") method returns a predicate that tests whether an object is equal to the string "two"

In this case, the predicate will return true for any string that exactly matches "two"

**Creating a Stream**

```java
Stream<String> stream1 = Stream.of("one", "two", "three");
```

This line creates a Stream<String> containing three strings: "one", "two", and "three"

Streams are a sequence of elements supporting sequential and parallel aggregate operations

**Applying the Predicate**

```java
Stream<String> stream2 = stream1.filter(p.negate());
```

Here, the filter method is used on the stream1 object, which contains the three strings

The filter method takes a predicate to determine which elements should remain in the resulting stream

In this case, p.negate() is used, which returns a predicate that is the logical negation of the original predicate p

**p.negate() will therefore return true for any string that is not "two"**

Hence, it filters out the string "two" and keeps strings that do not match "two"



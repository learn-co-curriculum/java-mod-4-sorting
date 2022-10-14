# Sorting and Comparative Ordering 

## Learning Goals

- Introduce sorting and comparative ordering
- Define the `java.lang.Comparable` interface
- Create a class that implements `java.lang.Comparable`
- Define the `java.util.Comparator` functional interface
- Create a class that implements `java.util.Comparator`

## Introduction

We often need to sort the elements of an array or collection by rearranging
the elements in ascending or descending order.
There are many algorithms for sorting, but a general technique is
to compare pairs of elements and swap them if they are out of order. 

In Java, the natural ordering for data types `int`, `double`, and `long`
is ascending numeric order, while the natural ordering for `String` is defined to be lexicographical (alphabetic) order.

| Array                                               | Natural order                 | 
|-----------------------------------------------------|-------------------------------|
| `int[] nums = {7, 12, -4, 7, 0};`                   | `{-4, 0, 7, 7, 12}`           | 
| `String[] names = {"pat",  "amir", "chris", "al"};` | `{"al", "amir", "chris", "pat"}` |

Java does not know how to order the instances of a new class unless the programmer explicitly defines an order.
Assume an `Employee` class has instance variables for name, age, and salary.
Is there a natural ordering for employees?  Should we sort employees by name or age or salary?

We can define a natural order for a class by having the class implement the `java.lang.Comparable` interface.
We can also define one or more alternative ways to order class instances by 
creating new classes that implement the `java.util.Comparator` functional interface.

## Alphabetic Ordering of Strings 

Java provides several methods for sorting an array or a list based on the natural order of a data type:

| Method                        | Description                                                                                 | 
|-------------------------------|---------------------------------------------------------------------------------------------|
| `void Arrays.sort(a)`         | Sort array `a` into ascending order,<br>according to the natural ordering of its elements   | 
| `void Collections.sort(l)`    | Sorts list `l` into ascending order,<br>according to the natural ordering of its elements   | 

The `Arrays.sort` method sorts an array into ascending order, based on the natural order
defined for the data type.  The method sorts an array of `int` in ascending numeric values, 
while an array of `String` is sorted in alphabetic order.
The `Collections.sort` method is similarly used to sort a list in ascending order.

```java
int[] nums = {7, 12, -4, 0};
Arrays.sort(nums);
System.out.println(Arrays.toString(nums));  //-4, 0, 7, 1

List<Integer> nums = Arrays.asList(7, 12, -4, 0);
Collections.sort(nums);
System.out.println(nums);  //-4, 0, 7, 1
```

We can use the relational operators `<`,  and `>` to compare
values of primitive numeric data types such as `int`, `double`, and `long`.
But what if we need to compare two strings to see if they are in order?

| Java Expression  | Boolean Value                                                      | 
|------------------|--------------------------------------------------------------------|
| `7 < 5`          | `false`                                                            | 
| `-4 < -2`        | `true`                                                             | 
| `3.5 < 9.28`     | `true`                                                             | 
| `"pat" < "amir"` | Compiler error: operator '<' cannot be applied to java.lang.String |

The relational operators `<` and `>` do not know how to operate on strings or other non-numeric objects. 
We will compare strings and other objects by implementing
a comparison method rather than using the `<` and `>` operators.

### The `java.lang.Comparable` interface

To define a natural ordering between objects, a class must implement the `java.lang.Comparable` interface.
The interface defines a single abstract method `int compareTo(T o)`, where type `T` represents the class
whose natural order is being defined:

```java
interface Comparable<T> {
    int compareTo(T o);
}
```

The `compareTo` method compares `this` object with the parameter object `o` for order.
The method returns a negative integer, zero, or a positive integer as this object is less than,
equal to, or greater than the specified parameter object.

| this.compareTo(o)                    | Method return      |
|--------------------------------------|--------------------|
| this < o (objects are in order)      | a negative integer |
| this equals o (objects are in order) | zero               |
| this > o (objects are out of order)  | a positive integer |

The `String` class implements the `Comparable` interface by
overriding the `compareTo` method to induce an alphabetical order among strings.
The comparison is based on the Unicode value of each character in the string.

```java
String s1 = "apple";
String s2 = "bee";
String s3 = "almond";
String s4 = "beetle";

//Use String.compareTo to compare strings based on alphabetical order
System.out.println(s1.compareTo(s2));   //negative number:  apple < bee
System.out.println(s2.compareTo(s3));   //positive number:  bee > almond
System.out.println(s1.compareTo(s3));   //positive number: apple > almond
System.out.println(s2.compareTo(s4.substring(0,3))); //0:  bee == bee
System.out.println("A".compareTo("a"));  //negative number: uppercase precedes lowercase
```

The output is shown below.  The magnitude of the value is not important,
rather, the sign (positive, 0, negative) is what tells us whether the strings are in order.
A negative value or zero indicate the pair of strings is in alphabetical order,
while a positive value indicates out of order.

```text
-1
1
4
0
-32
```

A sorting method such as `Arrays.sort()`  will call the `compareTo` method to check
the order of pairs of strings, and swaps when they are out of order:

```java
String[] names = {"fred",  "bob", "chris", "albert"};
Arrays.sort(names);
System.out.println(Arrays.toString(names));  //albert, bob, chris, fred
```

Primitive wrapper classes such as `Integer` and `Double` also implement the `Comparable` interface.
However, the `compareTo` method must be invoked on an object, not a primitive value. 
To compare primitive values you can use the `Integer.compare(int,int)`
and `Double.compare(double, double)` methods, passing the values as parameters.

```java
//Comparing Integer objects
Integer x = 2;
Integer y = 10;
Integer z = 5;
System.out.println(x.compareTo(y));     //  -1: x < y
System.out.println(y.compareTo(z));     //   1: y > z
System.out.println(z.compareTo(y/x));   //   0: z == y/x

// Comparing int values
int a = 4;
int b = 8;
int c = 2;
System.out.println(Integer.compare(a, b));    //  -1: a < b
System.out.println(Integer.compare(b, c));    //   1: b > c
System.out.println(Integer.compare(c, b/a));  //   0: c == b/a
```

Use the appropriate comparison method based on the variable data type:

| Variable declaration | Comparison method         |
|----------------------|---------------------------|
| String s1, s2;       | s1.compareTo(s2);         |
| int i1, i2;          | Integer.compare(i1, i2);  |
| double d1, d2;       | Double.compare(d1, d2);   |


## Implementing `java.lang.Comparable`

Assume we have a `Book` class with instance variables for the title and isbn.

```java
public class Book {
    private String title;
    private String isbn;

    public Book(String title, String isbn) {
        super();
        this.title = title;
        this.isbn = isbn;
    }

    @Override
    public String toString() {
        return "Book [title=" + title + ", isbn=" + isbn + "]";
    }
}
```

We can create an array of books and call `Arrays.sort()` as shown in the code below.
The code will compile without error; however, program execution will result in a runtime exception because the method
`Arrays.sort` expects a class that implements `java.lang.Comparable`.

```java
public static void main(String[] args) {
    Book[] books = { new Book("The Shining", "9780385121675"), 
        new Book("A Brief History of Time", "9780593015186"),
        new Book("Learn to knit", "9001234567899")};
    Arrays.sort(books);
    System.out.println(Arrays.toString(books));
}
```

The `Book` class needs to implement `java.lang.Comparable` and override the `compareTo` method
to provide a natural order for books.
Should we order books by isbn or title?  It does not make
sense to order by isbn since that is simply a unique identifier, 
but it could be useful to order alphabetically by title.
As `title` is a string, we will use the `String.compareTo` method to 
compare the current book's title with the title of the book passed as a parameter:

```java
public class Book implements Comparable<Book>{
    private String title;
    private String isbn;

    //constructor, toString, ...
    
    //Natural ordering based on title
    @Override
    public int compareTo(Book o) {
        return this.title.compareTo(o.title);
    }
}
```

The sample code below shows how to call the `compareTo` method on one book object,
passing a second book object as a parameter.
The method will return a negative integer, zero, or a positive integer to indicate whether
the titles of two books are in alphabetic order.

```java
Book book1 =  new Book("The Shining", "9780385121675");
Book book2 = new Book("A Brief History of Time", "9780593015186");
Book book3 = new Book("Learn to knit", "9001234567899");
//compare books by alphebetic order of title
System.out.println(book1.compareTo(book2));  //positive number: "The Shining" > "A Brief History of Time"
System.out.println(book2.compareTo(book3));  //negative number: "A Brief History of Time" < "Learn to knit"
```

The `Array.sort` method can compare books by title now that `Book` implements `Comparable`,
resulting in an array sorted by title:

```text
Book [title=A Brief History of Time, isbn=9780593015186], 
Book [title=Learn to knit, isbn=9001234567899]
Book [title=The Shining, isbn=9780385121675]
```

As another example, consider a `BankAcount` class with instance variables for the account balance
and owner's social security number.  We will order bank accounts by comparing their balance.
Since `balance` has type `double`,  the `Double.compare(double, double)` method is used to compare balances.

```java
public class BankAccount implements Comparable<BankAccount>{
    private double balance;
    private String ownerSSN;
    
    //constructors, toString, etc...
    
    //Natural ordering is based on balance
    @Override
    public int compareTo(BankAccount o) {
        return Double.compare(this.balance, o.balance);
    }
}
```

## Implementing `java.util.Comparator`

Not every data type has a natural order and in fact many classes do not implement `java.lang.Comparable`.
It may also be the case that a class may require more than one way to order its instances.

For example, alphabetic order is the natural ordering for the `String` class.
What if we wanted to sort strings based on their length?

The `java.util.Comparator` interface lets us create an alternative way to order a class.
The interface defines a single abstract method `int compare(T o, T o2)`, along with
several static and default methods.  A functional interface may have default and 
static methods, but it should have only one abstract method (aside from those inherited from `Object`).

```java
@FunctionalInterface
interface Comparator<T> {
    int compare(T o1, T o2);
    //static and default methods ...
}
```

The `compare` method returns a negative integer, zero, or a positive integer
as object `o1` is less than, equal to, or greater than object `o2`.

Java's sorting methods can be called passing an object that implements `java.util.Comparator`
as an optional second parameter to provide a particular scheme for ordering the objects:

| Method                        | Description                                                                                  | 
|-------------------------------|----------------------------------------------------------------------------------------------|
| `void Arrays.sort(a, c)`      | Sort array `a` into ascending order,<br> according to the ordering induced by comparator `c` | 
| `void Collections.sort(l, c)` | Sorts list `l` into ascending order,<br> according to the ordering induced by comparator `c` | 

As an example, the class `StringLengthComparator` shown below implements `java.util.Comparator`.
The `compare` method compares the length of the two strings passed as parameters:

```java
import java.util.Comparator;

public class StringLengthComparator implements Comparator<String>{
	@Override
	public int compare(String o1, String o2) {
		return Integer.compare(o1.length(), o2.length());
	}
}
```

We can create an instance of `StringLengthComparator()` and call the `compare` method
to compare two strings by length:

```java
String s1 = "pat";
String s2 = "christopher";
String s3 = "bob";

Comparator<String> comparator = new StringLengthComparator();
System.out.println(comparator.compare(s1, s2));  //-1  (length of pat < length of christopher)
System.out.println(comparator.compare(s2, s3));  //1  (length of christopher > length of bob)
System.out.println(comparator.compare(s1, s3));  //0 (length of pat == length of bob)
```

An instance of `StringLengthComparator` can be passed to `Arrays.sort`
to sort strings by length rather than alphabetical:

```java
String[] names = new String[] {"fred",  "bob", "chris", "albert"};	
StringLengthComparator lengthComparator = new StringLengthComparator();
Arrays.sort(names,lengthComparator);  //pass in a comparator to determine ordering
System.out.println(Arrays.toString(names)); //[bob, fred, chris, albert]
```

The output shows the array sorted in ascending order of string length:

```text
[bob, fred, chris, albert]
```

### Multiple orderings

The `java.util.Comparator` interface lets us define multiple ways to order
the instances of a class.
Consider the `Employee` class with instance variables for name, age, and salary:

```java
public class Employee {
    private String name;
    private int age;
    private double salary;

    //constructor, get/set methods, toString
    ...
}
```

We can define a new class `AgeComparator` that implements `java.util.Comparator` and
overrides the `compare` method to order employees by age:

```java
import java.util.Comparator;

public class AgeComparator implements Comparator<Employee> {
    @Override
    public int compare(Employee o1, Employee o2) {
        return Integer.compare(o1.getAge(), o2.getAge());
    }
}
```

Pass an instance of `AgeComparator` as a second parameter to the `Arrays.sort` method to sort employees by age:

```java
Employee[] employees = {
        new Employee("123456789", 30, 48000.0),
        new Employee("999999999", 22, 50000.0),
        new Employee("858585858",40, 90000.0),
        new Employee("3333333333",22, 45000.0)};

Arrays.sort(employees, new AgeComparator());
System.out.println("Sorted by age: " +  Arrays.toString(employees));
```

To order employees by salary, create another class that implements `java.util.Comparator`:

```java
import java.util.Comparator;

public class SalaryComparator implements Comparator<Employee> {
    @Override
    public int compare(Employee o1, Employee o2) {
        return Double.compare(o1.getSalary(), o2.getSalary());
    }
}
```

## Is `java.lang.Comparable` a functional interface?

<table>
<tr>
<td>
<pre>
<code>

interface Comparable<T> {
    int compareTo(T o);
}

</code>
</pre>
</td>

<td>
<pre>
<code>

@FunctionalInterface
interface Comparator<T> {
    int compare(T o1, T o2);
    //static and default methods ...
}

</code>
</pre>
</td>
</tr>
</table>



The `Comparator` interface is annotated with `@FunctionalInterface`, yet the `Comparable`  interface is not.
While both interfaces define a single abstract method, the `Comparable` interface does not quite meet
the concept of a function.  

A function computes a value from its parameters, which is what the `compare` method in `Comparator` does
as it compares the two parameter objects `o1` and `o2`.

The `compareTo` method in `Comparable` is an instance method that compares the object referenced by `this`
with the object `o` passed as a parameter.  The `compareTo` method is not considered a function since it
computes a result using the variable `this`, which is not passed as a parameter.
`Comparable` is not annotated with `@FunctionalInterface` as its implementation
is that of an instance method rather than a function.

## Summary

Java provides several methods for sorting an array or collection:

| Method                        | Description                                                                                 | Interface              |
|-------------------------------|---------------------------------------------------------------------------------------------|------------------------|
| `void Arrays.sort(a)`         | Sort array `a` into ascending order,<br>according to the natural ordering of its elements   | `java.lang.Comparable` |
| `void Collections.sort(l)`    | Sorts list `l` into ascending order,<br>according to the natural ordering of its elements   | `java.lang.Comparable` |
| `void Arrays.sort(a, c)`      | Sort array `a` into ascending order,<br>according to the ordering induced by comparator `c` | `java.util.Comparator` |
| `void Collections.sort(l, c)` | Sorts list `l` into ascending order,<br>according to the ordering induced by comparator `c` | `java.util.Comparator` |


The `java.lang.Comparable` interface is used to define a natural order for a class.

```java
interface Comparable<T> {
    int compareTo(T o);
}
```

A class directly implements `java.lang.Comparable` and overrides the `compareTo` method,
comparing `this` object with the parameter object `o` for order.
The `compareTo` method returns a negative integer, zero, or a positive integer as this object is less than,
equal to, or greater than the specified parameter object.


```java
public class Book implements Comparable<Book> {
    private String title;
    private String isbn;
    
    //constructor, toString, etc
    ...
    
    //Natural ordering based on title
    @Override
    public int compareTo(Book o) {
        return this.title.compareTo(o.title);
    }
}
```

The `java.util.Comparator` interface is used to induce a specific order on a class.

```java
@FunctionalInterface
interface Comparator<T> {
    int compare(T o1, T o2);
}
```

We can define multiple comparators for a class.  Each comparator is defined
in a separate class that implements `java.util.Comparator`, overriding 
the `compare` method to indicate the order of the two objects passed as parameters:

```java
import java.util.Comparator;

public class AgeComparator implements Comparator<Employee> {
    @Override
    public int compare(Employee o1, Employee o2) {
        return Integer.compare(o1.getAge(), o2.getAge());
    }
}
```

## Resources

- [java.util.Arrays](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Arrays.html)
- [java.util.Collections](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Collections.html)
- [java.lang.Comparable](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Comparable.html)
- [java.util.Comparator](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Comparator.html)
- [java.lang.String](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html)

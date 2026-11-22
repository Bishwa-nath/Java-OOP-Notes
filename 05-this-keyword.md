# Topic 5: The `this` Keyword

> **Level:** Beginner | **Prerequisites:** [Topic 4: Constructor](04-constructor.md)

## Table of Contents
1. [Overview](#1-overview)
2. [Use 1: Separate Field from Parameter](#2-use-1-separate-field-from-parameter)
3. [Use 2: Call Another Constructor `this(...)`](#3-use-2-call-another-constructor-this)
4. [Use 3: Call Another Method of the Same Object](#4-use-3-call-another-method-of-the-same-object)
5. [Use 4: Pass the Current Object as an Argument](#5-use-4-pass-the-current-object-as-an-argument)
6. [Use 5: Return `this` (Method Chaining)](#6-use-5-return-this-method-chaining)
7. [Where `this` Cannot Be Used](#7-where-this-cannot-be-used)
8. [Complete Example](#8-complete-example)
9. [Tips and Tricks](#9-tips-and-tricks)
10. [Common Mistakes](#10-common-mistakes)
11. [Interview Questions and Answers](#11-interview-questions-and-answers)
12. [Practice Exercises](#12-practice-exercises)
13. [Summary](#13-summary)

---

## 1. Overview

`this` is a **reference to the current object**, the object whose method or constructor is running right now.

> **Real-life:** When you say "**I** am hungry", the word "I" means *you*. When your friend says "**I** am hungry", "I" means *your friend*. `this` works the same way: it always means "the object that is speaking (running this code) right now".

```java
Book a = new Book();
Book b = new Book();
a.show();   // inside show(), 'this' is a
b.show();   // inside show(), 'this' is b
```

---

## 2. Use 1: Separate Field from Parameter

When a parameter has the same name as a field, the parameter **shadows** the field. Use `this.` to refer to the field.

```java
class Rectangle {
    double width;
    double height;

    Rectangle(double width, double height) {
        this.width = width;      // field = parameter
        this.height = height;
    }
}
```

Without `this`:
```java
Rectangle(double width, double height) {
    width = width;     // parameter assigned to itself! field stays 0.0
}
```

---

## 3. Use 2: Call Another Constructor `this(...)`

Constructor chaining: reuse one constructor from another **in the same class**.

```java
class Book {
    String title, author;
    double price;

    Book() {
        this("Untitled", "Unknown", 0.0);    // calls the 3-arg constructor
    }

    Book(String title, String author, double price) {
        this.title = title;
        this.author = author;
        this.price = price;
    }
}
```

**Rules**
- `this(...)` must be the **first statement** in the constructor (traditional rule, still expected in interviews).
- You cannot create a **loop** of constructor calls (A calls B, B calls A): compile error.
- You cannot use both `this(...)` and `super(...)` in the same constructor; each wants to be first.

> **Note:** Very new Java versions (Java 25 and later) relax this rule slightly, allowing some statements that don't use `this` before `this(...)`/`super(...)`. For learning and interviews, treat "first statement" as the rule.

---

## 4. Use 3: Call Another Method of the Same Object

`this.method()` and `method()` are the same inside an instance method. Using `this.` just makes it explicit.

```java
void show() {
    System.out.println("Area = " + this.area());   // same as area()
}
```

---

## 5. Use 4: Pass the Current Object as an Argument

Sometimes an object needs to hand *itself* to another method or class.

```java
class Printer {
    static void print(Book b) {
        System.out.println("Printing: " + b.getTitle());
    }
}

class Book {
    private String title;
    Book(String title) { this.title = title; }
    String getTitle() { return title; }

    void printSelf() {
        Printer.print(this);     // pass this very object
    }
}
```

> **Real-life:** Handing over *your own* ID card to the security guard.

---

## 6. Use 5: Return `this` (Method Chaining)

If a method returns `this`, you can call the next method on the result in one line. This is called **method chaining** (a "fluent" style).

```java
class Book {
    private String title;
    private String author;
    private double price;

    Book setTitle(String title)   { this.title = title;   return this; }
    Book setAuthor(String author) { this.author = author; return this; }
    Book setPrice(double price)   { this.price = price;   return this; }

    void print() { System.out.println(title + " by " + author + " ($" + price + ")"); }
}
```

```java
new Book()
    .setTitle("Clean Code")
    .setAuthor("Robert C. Martin")
    .setPrice(40.0)
    .print();
```

This style is used widely: `StringBuilder.append("a").append("b")`, builders, and many libraries.

---

## 7. Where `this` Cannot Be Used

`this` needs an object. **Static** methods and static blocks belong to the class, not to any object, so `this` is not available.

```java
class Test {
    int x = 5;
    static void show() {
        // System.out.println(this.x);   // Compile error: no 'this' in static context
    }
}
```

---

## 8. Complete Example

```java
// FILE: Main.java
class Printer {
    static void print(Book b) {
        System.out.println("Printing -> " + b.getTitle());
    }
}

class Book {
    private String title;
    private String author;
    private double price;

    Book() {
        this("Untitled", "Unknown", 0.0);           // constructor chaining
    }

    Book(String title, String author, double price) {
        this.title = title;                           // this to separate field and parameter
        this.author = author;
        this.price = price;
    }

    Book setTitle(String title)   { this.title = title;   return this; }
    Book setAuthor(String author) { this.author = author; return this; }
    Book setPrice(double price)   { this.price = price;   return this; }

    String getTitle() { return this.title; }

    void printSelf() {
        Printer.print(this);                          // pass current object
    }

    void show() {
        System.out.println(title + " by " + author + " ($" + price + ")");
    }
}

class Rectangle {
    private double width;
    private double height;

    Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    Rectangle scale(double factor) {
        this.width *= factor;
        this.height *= factor;
        return this;                                  // allows chaining
    }

    double area() { return width * height; }

    void show() {
        System.out.println(width + " x " + height + " | area = " + this.area());
    }
}

public class Main {
    public static void main(String[] args) {
        new Book().show();                            // uses this("Untitled", ...)

        Book b = new Book()
                .setTitle("Clean Code")
                .setAuthor("Robert C. Martin")
                .setPrice(40.0);                      // method chaining
        b.show();
        b.printSelf();

        Rectangle r = new Rectangle(2, 3);
        r.scale(2).scale(1.5).show();                 // chain: 2x3 -> 4x6 -> 6x9
    }
}
```

**Output**
```
Untitled by Unknown ($0.0)
Clean Code by Robert C. Martin ($40.0)
Printing -> Clean Code
6.0 x 9.0 | area = 54.0
```

---

## 9. Tips and Tricks

- **Always use `this.field = field;` in constructors and setters.** It is the standard Java style and avoids the "assigned to itself" bug.
- **Use `this(...)` to build "one main constructor, others delegate".** Put the validation in the main constructor only.
- **Return `this` for fluent APIs**, but only when it improves readability (builders, configuration objects).
- **`this` is just a reference.** You can print it (`System.out.println(this)`), compare it (`this == other`), or pass it around.
- **Quick check:** if a method is `static`, there is no `this`. If you get an error about "non-static ... cannot be referenced from a static context", you are missing an object.
- **Compare objects of the same class:**
  ```java
  boolean isBiggerThan(Rectangle other) {
      return this.area() > other.area();   // 'this' vs 'other'
  }
  ```
- **Lambda note (preview):** inside a lambda, `this` refers to the enclosing object, but inside an anonymous class, `this` refers to the anonymous object. This is a classic advanced interview point.

---

## 10. Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| `width = width;` in a constructor | Assigns parameter to itself; field is unchanged | `this.width = width;` |
| `this(...)` is not the first statement | Compile error (traditional Java) | Move it to the first line |
| Circular `this(...)` calls | Compile error "recursive constructor invocation" | Break the cycle |
| Using `this` in a `static` method | Compile error | Pass an object in, or make the method non-static |
| Returning `this` but declaring `void` | Chaining impossible | Change the return type to the class name |

---

## 11. Interview Questions and Answers

**Q1. What is the `this` keyword?**
A reference variable that points to the current object, the object on which the method or constructor is being executed.

**Q2. What are the uses of `this`?**
(1) Differentiate fields from parameters, (2) call another constructor with `this(...)`, (3) call the current object's methods, (4) pass the current object as an argument, (5) return the current object for method chaining.

**Q3. What is constructor chaining? How do you achieve it?**
Calling one constructor from another. Within the same class, use `this(...)`; to call a parent constructor, use `super(...)`.

**Q4. Why must `this(...)` be the first statement of a constructor?**
Because the called constructor should initialize the object before any other code in the current constructor runs, which keeps initialization order predictable. (Newer Java versions allow limited statements before it, but the classic rule is "first statement".)

**Q5. Can we use `this` inside a static method? Why not?**
No. Static methods belong to the class and can run without any object, so there is no "current object" for `this` to refer to.

**Q6. Can we use `this()` and `super()` together in one constructor?**
No. Each must be the first statement, so only one can be used.

**Q7. What happens if you don't use `this` when parameter and field have the same name?**
The parameter shadows the field, so `name = name;` assigns the parameter to itself and the field is not changed.

**Q8. What is method chaining? How does `this` help?**
Calling several methods in one statement, `obj.a().b().c()`. Each method returns `this`, so the next call is made on the same object.

**Q9. Can `this` be assigned a new value (`this = new Book();`)?**
No. `this` is effectively final and cannot be reassigned.

**Q10. Can `this` be used to call a constructor from a normal method?**
No. `this(...)` can only be used inside constructors.

**Q11. Output question:**
```java
class A {
    int x = 10;
    A(int x) { x = x; }
}
System.out.println(new A(50).x);
```
**Answer:** `10`. The line `x = x;` assigns the parameter to itself. The field keeps its initial value of `10`.

---

## 12. Practice Exercises

1. Rewrite the `Rectangle` class so the constructor uses `this.width = width`, add a no-arg constructor that calls `this(1, 1)`.
2. Add `setWidth` and `setHeight` methods that return `this`, then create and print a rectangle in a single chained statement.
3. Add `boolean isBiggerThan(Rectangle other)` using `this` and `other`.
4. Create a `Counter` class with an `increment()` method that returns `this`, and chain `increment().increment().increment()`.

---

## 13. Summary

- `this` = reference to the **current object**.
- Uses: separate field/parameter, `this(...)` constructor chaining, call own methods, pass itself, return itself for chaining.
- `this(...)` goes first in a constructor; no `this` in static context.

**Previous:** [Topic 4](04-constructor.md) | **Next:** [Topic 6: Encapsulation](06-encapsulation.md)

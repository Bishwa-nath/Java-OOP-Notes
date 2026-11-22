# Topic 4: Constructor

> **Level:** Beginner | **Prerequisites:** [Topic 3: Field and Method](03-field-and-method.md)

## Table of Contents
1. [Overview](#1-overview)
2. [Rules of a Constructor](#2-rules-of-a-constructor)
3. [Default Constructor](#3-default-constructor)
4. [Parameterized Constructor](#4-parameterized-constructor)
5. [Constructor Overloading](#5-constructor-overloading)
6. [Copy Constructor](#6-copy-constructor)
7. [Initialization Order](#7-initialization-order)
8. [Complete Example](#8-complete-example)
9. [Tips and Tricks](#9-tips-and-tricks)
10. [Common Mistakes](#10-common-mistakes)
11. [Interview Questions and Answers](#11-interview-questions-and-answers)
12. [Practice Exercises](#12-practice-exercises)
13. [Summary](#13-summary)

---

## 1. Overview

A **constructor** is a special block of code that runs **automatically when an object is created** with `new`. Its job is to **initialize** the object so it starts in a valid state.

> **Real-life:** When a new phone is manufactured, it is created with a model name, a factory-reset state, and 100% setup. You do not get a phone with random garbage inside. A constructor does the same for objects.

Without constructors you must set every field manually after creating the object, which is easy to forget:

```java
Book b = new Book();
b.title = "Clean Code";   // what if we forget to set author or price?
```

With a constructor, the object is ready the moment it exists:

```java
Book b = new Book("Clean Code", "Robert C. Martin", 40.0);
```

---

## 2. Rules of a Constructor

1. The name **must be exactly the same as the class name**.
2. It has **no return type** (not even `void`).
3. It is called **automatically** by `new`; you cannot call it like a normal method.
4. A class can have **many constructors** (overloading) as long as their parameters differ.
5. Constructors can use access modifiers (`public`, `private`, `protected`, default).
6. Constructors **cannot** be `static`, `final`, or `abstract`.
7. Constructors are **not inherited** by subclasses.

```java
class Book {
    Book() { }            // constructor (no return type)
    void Book() { }       // NOT a constructor! It is a normal method (legal but confusing)
}
```

---

## 3. Default Constructor

If you write **no constructor at all**, the compiler adds a hidden **no-argument constructor** that does nothing special (fields get default values).

```java
class Car {
    String brand;
}
Car c = new Car();   // works because compiler added: Car() { }
```

> **Important:** The moment you write **any** constructor yourself, the compiler **no longer** adds the default one.

```java
class Car {
    String brand;
    Car(String brand) { this.brand = brand; }
}
Car c = new Car();   // ERROR: no no-arg constructor exists anymore
```

If you still need a no-arg constructor, write it yourself.

---

## 4. Parameterized Constructor

Takes values and uses them to set up the fields.

```java
class Book {
    String title;
    String author;
    double price;

    Book(String title, String author, double price) {
        this.title = title;      // 'this' will be explained in Topic 5
        this.author = author;
        this.price = price;
    }
}
```

`this.title` means "the `title` field of this object", while plain `title` is the constructor parameter.

---

## 5. Constructor Overloading

Multiple constructors with different parameter lists give flexible ways to create an object.

```java
class Book {
    String title, author;
    double price;

    Book() {
        this("Unknown", "Unknown", 0.0);      // calls the 3-arg constructor
    }

    Book(String title) {
        this(title, "Unknown", 0.0);
    }

    Book(String title, String author, double price) {
        this.title = title;
        this.author = author;
        this.price = price;
    }
}
```

`this(...)` calls another constructor of the same class (called **constructor chaining**). It avoids repeating code. You will study it in depth in **Topic 5**.

---

## 6. Copy Constructor

Java does not provide one automatically (unlike C++), but you can write one: a constructor that takes an object of the same class and copies its values.

```java
Book(Book other) {
    this(other.title, other.author, other.price);
}
```

```java
Book original = new Book("Clean Code", "Robert C. Martin", 40.0);
Book copy = new Book(original);   // a NEW object with the same values
copy.title = "Changed";
// original.title is still "Clean Code"
```

This is how you make a **real copy** (compare with `Book b2 = b1;` which only copies the reference).

---

## 7. Initialization Order

When you write `new Demo()`, Java runs things in this order:

1. **Static blocks** (only once, when the class is first loaded)
2. **Instance initializer blocks** (every time, in the order they appear)
3. **Constructor body**

```java
class Demo {
    static { System.out.println("1. static block"); }
    { System.out.println("2. instance block"); }
    Demo() { System.out.println("3. constructor"); }
}
```

Creating two objects prints:
```
1. static block
2. instance block
3. constructor
2. instance block
3. constructor
```

(The static block runs once; the other two run for each object.)

---

## 8. Complete Example

```java
// FILE: Main.java
class Book {
    String title;
    String author;
    double price;

    Book() {
        this("Unknown", "Unknown", 0.0);
    }

    Book(String title) {
        this(title, "Unknown", 0.0);
    }

    Book(String title, String author, double price) {
        this.title = title;
        this.author = author;
        this.price = price;
    }

    Book(Book other) {              // copy constructor
        this(other.title, other.author, other.price);
    }

    void show() {
        System.out.println(title + " by " + author + " ($" + price + ")");
    }
}

class Demo {
    static { System.out.println("1. static block"); }
    { System.out.println("2. instance block"); }
    Demo() { System.out.println("3. constructor"); }
}

public class Main {
    public static void main(String[] args) {
        Book b1 = new Book();
        Book b2 = new Book("Java Basics");
        Book b3 = new Book("Clean Code", "Robert C. Martin", 40.0);
        Book b4 = new Book(b3);          // copy
        b4.title = "Changed Title";

        b1.show();
        b2.show();
        b3.show();
        b4.show();

        System.out.println("--- Initialization order ---");
        new Demo();
        new Demo();
    }
}
```

**Output**
```
Unknown by Unknown ($0.0)
Java Basics by Unknown ($0.0)
Clean Code by Robert C. Martin ($40.0)
Changed Title by Robert C. Martin ($40.0)
--- Initialization order ---
1. static block
2. instance block
3. constructor
2. instance block
3. constructor
```

---

## 9. Tips and Tricks

- **Always give objects a valid starting state.** A `Student` without a name is a bug waiting to happen. Force the important data through the constructor.
- **Use constructor chaining (`this(...)`)** so that one "main" constructor holds the real logic and the others just supply defaults. This avoids duplicated code.
- **Validate inside constructors.** Reject invalid input early (`if (price < 0) throw new IllegalArgumentException(...)`).
- **Make fields `final`** when they should never change after creation, and set them in the constructor.
- **If you write any constructor, add a no-arg one yourself** when you still need it (many frameworks such as JPA/Hibernate require it).
- **Private constructor** is valid and useful: it blocks creating objects from outside. It is used in **Singleton** and **utility classes** (like `Math`).
- **Don't do heavy work** (database calls, network calls) in constructors; keep them simple.
- **IDE shortcut:** IntelliJ `Alt+Insert` -> Constructor; Eclipse `Alt+Shift+S` -> Generate Constructor using Fields.

---

## 10. Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Writing a return type: `void Book() {}` | It is a normal method, not a constructor. The default constructor is used, so your setup code never runs | Remove `void` |
| Defining only a parameterized constructor, then calling `new Book()` | Compile error | Add a no-arg constructor |
| Parameter name same as field without `this` | `title = title;` assigns parameter to itself; field stays `null` | `this.title = title;` |
| Class name and constructor name differ in case (`book()` vs `Book`) | Not a constructor | Match exactly |
| Calling `this(...)` not as the first line (older Java) | Compile error | Put it first (see note in Topic 5) |

---

## 11. Interview Questions and Answers

**Q1. What is a constructor?**
A special member of a class that is called automatically when an object is created, used to initialize the object's state. It has the same name as the class and no return type.

**Q2. What is the difference between a constructor and a method?**

| Constructor | Method |
|---|---|
| Same name as class | Any valid name |
| No return type | Has a return type (or `void`) |
| Called automatically by `new` | Called explicitly |
| Initializes an object | Performs an action |
| Not inherited | Inherited by subclasses |

**Q3. What is a default constructor?**
A no-argument constructor that the compiler adds **only if** the class has no constructors written by the programmer.

**Q4. If I write a parameterized constructor, is the default constructor still available?**
No. The compiler stops generating it. You must write a no-arg constructor yourself if you need one.

**Q5. What is constructor overloading?**
Having multiple constructors in the same class with different parameter lists.

**Q6. Does Java have a copy constructor?**
Not built in. You can write one yourself that takes an object of the same class and copies its fields. (Alternatives: `clone()` or a factory method.)

**Q7. Can a constructor be private? Why would you do that?**
Yes. It prevents object creation from outside the class. It is used in the Singleton pattern, in utility classes, and with static factory methods.

**Q8. Can a constructor be `final`, `static`, or `abstract`?**
No. A constructor is not inherited, so `final` and `abstract` make no sense. It also belongs to the instance being created, so `static` is not allowed.

**Q9. Can a constructor return a value?**
No. It has no return type. Implicitly, `new` returns the reference to the newly created object.

**Q10. What is constructor chaining?**
Calling one constructor from another, using `this(...)` within the same class or `super(...)` to call the parent class constructor.

**Q11. What is the order of execution: static block, instance block, constructor?**
Static block (once, at class load) -> instance initializer block -> constructor body.

**Q12. Can a constructor throw an exception?**
Yes. A constructor can throw checked or unchecked exceptions, which is commonly used to reject invalid arguments.

**Q13. Tricky output question:**
```java
class Test {
    Test() { System.out.println("A"); }
    void Test() { System.out.println("B"); }
}
new Test();
```
**Answer:** Prints `A`. The `void Test()` is just a regular method and is never called by `new`.

---

## 12. Practice Exercises

1. Create a `Student` class with `name`, `roll`, `marks`. Add a no-arg constructor, a 2-arg constructor, and a 3-arg constructor using constructor chaining.
2. Add a copy constructor to `Student` and prove the copy is independent by changing its name.
3. Create a `Singleton`-like class with a private constructor and a `static getInstance()` method (you can try this as a challenge).
4. Predict the output of creating three `Demo` objects (use the class from section 7).

---

## 13. Summary

- A constructor initializes a new object; name = class name, no return type.
- The compiler gives a default constructor **only if** you write none.
- Overload constructors for flexibility; chain them with `this(...)` to avoid duplicate code.
- A copy constructor creates an independent copy.
- Order: static block -> instance block -> constructor.

**Previous:** [Topic 3](03-field-and-method.md) | **Next:** [Topic 5: this Keyword](05-this-keyword.md)

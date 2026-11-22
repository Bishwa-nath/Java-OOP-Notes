# Topic 2: Class and Object

> **Level:** Beginner | **Prerequisites:** [Topic 1: What is OOP](01-what-is-oop.md)

## Table of Contents
1. [Overview](#1-overview)
2. [Class: The Blueprint](#2-class-the-blueprint)
3. [Object: The Real Thing](#3-object-the-real-thing)
4. [Creating and Using Objects](#4-creating-and-using-objects)
5. [What Happens in Memory](#5-what-happens-in-memory)
6. [Reference Variables, Copying, and null](#6-reference-variables-copying-and-null)
7. [Complete Example](#7-complete-example)
8. [Tips and Tricks](#8-tips-and-tricks)
9. [Common Mistakes](#9-common-mistakes)
10. [Interview Questions and Answers](#10-interview-questions-and-answers)
11. [Practice Exercises](#11-practice-exercises)
12. [Summary](#12-summary)

---

## 1. Overview

A **class** is a template. An **object** is a real thing built from that template. You cannot live in a house *plan*, but you can live in a *house* built from the plan. The plan is the class; the house is the object.

---

## 2. Class: The Blueprint

A class defines:
- **Fields** (what data each object will hold)
- **Methods** (what each object can do)

```java
class Car {
    // fields (state)
    String brand;
    String color;
    int speed;

    // methods (behavior)
    void accelerate(int amount) {
        speed += amount;
    }

    void brake() {
        speed = 0;
    }

    void showInfo() {
        System.out.println(brand + " | " + color + " | speed = " + speed + " km/h");
    }
}
```

A class by itself uses almost no memory for data. Nothing happens until you create objects from it.

---

## 3. Object: The Real Thing

An object is an **instance** of a class. It has:

| Property | Meaning | Example |
|---|---|---|
| **Identity** | It is unique in memory | Two Toyotas are two different objects |
| **State** | Its current field values | `color = "White"`, `speed = 60` |
| **Behavior** | What it can do | `accelerate()`, `brake()` |

Real-life examples:

| Class (blueprint) | Objects (real things) |
|---|---|
| `Student` | Ayesha, Rahim, Nabil |
| `Car` | Your Toyota, your friend's Honda |
| `Phone` | Your iPhone, your sister's Samsung |
| Cookie cutter | Every cookie cut with it |

---

## 4. Creating and Using Objects

```java
Car myCar = new Car();      // create an object
myCar.brand = "Toyota";     // set a field using the dot operator
myCar.accelerate(60);       // call a method
myCar.showInfo();           // Toyota | null | speed = 60 km/h
```

Breaking down `Car myCar = new Car();`:

| Part | Meaning |
|---|---|
| `Car` | The type of the variable |
| `myCar` | The **reference variable** (a remote control, not the object itself) |
| `=` | Assign |
| `new` | Allocate memory for a new object on the **heap** |
| `Car()` | Call the **constructor** to initialize the object |

The **dot operator** (`.`) means "go to this object and access its field or method".

---

## 5. What Happens in Memory

```
STACK (variables)                 HEAP (objects)
+-------------------+            +---------------------------+
| c1  ------------------------->  | Car object                 |
|                   |            |  brand = "Toyota"          |
|                   |            |  color = "White"           |
|                   |            |  speed = 60                |
|                   |            +---------------------------+
| c2  ------------------------->  | Car object                 |
|                   |            |  brand = "Honda"           |
|                   |            |  color = "Black"           |
+-------------------+            |  speed = 0                 |
                                 +---------------------------+
```

- The **reference variable** (`c1`, `c2`) lives on the **stack** and stores the *address* of the object.
- The **object** lives on the **heap**.
- When no reference points to an object anymore, the **Garbage Collector** can free its memory automatically.

> **Analogy:** The reference variable is like a TV remote. The object is the TV. You can have two remotes for the same TV.

---

## 6. Reference Variables, Copying, and null

### Two references, one object

```java
Car c1 = new Car();
c1.color = "White";

Car c3 = c1;        // does NOT copy the object; copies the reference
c3.color = "Red";

System.out.println(c1.color);   // Red  (same object!)
```

```
c1 ----\
        >---> [ Car: color = "Red" ]
c3 ----/
```

### null

`null` means "this reference points to no object".

```java
Car c4 = null;
c4.showInfo();   // NullPointerException at runtime!
```

Safe way:

```java
if (c4 != null) {
    c4.showInfo();
}
```

### Default value of references
A field of class type (for example `String name;` or `Car car;`) is `null` by default. This is why `myCar.color` printed `null` above before we set it.

---

## 7. Complete Example

```java
// FILE: Main.java
class Car {
    String brand;
    String color;
    int speed;

    void accelerate(int amount) {
        speed += amount;
    }

    void brake() {
        speed = 0;
    }

    void showInfo() {
        System.out.println(brand + " | " + color + " | speed = " + speed + " km/h");
    }
}

public class Main {
    public static void main(String[] args) {
        Car c1 = new Car();
        c1.brand = "Toyota";
        c1.color = "White";
        c1.accelerate(60);
        c1.showInfo();

        Car c2 = new Car();
        c2.brand = "Honda";
        c2.color = "Black";
        c2.showInfo();

        Car c3 = c1;          // same object, two references
        c3.color = "Red";
        c1.showInfo();        // color is Red now

        c2.accelerate(40);
        c2.brake();
        c2.showInfo();

        Car c4 = null;
        if (c4 == null) {
            System.out.println("c4 points to nothing");
        }
    }
}
```

**Output**
```
Toyota | White | speed = 60 km/h
Honda | Black | speed = 0 km/h
Toyota | Red | speed = 60 km/h
Honda | Black | speed = 0 km/h
c4 points to nothing
```

---

## 8. Tips and Tricks

- **Remember: variable holds a reference, not the object.** Most beginner bugs come from forgetting this.
- **`new` is the only everyday way** to create an object. Every `new` creates a *brand-new* object.
- **Use `==` carefully.** For objects, `==` compares references (are these the same object?), not content. Use `.equals()` to compare content (you will see this later).
- **Print an object?** Printing `System.out.println(c1)` shows something like `Car@1b6d3586` unless the class overrides `toString()`. Override `toString()` to get a readable output.
- **Anonymous object:** `new Car().showInfo();` creates an object and uses it once, without storing a reference. Useful for one-time use.
- **Keep one public class per file**, and the file name must match the public class name (`Main.java` for `public class Main`).
- **Name your references meaningfully.** `studentAyesha` is better than `s1` once the program grows.

---

## 9. Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `Car c;` then `c.speed = 10;` | Compile error: variable might not have been initialized (local variable) | `Car c = new Car();` |
| `Car c = null; c.brake();` | `NullPointerException` | Check for `null` or make sure the object exists |
| Thinking `Car b = a;` makes a copy | Both refer to the same object | Create a new object and copy field values (or a copy constructor) |
| Calling an instance method without an object | Compile error | Create an object first: `new Car().brake();` |
| File name does not match public class | Compile error | Rename file or class |

---

## 10. Interview Questions and Answers

**Q1. What is the difference between a class and an object?**
A class is a blueprint that defines fields and methods. An object is an actual instance of that class with its own memory and its own field values. One class can create many objects.

**Q2. Does a class occupy memory?**
The class definition is loaded into memory by the JVM (as metadata), but it does not allocate memory for instance data. Each object allocates memory on the heap when created with `new`.

**Q3. Where are objects and reference variables stored?**
Objects are stored in the **heap**. Local reference variables are stored in the **stack** and hold the address (reference) of the object.

**Q4. What does the `new` keyword do?**
It allocates memory for a new object on the heap, calls the constructor to initialize it, and returns a reference to that object.

**Q5. What is a reference variable?**
A variable that stores the address of an object, not the object itself. Several reference variables can point to the same object.

**Q6. What is `null`? What happens if you call a method on `null`?**
`null` means a reference points to no object. Calling a method or accessing a field through a `null` reference throws a `NullPointerException`.

**Q7. What is an anonymous object?**
An object created without assigning it to a reference variable, for example `new Car().showInfo();`. It can be used only once.

**Q8. In how many ways can you create an object in Java?**
Using `new`, `clone()`, reflection (`Constructor.newInstance()`), deserialization, and factory methods (which internally use one of these). The `new` keyword is by far the most common.

**Q9. What happens to objects that no longer have any reference?**
They become eligible for garbage collection, and the JVM's Garbage Collector may reclaim their memory automatically.

**Q10. Output question: what does this print?**
```java
Car a = new Car();
a.speed = 10;
Car b = a;
b.speed = 50;
System.out.println(a.speed);
```
**Answer:** `50`, because `a` and `b` refer to the same object.

**Q11. Can we create an object of a class without a `main` method?**
Yes. `main` is only the entry point to start the program. Objects can be created from any method in any class.

---

## 11. Practice Exercises

1. Create a `Phone` class with `model`, `battery`, and methods `charge(int)` and `use(int)`. Create two phones and show their info.
2. Write code that creates two references to the same `Phone` object, change one through the other, and explain the output.
3. Create an array of 3 `Car` objects using a loop and print each one.
4. Predict the output: what happens if you call `showInfo()` on a `Car` reference that is `null`?

---

## 12. Summary

- **Class** = blueprint; **Object** = instance built from the blueprint using `new`.
- Reference variables live on the stack; objects live on the heap.
- `Car b = a;` copies the reference, not the object.
- `null` means "no object"; using it causes `NullPointerException`.

**Previous:** [Topic 1](01-what-is-oop.md) | **Next:** [Topic 3: Field and Method](03-field-and-method.md)

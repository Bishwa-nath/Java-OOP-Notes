# Topic 3: Field and Method

> **Level:** Beginner | **Prerequisites:** [Topic 2: Class and Object](02-class-and-object.md)

## Table of Contents
1. [Overview](#1-overview)
2. [Fields (Variables)](#2-fields-variables)
3. [Default Values](#3-default-values)
4. [Methods](#4-methods)
5. [Parameters, Arguments, and Return Values](#5-parameters-arguments-and-return-values)
6. [Java is Pass-by-Value](#6-java-is-pass-by-value)
7. [Method Overloading (Preview)](#7-method-overloading-preview)
8. [Complete Example](#8-complete-example)
9. [Tips and Tricks](#9-tips-and-tricks)
10. [Common Mistakes](#10-common-mistakes)
11. [Interview Questions and Answers](#11-interview-questions-and-answers)
12. [Practice Exercises](#12-practice-exercises)
13. [Summary](#13-summary)

---

## 1. Overview

- **Fields** store the data of an object (its state).
- **Methods** define what an object can do (its behavior).

Together they make a class useful.

---

## 2. Fields (Variables)

Java has three kinds of variables. Beginners often mix them up.

| Type | Declared where | Lives as long as | Default value? | Belongs to |
|---|---|---|---|---|
| **Instance variable (field)** | Inside class, outside methods | The object lives | Yes | Each object |
| **Static variable** | Inside class with `static` | The program runs (class loaded) | Yes | The class (shared by all objects) |
| **Local variable** | Inside a method/block | Until the method/block ends | **No** (must initialize) | That method call |

```java
class Student {
    String name;                 // instance variable: each student has own name
    static String school = "ABC School";   // static: shared by all students

    void greet() {
        String message = "Hi, I'm " + name;   // local variable
        System.out.println(message);
    }
}
```

> **Real-life:** `name` is personal to each student. `school` is common to all students. `message` is a sticky note that is thrown away after the greeting.

---

## 3. Default Values

Instance and static variables get default values automatically. **Local variables do not.**

| Type | Default |
|---|---|
| `byte`, `short`, `int`, `long` | `0` |
| `float`, `double` | `0.0` |
| `char` | `'\u0000'` (null character) |
| `boolean` | `false` |
| Any object/`String`/array | `null` |

```java
class Demo {
    int count;          // 0
    boolean active;     // false
    String title;       // null

    void test() {
        int x;
        // System.out.println(x);   // Compile error: x might not have been initialized
    }
}
```

---

## 4. Methods

A method is a named block of code that performs a task.

### Anatomy of a method

```java
//  access   return   name     parameters
    public   double   area (   double w, double h ) {
        return w * h;            // body
    }
```

| Part | Purpose |
|---|---|
| Access modifier | Who can call it (`public`, `private`, ...). You will study this in Topic 6 |
| Return type | Type of value sent back (`void` if nothing is returned) |
| Name | A verb in `camelCase` (`calculateArea`) |
| Parameters | Inputs the method needs |
| Body | The actual code |

### Types of methods by behavior

```java
void sayHello() { System.out.println("Hello"); }           // no input, no output
void greet(String name) { System.out.println("Hi " + name); } // input, no output
int getAge() { return age; }                               // no input, output
int add(int a, int b) { return a + b; }                    // input and output
```

A method with a non-`void` return type **must** return a value on every path.

---

## 5. Parameters, Arguments, and Return Values

- **Parameter**: the variable in the method definition (`int a`).
- **Argument**: the actual value you pass when calling (`add(3, 4)`; `3` and `4` are arguments).

```java
static int add(int a, int b) {     // a, b are parameters
    return a + b;
}

int result = add(3, 4);            // 3, 4 are arguments
```

A method can return only **one** value. To return several values, return an object, an array, or a list.

### Varargs (variable number of arguments)

```java
static int sum(int... numbers) {
    int total = 0;
    for (int n : numbers) total += n;
    return total;
}

sum();             // 0
sum(1, 2);         // 3
sum(1, 2, 3, 4);   // 10
```

---

## 6. Java is Pass-by-Value

**Java always passes a copy of the value.** For primitives, the value is the number itself. For objects, the value is a *copy of the reference*.

```java
static void changePrimitive(int x) { x = 99; }
static void changeField(Rectangle r) { r.width = 100; }       // modifies the same object
static void reassign(Rectangle r) { r = new Rectangle(); r.width = 1; } // only changes the local copy of the reference
```

| Call | Effect on caller's variable |
|---|---|
| `changePrimitive(n)` | None. `n` stays the same |
| `changeField(rect)` | **Yes**, the object's field changes (same object) |
| `reassign(rect)` | None. The caller's reference still points to the original object |

> **Analogy:** You give a friend a photocopy of your house address. They can walk to *your* house and repaint it (modify the object), but if they scribble a different address on their copy, your own address card doesn't change (reassignment).

---

## 7. Method Overloading (Preview)

Same method name, different parameters.

```java
int add(int a, int b)           { return a + b; }
double add(double a, double b)  { return a + b; }
int add(int a, int b, int c)    { return a + b + c; }
```

You will study this fully in **Topic 8 (Polymorphism)**.

---

## 8. Complete Example

```java
// FILE: Main.java
class Rectangle {
    double width;
    double height;

    double area() {
        return width * height;
    }

    double perimeter() {
        return 2 * (width + height);
    }

    boolean isSquare() {
        return width == height;
    }

    void scale(double factor) {
        width *= factor;
        height *= factor;
    }

    void show() {
        System.out.println(width + " x " + height
                + " | area = " + area()
                + " | perimeter = " + perimeter());
    }
}

public class Main {
    static void changePrimitive(int x) { x = 99; }
    static void changeField(Rectangle r) { r.width = 100; }
    static void reassign(Rectangle r) { r = new Rectangle(); r.width = 1; }

    public static void main(String[] args) {
        Rectangle r = new Rectangle();
        r.width = 4;
        r.height = 5;
        r.show();
        System.out.println("Is square? " + r.isSquare());

        r.scale(2);
        r.show();

        int n = 10;
        changePrimitive(n);
        System.out.println("n = " + n);             // 10

        changeField(r);
        System.out.println("width = " + r.width);   // 100.0

        reassign(r);
        System.out.println("width = " + r.width);   // still 100.0
    }
}
```

**Output**
```
4.0 x 5.0 | area = 20.0 | perimeter = 18.0
Is square? false
8.0 x 10.0 | area = 80.0 | perimeter = 36.0
n = 10
width = 100.0
width = 100.0
```

---

## 9. Tips and Tricks

- **Method names are verbs** (`calculateArea`, `isSquare`). **Boolean methods** often start with `is`, `has`, or `can`.
- **Keep methods small.** If a method needs a long comment to explain it, split it into smaller methods.
- **One method, one job.** `area()` should compute the area, not print it. Let the caller decide what to do with the result.
- **Prefer returning values over printing** inside methods; returned values can be reused, tested, and printed anywhere.
- **Methods can call other methods** in the same class directly (see `show()` calling `area()`).
- **Local variable shadows field:** if a local variable has the same name as a field, the local one wins inside that method. Use `this.field` to reach the field (Topic 5).
- **Initialize local variables** before use. Fields get defaults; locals do not.
- **Return early** to avoid deep nesting:
  ```java
  void withdraw(double amount) {
      if (amount <= 0) return;
      // main logic...
  }
  ```

---

## 10. Common Mistakes

| Mistake | Why wrong | Fix |
|---|---|---|
| Forgetting `return` in a non-void method | Compile error: missing return statement | Return a value on every path |
| Using a local variable without initializing | Compile error | Give it a value first |
| Believing Java passes objects by reference | It passes a copy of the reference | Understand the three cases in section 6 |
| `int result = r.show();` where `show()` is `void` | Cannot assign void | Change return type or don't assign |
| Putting `;` after the method header `void f();` | Treated as an abstract/declaration, error in a normal class | Use `{ ... }` |

---

## 11. Interview Questions and Answers

**Q1. What is the difference between an instance variable, a static variable, and a local variable?**
An instance variable belongs to each object and is stored on the heap with the object. A static variable belongs to the class and is shared by all objects. A local variable belongs to a method or block, lives on the stack, and has no default value.

**Q2. What are the default values of instance variables?**
`0` for numeric types, `false` for boolean, `'\u0000'` for char, and `null` for references. Local variables have no default values.

**Q3. What is the difference between a parameter and an argument?**
A parameter is the variable declared in the method signature. An argument is the actual value passed when calling the method.

**Q4. Is Java pass-by-value or pass-by-reference?**
Java is **always pass-by-value**. For objects, the value passed is a copy of the reference, so the method can modify the object's contents but cannot make the caller's variable point to a different object.

**Q5. Can a method return more than one value?**
Not directly. It can return an object, array, or collection that contains multiple values.

**Q6. What is the difference between a method and a function?**
In Java, functions are always inside classes and are called methods. A method is associated with an object (instance method) or a class (static method).

**Q7. What is the difference between an instance method and a static method?**
An instance method needs an object to be called and can access instance fields. A static method belongs to the class, can be called without an object, and cannot directly access instance fields.

**Q8. What are varargs?**
A parameter declared as `type... name` accepts zero or more arguments of that type and is treated as an array inside the method. It must be the last parameter, and a method can have only one.

**Q9. What is `void`?**
It means the method does not return any value.

**Q10. Output question:**
```java
static void change(int[] arr) { arr[0] = 99; }
int[] a = {1, 2, 3};
change(a);
System.out.println(a[0]);
```
**Answer:** `99`. Arrays are objects, and the method received a copy of the reference to the same array.

---

## 12. Practice Exercises

1. Write a `Circle` class with `radius`, `area()`, `circumference()`, and `isLargerThan(Circle other)`.
2. Write a method `maxOf(int... nums)` that returns the maximum of any number of integers.
3. Predict the output (then run it):
   ```java
   static void swap(int a, int b) { int t = a; a = b; b = t; }
   // swap(1, 2); does the caller's 1 and 2 change?
   ```
4. Create a `Temperature` class with `celsius` and a method `toFahrenheit()`.

---

## 13. Summary

- **Fields** hold state. Instance fields are per-object, static fields are shared, locals are temporary.
- Instance/static fields get default values; local variables do not.
- **Methods** have a return type, name, parameters, and body.
- Java is **pass-by-value**, always.

**Previous:** [Topic 2](02-class-and-object.md) | **Next:** [Topic 4: Constructor](04-constructor.md)

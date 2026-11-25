# Topic 9: Abstraction (Abstract Classes)

> **Level:** Intermediate | **Prerequisites:** [Topic 8: Polymorphism](08-polymorphism.md)

## Table of Contents
1. [Overview](#1-overview)
2. [Why Do We Need Abstraction?](#2-why-do-we-need-abstraction)
3. [Abstract Class and Abstract Method](#3-abstract-class-and-abstract-method)
4. [Rules of Abstract Classes](#4-rules-of-abstract-classes)
5. [What Can an Abstract Class Contain?](#5-what-can-an-abstract-class-contain)
6. [Constructors in Abstract Classes](#6-constructors-in-abstract-classes)
7. [Abstract Class + Polymorphism](#7-abstract-class--polymorphism)
8. [Template Method Pattern](#8-template-method-pattern)
9. [Illegal Combinations](#9-illegal-combinations)
10. [Abstraction vs Encapsulation](#10-abstraction-vs-encapsulation)
11. [Complete Example](#11-complete-example)
12. [Tips and Tricks](#12-tips-and-tricks)
13. [Common Mistakes](#13-common-mistakes)
14. [Interview Questions and Answers](#14-interview-questions-and-answers)
15. [Practice Exercises](#15-practice-exercises)
16. [Summary](#16-summary)

---

## 1. Overview

**Abstraction** means **hiding the complex details and showing only the essential features**. You focus on *what* an object does, not *how* it does it.

> **Real-life examples**
> - **Driving a car:** you use the steering wheel, pedals, and gear. You do not need to know how fuel injection or the gearbox works inside.
> - **ATM:** you press "Withdraw". You do not see how the bank verifies your balance.
> - **TV remote:** you press the power button. The circuit inside is hidden.
> - **Mobile banking app:** you tap "Send Money". The app hides the network calls, security checks, and database updates.

In Java, abstraction is achieved in two ways:

| Way | Topic |
|---|---|
| **Abstract classes** (0% to 100% abstraction) | This topic |
| **Interfaces** (100% contract; see Topic 10) | Next topic |

---

## 2. Why Do We Need Abstraction?

Imagine a `Shape` class. What is the **area of a "shape"**? You cannot say. A circle, rectangle, and triangle each calculate it differently. But you do know **every shape must have an area**.

```java
class Shape {
    double area() {
        return 0;     // meaningless! A generic "shape" has no real area
    }
}

Shape s = new Shape();   // Does this even make sense? What does a "shape" look like?
```

Problems:
1. A generic `Shape` object makes no sense, yet Java lets you create one.
2. A child class can **forget** to override `area()`, and the wrong answer (0) is silently used.

**Abstract classes fix both problems:**
- You **cannot create** an object of the abstract class.
- You can **force** every child to implement the required methods.

---

## 3. Abstract Class and Abstract Method

- An **abstract class** is declared with the `abstract` keyword. It cannot be instantiated.
- An **abstract method** has **no body** (ends with `;`). Child classes must provide the body.

```java
abstract class Shape {
    abstract double area();          // no body: "every shape must know its area"
    abstract double perimeter();

    void describe() {                // normal method with a body is allowed
        System.out.println("I am a shape with area " + area());
    }
}

class Circle extends Shape {
    private final double r;
    Circle(double r) { this.r = r; }

    @Override double area()      { return Math.PI * r * r; }
    @Override double perimeter() { return 2 * Math.PI * r; }
}
```

```java
Shape s = new Shape();          // ERROR: Shape is abstract; cannot be instantiated
Shape c = new Circle(5);        // OK: reference of abstract type, object of child type
```

> **Real-life analogy:** An abstract class is like a **job description**: "Employee: must calculate salary, must attend meetings." There is no such person as a generic "employee". There are only specific people such as a Manager or Developer who fill the role and do the work in their own way.

---

## 4. Rules of Abstract Classes

1. Use the `abstract` keyword on the class. It **cannot be instantiated** with `new`.
2. An abstract method has **no body** and ends with a semicolon.
3. If a class has **even one** abstract method, the class **must be declared abstract**.
4. An abstract class **may have zero abstract methods** (it is still not instantiable).
5. A child class must **implement all** inherited abstract methods, **or** be declared abstract itself.
6. An abstract class **can have constructors**, fields, normal methods, static methods, and final methods.
7. An abstract class can **extend** another class and **implement** interfaces.
8. You **can** create a reference variable of an abstract type (that is how polymorphism works).

```java
abstract class A {
    abstract void f();
}

abstract class B extends A { }     // OK: B is also abstract, so it need not implement f()

class C extends B {
    @Override void f() { System.out.println("f implemented in C"); }   // someone must implement it
}
```

---

## 5. What Can an Abstract Class Contain?

| Member | Allowed? |
|---|:---:|
| Abstract methods | Yes |
| Concrete (normal) methods | Yes |
| Instance fields (any access) | Yes |
| `static` fields and methods | Yes |
| `final` methods | Yes |
| Constructors | Yes |
| Instance and static blocks | Yes |
| Creating an object with `new` | **No** |

This flexibility is why an abstract class is perfect for **sharing common code (fields + methods) and forcing specific behavior** at the same time.

---

## 6. Constructors in Abstract Classes

You cannot call `new Shape()`, but the abstract class **constructor still runs** when a child object is created, because the child constructor calls `super(...)` (explicitly or implicitly). It is used to initialize the common fields.

```java
abstract class Employee {
    private final String name;
    private final int id;

    Employee(String name, int id) {         // constructor in abstract class
        this.name = name;
        this.id = id;
    }

    String getName() { return name; }
    int getId() { return id; }
    abstract double calculateSalary();
}

class Manager extends Employee {
    private final double baseSalary;
    private final double bonus;

    Manager(String name, int id, double baseSalary, double bonus) {
        super(name, id);                    // calls the abstract parent's constructor
        this.baseSalary = baseSalary;
        this.bonus = bonus;
    }

    @Override double calculateSalary() { return baseSalary + bonus; }
}
```

Creation order for `new Manager(...)`: `Employee` constructor runs first, then `Manager` constructor.

---

## 7. Abstract Class + Polymorphism

Abstraction and polymorphism work together. You write code against the abstract type, and each concrete child supplies its own behavior at runtime.

```java
Shape[] shapes = { new Circle(2), new Rectangle(3, 4) };
double total = 0;
for (Shape s : shapes) {
    total += s.area();      // each shape calculates in its own way
}
```

Add a `Triangle` later and this loop works unchanged.

---

## 8. Template Method Pattern

An abstract class can define the **skeleton of an algorithm** in a normal (often `final`) method, and let children fill in the **specific steps**. This is the **Template Method design pattern**, a very common interview topic.

> **Real-life:** The steps of making a hot drink are the same: boil water, brew, pour into cup, add extras. Only the "brew" and "add extras" steps differ for tea and coffee.

```java
abstract class HotDrink {
    final void prepare() {              // the template: fixed order, cannot be overridden
        boilWater();
        brew();
        pourInCup();
        addExtras();
    }

    void boilWater() { System.out.println("Boiling water"); }
    void pourInCup() { System.out.println("Pouring into cup"); }

    abstract void brew();               // steps that differ
    abstract void addExtras();
}

class Tea extends HotDrink {
    @Override void brew()      { System.out.println("Steeping the tea"); }
    @Override void addExtras() { System.out.println("Adding lemon"); }
}

class Coffee extends HotDrink {
    @Override void brew()      { System.out.println("Brewing the coffee"); }
    @Override void addExtras() { System.out.println("Adding sugar and milk"); }
}
```

`new Tea().prepare()` and `new Coffee().prepare()` follow the same order, with different details.

---

## 9. Illegal Combinations

An abstract method **must be overridable by a child**, so these modifiers conflict with `abstract`:

| Combination | Why it is illegal |
|---|---|
| `abstract` + `final` | `final` forbids overriding; `abstract` demands it |
| `abstract` + `private` | A child cannot see a private method, so it cannot implement it |
| `abstract` + `static` | `static` methods belong to the class and are not overridden |
| `abstract class` + `final class` | `final` forbids extending; `abstract` needs a child |

```java
abstract final void f();     // ERROR
private abstract void g();   // ERROR
abstract static void h();    // ERROR
final abstract class X { }   // ERROR
```

---

## 10. Abstraction vs Encapsulation

| Abstraction | Encapsulation |
|---|---|
| Hides **complexity** (how it works) | Hides **data** (how it is stored) |
| Works at the **design** level | Works at the **implementation** level |
| Achieved with abstract classes and interfaces | Achieved with `private` fields + getters/setters |
| "What it does" | "Protecting its data" |
| Example: `Shape.area()` hides the formula | Example: private `balance` with controlled access |

---

## 11. Complete Example

```java
// FILE: Main.java
abstract class Shape {
    private final String name;

    Shape(String name) {
        this.name = name;
    }

    abstract double area();
    abstract double perimeter();

    void describe() {
        System.out.printf("%s -> area = %.2f, perimeter = %.2f%n", name, area(), perimeter());
    }
}

class Circle extends Shape {
    private final double r;

    Circle(double r) {
        super("Circle");
        this.r = r;
    }

    @Override double area()      { return Math.PI * r * r; }
    @Override double perimeter() { return 2 * Math.PI * r; }
}

class Rectangle extends Shape {
    private final double w, h;

    Rectangle(double w, double h) {
        super("Rectangle");
        this.w = w;
        this.h = h;
    }

    @Override double area()      { return w * h; }
    @Override double perimeter() { return 2 * (w + h); }
}

class Triangle extends Shape {
    private final double a, b, c;

    Triangle(double a, double b, double c) {
        super("Triangle");
        this.a = a;
        this.b = b;
        this.c = c;
    }

    @Override double perimeter() { return a + b + c; }

    @Override double area() {           // Heron's formula
        double s = perimeter() / 2;
        return Math.sqrt(s * (s - a) * (s - b) * (s - c));
    }
}

abstract class Employee {
    private final String name;
    private final int id;

    Employee(String name, int id) {
        this.name = name;
        this.id = id;
    }

    abstract double calculateSalary();

    void showPayslip() {
        System.out.println(id + " | " + name + " | salary = " + calculateSalary());
    }
}

class Manager extends Employee {
    private final double base, bonus;

    Manager(String name, int id, double base, double bonus) {
        super(name, id);
        this.base = base;
        this.bonus = bonus;
    }

    @Override double calculateSalary() { return base + bonus; }
}

class Developer extends Employee {
    private final double hourlyRate;
    private final int hours;

    Developer(String name, int id, double hourlyRate, int hours) {
        super(name, id);
        this.hourlyRate = hourlyRate;
        this.hours = hours;
    }

    @Override double calculateSalary() { return hourlyRate * hours; }
}

abstract class HotDrink {
    final void prepare() {              // template method
        boilWater();
        brew();
        pourInCup();
        addExtras();
    }

    void boilWater() { System.out.println("Boiling water"); }
    void pourInCup() { System.out.println("Pouring into cup"); }

    abstract void brew();
    abstract void addExtras();
}

class Tea extends HotDrink {
    @Override void brew()      { System.out.println("Steeping the tea"); }
    @Override void addExtras() { System.out.println("Adding lemon"); }
}

class Coffee extends HotDrink {
    @Override void brew()      { System.out.println("Brewing the coffee"); }
    @Override void addExtras() { System.out.println("Adding sugar and milk"); }
}

public class Main {
    public static void main(String[] args) {
        // Shape shape = new Shape("x");   // ERROR: abstract class cannot be instantiated

        Shape[] shapes = { new Circle(2), new Rectangle(3, 4), new Triangle(3, 4, 5) };
        double total = 0;
        for (Shape s : shapes) {
            s.describe();
            total += s.area();
        }
        System.out.printf("Total area = %.2f%n", total);

        System.out.println("--- Payroll ---");
        Employee[] staff = {
            new Manager("Rahim", 1, 50000, 10000),
            new Developer("Karim", 2, 500, 120)
        };
        for (Employee e : staff) {
            e.showPayslip();
        }

        System.out.println("--- Template method ---");
        HotDrink[] drinks = { new Tea(), new Coffee() };
        for (HotDrink d : drinks) {
            d.prepare();
            System.out.println();
        }
    }
}
```

**Output**
```
Circle -> area = 12.57, perimeter = 12.57
Rectangle -> area = 12.00, perimeter = 14.00
Triangle -> area = 6.00, perimeter = 12.00
Total area = 30.57
--- Payroll ---
1 | Rahim | salary = 60000.0
2 | Karim | salary = 60000.0
--- Template method ---
Boiling water
Steeping the tea
Pouring into cup
Adding lemon

Boiling water
Brewing the coffee
Pouring into cup
Adding sugar and milk

```

---

## 12. Tips and Tricks

- **Ask: "Does a generic object of this type make sense?"** If not (generic `Shape`, `Employee`, `Vehicle`), make it abstract.
- **Use abstract classes when children share code AND must provide specific behavior.** Shared fields and methods go in the abstract class; the differing behavior is abstract.
- **Think "is-a with partial implementation".** Abstract class = a half-built template. Interface = a pure contract (next topic).
- **Program to the abstract type:** `Shape s = new Circle(2);` and `List<Shape>` keep your code flexible.
- **IDE helper:** if you extend an abstract class, IntelliJ/Eclipse can auto-generate the missing methods (`Alt+Enter` -> "Implement methods").
- **Mark the template method `final`** so children cannot break the algorithm order.
- **Never call an abstract (or overridable) method inside the abstract class's constructor.** The child's fields are not initialized yet, so you may see `0` or `null`:
  ```java
  abstract class P {
      P() { show(); }            // dangerous
      abstract void show();
  }
  class Q extends P {
      int x = 5;
      @Override void show() { System.out.println(x); }   // prints 0, not 5
  }
  ```
- **Keep abstract classes small.** Too many abstract methods make children painful to write.
- **Abstract class with no abstract methods** is a valid way to say "this is a base class; do not create it directly" (like `HttpServlet` in Java EE).

---

## 13. Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| `new Shape()` on an abstract class | Compile error | Create a concrete child object |
| Child does not implement all abstract methods | Compile error | Implement them all, or make the child abstract |
| Giving an abstract method a body `abstract void f() { }` | Compile error | Remove the body and end with `;` |
| `abstract final`, `abstract private`, `abstract static` methods | Compile error | Remove the conflicting modifier |
| Forgetting `super(...)` when the abstract parent has no no-arg constructor | Compile error | Call `super(args)` as the first line |
| Weaker access when implementing (`protected` -> package) | Compile error | Keep same or wider access |
| Thinking the abstract constructor is useless | It initializes shared fields | Use it for common state |
| Putting an abstract method in a non-abstract class | Compile error | Declare the class `abstract` |

---

## 14. Interview Questions and Answers

**Q1. What is abstraction?**
Hiding implementation details and exposing only the essential features. In Java it is achieved through abstract classes and interfaces.

**Q2. What is an abstract class?**
A class declared with the `abstract` keyword that cannot be instantiated. It may contain abstract methods (no body) and concrete methods, fields, and constructors.

**Q3. What is an abstract method?**
A method declared without a body (`abstract void f();`) that child classes must implement.

**Q4. Can an abstract class have a constructor? Why, if we cannot create its object?**
Yes. The constructor runs when a child object is created (through `super(...)`) and initializes the common fields of the abstract parent.

**Q5. Can an abstract class have no abstract methods?**
Yes. It simply prevents direct instantiation. This is useful for base classes that are only meant to be extended.

**Q6. Can we create an object of an abstract class?**
No, not directly. But we can create a **reference** of the abstract type that points to a child object, and an **anonymous subclass**: `new Shape("x") { ... }` that implements all abstract methods.

**Q7. Can an abstract class have `static` and `final` methods?**
Yes. Only the *abstract methods* cannot be `static`, `final`, or `private`.

**Q8. Why can't an abstract method be `final`, `private`, or `static`?**
`final` blocks overriding, `private` hides the method from children, and `static` methods are not overridden. Abstract methods exist precisely to be overridden.

**Q9. What if a child class does not implement all abstract methods?**
The child must itself be declared `abstract`, or the code will not compile.

**Q10. Can an abstract class extend another class or implement interfaces?**
Yes. It can extend one class (abstract or concrete) and implement any number of interfaces, and it does not need to implement the interface methods.

**Q11. Can an abstract class be `final`?**
No. `final` prevents extending, and an abstract class is useless unless extended.

**Q12. Difference between an abstract class and a concrete class?**
An abstract class cannot be instantiated and may include abstract methods. A concrete class can be instantiated and must have all methods implemented.

**Q13. Difference between abstraction and encapsulation?**
Abstraction hides complexity (design level, using abstract classes/interfaces). Encapsulation hides data (implementation level, using private fields and access methods).

**Q14. Abstract class vs interface: a first look (full comparison in Topic 10)**

| Abstract class | Interface |
|---|---|
| Can have instance fields | Only constants (`public static final`) |
| Can have constructors | No constructors |
| A class extends **one** abstract class | A class can implement **many** interfaces |
| Can have any access modifier members | Methods are `public` by default |
| Describes **what something is** (is-a with shared code) | Describes **what something can do** (capability) |

**Q15. What is the Template Method pattern?**
A design pattern where an abstract class defines the skeleton of an algorithm in a (usually `final`) method and lets subclasses implement specific steps.

**Q16. Real-life example of abstraction in Java's standard library?**
`List` is an interface and you use `ArrayList` or `LinkedList` without caring how they store data. `InputStream` and `Reader` are abstract classes whose concrete subclasses read from files, networks, and so on.

**Q17. Can an abstract class have a `main` method?**
Yes. A `public static void main` method works in an abstract class and can be run, because it needs no object.

**Q18. Can we declare an abstract method in a non-abstract class?**
No. A class with an abstract method must be declared `abstract`.

**Q19. Output question:**
```java
abstract class A {
    A() { System.out.println("A()"); }
    abstract void f();
}
class B extends A {
    B() { System.out.println("B()"); }
    void f() { System.out.println("f()"); }
}
new B().f();
```
**Answer:** `A()`, `B()`, `f()`. The abstract parent constructor runs first.

**Q20. Does this compile?**
```java
abstract class X {
    abstract void show() { }
}
```
**Answer:** No. An abstract method cannot have a body (not even `{ }`).

---

## 15. Practice Exercises

1. Create an abstract class `Vehicle` with `brand`, a constructor, an abstract method `start()`, and a normal method `showBrand()`. Create `Car` and `Bike` and test them using a `Vehicle[]` array.
2. Extend the `Shape` example with a `Square` class and compute the total area of five shapes.
3. Build an abstract `Employee` with `Manager`, `Developer`, and `Intern`, and print the total salary of all employees with a loop.
4. Write a template method `processOrder()` in an abstract `OrderProcessor` with steps `validate()`, `charge()` (abstract), and `sendReceipt()`.
5. Refactor your `Animal` class from Topic 7 so `Animal` is abstract with an abstract `makeSound()`. What breaks? What gets better?
6. Try to compile `abstract final class Test {}` and read the error message.

---

## 16. Summary

- **Abstraction** = show *what*, hide *how*.
- An **abstract class** cannot be instantiated and may contain abstract methods, concrete methods, fields, and constructors.
- Children **must implement** all abstract methods, or be abstract themselves.
- Abstract methods cannot be `final`, `private`, or `static`.
- The abstract constructor runs first when a child is created.
- The **Template Method** pattern uses an abstract class for the algorithm skeleton.
- Abstraction + polymorphism lets you write flexible code against the abstract type.

**Previous:** [Topic 8](08-polymorphism.md) | **Next:** [Topic 10: Interface](10-interface.md)

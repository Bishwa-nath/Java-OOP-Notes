# Topic 8: Polymorphism

> **Level:** Intermediate | **Prerequisites:** [Topic 7: Inheritance](07-inheritance.md)

## Table of Contents
1. [Overview](#1-overview)
2. [Types of Polymorphism](#2-types-of-polymorphism)
3. [Compile-Time Polymorphism: Method Overloading](#3-compile-time-polymorphism-method-overloading)
4. [Runtime Polymorphism: Method Overriding](#4-runtime-polymorphism-method-overriding)
5. [Upcasting and Dynamic Method Dispatch](#5-upcasting-and-dynamic-method-dispatch)
6. [Downcasting and `instanceof`](#6-downcasting-and-instanceof)
7. [What is NOT Polymorphic](#7-what-is-not-polymorphic)
8. [Overload Resolution Rules](#8-overload-resolution-rules)
9. [Complete Example](#9-complete-example)
10. [Tips and Tricks](#10-tips-and-tricks)
11. [Common Mistakes](#11-common-mistakes)
12. [Interview Questions and Answers](#12-interview-questions-and-answers)
13. [Practice Exercises](#13-practice-exercises)
14. [Summary](#14-summary)

---

## 1. Overview

**Polymorphism** means "**many forms**": the same action behaves differently depending on the object that performs it.

> **Real-life examples**
> - **"Speak" command:** a dog barks, a cat meows, a bird tweets. Same command, different result.
> - **Payment button:** "Pay" works differently for card, bKash, or cash, but the cashier presses the same button.
> - **A person:** the same person is a teacher at school, a son at home, a customer at a shop.
> - **Remote's "play" button:** plays music in a music app and a video in a video app.

**Why it matters:** you can write code against a general type (`Animal`) and it automatically works for every specific type (`Dog`, `Cat`, `Bird`), including ones added in the future. No `if-else` chains needed.

---

## 2. Types of Polymorphism

| | Compile-time (static) | Runtime (dynamic) |
|---|---|---|
| Achieved by | **Method overloading** | **Method overriding** |
| Decided | At compile time (by the compiler) | At runtime (by the JVM) |
| Depends on | Reference type and arguments | Actual **object** type |
| Needs inheritance? | No | Yes |
| Also called | Early binding | Late binding / dynamic dispatch |

---

## 3. Compile-Time Polymorphism: Method Overloading

Same method name, **different parameter lists** (number, type, or order of parameters).

```java
class Calculator {
    int add(int a, int b)           { return a + b; }
    double add(double a, double b)  { return a + b; }
    int add(int a, int b, int c)    { return a + b + c; }
    String add(String a, String b)  { return a + b; }
}
```

```java
Calculator c = new Calculator();
c.add(2, 3);          // 5          -> add(int, int)
c.add(2.5, 3.5);      // 6.0        -> add(double, double)
c.add(1, 2, 3);       // 6          -> add(int, int, int)
c.add("Hi ", "Java"); // "Hi Java"  -> add(String, String)
```

### Rules
- Parameter list **must differ** (number, type, or order).
- **Return type alone cannot** distinguish overloaded methods (compile error).
- Access modifiers and `throws` clauses don't count.
- You can overload `static` methods and constructors too.

```java
int get(int a)     { ... }
double get(int a)  { ... }   // ERROR: same parameters, only return type differs
```

---

## 4. Runtime Polymorphism: Method Overriding

The child class overrides a parent method, and the JVM chooses the version based on the **actual object** at runtime.

```java
Animal a = new Dog("Rex");
a.makeSound();    // "Rex says Woof!"   (Dog's version, even though the variable type is Animal)
```

---

## 5. Upcasting and Dynamic Method Dispatch

**Upcasting:** assigning a child object to a parent reference. It is automatic and always safe.

```java
Animal a = new Dog("Rex");      // upcasting: Dog -> Animal
```

Rules to remember:
- **What you can call** is decided by the **reference type** (`Animal`).
- **Which version runs** is decided by the **object type** (`Dog`).

```java
a.makeSound();   // OK. Dog's version runs.
a.fetch();       // ERROR: Animal has no fetch(), even though the object is a Dog
```

**Dynamic Method Dispatch** is the process of selecting the overridden method at runtime based on the actual object.

### Power: one method works for everything

```java
static void feed(Animal a) {       // accepts Dog, Cat, Bird, Puppy, anything that is an Animal
    a.eat();
    a.makeSound();
}

feed(new Dog("Rex"));
feed(new Cat("Tom"));
```

### Power: one array holds many types

```java
Animal[] zoo = { new Dog("Rex"), new Cat("Tom"), new Bird("Tweety") };
for (Animal a : zoo) {
    a.makeSound();      // each animal responds in its own way
}
```

If you later add `class Lion extends Animal`, this code works without any change. This is the **Open/Closed Principle** in action: open for extension, closed for modification.

---

## 6. Downcasting and `instanceof`

**Downcasting:** converting a parent reference back to a child type to access child-only methods. It must be explicit and can fail at runtime.

```java
Animal a = new Dog("Rex");
Dog d = (Dog) a;        // OK: the object really is a Dog
d.fetch();

Animal b = new Cat("Tom");
Dog bad = (Dog) b;      // compiles, but throws ClassCastException at runtime
```

**Always check with `instanceof` first:**

```java
if (b instanceof Dog) {
    Dog dd = (Dog) b;
    dd.fetch();
}

// Modern Java (16+): pattern matching, cast included
if (a instanceof Dog dog) {
    dog.fetch();
}
```

---

## 7. What is NOT Polymorphic

Only **instance methods** are overridden polymorphically. These are resolved by the **reference type**, not the object type:

| Member | Resolved by | Behavior |
|---|---|---|
| Instance method (overridden) | Object type (runtime) | Polymorphic |
| Instance **field** | Reference type (compile time) | **Not** polymorphic (field hiding) |
| **static** method | Reference type (compile time) | **Not** polymorphic (method hiding) |
| **private** method | Class where it is written | Not overridable |

```java
class P { String type = "Parent"; static void hello() { System.out.println("Parent static"); } }
class C extends P { String type = "Child"; static void hello() { System.out.println("Child static"); } }

P p = new C();
System.out.println(p.type);   // Parent   (field uses reference type)
p.hello();                    // Parent static
```

---

## 8. Overload Resolution Rules

When several overloads could match, Java picks in this order:

1. **Exact match**
2. **Widening** primitive conversion (`int` -> `long` -> `float` -> `double`)
3. **Boxing/unboxing** (`int` -> `Integer`)
4. **Varargs**

Among reference types, the **most specific** type wins.

```java
static void m(long x)    { System.out.println("long"); }
static void m(Integer x) { System.out.println("Integer"); }
m(5);       // long   (widening beats boxing)

static void n(Object o)  { System.out.println("Object"); }
static void n(String s)  { System.out.println("String"); }
n("hi");    // String
n(5);       // Object
n(null);    // String  (most specific; if you had n(Integer) too, this would be ambiguous -> compile error)
```

---

## 9. Complete Example

```java
// FILE: Main.java
class Calculator {
    int add(int a, int b)           { return a + b; }
    double add(double a, double b)  { return a + b; }
    int add(int a, int b, int c)    { return a + b + c; }
}

class Animal {
    protected String name;
    Animal(String name) { this.name = name; }
    void eat() { System.out.println(name + " is eating"); }
    void makeSound() { System.out.println(name + " makes a sound"); }
}

class Dog extends Animal {
    Dog(String name) { super(name); }
    @Override void makeSound() { System.out.println(name + " says Woof!"); }
    void fetch() { System.out.println(name + " fetches the ball"); }
}

class Cat extends Animal {
    Cat(String name) { super(name); }
    @Override void makeSound() { System.out.println(name + " says Meow!"); }
}

class Bird extends Animal {
    Bird(String name) { super(name); }
    @Override void makeSound() { System.out.println(name + " says Tweet!"); }
}

class Shape {
    double area() { return 0; }
    String label() { return "Shape"; }
}

class Circle extends Shape {
    private final double r;
    Circle(double r) { this.r = r; }
    @Override double area() { return Math.PI * r * r; }
    @Override String label() { return "Circle"; }
}

class Rect extends Shape {
    private final double w, h;
    Rect(double w, double h) { this.w = w; this.h = h; }
    @Override double area() { return w * h; }
    @Override String label() { return "Rectangle"; }
}

class P {
    String type = "Parent";
    static void hello() { System.out.println("Parent static"); }
}

class C extends P {
    String type = "Child";
    static void hello() { System.out.println("Child static"); }
}

public class Main {
    static void feed(Animal a) {
        a.eat();
        a.makeSound();
    }

    static void m(long x)    { System.out.println("long"); }
    static void m(Integer x) { System.out.println("Integer"); }
    static void n(Object o)  { System.out.println("Object"); }
    static void n(String s)  { System.out.println("String"); }

    public static void main(String[] args) {
        // 1. Compile-time polymorphism
        Calculator calc = new Calculator();
        System.out.println(calc.add(2, 3));
        System.out.println(calc.add(2.5, 3.5));
        System.out.println(calc.add(1, 2, 3));

        // 2. Runtime polymorphism with an array
        Animal[] zoo = { new Dog("Rex"), new Cat("Tom"), new Bird("Tweety") };
        for (Animal a : zoo) {
            a.makeSound();
        }

        // 3. One method, any Animal
        feed(new Cat("Luna"));

        // 4. Shapes
        Shape[] shapes = { new Circle(2), new Rect(3, 4) };
        for (Shape s : shapes) {
            System.out.printf("%s area = %.2f%n", s.label(), s.area());
        }

        // 5. Downcasting safely
        Animal a1 = new Dog("Bolt");
        Animal a2 = new Cat("Kitty");
        for (Animal a : new Animal[]{a1, a2}) {
            if (a instanceof Dog d) {
                d.fetch();
            } else {
                System.out.println(a.name + " is not a dog");
            }
        }
        try {
            Dog bad = (Dog) a2;
        } catch (ClassCastException e) {
            System.out.println("ClassCastException caught");
        }

        // 6. Fields and static methods are NOT polymorphic
        P p = new C();
        System.out.println(p.type);
        P.hello();
        C.hello();

        // 7. Overload resolution
        m(5);
        n("hi");
        n(5);
        n(null);
    }
}
```

**Output**
```
5
6.0
6
Rex says Woof!
Tom says Meow!
Tweety says Tweet!
Luna is eating
Luna says Meow!
Circle area = 12.57
Rectangle area = 12.00
Bolt fetches the ball
Kitty is not a dog
ClassCastException caught
Parent
Parent static
Child static
long
String
Object
String
```

---

## 10. Tips and Tricks

- **Program to the parent type:** declare variables, parameters, and arrays using the general type (`Animal`, `Shape`) so your code works with all subtypes.
- **Two golden rules of upcasting:**
  1. *Reference type decides what you can call.*
  2. *Object type decides which overridden version runs.*
- **Replace `if-else` / `switch` on type with polymorphism.** If you write `if (obj is Dog) ... else if (obj is Cat) ...` everywhere, move that behavior into overridden methods.
- **Always check `instanceof` before downcasting**; prefer the pattern-matching form `if (a instanceof Dog d)` in modern Java.
- **If you need to downcast a lot, your design may need rethinking.** Maybe the method belongs in the parent class.
- **Use `@Override`** to be sure you are really overriding and not accidentally overloading.
- **Quick way to tell overload vs override:** same class + different parameters = overload; parent/child + same signature = override.
- **Overloading is best when methods do the "same thing in different ways"** (`print(int)`, `print(String)`). Don't overload with confusing, unrelated behavior.

---

## 11. Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| Expecting fields to be overridden | `p.type` uses the reference type | Use methods (getters) instead of fields for polymorphic behavior |
| Calling a child-only method through a parent reference | Compile error | Downcast safely with `instanceof` |
| Downcasting without a check | `ClassCastException` | Check with `instanceof` |
| Overloading only by return type | Compile error | Change the parameters |
| Accidentally overloading when you meant to override (`makeSound(int x)`) | Polymorphism doesn't work | Use `@Override` |
| Believing `static` methods are overridden | They are hidden, resolved by reference type | Don't rely on this; use instance methods |

---

## 12. Interview Questions and Answers

**Q1. What is polymorphism?**
The ability of one interface (method name, reference type) to take many forms. In Java, the same method call can behave differently depending on the object (runtime) or the arguments (compile time).

**Q2. What are the types of polymorphism in Java?**
Compile-time polymorphism (method overloading) and runtime polymorphism (method overriding).

**Q3. What is method overloading? What are its rules?**
Defining multiple methods with the same name but different parameter lists in the same class. The parameters must differ in number, type, or order. A different return type alone is not enough.

**Q4. What is method overriding?**
A subclass providing its own implementation of a method already defined in its superclass, with the same signature.

**Q5. What is the difference between overloading and overriding?**

| Overloading | Overriding |
|---|---|
| Same class | Parent and child classes |
| Different parameters | Same parameters |
| Compile time | Runtime |
| Return type can differ (but not alone) | Return type same or covariant |
| Can overload static methods | Cannot override static methods |

**Q6. What is upcasting and downcasting?**
Upcasting assigns a child object to a parent reference (automatic, safe). Downcasting casts a parent reference back to a child type (explicit, may throw `ClassCastException`).

**Q7. What is dynamic method dispatch?**
The runtime process by which a call to an overridden method through a parent reference is resolved based on the **actual object type**, not the reference type.

**Q8. What is `ClassCastException`? How do you avoid it?**
An exception thrown when you cast an object to a type it is not an instance of. Avoid it by checking with `instanceof` before casting.

**Q9. Can we overload the `main` method?**
Yes, you can write other methods named `main` with different parameters, but the JVM only starts from `public static void main(String[] args)`.

**Q10. Can we override static methods or private methods?**
No. Static methods are hidden (resolved by reference type at compile time). Private methods are not visible to subclasses, so they cannot be overridden.

**Q11. Why are fields not polymorphic?**
Field access is resolved at compile time using the reference type. Only instance method calls are dispatched at runtime.

**Q12. Can a parent reference call a child-specific method?**
Not directly. You must downcast to the child type first (after an `instanceof` check).

**Q13. What is a covariant return type?**
An overriding method can return a subtype of the parent's method return type.

**Q14. Which is chosen: `m(long)` or `m(Integer)` when you call `m(5)`?**
`m(long)`. Java prefers widening primitive conversion over boxing.

**Q15. What happens when you call `n(null)` with overloads `n(Object)` and `n(String)`?**
`n(String)` is chosen because `String` is more specific. If the overloads were `n(String)` and `n(Integer)`, the call would be ambiguous and fail to compile.

**Q16. Can constructors be overridden or overloaded?**
Overloaded, yes. Overridden, no, because constructors are not inherited.

**Q17. What is the real-world benefit of polymorphism?**
Extensibility and cleaner code: you can add new subclasses without changing the code that uses the parent type, and you avoid long `if-else` type checks.

**Q18. Output question:**
```java
class A { void show() { System.out.println("A"); } }
class B extends A { void show() { System.out.println("B"); } void only() { System.out.println("only B"); } }
A obj = new B();
obj.show();
obj.only();
```
**Answer:** Compile error on `obj.only()`, because the reference type `A` has no `only()` method. If you remove that line, `obj.show()` prints `B`.

---

## 13. Practice Exercises

1. Build `Shape`, `Circle`, `Rect`, `Triangle` and an array of shapes. Print every area and the **total** area using a loop.
2. Create `PaymentMethod` with `pay(double amount)`, and `CardPayment`, `MobilePayment`, `CashPayment` overriding it. Write `checkout(PaymentMethod m, double amount)`.
3. Extend the zoo: add a `Lion` class and show that your `feed()` method and the zoo loop work without modification.
4. Write a safe downcast loop: for each animal, call `fetch()` only if it is a `Dog`.
5. Predict the output, then run:
   ```java
   class X { String name = "X"; String get() { return "X.get"; } }
   class Y extends X { String name = "Y"; String get() { return "Y.get"; } }
   X obj = new Y();
   System.out.println(obj.name + " " + obj.get());
   ```
   *(Answer: `X Y.get`)*

---

## 14. Summary

- **Polymorphism = one name, many behaviors.**
- **Compile-time:** overloading (same class, different parameters, resolved by the compiler).
- **Runtime:** overriding + upcasting (resolved by the JVM using the actual object).
- Reference type decides **what you can call**; object type decides **which override runs**.
- Fields and static methods are **not** polymorphic.
- Downcast safely using `instanceof`.

**Previous:** [Topic 7](07-inheritance.md) | **Next:** Topic 9: Abstraction

---

### Day 1 + Topic 8 Complete

You have finished the core of OOP: **class, object, field, method, constructor, `this`, encapsulation, inheritance, and polymorphism**. Next comes abstraction (abstract classes and interfaces).

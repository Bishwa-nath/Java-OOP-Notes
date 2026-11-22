# Topic 7: Inheritance

> **Level:** Intermediate | **Prerequisites:** [Topic 6: Encapsulation](06-encapsulation.md)

## Table of Contents
1. [Overview](#1-overview)
2. [Syntax: `extends`](#2-syntax-extends)
3. [Types of Inheritance](#3-types-of-inheritance)
4. [The `super` Keyword](#4-the-super-keyword)
5. [Constructors in Inheritance](#5-constructors-in-inheritance)
6. [Method Overriding](#6-method-overriding)
7. [The `Object` Class](#7-the-object-class)
8. [`final` with Inheritance](#8-final-with-inheritance)
9. [Is-A vs Has-A (Inheritance vs Composition)](#9-is-a-vs-has-a-inheritance-vs-composition)
10. [Complete Example](#10-complete-example)
11. [Tips and Tricks](#11-tips-and-tricks)
12. [Common Mistakes](#12-common-mistakes)
13. [Interview Questions and Answers](#13-interview-questions-and-answers)
14. [Practice Exercises](#14-practice-exercises)
15. [Summary](#15-summary)

---

## 1. Overview

**Inheritance** lets a new class (the **child / subclass / derived class**) **reuse** the fields and methods of an existing class (the **parent / superclass / base class**) and add or change behavior.

> **Real-life:** A child inherits traits from parents (eye color, last name) and also develops their own skills. Or: every `Dog`, `Cat`, and `Bird` **is an** `Animal`, so they all eat and sleep. You write that once in `Animal`.

**Why inheritance?**
- **Code reuse:** write common code once.
- **Logical hierarchy:** models "is-a" relationships.
- **Foundation for polymorphism** (Topic 8).

**Test:** inherit only if you can say "**Child is a Parent**". `Dog is an Animal` (good). `Car is an Engine` (wrong; a car *has an* engine).

---

## 2. Syntax: `extends`

```java
class Animal {
    void eat() { System.out.println("Eating..."); }
}

class Dog extends Animal {          // Dog inherits eat()
    void bark() { System.out.println("Woof!"); }
}
```

```java
Dog d = new Dog();
d.eat();    // inherited from Animal
d.bark();   // its own
```

What a subclass inherits:

| Member | Inherited? |
|---|---|
| `public` / `protected` fields and methods | Yes |
| default (package-private) members | Only if in the same package |
| `private` members | Exist in the object but are **not accessible** directly (use getters) |
| Constructors | **No** (but the child constructor can call the parent's) |
| `static` members | Accessible, but they are class-level (hidden, not overridden) |

---

## 3. Types of Inheritance

```
Single           Multilevel          Hierarchical
 A                 A                   A
 |                 |                  / \
 B                 B                 B   C
                   |
                   C
```

| Type | Meaning | Java support |
|---|---|---|
| **Single** | B extends A | Yes |
| **Multilevel** | C extends B, B extends A | Yes |
| **Hierarchical** | B and C both extend A | Yes |
| **Multiple** | C extends A **and** B | **Not with classes** (use interfaces) |
| **Hybrid** | Mix of the above | Only via interfaces |

**Why no multiple inheritance of classes?** The **Diamond Problem**: if `B` and `C` both inherit `show()` from `A` and override it differently, and `D` extends both, which `show()` does `D` get? Java avoids this ambiguity by allowing a class to extend only **one** class (it can implement many interfaces).

---

## 4. The `super` Keyword

`super` refers to the **parent part** of the current object.

| Usage | Meaning |
|---|---|
| `super.method()` | Call the parent's version of an overridden method |
| `super.field` | Access a parent field hidden by a child field with the same name |
| `super(...)` | Call a parent constructor (must be the first statement in the constructor in traditional Java) |

```java
class Puppy extends Dog {
    @Override
    void makeSound() {
        super.makeSound();                 // first do the Dog version
        System.out.println("...and yips!");
    }
}
```

---

## 5. Constructors in Inheritance

### Rule 1: Parent constructor always runs first
When you create a child object, the **parent constructor runs before the child constructor**.

### Rule 2: Implicit `super()`
If you don't write `super(...)` or `this(...)`, the compiler inserts `super();` as the first line of every constructor.

### Rule 3: If the parent has no no-arg constructor, you MUST call `super(args)` yourself

```java
class Animal {
    protected String name;
    Animal(String name) { this.name = name; }   // no no-arg constructor
}

class Dog extends Animal {
    Dog(String name) {
        super(name);       // required, otherwise compile error
    }
}
```

### Order demonstration

```java
class A { A() { System.out.println("A constructor"); } }
class B extends A { B() { System.out.println("B constructor"); } }
class C extends B { C() { System.out.println("C constructor"); } }

new C();
// A constructor
// B constructor
// C constructor
```

> **Analogy:** You must build the foundation (A) before the first floor (B) before the second floor (C).

---

## 6. Method Overriding

A child class provides its **own version** of a method that the parent already defines.

### Rules of overriding
1. Same **name** and same **parameter list**.
2. Return type must be the **same or a subtype** (covariant return).
3. Access level can be the **same or wider** (cannot be more restrictive).
4. Cannot override `private`, `static`, or `final` methods.
5. Cannot throw broader **checked exceptions** than the parent method.
6. Use **`@Override`** so the compiler checks your work.

```java
class Animal {
    void makeSound() { System.out.println("Some sound"); }
}

class Dog extends Animal {
    @Override
    void makeSound() { System.out.println("Woof!"); }
}
```

### Overriding vs Overloading

| Overriding | Overloading |
|---|---|
| Between parent and child | Within the same class (or inherited) |
| Same signature | Different parameters |
| Decided at **runtime** | Decided at **compile time** |
| Needs inheritance | Does not need inheritance |

---

## 7. The `Object` Class

Every class in Java **implicitly extends `java.lang.Object`** (the root of the hierarchy). So every object has methods like:

| Method | Purpose |
|---|---|
| `toString()` | Text representation (override it for readable output) |
| `equals(Object o)` | Content comparison (override it with `hashCode()`) |
| `hashCode()` | Hash value used in `HashMap`, `HashSet` |
| `getClass()` | Runtime class information |

```java
class Dog extends Animal {
    @Override
    public String toString() { return "Dog(" + name + ")"; }
}
System.out.println(new Dog("Rex"));   // Dog(Rex)
```

---

## 8. `final` with Inheritance

| Where | Effect |
|---|---|
| `final class X` | Cannot be extended (example: `String`) |
| `final void m()` | Cannot be overridden |
| `final int x` | Cannot be reassigned |

---

## 9. Is-A vs Has-A (Inheritance vs Composition)

| Is-A (inheritance) | Has-A (composition) |
|---|---|
| `Dog` **is an** `Animal` | `Car` **has an** `Engine` |
| `class Dog extends Animal` | `class Car { Engine engine; }` |
| Tight coupling | Loose coupling, more flexible |

> **Industry advice: "Favor composition over inheritance."** Use inheritance only for a true is-a relationship. Inheriting just to reuse code often creates fragile designs.

```java
class Engine { void start() { System.out.println("Engine started"); } }

class Car {
    private Engine engine = new Engine();       // has-a
    void drive() { engine.start(); System.out.println("Car moving"); }
}
```

---

## 10. Complete Example

```java
// FILE: Main.java
class Animal {
    protected String name;

    Animal(String name) {
        this.name = name;
    }

    void eat() {
        System.out.println(name + " is eating");
    }

    void makeSound() {
        System.out.println(name + " makes a sound");
    }
}

class Dog extends Animal {
    Dog(String name) {
        super(name);
    }

    @Override
    void makeSound() {
        System.out.println(name + " says Woof!");
    }

    void fetch() {
        System.out.println(name + " fetches the ball");
    }
}

class Cat extends Animal {
    Cat(String name) {
        super(name);
    }

    @Override
    void makeSound() {
        System.out.println(name + " says Meow!");
    }

    void climb() {
        System.out.println(name + " climbs the tree");
    }
}

class Bird extends Animal {
    Bird(String name) {
        super(name);
    }

    @Override
    void makeSound() {
        System.out.println(name + " says Tweet!");
    }

    void fly() {
        System.out.println(name + " is flying");
    }
}

class Puppy extends Dog {               // multilevel: Animal -> Dog -> Puppy
    Puppy(String name) {
        super(name);
    }

    @Override
    void makeSound() {
        super.makeSound();              // Dog's version first
        System.out.println(name + " also yips!");
    }

    void play() {
        System.out.println(name + " plays with a toy");
    }
}

class A { A() { System.out.println("A constructor"); } }
class B extends A { B() { System.out.println("B constructor"); } }
class C extends B { C() { System.out.println("C constructor"); } }

public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog("Rex");
        dog.eat();          // inherited
        dog.makeSound();    // overridden
        dog.fetch();        // own

        Cat cat = new Cat("Tom");
        cat.makeSound();
        cat.climb();

        Bird bird = new Bird("Tweety");
        bird.makeSound();
        bird.fly();

        Puppy puppy = new Puppy("Bolt");
        puppy.eat();        // from Animal (two levels up)
        puppy.makeSound();  // uses super.makeSound()
        puppy.play();

        System.out.println("--- Constructor order ---");
        new C();
    }
}
```

**Output**
```
Rex is eating
Rex says Woof!
Rex fetches the ball
Tom says Meow!
Tom climbs the tree
Tweety says Tweet!
Tweety is flying
Bolt is eating
Bolt says Woof!
Bolt also yips!
Bolt plays with a toy
--- Constructor order ---
A constructor
B constructor
C constructor
```

---

## 11. Tips and Tricks

- **Always write `@Override`.** If you misspell the method name or change a parameter, the compiler tells you instead of silently creating a new method.
- **Is-a test first.** If "Child is a Parent" sounds wrong, use composition.
- **Keep hierarchies shallow** (2-3 levels). Deep hierarchies are hard to understand and change.
- **Use `protected` carefully.** It exposes fields to every subclass. Prefer `private` fields plus `protected`/`public` getters.
- **Put common code in the parent; specialized code in the child.** If a method is not valid for all children, it probably does not belong in the parent.
- **Never call an overridable method from a constructor.** The child's override may run *before* the child's fields are initialized:
  ```java
  class P { P() { init(); } void init() {} }
  class Q extends P { int x = 5; @Override void init() { System.out.println(x); } }
  new Q();   // prints 0, not 5!
  ```
- **Override `toString()`** in your classes for easier debugging.
- **Use `final`** on classes/methods that must not be changed (security, immutability).
- **Remember:** a child object contains the parent part inside it, which is why the parent constructor must run first.

---

## 12. Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| Forgetting `super(args)` when parent has no no-arg constructor | Compile error: "constructor Animal cannot be applied" | Call `super(name)` explicitly |
| Overriding with weaker access (`public` -> `protected`) | Compile error | Keep same or wider access |
| Thinking `private` members are not part of the object | They exist but are inaccessible directly | Use getters |
| Trying to override a `static` method | It is **hiding**, not overriding | Use instance methods |
| Using inheritance only for code reuse | Fragile design | Use composition |
| Trying `class C extends A, B` | Compile error | Extend one class; implement interfaces |
| Overriding without `@Override` and making a typo | New method instead of an override | Always use `@Override` |

---

## 13. Interview Questions and Answers

**Q1. What is inheritance?**
A mechanism where a subclass acquires the properties and behaviors of a superclass using the `extends` keyword, enabling code reuse and an is-a hierarchy.

**Q2. What are the types of inheritance in Java?**
Single, multilevel, and hierarchical are supported with classes. Multiple and hybrid inheritance are achieved only through interfaces.

**Q3. Why doesn't Java support multiple inheritance with classes?**
To avoid the **diamond problem**, where a class inherits conflicting implementations of the same method from two parents. Java allows multiple inheritance of *type* through interfaces.

**Q4. What is the `super` keyword used for?**
To call the parent's constructor (`super(...)`), call the parent's overridden method (`super.method()`), and access a parent's hidden field (`super.field`).

**Q5. What is the difference between `this` and `super`?**
`this` refers to the current object's class members; `super` refers to the parent class members. `this(...)` calls another constructor of the same class; `super(...)` calls the parent constructor.

**Q6. Are constructors inherited?**
No. A subclass does not inherit constructors, but it must call a parent constructor (explicitly or implicitly) when its own constructor runs.

**Q7. What is method overriding? State its rules.**
A subclass providing its own implementation of a parent method with the same signature. Rules: same name and parameters, same or covariant return type, same or wider access, no overriding of `private`/`static`/`final` methods, and no broader checked exceptions.

**Q8. What is the difference between overloading and overriding?**
Overloading: same name, different parameters, in the same class, resolved at compile time. Overriding: same signature in parent and child, resolved at runtime.

**Q9. Can we override a `static` method? A `private` method?**
No. A static method with the same signature in the child **hides** the parent's. A private method is not visible to the child, so a same-named method there is a completely new method.

**Q10. Can we override a `final` method or extend a `final` class?**
No to both.

**Q11. What is the `Object` class?**
The root of the Java class hierarchy. Every class extends it directly or indirectly, giving all objects methods like `toString()`, `equals()`, `hashCode()`, and `getClass()`.

**Q12. What happens if the parent class has only a parameterized constructor and the child doesn't call `super(...)`?**
Compile error. The compiler inserts `super()` automatically, but no no-arg constructor exists in the parent.

**Q13. What is the order of constructor execution in multilevel inheritance?**
From the top-most parent down to the child: grandparent -> parent -> child.

**Q14. What is the difference between inheritance and composition?**
Inheritance models "is-a" (class extends class) and creates tight coupling. Composition models "has-a" (a class holds a reference to another) and is more flexible. Prefer composition unless there is a true is-a relationship.

**Q15. What is a covariant return type?**
An overriding method may return a **subtype** of the parent method's return type.
```java
class Animal { Animal get() { return this; } }
class Dog extends Animal { @Override Dog get() { return this; } }
```

**Q16. Can a subclass access a parent's private field?**
Not directly. It must use a public/protected getter defined in the parent.

**Q17. Output question:**
```java
class P { P() { System.out.println("P"); } }
class Q extends P { Q() { System.out.println("Q"); } Q(int x) { this(); System.out.println("Q-int"); } }
new Q(5);
```
**Answer:** `P`, `Q`, `Q-int`. Parent constructor first, then `Q()`, then the rest of `Q(int)`.

---

## 14. Practice Exercises

1. Create `Vehicle` (brand, speed, `move()`), `Car` and `Bike` extending it, each overriding `move()`.
2. Add a `ElectricCar extends Car` with a `batteryLevel` and use `super.move()` inside its `move()`.
3. Create classes A, B, C with constructors that print messages. Predict the order, then run it.
4. Redesign a `Car extends Engine` mistake as `Car has an Engine` using composition.
5. Create a `final class` and try to extend it. Read the compiler error.

---

## 15. Summary

- `extends` = reuse + specialize; test with **is-a**.
- Java supports single, multilevel, hierarchical inheritance with classes (no multiple inheritance).
- `super` accesses the parent; the parent constructor always runs first.
- Overriding changes behavior in the child; use `@Override`.
- Every class extends `Object`. Prefer composition when the relationship is not truly is-a.

**Previous:** [Topic 6](06-encapsulation.md) | **Next:** [Topic 8: Polymorphism](08-polymorphism.md)

# Topic 1: What is OOP and Why?

> **Level:** Beginner | **Prerequisites:** Java basic syntax (variables, loops, methods)

## Table of Contents
1. [Overview](#1-overview)
2. [The Problem OOP Solves](#2-the-problem-oop-solves)
3. [Core Idea: State + Behavior](#3-core-idea-state--behavior)
4. [The Four Pillars of OOP](#4-the-four-pillars-of-oop)
5. [Complete Example](#5-complete-example)
6. [Advantages and Disadvantages](#6-advantages-and-disadvantages)
7. [Tips and Tricks](#7-tips-and-tricks)
8. [Common Mistakes](#8-common-mistakes)
9. [Interview Questions and Answers](#9-interview-questions-and-answers)
10. [Practice Exercises](#10-practice-exercises)
11. [Summary](#11-summary)

---

## 1. Overview

**Object-Oriented Programming (OOP)** is a way of designing programs around **objects** instead of around a list of steps (functions). An object bundles **data** (what it knows) and **behavior** (what it can do) in one place.

Think of the real world. You do not think "a car is a list of functions". You think: *a car has a brand, color, and speed, and it can accelerate and brake*. OOP lets you write code the same way you think about the world.

---

## 2. The Problem OOP Solves

### Procedural style (before OOP)

In procedural programming, data and functions live separately.

```java
// Procedural thinking: one account only
static double balance = 1000;

static void deposit(double amount) {
    balance += amount;
}
```

What if you need **100 accounts**? You would need `balance1`, `balance2`, ... `balance100`, and every function would need to know *which* balance to change. Anyone can also change `balance` directly, even to a wrong value. This quickly becomes messy and unsafe.

### OOP style

```java
class BankAccount {
    String owner;
    double balance;

    void deposit(double amount) {
        balance += amount;
    }
}
```

Now you can create as many accounts as you want. Each one carries its own data and knows how to operate on that data.

| Procedural | Object-Oriented |
|---|---|
| Focus on **functions** (steps) | Focus on **objects** (things) |
| Data and functions are separate | Data and functions are bundled together |
| Data is usually open to everyone | Data can be protected (encapsulation) |
| Harder to scale to large programs | Easier to organize and reuse in large programs |
| Examples: C, early Pascal | Examples: Java, C++, Python, C# |

---

## 3. Core Idea: State + Behavior

Every object has two things:

- **State** (also called *attributes* or *fields*): the data the object holds.
- **Behavior** (also called *methods*): the actions the object can perform.

| Real-life thing | State (data) | Behavior (actions) |
|---|---|---|
| Car | brand, color, speed | accelerate, brake, honk |
| Student | name, roll, marks | study, giveExam, showResult |
| Phone | model, battery%, storage | call, sendMessage, charge |
| ATM | location, cashAvailable | withdraw, checkBalance, deposit |
| Mobile banking app (bKash style) | accountNo, balance | sendMoney, cashOut, checkBalance |

> **Trick to find objects:** read a problem statement and underline the **nouns** (these become classes/fields) and the **verbs** (these become methods).
> Example: "A **student** takes **exams** and receives **marks**." -> classes: `Student`, `Exam`; field: `marks`; method: `takeExam()`.

---

## 4. The Four Pillars of OOP

| Pillar | One-line meaning | Real-life example |
|---|---|---|
| **Encapsulation** | Hide data inside the object and control access to it | A capsule hides the medicine inside; an ATM hides its internal cash handling and gives you only buttons |
| **Inheritance** | A new class reuses and extends an existing class | A `Dog` *is an* `Animal`, so it gets eating and sleeping for free |
| **Polymorphism** | One action, many forms | The "play" button plays music, video, or a podcast depending on what you open |
| **Abstraction** | Show only what is necessary, hide the complexity | You press the car pedal; you don't manage fuel injection yourself |

You will learn each pillar in detail in later topics. For now, just remember the names and the one-line meaning. A common memory trick is **A-PIE**: **A**bstraction, **P**olymorphism, **I**nheritance, **E**ncapsulation.

---

## 5. Complete Example

```java
// FILE: Main.java
class BankAccount {
    String owner;
    double balance;

    void deposit(double amount) {
        balance += amount;
    }

    void withdraw(double amount) {
        if (amount <= balance) {
            balance -= amount;
        } else {
            System.out.println("Insufficient balance for " + owner);
        }
    }

    void show() {
        System.out.println(owner + " -> " + balance);
    }
}

public class Main {
    public static void main(String[] args) {
        BankAccount a = new BankAccount();
        a.owner = "Rahim";
        a.balance = 1000;

        BankAccount b = new BankAccount();
        b.owner = "Karim";
        b.balance = 500;

        a.deposit(200);   // only Rahim's account changes
        b.withdraw(800);  // Karim does not have enough money
        a.show();
        b.show();
    }
}
```

**Output**
```
Insufficient balance for Karim
Rahim -> 1200.0
Karim -> 500.0
```

**Explanation:** `BankAccount` is the blueprint. `a` and `b` are two separate objects made from it. Each has its own `owner` and `balance`. Changing `a` never affects `b`.

---

## 6. Advantages and Disadvantages

### Advantages
- **Reusability:** write a class once, use it many times (and extend it via inheritance).
- **Maintainability:** a bug in `BankAccount` is fixed in one place.
- **Security:** data can be hidden and validated (encapsulation).
- **Real-world modeling:** code structure matches the problem.
- **Teamwork:** different developers can work on different classes.
- **Easy to extend:** add new features without rewriting old code.

### Disadvantages
- More planning and design needed up front.
- For tiny scripts, it can feel like extra work.
- Slightly more memory/processing overhead compared to simple procedural code.

---

## 7. Tips and Tricks

- **Think in nouns and verbs.** Nouns become classes and fields; verbs become methods.
- **One class, one responsibility.** A `Student` class should not also send emails or connect to a database.
- **Start from the real world, then simplify.** Model only what your program actually needs (a `Car` in a racing game needs `speed`; in a car-rental app it needs `pricePerDay`).
- **Class names are nouns in `PascalCase`** (`BankAccount`); **method names are verbs in `camelCase`** (`withdrawMoney`).
- **Draw it on paper first.** A quick box with "fields" and "methods" saves a lot of confusion.
- **Don't force OOP everywhere.** A small helper like `Math.max()` is fine as a plain static method.

---

## 8. Common Mistakes

| Mistake | Why it is a problem | Better way |
|---|---|---|
| Putting everything in `main` | That is procedural code inside Java | Move logic into classes and methods |
| One giant class that does everything | Hard to read, test, and change | Split by responsibility |
| Confusing class and object | Leads to errors like calling instance methods without an object | Class = blueprint, object = real thing |
| Making every field public | Anyone can set invalid values | Use encapsulation (Topic 6) |

---

## 9. Interview Questions and Answers

**Q1. What is OOP?**
OOP is a programming paradigm that organizes software around objects, which combine data (fields) and behavior (methods). Its four main pillars are encapsulation, inheritance, polymorphism, and abstraction.

**Q2. What are the four pillars of OOP?**
Encapsulation (hide data), Inheritance (reuse code through an "is-a" relationship), Polymorphism (one interface, many forms), and Abstraction (expose only essential details).

**Q3. What is the difference between procedural programming and OOP?**
Procedural programming is function-centric and keeps data and functions separate. OOP is object-centric and bundles data with the methods that operate on it, which gives better organization, security, and reuse.

**Q4. What are the advantages of OOP?**
Code reusability, easier maintenance, data security through encapsulation, better modeling of real-world problems, and easier teamwork and extension.

**Q5. Is Java a 100% object-oriented language?**
No. Java is *mostly* object-oriented, but it has **primitive types** (`int`, `double`, `boolean`, etc.) that are not objects, and `static` members that belong to a class and not to an object. A purely object-oriented language treats everything as an object.

**Q6. What is the difference between a class and an object?**
A class is a blueprint (a definition), and an object is a real instance created from that blueprint in memory. Example: `Car` is the class; your white Toyota is an object.

**Q7. What do "state" and "behavior" mean for an object?**
State is the data stored in the object's fields at a given time (for example `speed = 60`). Behavior is what the object can do, defined by its methods (for example `brake()`).

**Q8. Why do we need OOP when we already have functions?**
Functions alone do not group related data together or protect it. In big programs this leads to scattered, duplicated, and unsafe code. OOP groups related data and behavior, hides internal details, and allows reuse through inheritance and polymorphism.

**Q9. What are some other OOP languages?**
C++, Python, C#, Kotlin, Swift, Ruby, and Scala.

**Q10. Name a real-life example for each pillar.**
Encapsulation: ATM machine. Inheritance: Dog is an Animal. Polymorphism: a "speak" action that sounds different for each animal. Abstraction: driving a car without knowing engine internals.

> **Interview tip:** Always follow a definition with a short real-life example. Interviewers remember examples more than definitions.

---

## 10. Practice Exercises

1. Pick three real-world things (for example Car, Student, Phone) and list 3 state items and 3 behaviors for each.
2. Read this sentence and find the classes, fields, and methods: *"A library has many books. A member can borrow a book and return it."*
3. Write a `BankAccount` class with `deposit` and `withdraw`, create two accounts, and print their balances.

---

## 11. Summary

- OOP organizes code around **objects** that hold **state** and **behavior**.
- It solves the scaling and safety problems of purely procedural code.
- The four pillars are **Encapsulation, Inheritance, Polymorphism, Abstraction**.
- Java is mostly (not 100%) object-oriented because of primitives and static members.

**Next:** [Topic 2: Class and Object](02-class-and-object.md)

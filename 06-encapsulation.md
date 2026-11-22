# Topic 6: Encapsulation

> **Level:** Beginner to Intermediate | **Prerequisites:** [Topic 5: this Keyword](05-this-keyword.md)

## Table of Contents
1. [Overview](#1-overview)
2. [Access Modifiers](#2-access-modifiers)
3. [How to Achieve Encapsulation](#3-how-to-achieve-encapsulation)
4. [Why Getters and Setters? Validation](#4-why-getters-and-setters-validation)
5. [Read-Only and Write-Only Fields](#5-read-only-and-write-only-fields)
6. [Immutable Classes](#6-immutable-classes)
7. [Defensive Copying](#7-defensive-copying)
8. [Complete Example](#8-complete-example)
9. [Tips and Tricks](#9-tips-and-tricks)
10. [Common Mistakes](#10-common-mistakes)
11. [Interview Questions and Answers](#11-interview-questions-and-answers)
12. [Practice Exercises](#12-practice-exercises)
13. [Summary](#13-summary)

---

## 1. Overview

**Encapsulation** means **bundling data and methods together in a class and hiding the data from direct outside access**. Outsiders can only use the data through controlled methods.

> **Real-life examples**
> - **Medicine capsule:** the powder is hidden inside; you just swallow the capsule.
> - **ATM:** you cannot reach into the machine and take cash. You press buttons, enter a PIN, and the machine decides if the request is valid.
> - **Mobile banking app:** you cannot edit your balance directly. You can only use `sendMoney()` or `cashIn()`, and the system checks the rules.

### The problem without encapsulation

```java
class BankAccount {
    public double balance;
}

BankAccount acc = new BankAccount();
acc.balance = -999999;      // nothing stops this!
```

### With encapsulation

```java
class BankAccount {
    private double balance;

    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }
    public double getBalance() { return balance; }
}
```

Now `balance` can only change in safe ways.

---

## 2. Access Modifiers

| Modifier | Same class | Same package | Subclass (different package) | Everywhere |
|---|:---:|:---:|:---:|:---:|
| `private` | Yes | No | No | No |
| *(default, no keyword)* | Yes | Yes | No | No |
| `protected` | Yes | Yes | Yes | No |
| `public` | Yes | Yes | Yes | Yes |

**Memory trick:** from most closed to most open: `private` -> default -> `protected` -> `public`.

---

## 3. How to Achieve Encapsulation

1. Declare fields as **`private`**.
2. Provide **public getter** methods to read values.
3. Provide **public setter** methods to change values (only when needed), with validation.

```java
class Student {
    private String name;                 // 1. private field

    public String getName() {            // 2. getter
        return name;
    }

    public void setName(String name) {   // 3. setter
        this.name = name;
    }
}
```

**Naming convention (JavaBeans)**
- `getXxx()` for fields.
- `setXxx(value)` for changing fields.
- `isXxx()` for `boolean` fields (e.g., `isActive()`).

---

## 4. Why Getters and Setters? Validation

You might ask: "If I just make getters and setters for everything, isn't that the same as a public field?" Not quite. Setters let you **validate**, **log**, **convert**, or **change the internal representation later** without breaking users of your class.

```java
public void setAge(int age) {
    if (age < 5 || age > 100) {
        throw new IllegalArgumentException("Age must be between 5 and 100");
    }
    this.age = age;
}
```

Other benefits:
- **Control:** make some fields read-only.
- **Flexibility:** you can later change `private double marks` to store marks in a different way without changing outside code.
- **Debugging:** put a breakpoint in a setter to see who changes a value.

---

## 5. Read-Only and Write-Only Fields

| Type | How |
|---|---|
| **Read-only** | Provide only a getter (no setter). Often combined with `final` and set in the constructor |
| **Write-only** | Provide only a setter (rare, e.g., a password you never show back) |
| **Read-write** | Provide both |

```java
class User {
    private final int id;            // read-only: set once in constructor
    private String password;         // write-only

    User(int id) { this.id = id; }
    public int getId() { return id; }
    public void setPassword(String password) { this.password = password; }
}
```

---

## 6. Immutable Classes

An **immutable** class's objects cannot change after creation (like `String`). Rules:
1. Make the class `final` (so it can't be subclassed).
2. Make all fields `private final`.
3. No setters.
4. Initialize all fields in the constructor.
5. If a field is a mutable object, copy it when you receive it and when you return it (see defensive copy).

```java
final class Point {
    private final int x;
    private final int y;

    Point(int x, int y) { this.x = x; this.y = y; }

    int getX() { return x; }
    int getY() { return y; }

    Point withX(int newX) { return new Point(newX, y); }   // returns a NEW object
}
```

Benefits: thread-safe, safe to share, safe as `HashMap` keys.

> **Modern Java:** a `record` (Java 16+) creates an immutable data class in one line: `record Point(int x, int y) {}`.

---

## 7. Defensive Copying

If a class stores a mutable object (like a `List`) and returns it directly, outsiders can modify your internal data and break encapsulation.

```java
// BAD: leaks internal list
List<String> getMembers() { return members; }

// GOOD: returns a read-only view / copy
List<String> getMembers() { return Collections.unmodifiableList(members); }
```

Also copy incoming mutable objects in the constructor:
```java
Team(List<String> members) {
    this.members = new ArrayList<>(members);   // defensive copy
}
```

---

## 8. Complete Example

```java
// FILE: Main.java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Student {
    private final int id;          // read-only
    private String name;
    private int age;
    private double marks;

    Student(int id, String name, int age, double marks) {
        this.id = id;
        setName(name);             // reuse validation
        setAge(age);
        setMarks(marks);
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public int getAge() { return age; }
    public double getMarks() { return marks; }

    public void setName(String name) {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
        this.name = name;
    }

    public void setAge(int age) {
        if (age < 5 || age > 100) {
            throw new IllegalArgumentException("Age must be between 5 and 100");
        }
        this.age = age;
    }

    public void setMarks(double marks) {
        if (marks < 0 || marks > 100) {
            throw new IllegalArgumentException("Marks must be between 0 and 100");
        }
        this.marks = marks;
    }

    public String getGrade() {     // computed value, no field needed
        if (marks >= 80) return "A+";
        if (marks >= 70) return "A";
        if (marks >= 60) return "B";
        return "C";
    }

    public void show() {
        System.out.println(id + " | " + name + " | age " + age
                + " | marks " + marks + " | grade " + getGrade());
    }
}

final class Point {
    private final int x;
    private final int y;

    Point(int x, int y) { this.x = x; this.y = y; }
    int getX() { return x; }
    int getY() { return y; }
    Point withX(int newX) { return new Point(newX, y); }
}

class Team {
    private final List<String> members;

    Team(List<String> members) {
        this.members = new ArrayList<>(members);       // defensive copy in
    }

    List<String> getMembers() {
        return Collections.unmodifiableList(members);  // read-only view out
    }

    void add(String name) { members.add(name); }
}

public class Main {
    public static void main(String[] args) {
        Student s = new Student(1, "Ayesha", 20, 85.5);
        s.show();

        s.setMarks(72);
        s.show();

        try {
            s.setAge(-5);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }

        Point p1 = new Point(1, 2);
        Point p2 = p1.withX(10);
        System.out.println(p1.getX() + " vs " + p2.getX());   // 1 vs 10

        List<String> original = new ArrayList<>(List.of("Rahim", "Karim"));
        Team team = new Team(original);
        original.add("Hacker");                               // does NOT affect team
        team.add("Nabil");
        System.out.println(team.getMembers());

        try {
            team.getMembers().add("Intruder");                // blocked
        } catch (UnsupportedOperationException e) {
            System.out.println("Cannot modify team from outside");
        }
    }
}
```

**Output**
```
1 | Ayesha | age 20 | marks 85.5 | grade A+
1 | Ayesha | age 20 | marks 72.0 | grade A
Error: Age must be between 5 and 100
1 vs 10
[Rahim, Karim, Nabil]
Cannot modify team from outside
```

---

## 9. Tips and Tricks

- **Default to `private`.** Open things up only when you have a reason.
- **Don't blindly generate setters for every field.** Ask "should anyone be allowed to change this after creation?" If not, skip the setter.
- **Validate in the constructor and setter** (reuse one method) so an object is never in an invalid state.
- **Expose behavior, not data.** Prefer `account.withdraw(500)` over `account.setBalance(account.getBalance() - 500)`.
- **Computed getters** (like `getGrade()`) can return values that are derived and not stored.
- **Use `final` fields** for values that never change after construction.
- **IDE shortcut:** IntelliJ `Alt+Insert` -> Getter and Setter. Libraries like Lombok (`@Getter`, `@Setter`) can generate them, and `record` removes the boilerplate for simple data classes.
- **Encapsulation is also about classes and packages:** keep helper classes package-private (no `public`) if only your package should use them.

---

## 10. Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| Public fields | Anyone can set invalid values | `private` + getters/setters |
| Setters without validation | Same as public fields | Add validation |
| Returning a mutable internal list/array directly | Outsiders can change internal state | Return a copy or unmodifiable view |
| Getter returns `void` or setter returns a value by mistake | Breaks conventions and tools | Follow `getX()` / `setX(value)` |
| Making fields `private` but adding a setter for all of them automatically | Defeats the purpose | Only add what is needed |
| Forgetting `this.` in setter | Field not updated | `this.age = age;` |

---

## 11. Interview Questions and Answers

**Q1. What is encapsulation?**
Binding data (fields) and the methods that operate on them into one unit (class) and restricting direct access to the data, typically by making fields `private` and exposing them through public methods.

**Q2. How do you achieve encapsulation in Java?**
Declare fields `private` and provide public getters and setters (with validation where needed).

**Q3. What are the benefits of encapsulation?**
Data hiding and security, validation and control, flexibility to change internal implementation without affecting users, easier maintenance and testing, and reduced coupling.

**Q4. What is the difference between encapsulation and abstraction?**

| Encapsulation | Abstraction |
|---|---|
| Hides **data** (how it is stored) | Hides **complexity** (how it works) |
| Achieved by access modifiers and getters/setters | Achieved by abstract classes and interfaces |
| About protecting state | About exposing only essential features |
| Example: private `balance` field | Example: `withdraw()` without showing internal steps |

**Q5. What is a fully encapsulated class?**
A class in which all fields are `private`, and access is only through public methods.

**Q6. What are the access modifiers in Java?**
`private`, default (package-private), `protected`, and `public`.

**Q7. Why not make all fields `public` if getters and setters just read and write them?**
Public fields allow invalid values and cannot be changed later without breaking all outside code. Setters let you validate and change internal representation freely.

**Q8. How do you create an immutable class?**
Make the class `final`, fields `private final`, set them only in the constructor, no setters, and defensively copy any mutable fields.

**Q9. Is `String` immutable? Why?**
Yes. Immutability makes strings safe to share, thread-safe, cacheable (the String pool), and safe to use as hash keys.

**Q10. Can encapsulation be achieved without getters and setters?**
Yes. The core idea is hiding data behind a controlled interface. A class can expose only behavior methods (`deposit()`, `withdraw()`) and no getters/setters at all.

**Q11. What is a JavaBean?**
A class that follows conventions: private fields, public getters/setters, a public no-arg constructor, and (optionally) serializable.

**Q12. What is `protected` access?**
Accessible within the same package and in subclasses (even in different packages).

**Q13. Tricky question:** "Does making a field `private` make an object immutable?"
No. `private` only hides access. If a setter exists, or if the field is a mutable object returned directly, the state can still change.

---

## 12. Practice Exercises

1. Build a `BankAccount` with private `balance`, `deposit`, `withdraw` (no negative or overdraft), and `getBalance()`. Do **not** add `setBalance`.
2. Create an immutable `Money` class (amount and currency) with a method `add(Money other)` returning a new `Money`.
3. Add a `private final List<String> subjects` to `Student` and return an unmodifiable view from `getSubjects()`.
4. Try breaking your own class from `main`. If you can create an invalid object, fix the validation.

---

## 13. Summary

- Encapsulation = **private data + controlled public access**.
- Use validation in setters and constructors to keep objects always valid.
- Read-only: no setter. Immutable: `final` class, `private final` fields, no setters.
- Never leak mutable internals; use defensive copies.
- Access levels: `private` < default < `protected` < `public`.

**Previous:** [Topic 5](05-this-keyword.md) | **Next:** [Topic 7: Inheritance](07-inheritance.md)

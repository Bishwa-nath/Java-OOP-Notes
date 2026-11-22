# Java OOP Notes (Topics 1-8)

Beginner-friendly Java Object-Oriented Programming notes. Every topic has its own file and follows the same structure, so you can study or review any topic on its own.

## What is inside each file

- Clear explanation with **real-life examples**
- **Complete, runnable code examples** with expected output
- **Tips and tricks**
- **Common mistakes** and how to fix them
- **Interview questions and answers**
- **Practice exercises**
- A short **summary**

## Topics

| # | Topic | File |
|---|---|---|
| 1 | What is OOP and Why? | [01-what-is-oop.md](01-what-is-oop.md) |
| 2 | Class and Object | [02-class-and-object.md](02-class-and-object.md) |
| 3 | Field and Method | [03-field-and-method.md](03-field-and-method.md) |
| 4 | Constructor | [04-constructor.md](04-constructor.md) |
| 5 | The `this` Keyword | [05-this-keyword.md](05-this-keyword.md) |
| 6 | Encapsulation | [06-encapsulation.md](06-encapsulation.md) |
| 7 | Inheritance | [07-inheritance.md](07-inheritance.md) |
| 8 | Polymorphism | [08-polymorphism.md](08-polymorphism.md) |

## Suggested learning order

Follow the topics in order. Each one builds on the previous one:

```
OOP idea -> Class/Object -> Fields/Methods -> Constructor -> this
        -> Encapsulation -> Inheritance -> Polymorphism
```

## How to run the code examples

Every complete example starts with `// FILE: Main.java`. To run one:

```bash
# 1. Copy the code block into Main.java
# 2. Compile and run
javac Main.java
java Main
```

**Requirement:** JDK 16 or newer (some examples use `instanceof` pattern matching, introduced in Java 16).

## How to study (suggested method)

1. Read the explanation and the real-life example.
2. Type the code yourself (do not copy-paste), run it, and change things.
3. Do the practice exercises without looking at notes.
4. Answer the interview questions out loud, with an example.

## How to push to GitHub

```bash
git init
git add .
git commit -m "Add Java OOP notes (topics 1-8)"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

## Quick revision cheat sheet

| Concept | One-line memory |
|---|---|
| Class | Blueprint |
| Object | Real thing built from the blueprint (`new`) |
| Field | Data (state) |
| Method | Action (behavior) |
| Constructor | Runs automatically on `new`; same name as class; no return type |
| `this` | The current object |
| Encapsulation | `private` data + public methods |
| Inheritance | `extends`; is-a; parent constructor runs first |
| Polymorphism | Overloading (compile time) + Overriding (runtime) |

Happy learning!

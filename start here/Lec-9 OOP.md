# 🐍 Python Basics — Object-Oriented Programming (CS50P Lecture 8 Notes)

> Part 9 of the series. Make sure you've been through [Lecture 0 — Functions & Variables](#) through [Lecture 7 — Regular Expressions](#) first — this builds directly on all of them, especially `def`, dictionaries, and exceptions.
> Beginner-friendly notes based on **CS50's Introduction to Programming with Python — Lecture 8**.

📺 Original video: [CS50P Lecture 8 — Object-Oriented Programming](https://youtu.be/e4fwY9ZsxPw)
📚 Official notes: [cs50.harvard.edu/python/notes/8](https://cs50.harvard.edu/python/notes/8/)

---

## 📖 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [A New Paradigm](#-a-new-paradigm)
3. [The Procedural Starting Point](#-the-procedural-starting-point)
4. [Bundling Data With Tuples](#-bundling-data-with-tuples)
5. [Mutability: Tuples vs Lists](#-mutability-tuples-vs-lists)
6. [Using a Dictionary Instead](#-using-a-dictionary-instead)
7. [Enter: Classes](#-enter-classes)
8. [`__init__` — The Constructor](#-__init__--the-constructor)
9. [`raise` — Validating on Creation](#-raise--validating-on-creation)
10. [`__str__` — Controlling How an Object Prints](#-__str__--controlling-how-an-object-prints)
11. [Writing Your Own Methods](#-writing-your-own-methods)
12. [`@property` — Getters and Setters](#-property--getters-and-setters)
13. [You've Been Using Classes All Along](#-youve-been-using-classes-all-along)
14. [`@classmethod` — Functionality on the Class Itself](#-classmethod--functionality-on-the-class-itself)
15. [`@staticmethod` — A Quick Mention](#-staticmethod--a-quick-mention)
16. [Inheritance](#-inheritance)
17. [Inheritance in Exceptions](#-inheritance-in-exceptions)
18. [Operator Overloading](#-operator-overloading)
19. [Summary](#-summary)
20. [Practice Ideas](#-practice-ideas)

---

## ✅ Prerequisites

- Comfortable with `def` functions, `return`, and dictionaries (Lectures 0 & 2)
- Comfortable with `raise` and exception hierarchies (Lecture 3)
- Comfortable importing modules (Lecture 4)

---

## 🧭 A New Paradigm

Every program you've written in this series so far has followed the same style: a sequence of steps, executed one after another, top to bottom — occasionally organized into functions. This is called **procedural programming**.

**Object-oriented programming (OOP)** is a different way of thinking about code entirely — instead of just functions acting on data, you build your own custom **data types**, bundling data *and* the behavior that belongs with it into a single unit. As you learn more languages down the road, you'll recognize this pattern everywhere.

---

## 🚶 The Procedural Starting Point

Let's build up to OOP the same way the lecture does — starting from something very familiar:

```bash
code student.py
```

```python
name = input("Name: ")
house = input("House: ")
print(f"{name} from {house}")
```

Totally procedural. Let's abstract it with functions, like you learned in Lecture 0:

```python
def main():
    name = get_name()
    house = get_house()
    print(f"{name} from {house}")


def get_name():
    return input("Name: ")


def get_house():
    return input("House: ")


if __name__ == "__main__":
    main()
```

---

## 📦 Bundling Data With Tuples

Right now `name` and `house` are two *separate* variables that just happen to be related. A **tuple** — a sequence of values, similar to a list but **immutable** (unchangeable) — lets us bundle them:

```python
def main():
    name, house = get_student()
    print(f"{name} from {house}")


def get_student():
    name = input("Name: ")
    house = input("House: ")
    return name, house


if __name__ == "__main__":
    main()
```

We can also keep the tuple packed together instead of immediately unpacking it:

```python
def main():
    student = get_student()
    print(f"{student[0]} from {student[1]}")


def get_student():
    name = input("Name: ")
    house = input("House: ")
    return (name, house)


if __name__ == "__main__":
    main()
```

`student[0]` and `student[1]` index into the tuple, just like a list.

---

## 🔒 Mutability: Tuples vs Lists

Because tuples are **immutable**, this deliberately fails:

```python
def main():
    student = get_student()
    if student[0] == "Padma":
        student[1] = "Ravenclaw"   # ❌ TypeError!
    print(f"{student[0]} from {student[1]}")


def get_student():
    name = input("Name: ")
    house = input("House: ")
    return name, house


if __name__ == "__main__":
    main()
```

Immutability is actually a **feature**, not a bug — it's a way of programming *defensively*, guaranteeing a value can't accidentally be changed later. If you genuinely need to modify values after creation, use a `list` instead:

```python
def get_student():
    name = input("Name: ")
    house = input("House: ")
    return [name, house]
```

But now `student[0]` and `student[1]` are a little *cryptic* — nothing in the code tells a reader which index means "name" and which means "house."

---

## 🗂️ Using a Dictionary Instead

A dictionary (Lecture 2!) solves the readability problem, since we can use meaningful **keys** instead of numeric indices:

```python
def main():
    student = get_student()
    print(f"{student['name']} from {student['house']}")


def get_student():
    name = input("Name: ")
    house = input("House: ")
    return {"name": name, "house": house}


if __name__ == "__main__":
    main()
```

Much more readable! But there's still a subtle problem: **nothing stops** another programmer from mistyping a key, like `student["naem"]`, and Python won't catch it until the program crashes at runtime. We want something more structured — a real, custom **data type**.

---

## 🏛️ Enter: Classes

A **class** is a blueprint for creating your own custom data type. Let's define one called `Student`:

```python
class Student:
    ...


def main():
    student = get_student()
    print(f"{student.name} from {student.house}")


def get_student():
    student = Student()
    student.name = input("Name: ")
    student.house = input("House: ")
    return student


if __name__ == "__main__":
    main()
```

- By convention, class names are **capitalized** (`Student`, not `student`).
- `student = Student()` creates a new **object** (also called an **instance**) of the `Student` class.
- `student.name` uses **dot notation** to access an attribute belonging to that specific object.

---

## 🛠️ `__init__` — The Constructor

Right now, nothing *guarantees* every `Student` object actually has a `name` and `house` — we're just bolting them on after the fact. Python lets us define a special method, `__init__`, that runs automatically the moment an object is created:

```python
class Student:
    def __init__(self, name, house):
        self.name = name
        self.house = house


def main():
    student = get_student()
    print(f"{student.name} from {student.house}")


def get_student():
    name = input("Name: ")
    house = input("House: ")
    student = Student(name, house)
    return student


if __name__ == "__main__":
    main()
```

- `__init__` is called the **constructor**.
- `self` refers to *this particular* object being created — it's always the first parameter of any method inside a class.
- `Student(name, house)` calls `__init__` behind the scenes, assigning both values onto the new object.

We can simplify further:

```python
def get_student():
    name = input("Name: ")
    house = input("House: ")
    return Student(name, house)
```

📚 Learn more: [Python's `classes` documentation](https://docs.python.org/3/tutorial/classes.html)

---

## 🚨 `raise` — Validating on Creation

One of OOP's biggest strengths: you can **encapsulate** validation logic directly inside the class itself, so it's *impossible* to create a broken object in the first place. Recall `raise` from Lecture 3:

```python
class Student:
    def __init__(self, name, house):
        if not name:
            raise ValueError("Missing name")
        if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
            raise ValueError("Invalid house")
        self.name = name
        self.house = house


def main():
    student = get_student()
    print(f"{student.name} from {student.house}")


def get_student():
    name = input("Name: ")
    house = input("House: ")
    return Student(name, house)


if __name__ == "__main__":
    main()
```

Now, trying to create a `Student` with a blank name or an invalid house immediately `raise`s a `ValueError` — the bad object never even gets created.

---

## 🖨️ `__str__` — Controlling How an Object Prints

Try `print(student)` right now and you'll get an ugly, unhelpful default like `<__main__.Student object at 0x...>`. Define `__str__` to control exactly what gets printed:

```python
class Student:
    def __init__(self, name, house, patronus):
        if not name:
            raise ValueError("Missing name")
        if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
            raise ValueError("Invalid house")
        self.name = name
        self.house = house
        self.patronus = patronus

    def __str__(self):
        return f"{self.name} from {self.house}"


def main():
    student = get_student()
    print(student)


def get_student():
    name = input("Name: ")
    house = input("House: ")
    patronus = input("Patronus: ")
    return Student(name, house, patronus)


if __name__ == "__main__":
    main()
```

Now `print(student)` calls `__str__` automatically, and shows a clean, human-readable string instead.

---

## ⚡ Writing Your Own Methods

Beyond `__init__` and `__str__` (which Python calls automatically), you can write **any method you like**, with any name:

```python
class Student:
    def __init__(self, name, house, patronus=None):
        if not name:
            raise ValueError("Missing name")
        if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
            raise ValueError("Invalid house")
        if patronus and patronus not in ["Stag", "Otter", "Jack Russell terrier"]:
            raise ValueError("Invalid patronus")
        self.name = name
        self.house = house
        self.patronus = patronus

    def __str__(self):
        return f"{self.name} from {self.house}"

    def charm(self):
        match self.patronus:
            case "Stag":
                return "🐴"
            case "Otter":
                return "🦦"
            case "Jack Russell terrier":
                return "🐶"
            case _:
                return "🪄"


def main():
    student = get_student()
    print("Expecto Patronum!")
    print(student.charm())


def get_student():
    name = input("Name: ")
    house = input("House: ")
    patronus = input("Patronus: ") or None
    return Student(name, house, patronus)


if __name__ == "__main__":
    main()
```

`charm()` is a custom **method** — a function that belongs to (and can access) the object it's called on, via `self`. `student.charm()` reuses the `match` statement you learned back in Lecture 1.

---

## 🎁 `@property` — Getters and Setters

Even with `raise` inside `__init__`, nothing stops someone from *later* doing `student.house = "Number Four, Privet Drive"` — completely bypassing our validation!

```python
class Student:
    def __init__(self, name, house):
        if not name:
            raise ValueError("Invalid name")
        if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
            raise ValueError("Invalid house")
        self.name = name
        self.house = house

    def __str__(self):
        return f"{self.name} from {self.house}"


def main():
    student = get_student()
    student.house = "Number Four, Privet Drive"   # 😱 slips right past our check!
    print(student)
```

The fix: **properties**, defined with `@` **decorators**.

```python
class Student:
    def __init__(self, name, house):
        if not name:
            raise ValueError("Invalid name")
        self.name = name
        self.house = house

    def __str__(self):
        return f"{self.name} from {self.house}"

    # Getter for house
    @property
    def house(self):
        return self._house

    # Setter for house
    @house.setter
    def house(self, house):
        if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
            raise ValueError("Invalid house")
        self._house = house
```

Here's what's going on:

- `@property` turns `house` into a **getter** — code that runs whenever someone *reads* `student.house`.
- `@house.setter` defines a **setter** — code that runs whenever someone *assigns* `student.house = ...`, including validation!
- `_house` (with a leading underscore) is the actual stored value. The underscore is a convention signaling: *"don't touch this directly — go through the `house` property instead."*

Now `student.house = "Number Four, Privet Drive"` correctly raises a `ValueError`, exactly as it should.

We can do the exact same thing for `name`:

```python
class Student:
    def __init__(self, name, house):
        self.name = name
        self.house = house

    def __str__(self):
        return f"{self.name} from {self.house}"

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, name):
        if not name:
            raise ValueError("Invalid name")
        self._name = name

    @property
    def house(self):
        return self._house

    @house.setter
    def house(self, house):
        if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
            raise ValueError("Invalid house")
        self._house = house
```

📚 Learn more: [Python's `methods` documentation](https://docs.python.org/3/tutorial/classes.html)

---

## 🔍 You've Been Using Classes All Along

Here's a fun realization: `int`, `str`, `list`, and `dict` are **all classes** — you've been using object-oriented programming this whole series without necessarily realizing it!

```python
print(type(50))
print(type("hello, world"))
print(type([]))
print(type({}))
```

**Output:**
```
<class 'int'>
<class 'str'>
<class 'list'>
<class 'dict'>
```

Every time you've called `.strip()`, `.title()`, or `.append()`, you've been calling a **method** that lives inside `str`'s or `list`'s own class definition.

---

## 🏫 `@classmethod` — Functionality on the Class Itself

Sometimes you want a function that belongs to the **class itself**, not to any one specific object. Consider the Sorting Hat — there's only ever *one* hat, so creating an "instance" of it every time feels unnecessary:

```python
import random


class Hat:
    houses = ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]

    @classmethod
    def sort(cls, name):
        print(name, "is in", random.choice(cls.houses))


Hat.sort("Harry")
```

Notice: no `__init__`, no `self` — instead, `cls` refers to the **class itself**. We can call `Hat.sort("Harry")` directly, without ever writing `Hat()`.

Let's apply this back to `Student`, replacing the standalone `get_student()` function with a class method:

```python
class Student:
    def __init__(self, name, house):
        self.name = name
        self.house = house

    def __str__(self):
        return f"{self.name} from {self.house}"

    @classmethod
    def get(cls):
        name = input("Name: ")
        house = input("House: ")
        return cls(name, house)


def main():
    student = Student.get()
    print(student)


if __name__ == "__main__":
    main()
```

`Student.get()` now handles collecting input *and* constructing the object — all encapsulated neatly inside the class.

---

## 📌 `@staticmethod` — A Quick Mention

Beyond `@classmethod`, Python also offers `@staticmethod` — a method that belongs to a class but needs neither `self` nor `cls`. It's not covered deeply in this lecture, but worth exploring further as your OOP skills grow.

---

## 🧬 Inheritance

**Inheritance** may be the single most powerful feature of OOP: a class can inherit attributes and methods from another class, avoiding duplicated code.

```bash
code wizard.py
```

```python
class Wizard:
    def __init__(self, name):
        if not name:
            raise ValueError("Missing name")
        self.name = name


class Student(Wizard):
    def __init__(self, name, house):
        super().__init__(name)
        self.house = house


class Professor(Wizard):
    def __init__(self, name, subject):
        super().__init__(name)
        self.subject = subject


wizard = Wizard("Albus")
student = Student("Harry", "Gryffindor")
professor = Professor("Severus", "Defense Against the Dark Arts")
```

- `class Student(Wizard):` means `Student` **inherits** from `Wizard` — every student *is* a wizard, plus a `house`.
- `super().__init__(name)` calls the **parent** class's `__init__`, reusing its validation logic instead of copy-pasting it.
- The same pattern applies to `Professor`, which inherits from `Wizard` too, but adds a `subject` instead of a `house`.

This is exactly the kind of "shared behavior, different specifics" relationship that inheritance is built for.

---

## 🌳 Inheritance in Exceptions

You've actually been using inheritance the *entire course*, every time you wrote `except ValueError:`. Python's built-in exceptions form a hierarchy:

```
BaseException
 └── Exception
      ├── ArithmeticError
      │    └── ZeroDivisionError
      ├── AssertionError
      ├── AttributeError
      ├── LookupError
      │    └── KeyError
      ├── NameError
      ├── SyntaxError
      └── ValueError
```

`ZeroDivisionError` **is-a** kind of `ArithmeticError`, which **is-a** kind of `Exception`. This is why catching a more general exception type (like `Exception`) will also catch its more specific children.

📚 Learn more: [Python's exception hierarchy](https://docs.python.org/3/library/exceptions.html)

---

## ➕ Operator Overloading

Python lets you redefine what operators like `+` and `-` mean for your **own** custom classes — this is called **operator overloading**.

```bash
code vault.py
```

```python
class Vault:
    def __init__(self, galleons=0, sickles=0, knuts=0):
        self.galleons = galleons
        self.sickles = sickles
        self.knuts = knuts

    def __str__(self):
        return f"{self.galleons} Galleons, {self.sickles} Sickles, {self.knuts} Knuts"

    def __add__(self, other):
        galleons = self.galleons + other.galleons
        sickles = self.sickles + other.sickles
        knuts = self.knuts + other.knuts
        return Vault(galleons, sickles, knuts)


potter = Vault(100, 50, 25)
print(potter)

weasley = Vault(25, 50, 100)
print(weasley)

total = potter + weasley
print(total)
```

`__add__` is called automatically whenever you write `potter + weasley`: `self` is the object on the *left* of `+` (`potter`), and `other` is the object on the *right* (`weasley`). The result is a brand-new `Vault` combining both.

📚 Learn more: [Python's operator overloading documentation](https://docs.python.org/3/reference/datamodel.html#special-method-names)

---

## 📝 Summary

By the end of this lecture, you've learned:

- ✅ Procedural vs. object-oriented programming
- ✅ Tuples, and the difference between mutable and immutable data
- ✅ Why classes beat dictionaries for structured, safe data
- ✅ `class`, `__init__`, and `self`
- ✅ `raise` for validating data at creation time
- ✅ `__str__` for controlling how an object prints
- ✅ Writing your own custom methods
- ✅ `@property` and setters for protecting attributes after creation
- ✅ That `int`, `str`, `list`, and `dict` are themselves classes
- ✅ `@classmethod` and `cls`
- ✅ A quick mention of `@staticmethod`
- ✅ Inheritance, `super().__init__()`, and the exception hierarchy
- ✅ Operator overloading with `__add__`

---

## 💪 Practice Ideas

1. Build a `BankAccount` class with a `balance` property that can never go negative — raise a `ValueError` if a withdrawal would overdraw the account.
2. Add a `__str__` method to your `BankAccount` class so `print(account)` shows a friendly formatted balance.
3. Create a `Shape` parent class and `Circle`/`Rectangle` child classes that inherit from it, each with their own `area()` method.
4. Overload `__eq__` on a custom class so two objects can be compared with `==` based on their attributes rather than their memory identity.

---

## 📚 Resources

- [CS50P official course](https://cs50.harvard.edu/python/)
- [Official Lecture 8 notes](https://cs50.harvard.edu/python/notes/8/)
- [Python `classes` documentation](https://docs.python.org/3/tutorial/classes.html)
- [Python exception hierarchy](https://docs.python.org/3/library/exceptions.html)
- [Python operator overloading (data model)](https://docs.python.org/3/reference/datamodel.html#special-method-names)

---

⭐ Next up in the series: **Et Cetera** (the final lecture!) — star this repo to keep track as more lecture notes get added!

# 🐍 Python Basics — Et Cetera (CS50P Lecture 9 Notes)

> Part 10 — the **final lecture** of the series! Make sure you've been through [Lecture 0 — Functions & Variables](#) through [Lecture 8 — Object-Oriented Programming](#) first — this lecture is a grab-bag that leans on everything you've learned so far.
> Beginner-friendly notes based on **CS50's Introduction to Programming with Python — Lecture 9**.

📺 Original video: [CS50P Lecture 9 — Et Cetera](https://youtu.be/6pgodt1mezg)
📚 Official notes: [cs50.harvard.edu/python/notes/9](https://cs50.harvard.edu/python/notes/9/)

---

## 📖 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [What Does "Et Cetera" Mean Here?](#-what-does-et-cetera-mean-here)
3. [`set` — Automatic Deduplication](#-set--automatic-deduplication)
4. [Global Variables](#-global-variables)
5. [The `global` Keyword](#-the-global-keyword)
6. [A Cleaner Fix: Use a Class Instead](#-a-cleaner-fix-use-a-class-instead)
7. [Constants](#-constants)
8. [Type Hints](#-type-hints)
9. [`mypy` — Catching Type Errors Before Runtime](#-mypy--catching-type-errors-before-runtime)
10. [Docstrings](#-docstrings)
11. [`argparse` — Professional Command-Line Arguments](#-argparse--professional-command-line-arguments)
12. [Unpacking With `*`](#-unpacking-with-)
13. [Unpacking a Dictionary With `**`](#-unpacking-a-dictionary-with-)
14. [`*args` and `**kwargs`](#-args-and-kwargs)
15. [`map`](#-map)
16. [List Comprehensions](#-list-comprehensions)
17. [`filter`](#-filter)
18. [Dictionary Comprehensions](#-dictionary-comprehensions)
19. [`enumerate`](#-enumerate)
20. [Generators, `yield`, and Why They Matter](#-generators-yield-and-why-they-matter)
21. [🎓 This Was CS50P!](#-this-was-cs50p)
22. [Summary](#-summary)
23. [Practice Ideas](#-practice-ideas)

---

## ✅ Prerequisites

- Comfortable with lists, dictionaries, and `for` loops (Lecture 2)
- Comfortable with `def`, `lambda`, and `sorted(..., key=...)` (Lectures 0 & 6)
- Comfortable with classes and `@property` (Lecture 8)

---

## 🎁 What Does "Et Cetera" Mean Here?

*"Et cetera"* literally means "and the rest." After eight full lectures, you've already built a strong, genuinely useful foundation in Python. This final lecture is a tour through a grab-bag of extra tools and conventions — the kind of things you'll bump into constantly once you start reading other people's Python code, or writing more advanced programs of your own.

None of these are *new paradigms* — they're refinements and shortcuts built on everything you already know.

---

## 🧺 `set` — Automatic Deduplication

Say we want a list of unique houses from a list of students, with no duplicates:

```python
students = [
    {"name": "Hermione", "house": "Gryffindor"},
    {"name": "Harry", "house": "Gryffindor"},
    {"name": "Ron", "house": "Gryffindor"},
    {"name": "Draco", "house": "Slytherin"},
    {"name": "Padma", "house": "Ravenclaw"},
]

houses = []
for student in students:
    if student["house"] not in houses:
        houses.append(student["house"])

for house in sorted(houses):
    print(house)
```

This works, but we're manually checking for duplicates every time. Python's built-in `set` type — a collection with **no duplicates allowed**, just like a set in math — does this automatically:

```python
students = [
    {"name": "Hermione", "house": "Gryffindor"},
    {"name": "Harry", "house": "Gryffindor"},
    {"name": "Ron", "house": "Gryffindor"},
    {"name": "Draco", "house": "Slytherin"},
    {"name": "Padma", "house": "Ravenclaw"},
]

houses = set()
for student in students:
    houses.add(student["house"])

for house in sorted(houses):
    print(house)
```

No `if` check needed — `houses.add(...)` silently does nothing if the value's already there.

📚 Learn more: [Python's `set` documentation](https://docs.python.org/3/library/stdtypes.html#set)

---

## 🌍 Global Variables

Every variable you've created inside a function so far has been **local** — it only exists within that function. A **global variable** lives outside any function, and is (in theory) accessible everywhere:

```python
balance = 0


def main():
    print("Balance:", balance)


if __name__ == "__main__":
    main()
```

This runs fine — reading a global variable works. But watch what happens the moment we try to **modify** it:

```python
balance = 0


def main():
    print("Balance:", balance)
    deposit(100)
    withdraw(50)
    print("Balance:", balance)


def deposit(n):
    balance += n


def withdraw(n):
    balance -= n


if __name__ == "__main__":
    main()
```

Python throws an `UnboundLocalError`. By default, Python assumes that assigning to a variable inside a function (`balance += n`) creates a **new local variable** — it won't automatically reach outside the function to modify the global one.

---

## 🔓 The `global` Keyword

To explicitly tell a function "use the global variable, not a new local one," use `global`:

```python
balance = 0


def main():
    print("Balance:", balance)
    deposit(100)
    withdraw(50)
    print("Balance:", balance)


def deposit(n):
    global balance
    balance += n


def withdraw(n):
    global balance
    balance -= n


if __name__ == "__main__":
    main()
```

This now works correctly. But be warned: **global variables should be used sparingly, if at all.** They make code harder to reason about, since *any* function could silently change a value that many other functions also depend on.

---

## 🏛️ A Cleaner Fix: Use a Class Instead

Remember Lecture 8? Object-oriented programming solves exactly this problem, more cleanly:

```python
class Account:
    def __init__(self):
        self._balance = 0

    @property
    def balance(self):
        return self._balance

    def deposit(self, n):
        self._balance += n

    def withdraw(self, n):
        self._balance -= n


def main():
    account = Account()
    print("Balance:", account.balance)
    account.deposit(100)
    account.withdraw(50)
    print("Balance:", account.balance)


if __name__ == "__main__":
    main()
```

Now `_balance` lives safely inside each `Account` object, accessible to all its own methods via `self` — no `global` keyword, no risk of some unrelated function accidentally messing with it.

---

## 🔒 Constants

Some languages let you declare truly unchangeable variables, called **constants**. Python doesn't *technically* support this — but there's a strong, universal **convention**: name a variable in `ALL_CAPS`, and everyone reading your code understands "don't reassign this."

```python
MEOWS = 3

for _ in range(MEOWS):
    print("meow")
```

Nothing stops you from writing `MEOWS = 5` later in the same file — Python won't complain. It's an honor system, but a very widely respected one.

You can also attach a constant to a class:

```python
class Cat:
    MEOWS = 3

    def meow(self):
        for _ in range(Cat.MEOWS):
            print("meow")


cat = Cat()
cat.meow()
```

Since `MEOWS` is defined at the class level (not inside `__init__`), every method in `Cat` can access it via `Cat.MEOWS`.

---

## 🏷️ Type Hints

Python is a **dynamically typed** language — you've never had to declare `int x` the way some other languages require. But you *can* add optional **type hints** as documentation for both humans and tools:

```python
def meow(n: int):
    for _ in range(n):
        print("meow")


number = input("Number: ")
meow(number)
```

`n: int` hints that `meow` expects an integer. Python **won't actually enforce this at runtime** — the program still runs, and still has a bug (`input()` returns a `str`, not an `int`). Type hints are documentation and tooling support, not a guarantee.

---

## 🔍 `mypy` — Catching Type Errors Before Runtime

To actually *catch* type mismatches, install `mypy`, a static type checker:

```bash
pip install mypy
```

```python
def meow(n: int):
    for _ in range(n):
        print("meow")


number: int = input("Number: ")
meow(number)
```

Run `mypy meows.py`, and it will flag that `number` is annotated as `int` but `input()` actually returns a `str`. Fix it with a cast, as you've done since Lecture 0:

```python
def meow(n: int):
    for _ in range(n):
        print("meow")


number: int = int(input("Number: "))
meow(number)
```

You can also hint a function's **return type** using `->`:

```python
def meow(n: int) -> str:
    return "meow\n" * n


number: int = int(input("Number: "))
meows: str = meow(number)
print(meows, end="")
```

`-> str` tells `mypy` (and any human reading your code) exactly what `meow` hands back — catching mismatches like assuming a function returns something it doesn't.

📚 Learn more: [Python `typing` docs](https://docs.python.org/3/library/typing.html) · [mypy documentation](https://mypy.readthedocs.io/)

---

## 📝 Docstrings

A **docstring** is a standardized way to document what a function does, using triple quotes right after the `def` line:

```python
def meow(n):
    """Meow n times."""
    return "meow\n" * n


number = int(input("Number: "))
meows = meow(number)
print(meows, end="")
```

Docstrings can be as detailed as you like — describing parameters, return values, and even what exceptions might be raised:

```python
def meow(n):
    """Meow n times.

    :param n: Number of times to meow
    :type n: int
    :raise TypeError: If n is not an int
    :return: A string of n meows, one per line
    :rtype: str"""
    return "meow\n" * n
```

Tools like [Sphinx](https://www.sphinx-doc.org/) can automatically turn well-written docstrings into polished documentation websites or PDFs.

📚 Learn more: [PEP 257 — Docstring Conventions](https://peps.python.org/pep-0257/)

---

## 💻 `argparse` — Professional Command-Line Arguments

Back in Lecture 4, we handled command-line arguments with `sys.argv`, checking their length manually:

```python
import sys

if len(sys.argv) == 1:
    print("meow")
elif len(sys.argv) == 3 and sys.argv[1] == "-n":
    n = int(sys.argv[2])
    for _ in range(n):
        print("meow")
else:
    print("usage: meows.py [-n NUMBER]")
```

This gets unmanageable fast as programs grow more complex. `argparse` is a built-in library purpose-built for parsing command-line arguments properly:

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("-n")
args = parser.parse_args()

for _ in range(int(args.n)):
    print("meow")
```

Run it as `python meows.py -n 3`. We can add a description and built-in help text:

```python
import argparse

parser = argparse.ArgumentParser(description="Meow like a cat")
parser.add_argument("-n", help="number of times to meow")
args = parser.parse_args()

for _ in range(int(args.n)):
    print("meow")
```

Now `python meows.py --help` (or `-h`) automatically shows usage instructions — for free, no extra code needed!

We can go even further — a default value, and automatic type conversion (no more manual `int(...)`!):

```python
import argparse

parser = argparse.ArgumentParser(description="Meow like a cat")
parser.add_argument("-n", default=1, help="number of times to meow", type=int)
args = parser.parse_args()

for _ in range(args.n):
    print("meow")
```

📚 Learn more: [Python's `argparse` documentation](https://docs.python.org/3/library/argparse.html)

---

## 📦 Unpacking With `*`

You've briefly seen unpacking already (Lecture 6, splitting `name, house = line.split(",")`). Let's go deeper:

```python
def total(galleons, sickles, knuts):
    return (galleons * 17 + sickles) * 29 + knuts


coins = [100, 50, 25]

print(total(coins[0], coins[1], coins[2]), "Knuts")
```

Manually indexing `coins[0]`, `coins[1]`, `coins[2]` is verbose. The `*` operator **unpacks** a list directly into separate positional arguments:

```python
def total(galleons, sickles, knuts):
    return (galleons * 17 + sickles) * 29 + knuts


coins = [100, 50, 25]

print(total(*coins), "Knuts")
```

`*coins` expands the list into `total(100, 50, 25)` automatically.

---

## 🎁 Unpacking a Dictionary With `**`

What if the values live in a dictionary, keyed by name?

```python
def total(galleons, sickles, knuts):
    return (galleons * 17 + sickles) * 29 + knuts


coins = {"galleons": 100, "sickles": 50, "knuts": 25}

print(total(coins["galleons"], coins["sickles"], coins["knuts"]), "Knuts")
```

Again, verbose. `**` unpacks a dictionary into **keyword arguments**, matching each key to the correctly named parameter:

```python
def total(galleons, sickles, knuts):
    return (galleons * 17 + sickles) * 29 + knuts


coins = {"galleons": 100, "sickles": 50, "knuts": 25}

print(total(**coins), "Knuts")
```

Since dictionary keys don't have a guaranteed order, `**coins` correctly matches `"galleons"` → `galleons`, `"sickles"` → `sickles`, and so on — regardless of the order they appear in the dictionary.

---

## 🌀 `*args` and `**kwargs`

Recall `print`'s real signature: `print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)`. It accepts *any number* of positional arguments (`args`) and *any number* of named/keyword arguments (`kwargs`). You can write functions like this yourself:

```python
def f(*args, **kwargs):
    print("Positional:", args)


f(100, 50, 25)
```

**Output:** `Positional: (100, 50, 25)` — `args` collects any number of positional arguments into a tuple.

```python
def f(*args, **kwargs):
    print("Named:", kwargs)


f(galleons=100, sickles=50, knuts=25)
```

**Output:** `Named: {'galleons': 100, 'sickles': 50, 'knuts': 25}` — `kwargs` collects any number of keyword arguments into a dictionary.

This is exactly how flexible, general-purpose functions like `print` are built — they don't know in advance how many arguments you'll pass them.

📚 Learn more: [Python's `print` documentation](https://docs.python.org/3/library/functions.html#print)

---

## 🗺️ `map`

Let's revisit a simple function and evolve it step by step, from Lecture 0's procedural style toward more advanced, "functional" tools:

```python
def main():
    yell("This is CS50")


def yell(word):
    print(word.upper())


if __name__ == "__main__":
    main()
```

What if we want to yell *multiple* words? First, the manual way:

```python
def main():
    yell(["This", "is", "CS50"])


def yell(words):
    uppercased = []
    for word in words:
        uppercased.append(word.upper())
    print(*uppercased)


if __name__ == "__main__":
    main()
```

Using `*words` to accept any number of positional arguments directly (no list brackets needed):

```python
def main():
    yell("This", "is", "CS50")


def yell(*words):
    uppercased = []
    for word in words:
        uppercased.append(word.upper())
    print(*uppercased)


if __name__ == "__main__":
    main()
```

Now, `map(function, iterable)` applies a function to **every element** of a sequence, all at once, without a manual loop:

```python
def main():
    yell("This", "is", "CS50")


def yell(*words):
    uppercased = map(str.upper, words)
    print(*uppercased)


if __name__ == "__main__":
    main()
```

`str.upper` is passed as a **function itself** (not called — no parentheses!), and `map` applies it to each word in turn.

📚 Learn more: [Python's `map` documentation](https://docs.python.org/3/library/functions.html#map)

---

## ✨ List Comprehensions

You've actually already seen these back in earlier lectures — here's the formal introduction. A **list comprehension** builds a list in a single, elegant line:

```python
def main():
    yell("This", "is", "CS50")


def yell(*words):
    uppercased = [arg.upper() for arg in words]
    print(*uppercased)


if __name__ == "__main__":
    main()
```

Let's see a more realistic example — filtering *and* extracting in one line:

```python
students = [
    {"name": "Hermione", "house": "Gryffindor"},
    {"name": "Harry", "house": "Gryffindor"},
    {"name": "Ron", "house": "Gryffindor"},
    {"name": "Draco", "house": "Slytherin"},
]

gryffindors = []
for student in students:
    if student["house"] == "Gryffindor":
        gryffindors.append(student["name"])

for gryffindor in sorted(gryffindors):
    print(gryffindor)
```

As a list comprehension:

```python
students = [
    {"name": "Hermione", "house": "Gryffindor"},
    {"name": "Harry", "house": "Gryffindor"},
    {"name": "Ron", "house": "Gryffindor"},
    {"name": "Draco", "house": "Slytherin"},
]

gryffindors = [
    student["name"] for student in students if student["house"] == "Gryffindor"
]

for gryffindor in sorted(gryffindors):
    print(gryffindor)
```

Read it left to right: *"give me `student["name"]`, for each `student` in `students`, but only if their house is Gryffindor."*

---

## 🔍 `filter`

`filter(function, iterable)` returns only the elements for which a function returns `True` — the "condition-checking" counterpart to `map`.

```python
students = [
    {"name": "Hermione", "house": "Gryffindor"},
    {"name": "Harry", "house": "Gryffindor"},
    {"name": "Ron", "house": "Gryffindor"},
    {"name": "Draco", "house": "Slytherin"},
]


def is_gryffindor(s):
    return s["house"] == "Gryffindor"


gryffindors = filter(is_gryffindor, students)

for gryffindor in sorted(gryffindors, key=lambda s: s["name"]):
    print(gryffindor["name"])
```

Or, more compactly, using a `lambda` (Lecture 6!) instead of a separate named function:

```python
students = [
    {"name": "Hermione", "house": "Gryffindor"},
    {"name": "Harry", "house": "Gryffindor"},
    {"name": "Ron", "house": "Gryffindor"},
    {"name": "Draco", "house": "Slytherin"},
]

gryffindors = filter(lambda s: s["house"] == "Gryffindor", students)

for gryffindor in sorted(gryffindors, key=lambda s: s["name"]):
    print(gryffindor["name"])
```

📚 Learn more: [Python's `filter` documentation](https://docs.python.org/3/library/functions.html#filter)

---

## 📖 Dictionary Comprehensions

The same comprehension idea works for building **dictionaries**, too:

```python
students = ["Hermione", "Harry", "Ron"]

gryffindors = []
for student in students:
    gryffindors.append({"name": student, "house": "Gryffindor"})

print(gryffindors)
```

As a list comprehension of dictionaries:

```python
students = ["Hermione", "Harry", "Ron"]

gryffindors = [{"name": student, "house": "Gryffindor"} for student in students]

print(gryffindors)
```

Or, as an actual **dictionary comprehension** (curly braces, `key: value` syntax):

```python
students = ["Hermione", "Harry", "Ron"]

gryffindors = {student: "Gryffindor" for student in students}

print(gryffindors)
```

---

## 🔢 `enumerate`

Recall this pattern from Lecture 6, combining `range` and `len` to get both index and value:

```python
students = ["Hermione", "Harry", "Ron"]

for i in range(len(students)):
    print(i + 1, students[i])
```

`enumerate()` does this more directly and more readably:

```python
students = ["Hermione", "Harry", "Ron"]

for i, student in enumerate(students):
    print(i + 1, student)
```

`enumerate(students)` hands back **both** the index (`i`) and the value (`student`) together, every iteration — no manual indexing required.

📚 Learn more: [Python's `enumerate` documentation](https://docs.python.org/3/library/functions.html#enumerate)

---

## 🐑 Generators, `yield`, and Why They Matter

Let's build a program that "counts sheep" — a classic insomnia remedy:

```python
n = int(input("What's n? "))
for i in range(n):
    print("🐑" * i)
```

Let's abstract this with a `main` function and a helper, the way you've done all series:

```python
def main():
    n = int(input("What's n? "))
    for i in range(n):
        print(sheep(i))


def sheep(n):
    return "🐑" * n


if __name__ == "__main__":
    main()
```

What if `sheep` should build the **entire flock** itself, as a list?

```python
def main():
    n = int(input("What's n? "))
    for s in sheep(n):
        print(s)


def sheep(n):
    flock = []
    for i in range(n):
        flock.append("🐑" * i)
    return flock


if __name__ == "__main__":
    main()
```

This works for small `n` — but try `n = 1000000`. Your program may hang or crash, because it's trying to build one **massive** list in memory, all at once, before returning any of it.

`yield` solves this. A function using `yield` is called a **generator** — instead of computing everything up front, it hands back one value **at a time**, only when asked:

```python
def main():
    n = int(input("What's n? "))
    for s in sheep(n):
        print(s)


def sheep(n):
    for i in range(n):
        yield "🐑" * i


if __name__ == "__main__":
    main()
```

Now, even for a huge `n`, your program stays responsive — it only ever holds **one** sheep string in memory at a time, generating the next one just as the `for` loop asks for it. This is a powerful pattern for working with large or even *infinite* sequences of data efficiently.

📚 Learn more: [Python's generators documentation](https://docs.python.org/3/howto/functional.html#generators) · [Python's iterators documentation](https://docs.python.org/3/howto/functional.html#iterators)

---

## 🎓 This Was CS50P!

As a fun send-off, here's a program that combines two libraries you haven't officially met — `cowsay` (from Lecture 4!) and `pyttsx3`, a text-to-speech library:

```bash
pip install cowsay pyttsx3
```

```python
import cowsay
import pyttsx3

engine = pyttsx3.init()
this = input("What's this? ")
cowsay.cow(this)
engine.say(this)
engine.runAndWait()
```

Type anything, and a cow will print it out **and** speak it aloud — a small, playful reminder that everything you've learned this series (functions, libraries, strings, `input()`) combines to build genuinely fun, real programs.

---

## 📝 Summary

By the end of this lecture — and the entire CS50P series — you've learned:

- ✅ `set` for automatic deduplication
- ✅ Global variables, `global`, and why classes are often the cleaner alternative
- ✅ Constants by convention (`ALL_CAPS`)
- ✅ Type hints and `mypy` for catching type mismatches before runtime
- ✅ Docstrings for standardized function documentation
- ✅ `argparse` for professional command-line argument parsing
- ✅ Unpacking lists with `*` and dictionaries with `**`
- ✅ `*args` and `**kwargs` for flexible function signatures
- ✅ `map`, list comprehensions, `filter`, and dictionary comprehensions
- ✅ `enumerate` for clean index + value iteration
- ✅ Generators and `yield` for memory-efficient, lazy computation

---

## 🏆 You've Completed the Series!

Across ten lectures, you went from `print("hello, world")` to writing your own classes, validating data with regular expressions, testing your code automatically, and now, generators and functional-style Python. That's a genuinely complete foundation — many working programmers use exactly this toolkit every day.

The single best next step: **build something of your own.** Pick a small, real problem you actually care about, and write a program to solve it. That's exactly what CS50P's own [Final Project](https://cs50.harvard.edu/python/project/) asks students to do.

---

## 💪 Practice Ideas

1. Rewrite one of your very first programs from Lecture 0 using at least one tool from this lecture (type hints, a docstring, or `argparse` instead of manual `input()`).
2. Take the `Student` list-of-dictionaries pattern from earlier lectures and rewrite a filtering loop as both a list comprehension **and** a `filter()` call — compare readability.
3. Write a generator function that yields Fibonacci numbers one at a time, and print the first 20 without ever storing them all in a list.
4. Convert a function of yours that currently takes fixed parameters into one that accepts `*args` and/or `**kwargs` instead, and test that it still works.

---

## 📚 Resources

- [CS50P official course](https://cs50.harvard.edu/python/)
- [Official Lecture 9 notes](https://cs50.harvard.edu/python/notes/9/)
- [Python `set` docs](https://docs.python.org/3/library/stdtypes.html#set)
- [Python `typing` docs](https://docs.python.org/3/library/typing.html) · [mypy](https://mypy.readthedocs.io/)
- [PEP 257 — Docstring Conventions](https://peps.python.org/pep-0257/)
- [Python `argparse` docs](https://docs.python.org/3/library/argparse.html)
- [Python `map` docs](https://docs.python.org/3/library/functions.html#map) · [`filter` docs](https://docs.python.org/3/library/functions.html#filter) · [`enumerate` docs](https://docs.python.org/3/library/functions.html#enumerate)
- [Python generators & iterators](https://docs.python.org/3/howto/functional.html)
- [CS50P Final Project guidelines](https://cs50.harvard.edu/python/project/)

---

🎉 **Congratulations — you've completed the full CS50P lecture series!** If these notes helped you, star this repo and share it with the next person starting their Python journey.

# 🐍 Python Basics — Libraries (CS50P Lecture 4 Notes)

> Part 5 of the series. Make sure you've been through [Lecture 0 — Functions & Variables](#), [Lecture 1 — Conditionals](#), [Lecture 2 — Loops](#), and [Lecture 3 — Exceptions](#) first — this builds directly on all of them.
> Beginner-friendly notes based on **CS50's Introduction to Programming with Python — Lecture 4**.

📺 Original video: [CS50P Lecture 4 — Libraries](https://youtu.be/MztLZWibctI)
📚 Official notes: [cs50.harvard.edu/python/notes/4](https://cs50.harvard.edu/python/notes/4/)

---

## 📖 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [What is a Library?](#-what-is-a-library)
3. [`random` — Letting Python Pick For You](#-random--letting-python-pick-for-you)
4. [`from` — Importing Just One Piece](#-from--importing-just-one-piece)
5. [More `random` Tools](#-more-random-tools)
6. [`statistics` — Built-In Math Helpers](#-statistics--built-in-math-helpers)
7. [Command-Line Arguments with `sys.argv`](#-command-line-arguments-with-sysargv)
8. [Handling Missing Arguments](#-handling-missing-arguments)
9. [`sys.exit` — Cleaner Error Handling](#-sysexit--cleaner-error-handling)
10. [`slice` — Trimming a List](#-slice--trimming-a-list)
11. [Packages — Libraries Other People Wrote](#-packages--libraries-other-people-wrote)
12. [APIs — Borrowing the Internet's Data](#-apis--borrowing-the-internets-data)
13. [Reading JSON Properly](#-reading-json-properly)
14. [Making Your Own Library](#-making-your-own-library)
15. [Summary](#-summary)
16. [Practice Ideas](#-practice-ideas)

---

## ✅ Prerequisites

- Comfortable with lists, `for` loops, and `def` functions (Lectures 0 & 2)
- Comfortable with `try`/`except` (Lecture 3)
- A terminal you can run `pip install` commands in

---

## 📦 What is a Library?

A **library** is just code — written by you, or by someone else — that you can reuse in your own program instead of writing everything from scratch.

Think about it: if you've ever copy-pasted code from an old project into a new one, you've basically already discovered the *idea* behind a library. Python formalizes this idea and calls a shareable chunk of code a **module**.

The huge benefit of programming in Python is that you get to **stand on the shoulders** of thousands of other programmers who've already solved common problems for you.

---

## 🎲 `random` — Letting Python Pick For You

Python ships with a built-in `random` module. To use any library, you load it with the `import` keyword.

```bash
code generate.py
```

```python
import random

coin = random.choice(["heads", "tails"])
print(coin)
```

Here, `random` is the **module**, and `choice` is a **function** that lives inside it — so we call it as `random.choice(...)`. We hand it a list (`["heads", "tails"]`), and it randomly picks one item for us, roughly 50/50.

---

## 🎯 `from` — Importing Just One Piece

Writing `random.choice` every time is a bit verbose. If you only need one function from a module, you can import *just that function* using `from`:

```python
from random import choice

coin = choice(["heads", "tails"])
print(coin)
```

Now `choice` is available directly — no need to prefix it with `random.` anymore. This also uses slightly fewer system resources, since Python doesn't load the *entire* module, just the one piece you asked for.

---

## 🔢 More `random` Tools

**Random integers** with `random.randint(a, b)`:

```python
import random

number = random.randint(1, 10)
print(number)
```

This picks a random whole number between `1` and `10` (inclusive).

**Shuffling a list** with `random.shuffle(x)`:

```python
import random

cards = ["jack", "queen", "king"]
random.shuffle(cards)

for card in cards:
    print(card)
```

Notice something different here: `random.shuffle` doesn't *return* a new list — it shuffles the original list **in place**. Run this program a few times and you'll see the order change each time.

📚 Learn more: [Python's `random` documentation](https://docs.python.org/3/library/random.html)

---

## 📊 `statistics` — Built-In Math Helpers

Python also ships with a `statistics` module for common calculations, like averages:

```bash
code average.py
```

```python
import statistics

print(statistics.mean([100, 90]))
```

`statistics.mean()` takes a list of numbers and returns their average — no need to write your own `sum(...) / len(...)` logic by hand.

📚 Learn more: [Python's `statistics` documentation](https://docs.python.org/3/library/statistics.html)

---

## 💻 Command-Line Arguments with `sys.argv`

So far, every value in our programs has come from `input()`. But what if you wanted to type values **directly into the terminal command** — like `python average.py 100 90` — instead of being prompted?

That's what the `sys` module (specifically `sys.argv`, a list of everything typed on the command line) is for.

```bash
code name.py
```

```python
import sys

print("hello, my name is", sys.argv[1])
```

Run it as:

```bash
python name.py David
```

**Output:** `hello, my name is David`

Why `sys.argv[1]` and not `[0]`? Because lists start counting at `0` — and `sys.argv[0]` is always the **script's own filename** (`name.py`). Your actual typed argument (`David`) lands at index `1`.

---

## 🚧 Handling Missing Arguments

What happens if you just run `python name.py` with no name typed? You'll get:

```
IndexError: list index out of range
```

There's nothing stored at `sys.argv[1]`, because the user never typed anything there. Let's protect against it using what you learned in Lecture 3:

```python
import sys

try:
    print("hello, my name is", sys.argv[1])
except IndexError:
    print("Too few arguments")
```

Even better — check the *length* of `sys.argv` up front instead of waiting for a crash:

```python
import sys

if len(sys.argv) < 2:
    print("Too few arguments")
elif len(sys.argv) > 2:
    print("Too many arguments")
else:
    print("hello, my name is", sys.argv[1])
```

---

## 🚪 `sys.exit` — Cleaner Error Handling

It's nice to keep your error-checking **separate** from your program's main logic, so the "happy path" reads cleanly. `sys.exit()` immediately stops the program:

```python
import sys

if len(sys.argv) < 2:
    sys.exit("Too few arguments")
elif len(sys.argv) > 2:
    sys.exit("Too many arguments")

print("hello, my name is", sys.argv[1])
```

Now we can be confident: **if** we ever reach that final `print` line, the arguments must already be valid — no extra checks needed there.

📚 Learn more: [Python's `sys` documentation](https://docs.python.org/3/library/sys.html)

---

## ✂️ `slice` — Trimming a List

What if you want to greet **multiple** names typed on the command line?

```python
import sys

if len(sys.argv) < 2:
    sys.exit("Too few arguments")

for arg in sys.argv:
    print("hello, my name is", arg)
```

Run `python name.py David Carter Rongxin` and you'll notice an unwanted extra greeting — for `name.py` itself! That's because `sys.argv[0]` is still in the list.

A **slice** lets you grab just part of a list. `sys.argv[1:]` means: "start at index `1`, and go all the way to the end":

```python
import sys

if len(sys.argv) < 2:
    sys.exit("Too few arguments")

for arg in sys.argv[1:]:
    print("hello, my name is", arg)
```

Now the script's own filename is skipped entirely, and only the real arguments get greeted.

---

## 📥 Packages — Libraries Other People Wrote

Everything so far has been **built into** Python already. But Python's real superpower is its enormous ecosystem of **third-party packages** — code written and shared by other programmers.

- **PyPI** (Python Package Index) is the central directory of virtually every publicly available Python package.
- **`pip`** is Python's built-in package manager — the tool you use to download and install these packages.

Let's install a fun one, `cowsay`:

```bash
pip install cowsay
```

```bash
code say.py
```

```python
import cowsay
import sys

if len(sys.argv) == 2:
    cowsay.cow("hello, " + sys.argv[1])
```

Run `python say.py David` and you'll get an ASCII-art cow saying hello. Swap `cowsay.cow` for `cowsay.trex` and you'll get a talking T-Rex instead!

📚 Explore more packages: [pypi.org](https://pypi.org/)

---

## 🌐 APIs — Borrowing the Internet's Data

An **API** ("application programming interface") lets your program talk to *someone else's* code or service over the internet — like a search engine, a weather service, or (in this case) Apple's iTunes catalog.

The `requests` package lets your Python program act like a web browser:

```bash
pip install requests
```

```bash
code itunes.py
```

```python
import requests
import sys

if len(sys.argv) != 2:
    sys.exit()

response = requests.get("https://itunes.apple.com/search?entity=song&limit=1&term=" + sys.argv[1])
print(response.json())
```

Run `python itunes.py weezer`, and you'll get back data in a format called **JSON** — a universal, text-based way for programs to exchange structured data. Python conveniently turns this JSON straight into a Python dictionary for you.

---

## 🧾 Reading JSON Properly

The raw JSON dump is dense and hard to read. Python's built-in `json` module can pretty-print it:

```python
import json
import requests
import sys

if len(sys.argv) != 2:
    sys.exit()

response = requests.get("https://itunes.apple.com/search?entity=song&limit=1&term=" + sys.argv[1])
print(json.dumps(response.json(), indent=2))
```

`json.dumps(..., indent=2)` reformats the dictionary with clean indentation, so you can actually read the structure — including a `results` list, where each result has fields like `trackName`.

Let's extract just what we care about:

```python
import json
import requests
import sys

if len(sys.argv) != 2:
    sys.exit()

response = requests.get("https://itunes.apple.com/search?entity=song&limit=50&term=" + sys.argv[1])

o = response.json()
for result in o["results"]:
    print(result["trackName"])
```

We store the parsed JSON in `o` (a dictionary), loop through its `"results"` list, and print just each song's `"trackName"` — turning a huge wall of raw data into a clean, useful list.

📚 Learn more: [`requests` docs](https://docs.python-requests.org/) · [Python's `json` docs](https://docs.python.org/3/library/json.html)

---

## 🛠️ Making Your Own Library

You're not limited to *using* libraries — you can **create** your own! Any `.py` file full of reusable functions can be imported elsewhere.

```bash
code sayings.py
```

```python
def hello(name):
    print(f"hello, {name}")


def goodbye(name):
    print(f"goodbye, {name}")
```

This file does nothing on its own — but another program can now borrow its functions:

```bash
code say.py
```

```python
import sys

from sayings import goodbye

if len(sys.argv) == 2:
    goodbye(sys.argv[1])
```

Run `python say.py David` and it prints `goodbye, David` — using a function you wrote in a completely different file. This is exactly how every Python library, including the ones you've used all lecture, ultimately works under the hood.

---

## 📝 Summary

By the end of this lecture, you've learned:

- ✅ What a library/module is, and why they matter
- ✅ `import` and `from ... import ...`
- ✅ `random.choice`, `random.randint`, `random.shuffle`
- ✅ `statistics.mean`
- ✅ Command-line arguments via `sys.argv`
- ✅ Defensive checks with `len(sys.argv)` and `sys.exit`
- ✅ Slicing a list with `[1:]`
- ✅ Third-party packages, PyPI, and `pip install`
- ✅ APIs, the `requests` package, and JSON
- ✅ Pretty-printing JSON with `json.dumps(..., indent=2)`
- ✅ Writing and importing your own custom module

---

## 💪 Practice Ideas

1. Write a program that takes any number of numbers as command-line arguments and prints their average using `statistics.mean`.
2. Build a simple "magic 8-ball" that uses `random.choice` on a list of at least 8 possible answers.
3. Pick a free public API (no key required) and write a script using `requests` + `json` to print just one specific field from the response.
4. Create your own two-function library (like `sayings.py`), then write a second script that imports and uses both functions.

---

## 📚 Resources

- [CS50P official course](https://cs50.harvard.edu/python/)
- [Official Lecture 4 notes](https://cs50.harvard.edu/python/notes/4/)
- [Python `random` docs](https://docs.python.org/3/library/random.html)
- [Python `statistics` docs](https://docs.python.org/3/library/statistics.html)
- [Python `sys` docs](https://docs.python.org/3/library/sys.html)
- [Python `json` docs](https://docs.python.org/3/library/json.html)
- [PyPI — Python Package Index](https://pypi.org/)

---

⭐ Next up in the series: **Unit Tests** — star this repo to keep track as more lecture notes get added!

# 🐍 Python Basics — Unit Tests (CS50P Lecture 5 Notes)

> Part 6 of the series. Make sure you've been through [Lecture 0 — Functions & Variables](#), [Lecture 1 — Conditionals](#), [Lecture 2 — Loops](#), [Lecture 3 — Exceptions](#), and [Lecture 4 — Libraries](#) first — this builds directly on all of them.
> Beginner-friendly notes based on **CS50's Introduction to Programming with Python — Lecture 5**.

📺 Original video: [CS50P Lecture 5 — Unit Tests](https://youtu.be/tIrcxwLqzjQ)
📚 Official notes: [cs50.harvard.edu/python/notes/5](https://cs50.harvard.edu/python/notes/5/)

---

## 📖 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [How Have You Been Testing So Far?](#-how-have-you-been-testing-so-far)
3. [`assert` — Teaching Python to Check Your Work](#-assert--teaching-python-to-check-your-work)
4. [Writing a Real Test Function](#-writing-a-real-test-function)
5. [Testing Multiple Cases](#-testing-multiple-cases)
6. [`pytest` — A Proper Testing Tool](#-pytest--a-proper-testing-tool)
7. [Separating Test Code From Program Code](#-separating-test-code-from-program-code)
8. [Why Group Tests Into Separate Functions](#-why-group-tests-into-separate-functions)
9. [Testing Strings](#-testing-strings)
10. [Organizing Tests Into a Folder](#-organizing-tests-into-a-folder)
11. [Summary](#-summary)
12. [Practice Ideas](#-practice-ideas)

---

## ✅ Prerequisites

- Comfortable writing your own functions with `def` and `return` (Lecture 0)
- Comfortable with `if`/`elif`/`else` (Lecture 1)
- Comfortable importing modules with `import` and `from` (Lecture 4)

---

## 🖨️ How Have You Been Testing So Far?

Up until now, you've probably been testing your programs in one of two ways:

- Sprinkling `print()` statements throughout your code to eyeball whether values look right, or
- Relying on someone (or something) else — like an automated grader — to test your code *for* you.

Both work in a pinch, but neither scales well. In the real world, professional programmers write dedicated code whose **entire job** is to test their other code. This is called a **unit test** — a small, automated check that a single "unit" (usually one function) behaves exactly as expected.

---

## ✅ `assert` — Teaching Python to Check Your Work

Python has a built-in keyword, `assert`, that checks whether something is `True`. If it isn't, Python raises an **`AssertionError`**.

Let's bring back the `square` function from Lecture 0:

```bash
code calculator.py
```

```python
def main():
    x = int(input("What's x? "))
    print("x squared is", square(x))


def square(n):
    return n * n


if __name__ == "__main__":
    main()
```

> 💡 **What's `if __name__ == "__main__":`?** It's a small safeguard that says: *"only run `main()` if this file is being run directly — not if it's being imported into another file."* You'll see exactly why this matters in a moment.

We could manually type `2` every time we run this and check the output says `4`. But that's slow, repetitive, and easy to forget. Let's automate it with `assert`:

```python
def square(n):
    return n * n


assert square(2) == 4
```

Run this, and if `square(2)` really does equal `4`, **nothing happens** — silence means success! Now break it on purpose to see what failure looks like:

```python
def square(n):
    return n + n  # bug: should be n * n


assert square(2) == 4
```

Run it, and Python raises:

```
AssertionError
```

That's the whole point — `assert` is your safety net, screaming the moment your code doesn't behave as expected.

📚 Learn more: [Python's `assert` documentation](https://docs.python.org/3/reference/simple_stmts.html#the-assert-statement)

---

## 🧪 Writing a Real Test Function

Let's wrap our checks in a proper **test function**, and even test more than one case:

```python
def main():
    test_square()


def square(n):
    return n * n


def test_square():
    assert square(2) == 4
    assert square(3) == 9


if __name__ == "__main__":
    main()
```

Notice the naming convention: `test_square` — prefixing test functions with `test_` isn't just a style choice, it's what testing tools (coming up next) look for automatically.

---

## 🔢 Testing Multiple Cases

A good test doesn't just check one "easy" case — it checks the **edge cases** too: negative numbers, zero, and anything else that could realistically go wrong.

```python
def test_square():
    assert square(2) == 4
    assert square(3) == 9
    assert square(-2) == 4
    assert square(-3) == 9
    assert square(0) == 0
```

If even *one* of these lines fails, Python raises an `AssertionError` and testing stops right there — which, as you'll see, isn't ideal.

---

## 🧰 `pytest` — A Proper Testing Tool

Manually running your file and reading raw `AssertionError`s isn't very friendly. `pytest` is a hugely popular third-party package (remember Lecture 4 — packages come from PyPI!) built specifically for running and reporting on tests.

```bash
pip install pytest
```

Create a **separate** file just for your tests:

```bash
code test_calculator.py
```

```python
from calculator import square


def test_square():
    assert square(2) == 4
    assert square(3) == 9
    assert square(-2) == 4
    assert square(-3) == 9
    assert square(0) == 0
```

Notice we `import` the `square` function straight from `calculator.py` — this is exactly why `if __name__ == "__main__":` mattered earlier! It stops `calculator.py`'s `main()` (which asks for input) from running just because we imported something from it.

Now run:

```bash
pytest test_calculator.py
```

`pytest` gives you a clean, readable report — a `.` for each passing test, an `F` for each failing one, plus a helpful summary explaining *exactly* which assertion failed and why.

📚 Learn more: [pytest documentation](https://docs.pytest.org/)

---

## 🧩 Separating Test Code From Program Code

You may have noticed: we now have two files — `calculator.py` (the actual program) and `test_calculator.py` (the tests for it). This separation is intentional and considered best practice:

- `calculator.py` — contains the logic you actually ship / run.
- `test_calculator.py` — contains code whose *only* job is verifying `calculator.py` works.

Keeping them apart means your tests never accidentally interfere with your real program, and anyone reading your repo instantly knows where to look for either.

---

## 🗂️ Why Group Tests Into Separate Functions

Here's a subtle problem: `assert` stops the **entire function** the moment one line fails. So if `square(2)` fails, you'll never even find out whether `square(-2)` also would have failed — `pytest` just stops there.

Let's fix this by splitting our one big test function into **several smaller ones**, grouped by category:

```python
from calculator import square


def test_positive():
    assert square(2) == 4
    assert square(3) == 9


def test_negative():
    assert square(-2) == 4
    assert square(-3) == 9


def test_zero():
    assert square(0) == 0
```

Now, even if `test_positive` fails, `pytest` will **still run** `test_negative` and `test_zero` independently, giving you the full picture of what's broken and what isn't — instead of stopping at the very first failure.

---

## 🔤 Testing Strings

Unit tests aren't just for numbers — you can test any function, including ones that manipulate text. Recall the `hello()` function style from Lecture 0:

```bash
code hello.py
```

```python
def main():
    print(hello("David"))


def hello(to="world"):
    return f"hello, {to}"


if __name__ == "__main__":
    main()
```

And its matching test file:

```bash
code test_hello.py
```

```python
from hello import hello


def test_default():
    assert hello() == "hello, world"


def test_argument():
    assert hello("David") == "hello, David"
```

Same exact pattern as before — one test for the *default* behavior, and one for a specific *argument*. This habit of testing both the "normal" case and the "default" case will serve you well in every language you ever program in.

---

## 📁 Organizing Tests Into a Folder

As a project grows, you'll end up with many test files. `pytest` can run an entire **folder** of tests at once. Let's organize things properly:

```bash
mkdir test
code test/test_hello.py
```

Move your test code into that file inside the `test/` folder:

```python
from hello import hello


def test_default():
    assert hello() == "hello, world"


def test_argument():
    assert hello("David") == "hello, David"
```

There's one catch — `pytest` needs a special (often empty) file to recognize that a folder is a proper **package** of tests:

```bash
code test/__init__.py
```

You can leave `test/__init__.py` completely empty — its mere *existence* is what tells `pytest`: "everything in this folder belongs together as a test suite."

Now run the whole folder at once:

```bash
pytest test
```

`pytest` will automatically discover and run every `test_*.py` file inside `test/`, giving you one unified report for your entire project's tests.

📚 Learn more: [pytest's import mechanisms documentation](https://docs.pytest.org/en/latest/explanation/goodpractices.html)

---

## 📝 Summary

By the end of this lecture, you've learned:

- ✅ Why automated testing beats manual `print()`-based checking
- ✅ `assert` and `AssertionError`
- ✅ Writing dedicated `test_` functions
- ✅ Testing edge cases (positive, negative, zero)
- ✅ Installing and using `pytest` for cleaner test reports
- ✅ Keeping test code in a separate file from program code
- ✅ `if __name__ == "__main__":` and why it matters for imports
- ✅ Splitting tests into multiple functions so one failure doesn't hide others
- ✅ Testing string-returning functions, including default arguments
- ✅ Organizing many tests into a `test/` folder with `__init__.py`

---

## 💪 Practice Ideas

1. Go back to your `is_even(n)` function from Lecture 1 and write a `test_is_even.py` file with at least 4 assertions (even, odd, zero, negative).
2. Write tests for the `get_int(prompt)` function from Lecture 3 — think about what's actually testable without needing real user input.
3. Take any function you've written across this series and deliberately introduce a bug — run `pytest` and read the failure report carefully. Then fix it and re-run.
4. Organize at least two different test files into a `test/` folder with an `__init__.py`, and run them all together with a single `pytest test` command.

---

## 📚 Resources

- [CS50P official course](https://cs50.harvard.edu/python/)
- [Official Lecture 5 notes](https://cs50.harvard.edu/python/notes/5/)
- [Python `assert` docs](https://docs.python.org/3/reference/simple_stmts.html#the-assert-statement)
- [pytest documentation](https://docs.pytest.org/)

---

⭐ Next up in the series: **File I/O** — star this repo to keep track as more lecture notes get added!

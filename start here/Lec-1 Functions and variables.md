# 🐍 Python Basics — Functions & Variables (CS50P Lecture 0 Notes)

> Beginner-friendly notes based on **CS50's Introduction to Programming with Python — Lecture 0**.
> If you're brand new to coding, don't worry. We start from literally zero — opening a text editor for the first time — and build up step by step until you're writing your own functions.

📺 Original video: [CS50P Lecture 0 — Functions, Variables](https://youtu.be/JP7ITIXGpHk)
📚 Official notes: [cs50.harvard.edu/python/notes/0](https://cs50.harvard.edu/python/notes/0/)

---

## 📖 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [Getting Started](#-getting-started)
3. [Your First Program](#-your-first-program)
4. [What is a Function?](#-what-is-a-function)
5. [Bugs Happen — Get Used to Them](#-bugs-happen--get-used-to-them)
6. [Making It Interactive: `input()`](#-making-it-interactive-input)
7. [Variables](#-variables)
8. [Comments & Pseudocode](#-comments--pseudocode)
9. [Combining Text: Strings and Parameters](#-combining-text-strings-and-parameters)
10. [f-strings — The Clean Way to Format Text](#-f-strings--the-clean-way-to-format-text)
11. [Cleaning User Input: `.strip()` and `.title()`](#-cleaning-user-input-strip-and-title)
12. [Working with Numbers: `int`](#-working-with-numbers-int)
13. [Working with Decimals: `float`](#-working-with-decimals-float)
14. [Rounding & Formatting Numbers](#-rounding--formatting-numbers)
15. [Writing Your Own Functions: `def`](#-writing-your-own-functions-def)
16. [Returning Values](#-returning-values)
17. [Summary](#-summary)
18. [Practice Ideas](#-practice-ideas)

---

## ✅ Prerequisites

You don't need any prior coding experience. You just need:

- A computer with internet access
- [Visual Studio Code](https://cs50.dev) (a free code editor — CS50 calls it "VS Code" or "cs50.dev")
- Curiosity, and patience for your first few bugs 🐛

VS Code has three main areas you'll use constantly:

| Area | What it's for |
|---|---|
| 📁 File Explorer (left) | Browse and create your files |
| 📝 Text Editor (middle) | Write your code |
| 💻 Terminal (bottom) | Run commands and see output |

---

## 🚀 Getting Started

Open the terminal in VS Code and create your first file:

```bash
code hello.py
```

This opens a new file called `hello.py` in the text editor. `.py` is the file extension for Python files.

---

## 👋 Your First Program

Inside `hello.py`, type:

```python
print("hello, world")
```

Now go back to the terminal and run it:

```bash
python hello.py
```

**Output:**
```
hello, world
```

🎉 Congrats — that's your first program! Under the hood, Python read your text file and translated it into the 0s and 1s your computer actually understands.

---

## ⚙️ What is a Function?

A **function** is just an *action* — a verb — that Python already knows how to do.

In `print("hello, world")`:
- `print` is the **function**.
- `"hello, world"` is the **argument** — the input you're handing to the function.

Think of a function like a small machine: you give it an input, it performs an action (and sometimes gives you something back).

---

## 🐛 Bugs Happen — Get Used to Them

Try removing the closing parenthesis:

```python
print("hello, world"
```

Run it, and Python will throw an **error**. This is called a **bug**.

> 💡 **Don't panic when you see errors.** They're not a sign you're bad at this — they're part of the process. Error messages often (not always) tell you exactly what's wrong and where.

---

## 🎮 Making It Interactive: `input()`

Let's make the program ask the user something. Edit `hello.py`:

```python
input("What's your name? ")
print("hello, world")
```

Run it — notice it asks a question, but still just prints `hello, world` no matter what you type. We haven't told Python to *use* the answer yet. For that, we need **variables**.

---

## 📦 Variables

A **variable** is a container that stores a value so you can use it later.

```python
name = input("What's your name? ")
print("hello, world")
```

The `=` sign here isn't "equals" like in math — it **assigns** the value on the right to the variable on the left. So whatever the user types gets stored in `name`.

⚠️ Common beginner trap:

```python
name = input("What's your name? ")
print("hello, name")   # ❌ prints the literal word "name", not the value
```

Since `"hello, name"` is inside quotes, Python treats `name` as plain text, not the variable. Try this instead:

```python
name = input("What's your name? ")
print("hello,")
print(name)
```

**Output:**
```
What's your name? David
hello,
David
```

Getting closer — but still on two lines. We'll fix that soon.

---

## 💬 Comments & Pseudocode

**Comments** start with `#` and are ignored by Python. They're notes for you and other humans reading your code.

```python
# Ask the user for their name
name = input("What's your name? ")
print("hello,")
print(name)
```

**Pseudocode** is a special kind of comment — a plain-English to-do list you write *before* you know how to code something:

```python
# Ask the user for their name
name = input("What's your name? ")

# Print hello
print("hello,")

# Print the name inputted
print(name)
```

This is a great habit: plan in English first, then translate to code.

---

## 🔗 Combining Text: Strings and Parameters

To get everything on one line, combine text with `+`:

```python
# Ask the user for their name
name = input("What's your name? ")

# Print hello and the inputted name
print("hello, " + name)
```

Or, since `print` can take multiple arguments separated by commas:

```python
name = input("What's your name? ")
print("hello,", name)
```

**Output:** `hello, David`

A **string** (`str` in Python) is just a sequence of text. Functions like `print` take **parameters** — extra settings that control their behavior. For example, `print` secretly has a hidden parameter `end='\n'` (a line break) every time it runs. You can override it:

```python
name = input("What's your name? ")
print("hello,", end="")
print(name)
```

> **Quotation marks inside strings?** `print("hello,"friend"")` will break. Either switch to single quotes, or escape them: `print("hello, \"friend\"")`.

---

## ✨ f-strings — The Clean Way to Format Text

The cleanest, most common way to insert variables into text is an **f-string**:

```python
# Ask the user for their name
name = input("What's your name? ")
print(f"hello, {name}")
```

The `f` before the quotes tells Python: "treat `{ }` as a placeholder for a variable." You'll use this constantly throughout the course.

---

## 🧹 Cleaning User Input: `.strip()` and `.title()`

Users don't always type nicely (extra spaces, weird casing). Strings have built-in **methods** to help:

```python
# Ask the user for their name
name = input("What's your name? ")

# Remove whitespace from the string
name = name.strip()

# Capitalize the first letter of each word
name = name.title()

# Print the output
print(f"hello, {name}")
```

You can chain methods together to keep things tidy:

```python
# Ask the user for their name
name = input("What's your name? ").strip().title()

# Print the output
print(f"hello, {name}")
```

💡 Tip: press the **↑ (up arrow)** in the terminal to reuse your last command instead of retyping `python hello.py` every time.

---

## 🔢 Working with Numbers: `int`

Let's build a tiny calculator. Create a new file:

```bash
code calculator.py
```

```python
x = 1
y = 2

z = x + y

print(z)
```

Run `python calculator.py` → Output: `3`

Now make it interactive:

```python
x = input("What's x? ")
y = input("What's y? ")

z = x + y

print(z)
```

Try `x = 1`, `y = 2` → you'll get `12`, not `3`! 😱

**Why?** `input()` always returns a **string**, and `+` on strings *concatenates* (glues) them together instead of adding numbers. Fix it by converting ("casting") the strings to integers:

```python
x = input("What's x? ")
y = input("What's y? ")

z = int(x) + int(y)

print(z)
```

Even cleaner — cast right when you take the input:

```python
x = int(input("What's x? "))
y = int(input("What's y? "))

print(x + y)
```

Note how functions can be nested: Python runs the **inner** function (`input`) first, then the **outer** one (`int`).

> 🎯 **Readability wins.** There's usually more than one correct way to write something — but always favor the version that's easiest for a human to read later (including future-you).

---

## 🌊 Working with Decimals: `float`

A **float** is a number with a decimal point, like `0.52`. Swap `int` for `float`:

```python
x = float(input("What's x? "))
y = float(input("What's y? "))

print(x + y)
```

Now `1.2 + 3.4` correctly gives `4.6`.

---

## 🎯 Rounding & Formatting Numbers

Round to the nearest whole number:

```python
x = float(input("What's x? "))
y = float(input("What's y? "))

z = round(x + y)

print(z)
```

Format big numbers with commas (e.g. `1000` → `1,000`):

```python
x = float(input("What's x? "))
y = float(input("What's y? "))

z = round(x + y)

print(f"{z:,}")
```

Round to a specific number of decimal places:

```python
x = float(input("What's x? "))
y = float(input("What's y? "))

z = x / y

print(round(z, 2))
```

Or do it inline with an f-string:

```python
x = float(input("What's x? "))
y = float(input("What's y? "))

z = x / y

print(f"{z:.2f}")
```

---

## 🛠️ Writing Your Own Functions: `def`

So far you've used Python's built-in functions (`print`, `input`, `int`, `float`). Now let's **create your own**.

Start fresh in `hello.py`:

```python
name = input("What's your name? ")
hello()
print(name)
```

This errors — `hello` doesn't exist yet! Let's define it:

```python
def hello():
    print("hello")


name = input("What's your name? ")
hello()
print(name)
```

⚠️ **Indentation matters in Python.** Everything indented under `def hello():` belongs *inside* that function. Unindented code is *not* part of it.

Let's make `hello` actually take the name as a **parameter**:

```python
# Create our own function
def hello(to):
    print("hello,", to)


# Output using our own function
name = input("What's your name? ")
hello(name)
```

Here, `to` is a parameter of your `hello` function. When you call `hello(name)`, whatever is stored in `name` gets passed in as `to`.

You can even give parameters **default values**:

```python
# Create our own function
def hello(to="world"):
    print("hello,", to)


name = input("What's your name? ")
hello(name)   # uses the name you typed

hello()       # no argument given → uses the default "world"
```

### Organizing with `main()`

As programs grow, it's good practice to have one clear **entry point** called `main`:

```python
def main():
    name = input("What's your name? ")
    hello(name)
    hello()


def hello(to="world"):
    print("hello,", to)
```

Running this... does nothing! Nothing is actually *calling* `main`. Fix it by calling `main()` at the bottom of the file:

```python
def main():
    name = input("What's your name? ")
    hello(name)
    hello()


def hello(to="world"):
    print("hello,", to)


main()
```

---

## 🔁 Returning Values

Sometimes a function shouldn't just *do* something — it should **hand a value back** to whoever called it. That's what `return` is for.

Rewrite `calculator.py`:

```python
def main():
    x = int(input("What's x? "))
    print("x squared is", square(x))


def square(n):
    return n * n


main()
```

Here, `x` is passed into `square(n)`, which calculates `n * n` and **returns** it back to `main`, where it gets printed.

---

## 📝 Summary

By the end of this lecture, you've learned:

- ✅ Writing and running your first Python program
- ✅ Functions and arguments
- ✅ Reading and debugging errors (bugs)
- ✅ Variables
- ✅ Comments and pseudocode
- ✅ Strings and parameters
- ✅ f-strings for clean formatting
- ✅ Integers (`int`) and type casting
- ✅ Readable, clean code
- ✅ Floats and rounding
- ✅ Writing your own functions with `def`
- ✅ Return values

---

## 💪 Practice Ideas

Try these on your own to cement what you learned:

1. Write a program that asks for someone's **first and last name** separately, then greets them using an f-string.
2. Build a calculator that supports `+`, `-`, `*`, and `/` using `def` functions for each operation.
3. Write a function `cube(n)` that returns `n * n * n`, then call it from `main()`.
4. Modify the greeting program to output the name in **UPPERCASE** using `.upper()`.

---

## 📚 Resources

- [CS50P official course](https://cs50.harvard.edu/python/)
- [Official Lecture 0 notes](https://cs50.harvard.edu/python/notes/0/)
- [Python `print` docs](https://docs.python.org/3/library/functions.html#print)
- [Python `str` docs](https://docs.python.org/3/library/stdtypes.html#str)
- [Python `int` docs](https://docs.python.org/3/library/functions.html#int)
- [Python `float` docs](https://docs.python.org/3/library/functions.html#float)

---

⭐ If these notes helped you, consider starring the repo and sharing it with someone starting their coding journey!

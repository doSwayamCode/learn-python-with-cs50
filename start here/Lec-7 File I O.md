# 🐍 Python Basics — File I/O (CS50P Lecture 6 Notes)

> Part 7 of the series. Make sure you've been through [Lecture 0 — Functions & Variables](#), [Lecture 1 — Conditionals](#), [Lecture 2 — Loops](#), [Lecture 3 — Exceptions](#), [Lecture 4 — Libraries](#), and [Lecture 5 — Unit Tests](#) first — this builds directly on all of them.
> Beginner-friendly notes based on **CS50's Introduction to Programming with Python — Lecture 6**.

📺 Original video: [CS50P Lecture 6 — File I/O](https://youtu.be/KD-Yoel6EVQ)
📚 Official notes: [cs50.harvard.edu/python/notes/6](https://cs50.harvard.edu/python/notes/6/)

---

## 📖 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [The Problem: Everything Disappears](#-the-problem-everything-disappears)
3. [`open()` — Writing to a File](#-open--writing-to-a-file)
4. [Append Mode, and a Sneaky Bug](#-append-mode-and-a-sneaky-bug)
5. [`with` — Never Forget to Close a File](#-with--never-forget-to-close-a-file)
6. [Reading a File Back](#-reading-a-file-back)
7. [Sorting What You Read](#-sorting-what-you-read)
8. [CSV — Comma Separated Values](#-csv--comma-separated-values)
9. [Turning Rows Into Dictionaries](#-turning-rows-into-dictionaries)
10. [Sorting a List of Dictionaries](#-sorting-a-list-of-dictionaries)
11. [`lambda` — A Function With No Name](#-lambda--a-function-with-no-name)
12. [When Commas Get Complicated](#-when-commas-get-complicated)
13. [`csv.reader` — Let Someone Else Parse It](#-csvreader--let-someone-else-parse-it)
14. [`csv.DictReader` — Defensive, Header-Aware Reading](#-csvdictreader--defensive-header-aware-reading)
15. [Writing CSVs with `csv.DictWriter`](#-writing-csvs-with-csvdictwriter)
16. [Binary Files & `PIL`](#-binary-files--pil)
17. [Summary](#-summary)
18. [Practice Ideas](#-practice-ideas)

---

## ✅ Prerequisites

- Comfortable with lists, `for` loops, and `sorted()` basics (Lecture 2)
- Comfortable with dictionaries (Lecture 2)
- Comfortable importing modules with `import` (Lecture 4)

---

## 💨 The Problem: Everything Disappears

Every program you've written so far has a flaw: the moment it finishes running, everything it collected — every name typed, every number calculated — vanishes into thin air. It all lived only in your computer's **memory**, which resets the instant the program exits.

```bash
code names.py
```

```python
names = []

for _ in range(3):
    names.append(input("What's your name? "))

for name in sorted(names):
    print(f"hello, {name}")
```

This collects three names, sorts them, and greets each one — but run it again, and it has no memory of who you typed in last time. **File I/O** ("input/output") is how a program can save information permanently to disk, so it survives after the program ends.

---

## ✍️ `open()` — Writing to a File

Python's built-in `open()` function lets you open a file for reading or writing.

```python
name = input("What's your name? ")

file = open("names.txt", "w")
file.write(name)
file.close()
```

- `"w"` means **write mode** — create the file if it doesn't exist, and overwrite it if it does.
- `file.write(name)` writes the text into the file.
- `file.close()` releases the file when you're done with it.

Run this a few times with different names... and you'll notice each run **completely erases** the previous name. `"w"` mode always starts the file fresh.

---

## ➕ Append Mode, and a Sneaky Bug

To *add onto* a file instead of overwriting it, use `"a"` (append) mode:

```python
name = input("What's your name? ")

file = open("names.txt", "a")
file.write(name)
file.close()
```

Better — now every run adds a new name. But open `names.txt` and look closely: all the names are **jammed together** with no separation, like `HarryRonHermione`.

Fix it by adding a newline character (`\n`) after each name:

```python
name = input("What's your name? ")

file = open("names.txt", "a")
file.write(f"{name}\n")
file.close()
```

Now each name lands on its own line.

---

## 🔒 `with` — Never Forget to Close a File

It's easy to forget `file.close()` — and a file left open can lead to bugs or data loss. Python's `with` keyword handles closing the file **automatically** for you, even if something goes wrong partway through:

```python
name = input("What's your name? ")

with open("names.txt", "a") as file:
    file.write(f"{name}\n")
```

Notice the indentation: everything inside the `with` block has access to `file`. The moment Python exits that indented block, the file is closed — no matter what.

> 🎯 From here on, always prefer `with open(...) as file:` over manually calling `open()` and `close()`.

---

## 📖 Reading a File Back

To read a file instead of writing to it, use `"r"` mode (or just leave the mode out — `"r"` is the default):

```python
with open("names.txt", "r") as file:
    lines = file.readlines()

for line in lines:
    print("hello,", line)
```

`readlines()` reads the whole file and returns a **list**, one string per line. But the output looks messy — there's an extra blank line after every greeting. That's because each line **already ends** with its own `\n`, and `print` adds *another* one on top.

Fix it with `.rstrip()` (from Lecture 0!) to strip that trailing newline:

```python
with open("names.txt", "r") as file:
    lines = file.readlines()

for line in lines:
    print("hello,", line.rstrip())
```

Even simpler — you can loop directly over a file without calling `readlines()` at all:

```python
with open("names.txt", "r") as file:
    for line in file:
        print("hello,", line.rstrip())
```

Python treats an open file as something you can iterate over line by line, automatically.

---

## 🔤 Sorting What You Read

The one thing missing: sorting. To sort, we first need every name collected into a list **in memory**, then sort *that*:

```python
names = []

with open("names.txt") as file:
    for line in file:
        names.append(line.rstrip())

for name in sorted(names):
    print(f"hello, {name}")
```

This is a common pattern you'll reuse constantly: **read from disk → build a list in memory → process/sort/filter that list.**

---

## 📊 CSV — Comma Separated Values

What if each record needs *more* than just a name — like a name **and** a house? A **CSV** file (comma-separated values) stores structured, table-like data as plain text, one record per line:

```bash
code students.csv
```

```
Hermione,Gryffindor
Harry,Gryffindor
Ron,Gryffindor
Draco,Slytherin
```

Read and parse it manually:

```bash
code students.py
```

```python
with open("students.csv") as file:
    for line in file:
        row = line.rstrip().split(",")
        print(f"{row[0]} is in {row[1]}")
```

`.split(",")` breaks each line into a list wherever it finds a comma. `row[0]` is the name, `row[1]` is the house.

Since `split` on exactly one comma always returns exactly two values, we can **unpack** them directly into two named variables — much more readable than `row[0]`/`row[1]`:

```python
with open("students.csv") as file:
    for line in file:
        name, house = line.rstrip().split(",")
        print(f"{name} is in {house}")
```

---

## 🧩 Turning Rows Into Dictionaries

Let's collect everything into a list first (so we can sort or process it later), storing each student as a **dictionary**:

```python
students = []

with open("students.csv") as file:
    for line in file:
        name, house = line.rstrip().split(",")
        student = {"name": name, "house": house}
        students.append(student)

for student in students:
    print(f"{student['name']} is in {student['house']}")
```

This mirrors the "list of dictionaries" pattern from Lecture 2 — extremely common for representing structured, real-world data.

---

## 🔀 Sorting a List of Dictionaries

Here's a wrinkle: `sorted()` doesn't know *what* to sort a dictionary by — a name? A house? We have to tell it explicitly using the `key` parameter:

```python
students = []

with open("students.csv") as file:
    for line in file:
        name, house = line.rstrip().split(",")
        students.append({"name": name, "house": house})


def get_name(student):
    return student["name"]


for student in sorted(students, key=get_name):
    print(f"{student['name']} is in {student['house']}")
```

`key=get_name` tells `sorted()`: *"for each student, call `get_name` on it, and sort based on whatever that returns."*

---

## ⚡ `lambda` — A Function With No Name

Writing a whole `def` just for a tiny one-line helper you'll only ever use once feels heavy. Python offers `lambda` — a way to write a small, throwaway, **anonymous** function inline:

```python
students = []

with open("students.csv") as file:
    for line in file:
        name, house = line.rstrip().split(",")
        students.append({"name": name, "house": house})

for student in sorted(students, key=lambda student: student["name"]):
    print(f"{student['name']} is in {student['house']}")
```

`lambda student: student["name"]` means: *"given a `student`, return `student['name']`"* — functionally identical to `get_name`, just without giving it a name or a separate `def`.

---

## 💥 When Commas Get Complicated

Let's make things trickier — what if the data itself *contains* a comma?

```
Harry,"Number Four, Privet Drive"
Ron,The Burrow
Draco,Malfoy Manor
```

Run our program on this file, and it crashes with:

```
ValueError: too many values to unpack
```

**Why?** Our naive `.split(",")` sees *three* commas in Harry's line (one intentional separator, two inside the quoted address) — and blindly splits on every single one, producing more pieces than we expected.

---

## 🧰 `csv.reader` — Let Someone Else Parse It

Rather than fight this ourselves, let's stand on the shoulders of the programmers who already solved it — Python's built-in `csv` module (Lecture 4 déjà vu!):

```python
import csv

students = []

with open("students.csv") as file:
    reader = csv.reader(file)
    for row in reader:
        students.append({"name": row[0], "home": row[1]})

for student in sorted(students, key=lambda student: student["name"]):
    print(f"{student['name']} is from {student['home']}")
```

`csv.reader` correctly understands quoted commas and handles them for us — no more crashes, no matter how messy the real-world data gets.

---

## 🛡️ `csv.DictReader` — Defensive, Header-Aware Reading

It's even better design to declare column names directly inside the CSV file itself, as a **header row**:

```
name,home
Harry,"Number Four, Privet Drive"
Ron,The Burrow
Draco,Malfoy Manor
```

Now use `csv.DictReader`, which reads that header row automatically and hands you back a proper dictionary for every row — no manual `row[0]`/`row[1]` indexing needed:

```python
import csv

students = []

with open("students.csv") as file:
    reader = csv.DictReader(file)
    for row in reader:
        students.append({"name": row["name"], "home": row["home"]})

for student in sorted(students, key=lambda student: student["name"]):
    print(f"{student['name']} is in {student['home']}")
```

This is a great example of **defensive coding**: as long as whoever created the CSV correctly labeled the header, your program correctly reads it — regardless of column order.

---

## 💾 Writing CSVs with `csv.DictWriter`

We've read CSVs — now let's *write* one, using `csv.DictWriter`:

```python
import csv

name = input("What's your name? ")
home = input("Where's your home? ")

with open("students.csv", "a") as file:
    writer = csv.DictWriter(file, fieldnames=["name", "home"])
    writer.writerow({"name": name, "home": home})
```

`DictWriter` takes the `file` to write to, plus the `fieldnames` (column names) up front. `writer.writerow(...)` then writes one dictionary as a properly comma-separated (and comma-safe!) row.

📚 Learn more: [Python's `csv` documentation](https://docs.python.org/3/library/csv.html)

---

## 🖼️ Binary Files & `PIL`

Not every file is plain text. A **binary file** is just raw ones and zeros — capable of storing anything: music, images, video.

`PIL` (Python Imaging Library, via the `Pillow` package) is a popular library for working with image files. Here's a program that combines two still images into an animated GIF:

```bash
pip install pillow
```

```python
import sys

from PIL import Image

images = []

for arg in sys.argv[1:]:
    image = Image.open(arg)
    images.append(image)

images[0].save(
    "costumes.gif", save_all=True, append_images=[images[1]], duration=200, loop=0
)
```

Run it as:

```bash
python costumes.py costume1.gif costume2.gif
```

`sys.argv[1:]` (remember slicing from Lecture 4!) grabs every image filename typed on the command line, opens each with `Image.open`, and finally `.save(...)` stitches them together into one looping, animated `costumes.gif`.

📚 Learn more: [Pillow's documentation](https://pillow.readthedocs.io/)

---

## 📝 Summary

By the end of this lecture, you've learned:

- ✅ Why programs need files: memory disappears, files persist
- ✅ `open()` in write (`"w"`) and append (`"a"`) modes
- ✅ `with` for automatically closing files
- ✅ Reading files with `readlines()` and direct iteration
- ✅ Loading data into a list so it can be sorted
- ✅ CSV files, `.split(",")`, and unpacking into named variables
- ✅ Building a list of dictionaries from file data
- ✅ Sorting dictionaries with `sorted(..., key=...)`
- ✅ `lambda` — quick, anonymous one-line functions
- ✅ Why naive comma-splitting breaks, and how `csv.reader` fixes it
- ✅ `csv.DictReader` for defensive, header-aware reading
- ✅ `csv.DictWriter` for writing structured CSV data
- ✅ Binary files, and using `PIL`/Pillow to build a GIF

---

## 💪 Practice Ideas

1. Write a program that lets a user keep adding entries to a `journal.txt` file (one entry per line, using append mode), then a second program that reads and prints every entry.
2. Build a small CSV-based contact book: write entries with `csv.DictWriter` (`name`, `phone`), then read and print them sorted alphabetically using `csv.DictReader` + `lambda`.
3. Take a CSV of your own (or make one up) with a field containing a comma inside quotes, and confirm `csv.reader` handles it correctly where manual `.split(",")` would fail.
4. Using `PIL`, try opening a single image and resizing or rotating it, then saving the result as a new file (check Pillow's documentation for the relevant methods).

---

## 📚 Resources

- [CS50P official course](https://cs50.harvard.edu/python/)
- [Official Lecture 6 notes](https://cs50.harvard.edu/python/notes/6/)
- [Python `open()` docs](https://docs.python.org/3/library/functions.html#open)
- [Python `csv` docs](https://docs.python.org/3/library/csv.html)
- [Python `sorted()` docs](https://docs.python.org/3/library/functions.html#sorted)
- [Pillow (PIL) documentation](https://pillow.readthedocs.io/)

---

⭐ Next up in the series: **Regular Expressions** — star this repo to keep track as more lecture notes get added!

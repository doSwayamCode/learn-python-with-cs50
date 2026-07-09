# 🐍 Python Basics — Regular Expressions (CS50P Lecture 7 Notes)

> Part 8 of the series. Make sure you've been through [Lecture 0 — Functions & Variables](#) through [Lecture 6 — File I/O](#) first — this builds directly on all of them.
> Beginner-friendly notes based on **CS50's Introduction to Programming with Python — Lecture 7**.

📺 Original video: [CS50P Lecture 7 — Regular Expressions](https://youtu.be/hy3sd9MOAcc)
📚 Official notes: [cs50.harvard.edu/python/notes/7](https://cs50.harvard.edu/python/notes/7/)

---

## 📖 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [The Problem With Plain `if` Checks](#-the-problem-with-plain-if-checks)
3. [Meet `re.search`](#-meet-research)
4. [Your First Real Pattern](#-your-first-real-pattern)
5. [Anchoring With `^` and `$`](#-anchoring-with--and-)
6. [Escaping the Dot](#-escaping-the-dot)
7. [Raw Strings](#-raw-strings)
8. [Character Sets `[...]`](#-character-sets-)
9. [Shorthand Classes: `\w`, `\d`, `\s`](#-shorthand-classes-w-d-s)
10. [`|` — Either This or That](#--either-this-or-that)
11. [Groups `(...)` and Non-Capturing Groups `(?:...)`](#-groups--and-non-capturing-groups-)
12. [Case Sensitivity With Flags](#-case-sensitivity-with-flags)
13. [Optional Groups](#-optional-groups)
14. [Cleaning Up User Input](#-cleaning-up-user-input)
15. [Extracting Matched Groups](#-extracting-matched-groups)
16. [The Walrus Operator `:=`](#-the-walrus-operator-)
17. [Extracting Data From URLs](#-extracting-data-from-urls)
18. [`re.sub` — Search and Replace](#-resub--search-and-replace)
19. [Summary](#-summary)
20. [Practice Ideas](#-practice-ideas)

---

## ✅ Prerequisites

- Comfortable with `if`/`elif`/`else` and strings (Lecture 1)
- Comfortable with `.strip()`, `.split()`, and f-strings (Lecture 0)
- Comfortable importing modules with `import` (Lecture 4)

---

## 🤔 The Problem With Plain `if` Checks

Let's validate an email address the "obvious" way first:

```bash
code validate.py
```

```python
email = input("What's your email? ").strip()

if "@" in email:
    print("Valid")
else:
    print("Invalid")
```

This technically works... but it's way too generous. Type in just `@@` and Python happily says `"Valid"`. Let's tighten it:

```python
email = input("What's your email? ").strip()

if "@" in email and "." in email:
    print("Valid")
else:
    print("Invalid")
```

Still too loose — `@.` alone passes! Let's split on the `@` and check each half:

```python
email = input("What's your email? ").strip()

username, domain = email.split("@")

if username and "." in domain:
    print("Valid")
else:
    print("Invalid")
```

Better — `malan@harvard` (no dot) now correctly fails. But we could keep patching edge case after edge case forever with plain `if` statements. There has to be a better way to describe a *pattern* of valid text — and that's exactly what **regular expressions** ("regexes") are for.

---

## 🔍 Meet `re.search`

Python's built-in `re` module lets you search for **patterns** inside text, not just exact substrings.

```python
import re

email = input("What's your email? ").strip()

if re.search("@", email):
    print("Valid")
else:
    print("Invalid")
```

Right now this is no smarter than our `if "@" in email` check — but `re.search` can understand much richer patterns than a plain string ever could.

---

## 🧩 Your First Real Pattern

Regular expressions use special symbols to describe patterns:

| Symbol | Meaning |
|---|---|
| `.` | any character except a newline |
| `*` | 0 or more repetitions |
| `+` | 1 or more repetitions |
| `?` | 0 or 1 repetition |
| `{m}` | exactly `m` repetitions |
| `{m,n}` | between `m` and `n` repetitions |

Let's use `.+` ("one or more of any character") to describe "something, then `@`, then something":

```python
import re

email = input("What's your email? ").strip()

if re.search(".+@.+", email):
    print("Valid")
else:
    print("Invalid")
```

Now `malan@` (nothing after the `@`) is correctly rejected, because `.+` after the `@` requires **at least one** character.

> 🧠 **Behind the scenes:** a regular expression is actually processed like a tiny "state machine" — the interpreter reads your input character by character, moving from state to state (`q1` → `q2` → `q3`...) until it either reaches a valid final state, or gets stuck.

---

## ⚓ Anchoring With `^` and `$`

Two more essential symbols:

| Symbol | Meaning |
|---|---|
| `^` | matches the start of the string |
| `$` | matches the end of the string |

Without these anchors, `.+@.+.edu` would match even if `@` and `.edu` were buried in the *middle* of a much longer sentence:

```python
import re

email = input("What's your email? ").strip()

if re.search(r"^.+@.+\.edu$", email):
    print("Valid")
else:
    print("Invalid")
```

With `^` and `$` in place, a sentence like `"My email address is malan@harvard.edu."` correctly fails — because there's extra text *before* and *after* the pattern, and the anchors demand the pattern span the *entire* string.

---

## 🔒 Escaping the Dot

Wait — didn't we say `.` means "any character"? That's a problem: `.edu` in our pattern would technically also match `?edu` or `Xedu`! We need to tell Python we mean a **literal** dot, using the backslash **escape character**:

```python
import re

email = input("What's your email? ").strip()

if re.search(r".+@.+\.edu", email):
    print("Valid")
else:
    print("Invalid")
```

Now `\.` means "an actual period character," and `malan@harvard?edu` is correctly rejected, while `malan@harvard.edu` passes.

---

## 🧵 Raw Strings

You'll notice all our patterns are written as `r"..."` — a **raw string**. Normally, Python treats sequences like `\n` as a special character (a newline). A raw string tells Python: *"don't interpret backslashes specially — treat every character exactly as typed."*

```python
pattern = r"^.+@.+\.edu$"
```

Since regular expressions use the backslash constantly (`\.`, `\d`, `\w`, and more), always prefix your regex strings with `r` to avoid Python misinterpreting them.

---

## 🎯 Character Sets `[...]`

Right now, `malan@@@harvard.edu` (multiple `@`s) would still slip through! We need a way to say "any character *except* this one":

| Symbol | Meaning |
|---|---|
| `[...]` | a set of characters |
| `[^...]` | the complement (anything *not* in the set) |

```python
import re

email = input("What's your email? ").strip()

if re.search(r"^[^@]+@[^@]+\.edu$", email):
    print("Valid")
else:
    print("Invalid")
```

`[^@]+` means "one or more characters that are **not** `@`." Now `malan@@@harvard.edu` is correctly rejected — there can only be exactly one `@`.

We can get even more specific about what characters *are* allowed:

```python
import re

email = input("What's your email? ").strip()

if re.search(r"^[a-zA-Z0-9_]+@[a-zA-Z0-9_]+\.edu$", email):
    print("Valid")
else:
    print("Invalid")
```

`[a-zA-Z0-9_]` means "any lowercase letter, uppercase letter, digit, or underscore."

---

## ⚡ Shorthand Classes: `\w`, `\d`, `\s`

That character set is common enough that regex gives us a shortcut: `\w` means exactly `[a-zA-Z0-9_]`.

```python
import re

email = input("What's your email? ").strip()

if re.search(r"^\w+@\w+\.edu$", email):
    print("Valid")
else:
    print("Invalid")
```

More useful shorthand classes:

| Symbol | Meaning |
|---|---|
| `\d` | a decimal digit |
| `\D` | **not** a decimal digit |
| `\s` | a whitespace character |
| `\S` | **not** a whitespace character |
| `\w` | a "word" character (letters, digits, underscore) |
| `\W` | **not** a word character |

---

## 🔀 `\|` — Either This or That

Not every email ends in `.edu`. Let's accept a few common endings using `|` ("or"):

```python
import re

email = input("What's your email? ").strip()

if re.search(r"^\w+@\w.+\.(com|edu|gov|net|org)$", email):
    print("Valid")
else:
    print("Invalid")
```

---

## 🎁 Groups `(...)` and Non-Capturing Groups `(?:...)`

| Symbol | Meaning |
|---|---|
| `A\|B` | either `A` or `B` |
| `(...)` | a **group** — also captures the matched text for later use |
| `(?:...)` | a group, but **without** capturing (used only for grouping/logic) |

We just used `(com|edu|gov|net|org)` as a group of alternatives. Groups become even more powerful once we start *extracting* what they matched — coming up shortly.

📚 You can find the **full**, industrial-strength email-validating regular expression in [Python's `re` documentation](https://docs.python.org/3/library/re.html) — it's dramatically longer than anything we've written here, a good reminder that "fully correct" validation can get surprisingly deep!

---

## 🔠 Case Sensitivity With Flags

`re.search` accepts an optional third argument: **flags**, which change how matching behaves.

```python
import re

email = input("What's your email? ").strip()

if re.search(r"^\w+@\w+\.edu$", email, re.IGNORECASE):
    print("Valid")
else:
    print("Invalid")
```

`re.IGNORECASE` means `MALAN@HARVARD.EDU` now matches too, not just the lowercase version. Other useful flags include `re.MULTILINE` and `re.DOTALL` — worth exploring in the docs as your patterns grow more advanced.

---

## ❓ Optional Groups

What about an email like `malan@cs50.harvard.edu`, with an *extra* subdomain? Right now that fails, since our pattern only expects one dot before `.edu`. Let's make a whole group **optional** using `?`:

```python
import re

email = input("What's your email? ").strip()

if re.search(r"^\w+@(\w+\.)?\w+\.edu$", email, re.IGNORECASE):
    print("Valid")
else:
    print("Invalid")
```

`(\w+\.)?` means "this whole group — a word followed by a dot — may appear once, or not at all." Now both `malan@harvard.edu` and `malan@cs50.harvard.edu` pass.

---

## 🧹 Cleaning Up User Input

Regexes aren't just for *validating* input — they're also great for **cleaning it up**. Let's build a name-formatter:

```bash
code format.py
```

```python
name = input("What's your name? ").strip()
print(f"hello, {name}")
```

Type `David` → works fine. Type `Malan, David` (last name first) → looks wrong. Let's detect and fix that:

```python
name = input("What's your name? ").strip()

if "," in name:
    last, first = name.split(", ")
    name = f"{first} {last}"

print(f"hello, {name}")
```

This handles `"Malan, David"` nicely — but crashes on `"Malan,David"` (no space after the comma), because `.split(", ")` expects that exact two-character separator.

---

## 🎯 Extracting Matched Groups

Time to bring in regex properly. `re.search` doesn't just tell you *if* something matched — it can hand back the **matched groups** themselves:

```python
import re

name = input("What's your name? ").strip()

matches = re.search(r"^(.+), (.+)$", name)
if matches:
    last, first = matches.groups()
    name = first + " " + last

print(f"hello, {name}")
```

`(.+)` and `(.+)` are two separate **capture groups** — the text before the comma, and the text after it. `matches.groups()` returns them as a tuple, ready to unpack.

You can also grab one group at a time with `.group(n)` (note: singular, no "s", and numbering starts at `1`, not `0`):

```python
import re

name = input("What's your name? ").strip()

matches = re.search(r"^(.+), (.+)$", name)
if matches:
    name = matches.group(2) + " " + matches.group(1)

print(f"hello, {name}")
```

Now let's finally fix the "no space after comma" bug, using `*` (zero or more) instead of a hardcoded literal space:

```python
import re

name = input("What's your name? ").strip()

matches = re.search(r"^(.+), *(.+)$", name)
if matches:
    name = matches.group(2) + " " + matches.group(1)

print(f"hello, {name}")
```

Now `"Malan,David"`, `"Malan, David"`, and even `"Malan,     David"` (extra spaces) all work correctly.

---

## 🦭 The Walrus Operator `:=`

We've been writing `matches = re.search(...)` on one line, then checking `if matches:` on the next. Python's **walrus operator** (`:=`) lets you assign *and* test in a single line:

```python
import re

name = input("What's your name? ").strip()

if matches := re.search(r"^(.+), *(.+)$", name):
    name = matches.group(2) + " " + matches.group(1)

print(f"hello, {name}")
```

Turn your head sideways — `:=` looks a bit like a walrus's tusks, which is exactly where the nickname comes from! This is purely a style/conciseness choice — both versions behave identically.

---

## 🔗 Extracting Data From URLs

Let's extract just the username from a Twitter URL. Starting simple:

```bash
code twitter.py
```

```python
url = input("URL: ").strip()

username = url.replace("https://twitter.com/", "")
print(f"Username: {username}")
```

This works for the *exact* full URL — but breaks the moment the user types anything slightly different, like leaving off `https://`. String methods like `.replace()` and `.removeprefix()` are too rigid for messy real-world input; regex is the right tool here.

```python
import re

url = input("URL: ").strip()

if matches := re.search(r"^https?://(?:www\.)?twitter\.com/([a-z0-9_]+)", url, re.IGNORECASE):
    print("Username:", matches.group(1))
```

Let's break this pattern down piece by piece:

| Piece | Meaning |
|---|---|
| `https?` | `"http"`, with an **optional** `s` — matches both `http` and `https` |
| `(?:www\.)?` | an optional, **non-capturing** `www.` prefix |
| `twitter\.com/` | the literal domain (escaped dot!) |
| `([a-z0-9_]+)` | the actual **username** — captured, since it's what we want back |
| `re.IGNORECASE` | so `TWITTER.COM` also matches |

`matches.group(1)` then hands back exactly the username, regardless of how the rest of the URL was typed.

---

## 🔄 `re.sub` — Search and Replace

Sometimes you don't want to just *find* a pattern — you want to **replace** it. That's `re.sub(pattern, repl, string)`:

```python
import re

url = input("URL: ").strip()

username = re.sub(r"https://twitter.com/", "", url)
print(f"Username: {username}")
```

This substitutes the matched pattern with an empty string, `""` — effectively deleting it. But just like before, this only works for one *exact* format. Let's make the pattern flexible using everything we've learned:

```python
import re

url = input("URL: ").strip()

username = re.sub(r"^(https?://)?(www\.)?twitter\.com/", "", url)
print(f"Username: {username}")
```

- `(https?://)?` — the protocol, entirely optional
- `(www\.)?` — the `www.` subdomain, also optional
- `twitter\.com/` — the literal domain

Now `twitter.com/davidjmalan`, `www.twitter.com/davidjmalan`, and `https://twitter.com/davidjmalan` all correctly reduce down to just `davidjmalan`.

---

## 📝 Summary

By the end of this lecture, you've learned:

- ✅ Why plain `if`/`in` checks eventually fall short for validating text
- ✅ `re.search` and the core regex symbols: `.`, `*`, `+`, `?`, `{m,n}`
- ✅ Anchors `^` and `$`
- ✅ Escaping special characters with `\`
- ✅ Raw strings (`r"..."`)
- ✅ Character sets `[...]` and their complement `[^...]`
- ✅ Shorthand classes: `\w`, `\d`, `\s` (and their opposites)
- ✅ Alternation with `|`
- ✅ Capturing groups `(...)` vs. non-capturing groups `(?:...)`
- ✅ Flags like `re.IGNORECASE`
- ✅ Optional groups with `(...)?`
- ✅ Extracting matched text with `.groups()` and `.group(n)`
- ✅ The walrus operator `:=`
- ✅ `re.sub` for search-and-replace

---

## 💪 Practice Ideas

1. Write a regex that validates a US phone number in the format `123-456-7890`, and test it against a few valid and invalid examples.
2. Extend the email validator to also accept `.co.uk` and `.io` domains.
3. Write a program using `re.search` and groups to parse a date typed as `MM/DD/YYYY` and print it back out as `YYYY-MM-DD`.
4. Using `re.sub`, write a program that "censors" any digits in a string of text, replacing each one with `*`.

---

## 📚 Resources

- [CS50P official course](https://cs50.harvard.edu/python/)
- [Official Lecture 7 notes](https://cs50.harvard.edu/python/notes/7/)
- [Python `re` documentation](https://docs.python.org/3/library/re.html)
- [Python Regular Expression HOWTO](https://docs.python.org/3/howto/regex.html)

---

⭐ Next up in the series: **Object-Oriented Programming** — star this repo to keep track as more lecture notes get added!

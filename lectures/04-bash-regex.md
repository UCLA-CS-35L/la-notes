---
layout: default
title: "04. Bash and Regular Expressions"
author: Eric Wang
date: 2025-04-10
---

## Little Languages

**Remark.** Think of programming as contained lots of little domains within each
language. Bash/shell is sort of its own domain: you'll want to understand how to
combine programs together. Regular expressions are also their own domain: you'll
want to understand how to match certain patterns of text.

For example, consider the command `grep`.

- In the regular expression domain, you'll want to understand the actual regex
  syntax itself, both BREs and EREs. You'll want to understand patterns like `*`
  and `.`.
- In the Bash/shell domain, you'll want to understand how to use it as a filter
  or a check. You'll want to understand things like what `grep -o` or `grep -q`
  does and checking the exit status for success/failure when you hook it up with
  pipes and redirection.

---

**Aside.** Professor Eggert tends to ask cross-domain questions. It lets him
think of good, hard questions.

---

## Regular Expressions Intro

Different regular expression engines will have different flavors. Typically,
there are three main categories: basic (BRE), extended (ERE), and Perl
compatible (PCRE). In this course, we'll focus on BRE and ERE since `grep`
supports this. You may assume that we are using GNU `grep`; other operating
systems (e.g. MacOS) and programming languages (e.g. JavaScript) may have their
own built-in extensions.

- `grep PAT FILE1 [FILE2] ...`: outputs lines in the files that match patterns

  - optionally have multiple files, and like `cat`, if no files, then read from
    stdin
  - toggle options:

    - `-l`: output filenames that containing a matching line
    - `-v`: invert i.e. consider a match to be contained if `PAT` is not a match
    - `-E`: use EREs (discussed below)

      - the default behavior is BREs (also discussed below)

    - `-r`: recursively walk if given a directory (not POSIX)
    - `-L`: output filenames not containing a matching line
    - `-q`: quiet mode, don't print anything

---

**Aside.** Once you understand how to write regular expressions, try
experimenting with `-l`, `-L`, `-lv`, and `-Lv`. Inverting with `-v` and
inverting in bracket expressions with `^` are also different.

---

## Extended Regular Expressions (EREs)

---

**Aside.** EREs are better (even Professor Eggert has said so in the past), so
just use `grep -E` when possible.

---

- `a`: just matches `a` itself
- `.`: matches any single character (except newline since `grep` goes line by
  line)
- `[aeiou]`: bracket expression that matches any characters inside

  - `[a-ik-z]`: using the `-` range shorthand to include all characters between
    `a` and `i` (inclusive) as well as `k` to `z` (also inclusive)
  - to include `-` in a bracket expression, you must put it as the last
    character
  - `[[:alpha:]]`: character class for alphabetical characters (also other
    standard regex character classes
    <https://www.gnu.org/software/grep/manual/html_node/Character-Classes-and-Bracket-Expressions.html>)
  - to include `]` in a bracket expression, make sure it is the first character

    - there is not a similar restriction for `[`: to include `[` in a bracket
      expression, just include it anywhere!

  - `[^0-9]`: negated bracket expression (anything except these characters)
  - to include `^` in a bracket expression, make sure it is _not_ the first
    character

- `^`: start of line (empty i.e. doesn't consume a character)
- `$`: end of line (also empty)
- `PAT*`: any number of instances of pattern `PAT`
- `PAT+`: at least one instance of pattern `PAT`

  - observe that `PAT+` is equivalent to `PAT` followed by `PAT*`

- `PAT?`: 0 or 1 instances of `PAT`

  - observe that `PAT*` is equivalent to `PAT+?`

- `PAT1|PAT2`: either `PAT1` or `PAT2`

  - bracket notation is effectively just a subset of the `|`, nice for shorthand

- `(PAT)` is equivalent to matching `PAT` but allows for operator precedence

  - e.g. `grep -E '^a|b'` vs. `grep -E ^(a|b)`

- `PAT{n}` is `PAT` exactly `n` times
- `PAT{m,n}` is `PAT` between `m` and `n` times (inclusive)
- `PAT1PAT2` is matching `PAT1` followed by `PAT2` (concatenation)
- `\1` matches the first parenthesized expression and can be used as a
  backreference
  <!-- cspell:disable-next-line -->

  - e.g. `(c+)x\1` matches `ccxcc` but not `ccxc` since `c != cc`.

- escape sequences: required so we can match some of these literal characters!

  - `\^`
  - `\*`
  - `\^`
  - `\$`
  - `\[`
  - `\(`
  - `\{`
  - `\.`
  - `\|`
  - `\?`
  - `\\`
  - observe that the closed bracket, parentheses, and braces are not there since
    an extraneous bracket/parenthesis/brace with no corresponding opening must
    be already escaped

Example: match lines that contain at least one "a" and no "b"s.

- using two greps: `grep a | grep -v b` (you could use `-E`, but it's not
  necessary)
- using one grep: `grep -E '^[^b]*a[^b]*$'` (i.e. match a bunch of not b,
  followed by a, followed by not bs from start to end of line)

## Basic Regular Expressions (BREs)

BRE syntax just requires you to replace escape the special characters `?+{|(` to
get their special meaning.

- e.g. `grep -E 'a?'` is equivalent to `grep 'a\?'`

## Bash Scripting Intro

All scripts start with a _shebang_. This is the first line and starts with `#!`.
The shebang indicates what program we will use to interpret the text below.

- e.g. in the above example, `#!/bin/bash` indicates that the program we will
  use is `/bin/bash`. This ensures that we are using `bash`. It is preferred to
  hardcode this name `/bin/bash` to `bash`.
- e.g. `#!/usr/bin/env python3` indicates that the program we will use is
  `/usr/bin/env python3`. This ensures that we are using `python3`. It is
  preferred to let `/usr/bin/env` (run in a modified environment) decide which
  `python3` to use.

## Common Bash Constructs

The following are common constructs when writing scripts in Bash.

---

**Aside.** This section will be updated once I have a clearer idea of what
Professor Eggert wants to lecture about with Bash. Historically, many Bash
scripts ended up being one-liners. Discussion slides will definitely talk about
Bash.

---

## Bash Quoting and Escaping

---

**Aside.** This section will be updated or removed once I have a clearer idea of
what Professor Eggert wants to lecture about with Bash.

---

---
title: "02. Emacs"
author: Eric Wang
date: 2025-04-03
---

## Emacs Intro

The key software quality attributes we care about for applications are as
follows:

- persistence: we want our data to not disappear e.g. survive power outages
- performance: be fast!
- maintainable: easy to read, write, maintain

---

**Aside.** Sometimes, persistence is referred to by the term "nonvolatile." Be
careful when using the term "volatile" since it has a different meaning in C,
C++ (the `volatile` keyword) than it does when you’re talking about
persistent/nonvolatile storage. The `volatile` C/C++ keyword will be briefly
covered later in the course.

---

**Aside.** Professor Eggert likes to call to refer to how Emacs refers to things
as "Emacsese." For example, `C-` is Emacsese for the control key (see below).

---

We’ll use Emacs as a "window" into application writing. It’s just another
program; _you_ could have written it, if you had the time, or you could write a
substitute.

Why Emacs?

- "I'm biased - I contribute to Emacs (>7000 commits)." - Professor Eggert (real
  quote)
- Emacs gives us two important things:

  - scriptability via a clean language: ELisp allows us to extend the basic
    Emacs application
  - introspection: Emacs is “self-aware”

To startup Emacs:

- `emacs`: default behavior, configurable in the file `~/.emacs`
- `emacs FOO`: start by reading file `FOO`
- `emacs -nw`: do not create a GUI window, just run in terminal

---

**Aside.** Professor Eggert will talk about character encodings like ASCII later
in the course. For now, all you need to understand is that any character is
encoded as a number. For example, `'a'` corresponds to the number `97`. This is
the "ASCII code" of `'a'`.

It is common to use `0x` to prefix hex values (base 16) and `0b` to prefix
binary values (base 2).

We can find the corresponding ASCII character for control codes by taking the
original character and bitwise and it with `0x1f`. A bit pattern like `0x1f` and
using it to bitwise and with another number is commonly referred to as a _bit
mask_.

For example, the value of `C-x` (see below) can be found as follows:

- take the ASCII value for `'x'`
- applying bitwise and with `0x1f`. In terms of decimal numbers, this expression
  becomes `120 & 31`. In binary, we get `0b1111000 & 0b11111 = 0b11000`. Thus,
  the resulting decimal value is `24`.

See the ASCII control characters here on Wikipedia!
<https://en.wikipedia.org/wiki/C0_and_C1_control_codes#C0_controls>

Some generally useful ones to know include the following:

- `^C`: often used to interrupt a program in execution in your terminal
- `^D`: used to indicate the end of a stream (e.g. a file), useful when you want
  to indicate the end of standard input
- `^I`: horizontal tabulation aka `\t` in most programming languages
- `^J`: line feed aka `\n` in most programming languages

  - Compare this with the Emacs help on `C-j`!

- `^M`: carriage return aka `\r` in most programming languages

  - Compare this with the Emacs help on the RET key!
  - This is why typing a return appears as `^M` in your dribble files.

- `^[`: escape aka the meta key in Emacs
- `^?`: delete aka the DEL key

---

When using Emacs, you'll be constantly using certain _keystrokes_, combinations
of keys. All keystrokes in Emacs correspond to a string of ASCII characters.
Colloquially, we use the following shorthand:

- Capital C corresponds to the CTRL ("control") key. When used in combination
  with another key, it can be used to form certain non-printable ASCII
  characters.

  - e.g. `C-x` corresponds to a single ASCII character with code `24`. You would
    type this character by holding control, then pressing x. Do not type the
    hyphen.
  - e.g. `C-x u` corresponds to a string of two ASCII characters since `C-x` is
    one ASCII character, and `u` is one ASCII character. You would type this
    character by holding control, then pressing x, then releasing, and finally
    pressing u.
  - In a dribble file, control characters are commonly notated as the original
    character capitalized prefixed by `^` e.g. `^X` corresponds to `C-x`. This
    is called _caret notation_.

- Capital M corresponds to the _meta_ key. Recall that the ASCII encoding takes
  up 8 bits (aka 1 byte) but only uses 7 bits, leaving the most significant bit
  unused. When using the meta key in combination with another key, the meta key
  flips the unused bit from 0 to 1.

  - e.g. `M-x` corresponds to the code formed by taking `x` with ASCII code
    `0b11111`, then flipping the 8th bit to become `0b10011111` to have decimal
    value `159`. This is equivalent to adding 128 to the decimal value.
  - e.g. `C-M-s` is equivalent to pressing the meta key followed by `C-s`. This
    is because we are just taking an ASCII character (`C-s`), then flipping the
    unused bit with the meta key. Do not type the hyphen.
  - In a dribble file, the meta key is either notated as its own character `^[`
    or as the ASCII control character `ESC` e.g. `^[x` or `ESCx` corresponds to
    `M-x`.

- RET corresponds to the RETURN or ENTER key. This corresponds to ASCII control
  code `C-m`. It will appear as `^M` in your dribble file.

---

**Aside.** Depending on your system, the meta key may be bound to a different
physical key. The ESC key should always work. On some systems, ALT or OPTION
should work.

---

## Common Emacs Keystrokes

---

**Aside.** The following keystrokes and terms are things Professor Eggert has
specifically thought of. It is certainly possible you will need more for the
exam and to finish Assignment 1. See GNU Emacs Reference Card.
<https://www.gnu.org/software/emacs/refcards/pdf/refcard.pdf>

---

Let's start with some definitions:

- _file_: something in the file system (persistent).
- _buffer_: a bunch of text in Emacs’s memory (fast but not persistent like a
  file)
- _window_: your view of the buffer in Emacs.
- _frame_: Emacsese for window.
- _point_: another way of referring to the current position of your cursor.
- _mark_: a spot where you can "save" your cursor position.
- _region_: the area between current position and the mark.
- _minibuffer_: a small buffer where Emacs commands are run!

Some good keystrokes to know:

- `C-h k`: used to find out what a key will do

  - e.g. `C-h k C-j` tells me what `C-j` does.

- `C-x o`: switch to other buffer
- `C-x 1`: look at just this buffer (it’s fast to type on this!)
- `C-x 2`: split buffer in half horizontally
- `C-x 3`: split buffer in half vertically
- `C-x 5`: create a new window (Zoom hates this)
- `C-x 0`: delete window
- `C-b NAME`: switch this frame to buffer `NAME` (replace `NAME` with actual
  name of your buffer e.g. `*scratch*`)
- `C-x C-b`: put buffer listing into a new buffer, and display as other buffer
- `C-x C-s`: save the current buffer into the corresponding file

  - buffers are not persistent, so Emacs saves a backup file FOO~ if you edit a
    file FOO and don't get to save before Emacs crashes or something like that

- `C-x s`: save all buffers (querying)
- `C-x b`: switch buffer
- `C-x C-b`: list buffers
- `C-x k`: kill buffer
- `C-x C-q`: toggle read-only
- `C-x d RET`; create a buffer containing a list of what’s in current directory

  - Recall that every directory has at least two entries:

    - `.`: current directory
    - `..`: parent directory

  - In a directory listing, typing `g` means refresh it from the file system.

- `M-q`: reformat the current paragraph
- `TAB`: completion (just like in the terminal)
- `C-g`: Get me out of here! Go to top level.
- `C-x C-f FILE RET`: visit file `FILE` (start editing `FILE`)
- `C-@ (C-space)`: how to copy text with a keyboard, sets the "mark" (the other
  cursor)
- `M-w`: copy all the text from the mark to the current position (point)
- `C-y` yanks the copy into the current location
- `C-k`: kill text to end line (copy into temp)
- `C-y`: yank what you just killed
- `DEL` delete region (or next character)
- `M-x view-lossage` put the last N commands into `*Help*`
- `M-! COMMAND RET`: runs the shell command `COMMAND`
- `M-x shell RET`: starts up a shell inside Emacs shell prompt (output by shell)

## Emacs Modes

Emacs is a _modeful_ editor: the action it takes when type a character depends
on the mode that Emacs is in.

- e.g. If you type `g` in directory listing, it means `revert-buffer` (that is,
  sync from file system). On the other hand, if you type `g` in a text buffer
  (in fundamental mode), it means insert the letter `g`.
- `C-h k` observes modes! You can compare `C-h k g` from within a directory
  listing and within a text buffer.

Some users hate multiple windows, and work just like in this lecture, one big
Emacs controlling the screen.

For now, assume there are only two types of files (actually there’s more, but
let’s pretend).

1. Regular files are sequences of bytes.
2. Directories are mappings from file names to files.

To edit a regular file, Emacs creates a buffer, copies the file’s contents into
this buffer, and then you edit the buffer. When you `C-x C-s`, Emacs copies the
buffer contents back into the file!

## Emacs Backups

Emacs convention for file names:

- `FOO~`: backup file for `FOO`
- `#FOO#`: last-saved version of `FOO` while you’re editing it; Emacs tries to
  get around the problem that buffers are not persistent by saving the contents
  of a modified buffer into #FOO#.
- `.#FOO` is a symbolic link to nowhere; it’s a string saying who’s in the
  middle of editing the file now

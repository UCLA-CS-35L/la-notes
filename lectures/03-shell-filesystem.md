---
title: "03. The Shell and the Filesystem"
author: Eric Wang
date: 2025-04-08
---

## Common Shell Commands for Text Manipulation

- `cat FILE1 FILE2 ...`: write the contents of each file to output

  - if just `FILE1`, then write the contents of just this file
  - if `FILE1` and `FILE2`, then first write `FILE1`, then `FILE2`
  - if no files supplied, then read from stdin; use `^D` to end the file

- `echo FOO`: write `FOO` to output
- `head -N A` - copy the first `N` lines of file `A` to output
- `grep`: regular expressions, covered in next set of notes

---

**Aside.** This section will be updated once I have a clearer idea of Professor
Eggert's favorite commands.

---

## Shell Commands for Filesystem Interaction

File names in Linux are of the form `/A/B/C/D..../Z`.

- each component is a directory except for the last component `Z`
- e.g. `/bin/bash` is a file with named `bash` inside of the directory `bin`
  which is a subdirectory of the root `/` directory
- to resolve an inode, just traverse each `/`-separated substring (A, B, C,
  etc.)

When you create a file, its owner is you (the creator), and its group is
typically inherited from the group of the parent directory; (this isn’t always
true but is good enough for now).

---

**Aside.** Unix style filesystems typically organize users under the directory
`/home`. Each username will be listed as a subdirectory e.g. `/home/ericwang`
would be a directory contain all of my files! This is not how SEASnet does it.

When you log in, your current shell session automatically sets `$HOME` to be
whatever directory you login to. You can also refer to this variable as the
tilde (`~`).

---

- `ls`: output names of files in current directory
- `cd`: change directory (`$HOME` by default)

  - Every directory has two entries `.` (self) and `..` (parent).
  - Directories are in a tree structure; you cannot create arbitrary hard links
    between directories (only through special commands like `mkdir` or symlinks
    through `ln -s`)
  - The parent of the root directory `/` is itself.

- `ls`: show contents (`.` by default). The following options can be combined
  arbitrarily.

  - `ls -i`: like `ls`, but also give inode numbers
  - `ls -d`: like `ls`, but display directories as regular files

    - e.g. `ls -l file.txt` will only display the info about `file.txt`, but
      `ls -l dir` will list out each file inside of `dir` by default. To display
      the extra info about `dir` itself, run `ls -ld dir`.

  - `ls -a`: like `ls`, but also output names of files beginning with `.`;
    normally these files are suppressed
  - `ls -l` gives more info

    - 2nd column: (hard) link count

      - symlinks do not count!

    - 3rd column: owner
    - 4th column: group
    - 5th column: size

      - the size of a symlink is equal to the number of characters in the string
      - the size of a directory is the number of bytes required to represent the
        directory entries

    - e.g. `ls -l` output:
      `-rw-rw-r-- 1 eggert eggert 3190 Oct 6 16:45 notes.txt`

      - -rw-rw-r--: mode

        - 1st character is file type, ‘-’ regular file, ‘d’ directory
        - next 3 characters are permission for owner of file

          - `r`: readable
          - `w`: writeable
          - `x`: executable (for regular file) or searchable (for dir)

        - next 3 are permissions for others in the group
        - last 3 are permissions for everyone else

      - 1 - link count (number of directory entries that point to this file)
      - eggert - owner
      - eggert - group
      - 3190 - number of bytes in the file
      - Oct 6 16:45 - last-modified time
      - notes.txt - file name within its parent directory (This name is quoted
        if it’s problematic to the shell)

- `ln A B`: create a (hard) link to `A` named `B`

  - create a new name `B` for the existing file `A`
  - `A` and `B` are “equal”; they both name the same file, and neither is more
    important than the other (e.g. they have the same inode)
  - Why have hard links? Efficient sharing of data e.g. `git clone` uses this to
    clone Git repositories.

- `ln -s A B`: creates a symbolic link (symlink) named B that points to the name
  A

  - symbolic links are neither regular files nor directories: they’re just "file
    name redirections" (like a pointer in C/C++)`
  - Why have symbolic links?

    - For metainformation (like emacs).
    - When your file has the “wrong” name.

  - e.g. `/a/x -> ../y` means that `/a/x/b` is equivalent to `/a/x/../y/b` which
    is equivalent to `/a/y/b`. This symlink could be created with the command
    `ln -s ../y /a/x` (reversed).
  - symlinks are just textual shortcuts: you can create loops! To resolve
    infinite loops, there is typically a finite limit on symlink resolution
    (e.g. 20).

- `rm A`: remove the name A for a file

  - note: this is not just "deleting" file A since there may be multiple names

- `chmod`: change file permissions

  - can be comma-separated format string (e.g. `chmod u+x,g=r,o-w file.sh`)
  - can be 3 octal numbers in decimal (e.g. `chmod 755 file.sh`)
  - note: the two examples given are _not_ equivalent

- `;`: separate commands

## I/O Redirection

One of the shell's purposes is configuring the environment for programs to be
run together. A _file descriptor_ is just a number that allows your program to
interact with a file on your system. For example, when you open a file in a
program, internally, your program acquires a new file descriptor e.g. 3.

The standard input (stdin), standard output (stdout), and standard error
(stderr) are automatically created. In general, we can think of these are the
common way of interacting with most programs.

- stdin: file descriptor 0
- stdout: file descriptor 1
- stderr: file descriptor 2
- Your shell treats your keyboard as stdin and your display as stdout and
  stderr.

I/O redirection is a form of interprocess communication: processes can exchange
information through their inputs and outputs. By default, programs accept input
from stdin, product output in stdout, and log error messages in stderr. We can
change this behavior:

- `<`: redirect input

  - e.g. `<input.txt` means that we are using `input.txt` as our input
  - note: technically, this is `0<`, but the `0` is implicit

- `>`: redirect output, overwriting the destination

  - e.g. `>output.txt` means that we are using `output.txt` as our output
  - note: technically, this is `1>`, but the `1` is implicit

- `2>`: change stderr (this is just a specific case of the `>` redirection)

  - e.g. `2>errors.txt` means that we are using `errors.txt` as our error

- `>>`: redirect output, appending to the destination

  - e.g. `>output.txt` would overwrite `output.txt` if the file already
    exists;`>>output.txt` means that we will not overwrite the existing contents
    and just append the new contents
  - note: technically, this is `1>`, but the `1` is implicit

These can be combined and used in conjunction.

- `cat file.txt >output.txt 2>errors.txt` means that we are running the command
  `cat file.txt` (outputting the contents of the file `file.txt`) and sticking
  the output in `output.txt` and the error in `errors.txt`.
- `>&`: redirect output and error

  - e.g. `>&all` puts both output and error in the file `all`

- `|`: pipe the stdout from the previous command into the stdin of the next
  command

  - e.g. `ls | sort` lists the contents of the current directory and sorts in
    (lexical) order

---

**Aside.** A lot of these shell operations with commands can be rewritten in
terms of each other. Professor Eggert has recently taken a liking to asking
questions of this nature.

As a challenge, try rewriting `>&word` without the ampersand. See the
documentation for a solution!
<https://www.gnu.org/software/bash/manual/html_node/Redirections.html>

---

**Aside.** You can create new file descriptors in the shell by referring to the
sequentially (e.g. the next file descriptor that is available is 3). Subshells
inherit file descriptors from the original process. See the LA Week 1 Worksheet
for an example.

These two topics (new file descriptors and setting up the environment of a
subshell) are included as an aside since I am not sure how in depth Professor
Eggert wishes to talk about this!

---

## Parallelism

Some parallelism is already achieved by `|`. Instead of waiting for the first
command to finish before piping the output, as each write occurs in the original
command, the next command can read!

If we want something to run in the background, use `&`.

- `sleep N` can be used to wait `N` seconds

  - e.g. `sleep 3 & echo foo`

    - the first line of output we see is just how the terminal lets us know what
      is the process ID of the command that is running in the background
    - instead of sleeping for 3 seconds first, we see that "foo" is immediately
      outputted to the terminal

- `wait PID` can be used to wait for process with ID `PID` to finish

  - it is useful to use `$!` to access the process ID of the last background
    process created
  - e.g. see the "useless" Bash script below that runs something in the
    background but then immediately waits for it

```bash
#!/bin/bash

sleep 10&
pid=$!
wait $pid
```

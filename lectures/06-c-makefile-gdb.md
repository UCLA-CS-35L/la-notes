---
layout: default
title: "06. C, Makefile, and GDB"
author: Pavan Gudavalli
date: 2025-05-13
---

# C

### Basics

An example C program may look like

```c++
#include <stdio.h>

int main(){
    printf("Hello World");
    return 0;
}
```

You should remember for CS31 what each line does, but for a quick recap

- The main function is where the program runs when you execute it
- `int` denotes the return type(0 -> success)
- `printf` is a function
- `<stdio.h>` is a header file that contains a ton of functions(like `printf`)

C has primitive data types

- `char` - 1 byte
- `int` - 4 bytes
- `float` - 4 bytes
- `double` - 8 bytes
- `short` - 2 bytes
- `long` - 4 bytes
- `long long` - 8 bytes
- `bool` - ` byte
- `void` - no return

There are other data types that you may run into like `unsigned`, `size_t`, and
`int32_t` for example depending on what header files you use.

Then, we also have arrays, strings, and structs.

```c++
#include <string.h>

int main(){
    int a[2] = {1,2};
    char a[] = "yo what up";

    struct yo{
        int a;
        char b;
    } typedef yuh;
}
```

Important things to note are

- Arrays must have all elements be the same type + are fixed size.
- Strings end with `\0` and are basically an array of chars.
- Structs consist of multiple members + are stored consecutively in memory.
- Access with `.` and `->` for normal members / pointer members.
- `typedef` simply renames the struct

### Pointers

A program using pointers might look like

```c++
int main(){
    int a=0;
    int *b = &a;
    int c = *p;
    *p = 1;
}
```

A pointer associates a memory location with a data value. So, in this program,
we

- defined an int a
- set int pointer b equal to the memory location of a with the `&` operator. `*`
  denotes a pointer type variable.
- set int c equal to the value at memory location p with the `*` operator.

So, at the end of the day, we might have values like `a=1`,
`b=0x777777ff`,`c=0`. Note how `a` and `c` are not the same!

This ties into dynamic memory. Often, we don't know how big we want containers
to be. We can thus allocate / free memory on the heap to get around that issue.
Note,

- use functions like `*calloc, *malloc, *realloc, free` to allocate/free memory
- need to zero out allocated memory before using it
- make sure to free!

### Compiling

Most people use `gcc` to compile C code. An example command could be
`gcc -o output input.c`

which compiles the `input.c` file to an executable called `output` which can
normally be run through `./output`.

Some common options include

- `-c` no linking(more below)
- `-Wall` enable warning
- `-O<int>` optimize, `<int>` is btwn 0 to 3
- `-fstack-protector` in the name
- and lots more!

When compiling, there are a few intermediary steps. Notably,

- Preprocessing: dealing with header files, comments, etc.
- Compiler: translate c code to assembly
- Assembler: translate assembly to machine code
- Linker: link imported libraries to main file to produce final exec
  - Static: copies code at compile time
  - Dynamic: copies code at run time

# Makefile

We use makefiles to provide an a way to easily build executables that may
require a lot of different commands / flags / etc. to create. So, you can simply
run `make` in your terminal to create your executable!

An example makefile may look like

```make
CC = gcc

default: exampleOut

exampleOut: example.o
    $(CC) -o $@ example.o

clean:
    rm -f example.o exampleOut
```

The format of each command is `target: dependency sysCommand(s)`

- Target: Generated file or action to carry out
  - One of our commands generates `exampleOut`, one executes the `clean` command
- Dependencies: files used to create target
- System Commands: the actual action that is run on the command line.

So, the above commands allow the user to, on the command line, run

```sh
make exampleOut # create the executable; make would also work
make clean # remove the executable
```

Some other aspects of makefile include

- You can define variables like in our first line
- `Default` signifies the default target
  - So, if we run `make`, then `exampleOut` will be run
- `$@` stands for target(so `exampleOut` in our case)
- Can use wildcards! Ex: could say `*.c` to refer to all c files in the current
  directory.

# Debugging

GDB and Valgrind are two tools that you can use to debug your code(aside from
print statements🫨)

### GDB

To use gdb, compile your code with the `-g` flag. Then, run `gdb <filename>.out`
GDB is mostly used for stepping through code line by line and checking variables
values.

Common commands you can run are

- `b <function_name>` -> create a breakpoint
- `continue` -> continue in execution till next breakpoint
- `info b` -> list breakpoints
- `step <n>` -> go through n lines of code
- `next <n>` -> go through n lines of code but fully execute functions as well
- `print <var>/<exp>` -> print variable(note: you can specify a lot with this
  command such as whether you print in hex, binary, etc.)
- and a lot more!

### Valgrind

To use valgrind, compile your code with the`-g` flag. Then, run
`valgrind --leak-check=full ./<filename>.exe` Valgrind is mostly used for
looking at memory management issues!

---

**Aside.** Assignment 6 may or may not be less focused on C/GDB/Makefiles in
Spring 2025 compared to previous quarters. However, it should still show up on
your final in an equal amount!

---

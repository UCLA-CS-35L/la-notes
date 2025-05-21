---
layout: default
title: "05. Git and Github"
author: Charlton Shih
date: 2025-05-6
---

# Version Control

## What do we want out of version control?

- A “useful” subset of the history of the project
- A backup (something went wrong)
- Answer to “Why is this here?”
- Look at related code (changed at the same time)
- Look at the commit message
- Look at relevant bug reports
- Has plans for the future: like to-do lists (README)

## Git (and other systems)

- Gives an object database (repository) [history]
- Persistent objects (live in secondary storage)
- Contains updates to the collection of objects
- **Index (cache)**: [plans for the future]
- Used to create commits, manage merges, cache data
- Commands manipulate objects as a consequence

## Getting Started with Git

### From Scratch:

```bash
git init
```

- Creates `.git`, a subdirectory

### From Existing Repository:

```bash
git clone [repository]
```

- Clones latest version into a new directory
- Most files are **hard links** to the original, not new copies
- Changes are computed as file differences (efficient)

### Git Components:

- **Head** – Most recent commit
- **Index** – Plan for the next commit
- **Working Files** – What the compiler or tools interact with

## Git Commit Structure

- `HEAD` → Most recent commit → parent → ... → first commit
- Merges can have multiple parents

## Git Commit Naming

- **Cryptographically secure checksums** to avoid collisions
- Example (simple): All bytes in commit → hash
- Uses **SHA-1** (160-bit secure hash algorithm)

## Git Internals

### Why Learn Internals?

1. Get intuition about what works
2. Good practice for software construction

### Plumbing vs Porcelain

- **Plumbing** – Low-level commands
- **Porcelain** – High-level, user-facing commands
- Git is a low-level collection of objects
- “Blobs check in, but they don’t check out”

## Important Git Files & Directories

### `.git/config`

- Very important
- Use `git config`, don’t edit directly
- `[user]` section: tied to commit identity

### `.git/hooks/`

- Contains shell/sample scripts
- `autogen.sh` sets up Git and makes custom scripts
- Hooks customize Git behavior
- Sample hooks are included but inactive
- Some hooks activate during operations like `rebase`

### `.git/index`

- The "Index" is Git’s plan for the next commit
- A cache containing info about the current state

### `.git/info/exclude`

- Local file ignore list (like `.gitignore`)
- `.gitignore` is version-controlled and shareable

### `.git/log`

- Meta-history of the repository
- A shorthand, not a full history

### `.git/objects`

- Where the real repository data lives
- Object-oriented DB containing all changes
- Each object has:
  - SHA-1 hash
  - Text field
  - Pointers (optional)
- Tuned for version control operations

#### Storage Format

- Files stored in:
  - `.git/objects/[first 2 hex digits]/[remaining 38 hex digits]`
- This prevents slow O(n²) operations in large directories
- Provides 256 top-level directories (1/256 entries)

## Git Object Types

### Simplest Object Path:

**Blob → Tree → Commit**

- **Blob** – Sequence of bytes
- **Tree** – Directory structure
  - Maps names to IDs
  - `[MODE] [TYPE] [ID] [NAME]`
- **Commit** – References tree and has metadata

### Example:

```bash
echo 'Arma Virumque cano' | git hash-object --stdin
```

- Does **not** write to repo

```bash
echo 'Arma Virumque cano' | git hash-object --stdin -w
```

- Writes to repo and creates an object

## Git Compression

### Why?

- Save space in secondary storage
- Reduce memory access time (e.g. SRAM vs DRAM)

### Techniques

#### Huffman Coding

- Maps symbols → bit strings
- ASCII: 8 bits per char
- Uses symbol frequency (e.g. ' ' = 5000, 'E' = 352)
- Tree built from symbol weights: binary, left = 0, right = 1
- Issues:
  - Doesn’t scale well
  - Doesn’t consider repeated patterns
- Solution:
  - Send the frequency table to decoder
  - **Adaptive Huffman Coding**: One-pass, adjusts on the fly

#### Dictionary Compression

- Compress repeated sequences to short codes
- Example: "ABC" = `1101`
- Assign int to words in a dictionary and send ints
- Sliding window: use offset and length to encode substrings
- Recipient updates dictionary during decode

#### Order of Compression:

1. **Dictionary compression** first (detect patterns)
2. **Huffman encoding** second (optimize bit width)

## Git Bisecting and Debugging

### Binary Search to Find Bug Introductions

```bash
git bisect start
git bisect bad HEAD
git bisect good v27.0
```

### Walkthrough:

```bash
git checkout <some_commit>
make check
git bisect good
git bisect skip
```

### Or automate:

```bash
git bisect start HEAD v27.0
git bisect run make check
```

- Works best with linear history
- Can struggle with merges (non-tree graph)
- Examines “cutpoints” to divide search space

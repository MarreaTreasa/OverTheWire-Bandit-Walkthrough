# OverTheWire Bandit Walkthrough (Level 0 → Level 5)

A brief documentation of Bandit levels completed, including objectives, commands used, and key Linux concepts learned.

---

# Level 0 → Level 1

## Goal:
Log into the Bandit server using SSH.

## Command:
```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

## Key Concept:
- `ssh` = Secure Shell for remote login
- `-p 2220` = Specifies custom port

---

# Level 1

## Goal:

Find the password stored in `readme`.

## Commands:

```bash
ls
cat readme
```

## Key Concept:

- `ls` lists files
- `cat` displays file contents

---

# Level 2

## Goal:

Read password from a file named `-`.

## Commands:

```bash
ls
cat ./-
```

## Why `./` is used:

Linux treats `-` as a command option, not a filename.

### `./` means:

“Use the file in the current directory.”

Without `./`:

```bash
cat -
```

---

# Level 3

## Goal:

Read password from file:

```bash
--spaces in this filename--
```

## Command:

```bash
cat ./--spaces\ in\ this\ filename--
```

## Key Concepts:

### `./`

Prevents Linux from treating `--` as an option.

### `\`

Escapes spaces so Linux reads the full filename correctly.

---

# Level 4

## Goal:

Find password inside a hidden file in `inhere`.

## Commands:

```bash
cd inhere
ls -la
cat ...Hiding-From-You
```

## Key Concepts:

- `cd` = Change directory
- `ls -la` = Show all files, including hidden ones
- Hidden files begin with `.`

---

# Level 5

## Goal:

Find the only human-readable file among multiple files.

## Commands:

```bash
cd inhere
file ./-file*
cat ./-file07
```

## Key Concepts:

### `file`

Checks file type (ASCII text, binary, data, etc.)

### `*`

Wildcard matching all files beginning with `-file`

### `./`

Used because filenames beginning with `-` may be mistaken for command options.

### Process:

1. Identify readable file using `file`
2. Read correct file using `cat`

---

# Core Linux Concepts Learned

## Special Filenames:

### File named `-`

```bash
cat ./-
```

### File with spaces:

```bash
cat filename\ with\ spaces
```

### Hidden files:

```bash
ls -la
```

### Multiple matching files:

```bash
file ./-file*
```

---

# Level 6

## Goal:

The password for the next level is stored in a file somewhere under the `inhere` directory and has all of the following properties:

- Human-readable
- 1033 bytes in size
- Not executable

## Commands:

```bash
cd inhere
find . -type f -size 1033c ! -executable
cat ./maybehere07/.file2
```

## Key Concepts:

### `find`

Searches recursively through directories and subdirectories.

### `.`

Search from the current directory.

### `-type f`

Limits results to regular files only.

### `-size 1033c`

Finds files exactly 1033 bytes in size.

### `! -executable`

Excludes executable files.

### Process:

1. Search all directories under `inhere`
2. Filter by file size
3. Exclude executable files
4. Read matching file

---

# Level 7

## Goal:

The password for the next level is stored somewhere on the server and has all of the following properties:

- Owned by user `bandit7`
- Owned by group `bandit6`
- 33 bytes in size

## Commands:

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
cat /var/lib/dpkg/info/bandit7.password
```

## Key Concepts:

### `/`

Search entire filesystem.

### `-user`

Filters files by owner.

### `-group`

Filters files by group ownership.

### `-size 33c`

Finds files exactly 33 bytes in size.

### `2>/dev/null`

Suppresses permission denied errors.

### Process:

1. Search entire system
2. Match owner and group
3. Match file size
4. Hide permission errors
5. Read correct file

---

# Level 8

## Goal:

The password for the next level is stored in the file `data.txt` next to the word `millionth`.

## Commands:

```bash
grep "millionth" data.txt
```

## Key Concepts:

### `grep`

Searches for lines containing specific text.

### `"millionth"`

Target search word.

### Process:

1. Search file for matching keyword
2. Display full line
3. Read password next to keyword

---

# Level 9

## Goal:

The password for the next level is stored in the only line of `data.txt` that occurs exactly once.

## Commands:

```bash
sort data.txt | uniq -u
```

## Key Concepts:

### `sort`

Groups identical lines together.

### `uniq -u`

Displays only unique lines (appearing once).

### `|` (Pipe)

Passes output from one command into another.

### Process:

1. Sort file contents
2. Group duplicate lines
3. Display only the unique line

---

# Core Linux Concepts Learned

## Special Filenames:

### File named `-`

```bash
cat ./-
```

### File with spaces:

```bash
cat filename\ with\ spaces
```

### Hidden files:

```bash
ls -la
```

### Multiple matching files:

```bash
file ./-file*
```

---

## Search Commands:

### Recursive search:

```bash
find .
```

### Search by size:

```bash
find . -size 1033c
```

### Search by owner/group:

```bash
find / -user username -group groupname
```

### Suppress errors:

```bash
2>/dev/null
```

---

## Text Processing:

### Find specific word:

```bash
grep "word" filename
```

### Find unique line:

```bash
sort filename | uniq -u
```

---

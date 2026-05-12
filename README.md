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


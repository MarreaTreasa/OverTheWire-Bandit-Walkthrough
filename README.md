# 🏴‍☠️ OverTheWire: Bandit Walkthrough

> My notes as I work through the Bandit wargame. Writing this mostly for myself so I don't forget stuff, but hopefully it helps someone else too.
>
> If you're here — don't copy-paste blindly. Actually try to understand what's happening. That's the whole point of Bandit.

---

## Table of Contents

- [Level 0 → 1](#level-0--1)
- [Level 1 → 2](#level-1--2)
- [Level 2 → 3](#level-2--3)
- [Level 3 → 4](#level-3--4)
- [Level 4 → 5](#level-4--5)
- [Level 5 → 6](#level-5--6)
- [Level 6 → 7](#level-6--7)
- [Level 7 → 8](#level-7--8)
- [Level 8 → 9](#level-8--9)
- [Level 9 → 10](#level-9--10)

---

## Level 0 → 1

Goal: Just log in via SSH.

```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

Password is `bandit0`. Simple enough.

The `-p 2220` flag is just because they're not running SSH on the default port 22. You'll see this a lot in CTF-style challenges.

---

## Level 1 → 2

Goal: Password is in a file called `readme`.

```bash
ls
cat readme
```

Nothing tricky here. `ls` to see what's there, `cat` to read it.

---

## Level 2 → 3

Goal: Password is in a file literally named `-`.

```bash
cat ./-
```

This one trips people up. If you just do `cat -`, bash thinks you're telling it to read from stdin (keyboard input) rather than a file. The `./` prefix forces it to treat `-` as a filename in the current directory.

```bash
# This doesn't work
cat -

# This works
cat ./-
```

---

## Level 3 → 4

Goal: Password is in a file called `spaces in this filename`.

```bash
cat "spaces in this filename"
# or
cat spaces\ in\ this\ filename
```

Two ways to handle spaces in filenames — quotes or backslash-escaping each space. I prefer quotes, feels cleaner.

> **Tip:** Tab completion is your best friend here. Start typing the filename and hit Tab — the shell handles the escaping for you automatically.

---

## Level 4 → 5

Goal: Password is in a hidden file inside the `inhere` directory.

```bash
cd inhere
ls -la
cat ...Hiding-From-You
```

Hidden files in Linux start with a `.` — `ls` won't show them by default. The `-a` flag reveals them, and `-l` gives you the detailed view. I usually just always use `ls -la`.

---

## Level 5 → 6

Goal: Only one of the files in `inhere` is human-readable. Find it.

```bash
cd inhere
file ./-file*
```

Output looks something like:

```
./-file00: data
./-file01: data
...
./-file07: ASCII text
...
```

```bash
cat ./-file07
```

The `file` command checks what type of data is actually in a file — binary, text, image, etc. Useful when you're guessing blind. The `*` wildcard matches all filenames starting with `-file`.

The `./` prefix is needed again because filenames starting with `-` look like flags.

---

## Level 6 → 7

Goal: Somewhere under `inhere`, find a file that is human-readable, exactly 1033 bytes, and not executable.

```bash
cd inhere
find . -type f -size 1033c ! -executable
```

Output:

```
./maybehere07/.file2
```

```bash
cat ./maybehere07/.file2
```

Breaking down the `find` command:

- `.` — start searching from current directory
- `-type f` — only regular files (not directories, symlinks, etc.)
- `-size 1033c` — exactly 1033 bytes (`c` = bytes)
- `! -executable` — exclude executable files

`find` is one of those commands that feels overwhelming at first but becomes second nature. This level is a good intro to it.

---

## Level 7 → 8

Goal: File is somewhere on the entire server. Owned by user `bandit7`, group `bandit6`, 33 bytes.

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

Output:

```
/var/lib/dpkg/info/bandit7.password
```

```bash
cat /var/lib/dpkg/info/bandit7.password
```

The `2>/dev/null` part is important here — searching from `/` will throw a ton of "Permission denied" errors that flood your output. Redirecting stderr (`2>`) to `/dev/null` silences them so you can actually see results.

- `-user bandit7` — owned by this user
- `-group bandit6` — owned by this group

---

## Level 8 → 9

Goal: Password is in `data.txt`, on the line next to the word "millionth".

```bash
grep "millionth" data.txt
```

`grep` searches through a file line by line and prints any line that contains your search string. Dead simple here, but grep is incredibly powerful — worth learning its flags (`-i`, `-r`, `-n`, `-v`, etc.).

---

## Level 9 → 10

Goal: Password is the only line in `data.txt` that appears exactly once.

```bash
sort data.txt | uniq -u
```

`sort` rearranges the lines alphabetically, which groups identical lines together. Then `uniq -u` filters down to only lines that appear exactly once (unique lines).

The `|` pipe is what connects them — output from `sort` goes directly into `uniq`. This is a core Linux pattern and you'll use it constantly.

---

_Still going — will keep updating as I progress through more levels._

# 🏴‍☠️ OverTheWire: Bandit Walkthrough

> My notes from going through Bandit. Mostly for myself, but feel free to follow along.
> Don't just copy paste. Actually read what the commands do.

## Table of Contents

- [Level 0 to 1](#level-0-to-1)
- [Level 1 to 2](#level-1-to-2)
- [Level 2 to 3](#level-2-to-3)
- [Level 3 to 4](#level-3-to-4)
- [Level 4 to 5](#level-4-to-5)
- [Level 5 to 6](#level-5-to-6)
- [Level 6 to 7](#level-6-to-7)
- [Level 7 to 8](#level-7-to-8)
- [Level 8 to 9](#level-8-to-9)
- [Level 9 to 10](#level-9-to-10)
- [Level 10 to 11](#level-10-to-11)
- [Level 11 to 12](#level-11-to-12)
- [Level 12 to 13](#level-12-to-13)
- [Level 13 to 14](#level-13-to-14)
- [Level 14 to 15](#level-14-to-15)
- [Level 15 to 16](#level-15-to-16)

---

## Level 0 to 1

Goal: Log in via SSH.

```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

Password is `bandit0`. The `-p 2220` is just because they use a non-default port.

---

## Level 1 to 2

Goal: Password is in a file called `readme`.

```bash
ls
cat readme
```

Nothing tricky. `ls` to look around, `cat` to read.

---

## Level 2 to 3

Goal: Password is in a file literally named `-`.

```bash
cat ./-
```

If you try `cat -`, bash thinks you want to read from keyboard input. Adding `./` tells it to treat `-` as an actual filename.

---

## Level 3 to 4

Goal: Password is in a file called `spaces in this filename`.

```bash
cat "spaces in this filename"
```

Quotes handle the spaces. Tab completion works here too, the shell handles escaping automatically.

---

## Level 4 to 5

Goal: Password is in a hidden file inside `inhere`.

```bash
cd inhere
ls -la
cat ...Hiding-From-You
```

Hidden files start with a `.` and won't show up with plain `ls`. The `-a` flag reveals them. I just always use `ls -la`.

---

## Level 5 to 6

Goal: One file in `inhere` is human-readable. Find it.

```bash
cd inhere
file ./-file*
```

Output looks like:

```
./-file00: data
./-file01: data
./-file07: ASCII text
```

```bash
cat ./-file07
```

`file` checks what is actually inside a file. The `*` wildcard matches everything starting with `-file`. The `./` is needed because filenames starting with `-` look like flags.

---

## Level 6 to 7

Goal: Find a file under `inhere` that is human-readable, exactly 1033 bytes, and not executable.

```bash
cd inhere
find . -type f -size 1033c ! -executable
cat ./maybehere07/.file2
```

Quick breakdown of the `find` flags:

- `-type f` means regular files only
- `-size 1033c` means exactly 1033 bytes
- `! -executable` skips executable files

`find` feels like a lot at first but you end up using it all the time.

---

## Level 7 to 8

Goal: File is somewhere on the server. Owned by user `bandit7`, group `bandit6`, 33 bytes in size.

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
cat /var/lib/dpkg/info/bandit7.password
```

Searching from `/` throws a flood of "Permission denied" errors. The `2>/dev/null` part redirects all that noise away so you can actually see the result.

---

## Level 8 to 9

Goal: Password is in `data.txt`, on the line next to the word "millionth".

```bash
grep "millionth" data.txt
```

`grep` scans every line and prints ones that match. Simple level but grep is worth knowing well.

---

## Level 9 to 10

Goal: Password is the only line in `data.txt` that appears exactly once.

```bash
sort data.txt | uniq -u
```

`sort` groups identical lines together, then `uniq -u` keeps only the ones that appear once. The pipe `|` connects the two, output of one feeds into the other. You will use this pattern constantly.

---

## Level 10 to 11

Goal: Password is in `data.txt`, hidden among binary junk, preceded by several `=` signs.

```bash
strings data.txt | grep "=="
```

Running `file data.txt` shows it is generic binary data, not normal text. `strings` pulls out any readable ASCII from inside a binary file. Then `grep "=="` narrows it down to the line with the password.

Output looks like:

```
========== password_here
```

When a file looks like garbage, inspect it before assuming anything.

---

## Level 11 to 12

Goal: Password is in `data.txt`, encoded in Base64.

```bash
base64 -d data.txt
```

Base64 is encoding, not encryption. It just converts data into printable characters. Decoding it is one command. If you get `invalid input`, there is probably extra whitespace or the file is not actually Base64.

---

## Level 12 to 13

Goal: `data.txt` is a hexdump of a file that has been compressed multiple times.

This one takes a few steps. You are peeling through layers of compression one at a time.

**Set up a temp folder**

```bash
cd $(mktemp -d)
cp ~/data.txt .
```

You need write permissions to rename and extract files, so working in `/tmp` is easier.

**Reverse the hexdump**

```bash
xxd -r data.txt > file
```

`data.txt` is not the real file, it is a hex representation of one. `xxd -r` converts it back to binary.

**Identify and extract, repeat until done**

```bash
file file
```

Based on the output, extract it:

```bash
# gzip
mv file file.gz && gunzip file.gz

# bzip2
mv file file.bz2 && bunzip2 file.bz2

# tar
tar -xf file
```

After each step run `file *` and `ls` to see what you have. Keep going until you get ASCII text. The chain can go pretty deep.

---

## Level 13 to 14

Goal: No password this time. You get a private SSH key to log into the next level.

```bash
ls -la
# sshkey.private
```

**Fix the permissions first**

```bash
chmod 600 sshkey.private
```

SSH refuses to use a key file if others can read it. You will get a "bad permissions" error if you skip this.

**Log in with the key**

```bash
ssh -i sshkey.private -p 2220 bandit14@bandit.labs.overthewire.org
```

Or copy it to your local machine first if you prefer:

```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .
chmod 600 sshkey.private
ssh -i sshkey.private -p 2220 bandit14@bandit.labs.overthewire.org
```

Most real servers use key-based auth. Good level to get comfortable with it.

---

## Level 14 to 15

Goal: Submit your current password to a service on port 30000 to get the next one.

```bash
cat /etc/bandit_pass/bandit14 | nc localhost 30000
```

`nc` (netcat) opens a raw TCP connection. You send data in, you get data back. First time Bandit gets into network territory.

---

## Level 15 to 16

Goal: Same as before but port 30001 requires SSL/TLS, so plain `nc` won't work.

```bash
echo "YOUR_PASSWORD" | openssl s_client -connect localhost:30001 -quiet -ign_eof
```

`openssl s_client` is basically netcat with encryption. `-quiet` hides the handshake output. `-ign_eof` keeps the connection alive long enough to get the reply.

If you want to do it interactively:

```bash
openssl s_client -connect localhost:30001 -quiet
```

Then paste the password and press `Ctrl + D`.

---

_Still going, will update as I get further._

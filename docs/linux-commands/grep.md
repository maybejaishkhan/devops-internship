> `grep` --> searches input (files or output) line by line for patterns (regex) and prints matching lines. It’s powerful for filtering logs, configs, command output, etc.

```bash
grep "<pattern>" <file> # Find lines with "pattern" in file.
grep "<pattern>" <file1> <file2> ... # Search multiple files.
```

| Flag         | What it does                           | Example                       |
| ------------ | -------------------------------------- | ----------------------------- |
| `-i`         | Ignore upper/lowercase                 | `grep -i "hello" file`        |
| `-v`         | Show lines not matching (invert).      | `grep -v "error" log.txt`     |
| `-r` or `-R` | Recursively search directories.        | `grep -r "TODO" .`            |
| `-n`         | Show line numbers                      | `grep -n "foo" file`          |
| `-c`         | Count matches per file                 | `grep -c "error" log.txt`     |
| `-l`         | Show only filenames with matches       | `grep -l "pattern" *.txt`     |
| `-L`         | Show only filenames without matches    | `grep -L "pattern" *.txt`     |
| `-w`         | Match whole words only                 | `grep -w "cat" file`          |
| `-x`         | Match whole lines only                 | `grep -x "hello world" file`  |
| `-A NUM`     | Show NUM lines After match             | `grep -A 2 "error" log.txt`   |
| `-B NUM`     | Show NUM lines Before match            | `grep -B 2 "error" log.txt`   |
| `-C NUM`     | Show NUM lines Before & After match    | `grep -C 2 "error" log.txt`   |
| `-E`         | Use extended regex (same as `egrep`)   | `grep -E "foo\|bar" file.txt` |
| `-F`         | Interpret pattern literally (no regex) | `grep -F "1.2.3.*" file.txt`  |
It uses "Basic Regex" which means that symbols like `|` , `+`, `?`, `{}` have to be escaped like `\|`, `\+`, `\?`, `\{}`.  
- With -E (Extended Regex), you don’t need the backslashes — so you can write regex like `foo|bar` instead of `foo\|bar`.
- We can also use -F (No Regex), if we want to search for symbols literally.
- `egrep` and `fgrep` are basically the same thing but are **deprecated**.

```bash
# Search dmesg for USB, ignore case
dmesg | grep -i usb

# Check if a service is running
ps aux | grep nginx

# Find TODOs in code, show line numbers and context
grep -rnC 2 "TODO" .

# Show all config lines not commented out
grep -v "^#" config.conf

# Multiple patterns (extended regex)
grep -E "foo|bar|baz" file.txt
```

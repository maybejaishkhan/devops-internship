> `sed` --> Edits text line-by-line as it streams through. Used for find & replace, insert, delete, print, multi-edit, etc.

It has **flags** (used for the script behaviour), **commands** (what sed does line-by-line)  and **substitution modifiers** (used for `s///` command).

| Flag        | What it means                 |
| ----------- | ----------------------------- |
| `-e`        | Add multiple editing commands |
| `-f`        | Read commands from a file     |
| `-i`        | Edit the file in-place        |
| `-n`        | Suppress default output       |
| `--version` | Show version info             |
| `--help`    | Show help                     |

| Command | What it does                     |
| ------- | -------------------------------- |
| `s`     | Substitution (find & replace)    |
| `p`     | Print                            |
| `d`     | Delete line                      |
| `a`     | Append text after line           |
| `i`     | Insert text before line          |
| `c`     | Change whole line                |
| `y`     | Transform characters (like `tr`) |
| `=`     | Print line number                |
| `{}`    | Group multiple commands          |

| Modifier | Meaning                                         |
| -------- | ----------------------------------------------- |
| `g`      | Global (all matches per line)                   |
| `1`      | Only the first occurrence (same as default) |
| `2`      | Only the second occurrence                  |
| `3`      | Only the third, etc.                        |
| `p`      | Print the line if a substitution was made       |
| `I`      | Ignore case (GNU sed only)                      |

```bash
# Replace first match per line
sed 's/foo/bar/' file.txt

# Replace all matches per line
sed 's/foo/bar/g' file.txt

# Replace second match per line
sed 's/foo/bar/2' file.txt

# Replace all, ignore case (GNU sed)
sed 's/foo/bar/gI' file.txt

# Replace and print only if changed
sed -n 's/foo/bar/p' file.txt

# Replace in-place
sed -i 's/foo/bar/g' file.txt

# Delete specific line (e.g., 2nd)
sed '2d' file.txt

# Delete lines matching pattern
sed '/pattern/d' file.txt

# Delete a range of lines (2nd to 4th)
sed '2,4d' file.txt

# Print range of lines (1 to 5)
sed -n '1,5p' file.txt

# Print lines matching pattern
sed -n '/foo/p' file.txt

# Print line numbers
sed '=' file.txt

# Insert text BEFORE line 3
sed '3i\
This is a new line' file.txt

# Append text AFTER line 3
sed '3a\
This is appended' file.txt

# Replace entire line 4
sed '4c\
This is the new line' file.txt

# Use multiple commands with -e
sed -e 's/foo/bar/g' -e '/baz/d' file.txt

# Group commands with {}
sed '{s/foo/bar/; /^$/d}' file.txt

# Replace chars a->b and c->d
echo "abc" | sed 'y/ac/bd/'
```

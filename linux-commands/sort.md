> `sort` --> sorts lines in text files (alphabetically, numerically, by column, reverse, etc.)

|Flag|Meaning|
|---|---|
|`-n`|Numeric sort (e.g. `2` comes before `10`)|
|`-r`|Reverse order|
|`-k`|Sort by a specific column|
|`-u`|Unique: remove duplicates|
|`-t`|Specify delimiter (default: whitespace)|

```bash
# Sort alphabetically (default)
sort file.txt

# Sort numerically
sort -n numbers.txt

# Sort numerically, reverse order
sort -nr numbers.txt

# Sort by 2nd column (delimiter = comma)
sort -t ',' -k 2 file.csv

# Remove duplicates
sort -u file.txt
```

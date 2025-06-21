> `awk` --> read input line-by-line, split into fields, process them using patterns & actions.

|Symbol|Meaning|
|---|---|
|`$1`|1st field|
|`$2`|2nd field|
|`$0`|Entire line|
|`FS`|Field separator|
|`print`|Output|

```bash
# Print the 1st column
awk '{print $1}' file.txt

# Print lines where 3rd column > 100
awk '$3 > 100' file.txt

# Use comma as separator, print 1st and 3rd columns
awk -F ',' '{print $1, $3}' file.csv

# Sum 2nd column
awk '{sum += $2} END {print sum}' file.txt

# Replace 'foo' with 'bar' in 2nd column
awk '{$2 = "bar"; print}' file.txt
```

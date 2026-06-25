# sed (Stream Editor)

It reads input line by line and can:

- Replace text
- Delete lines
- Insert lines
- Print selected lines
- Modify files automatically

## Replace
```bash
# Replace first
sed 's/old/new/' file

# Replace all
sed 's/old/new/g'

# Case-insensitive replace
sed 's/error/warning/i'
```

## Delete
```bash
# Deletes line number 3
sed '3d' file

# Deletes lines 2-5
sed '2,5d' file

# Delete empty lines
sed '/^$/d' file
```

## Print
```bash
# prints every line
sed -n file

# Print specific line
sed -n '5p' file

# Print lines 10-20
Print lines 10-20
```

## Modify files
```bash
# In-place Editing
sed -i 's/old/new/g' file

# Insert before line
sed '3i\
NEW LINE
' file

# Append after line
sed '3a\
NEW LINE
' file
```

## Useful Linux Admin Examples
```bash
# Remove comments and blanks
sed '/^#/d;/^$/d' file.conf

# Backup before editing
sed -i.bak 's/old/new/g' file

# Change SSH port
sed -i 's/^Port 22$/Port 2222/' /etc/ssh/sshd_config

# Convert Windows files to Linux format
sed -i 's/\r$//' file.txt

# Remove taling spaces
sed 's/[[:space:]]*$//'

# Replace tabs with spaces. Helpful when fixing YAML indentation.
sed 's/\t/    /g'
```

# awk

awk is a text-processing language designed for:

- Extracting columns
- Filtering rows
- Calculations
- Reports
- Log analysis

syntax:
```bash
awk 'pattern { action }' file
```

| Variable | Meaning                      |
| -------- | ---------------------------- |
| `$1`     | First field                  |
| `$2`     | Second field                 |
| `$NF`    | Last field                   |
| `$0`     | Entire line                  |
| `NR`     | Current record (line number) |
| `NF`     | Number of fields in line     |
| `$(NF-1)`| Last -1 field                |

## some basic examples:

```bash
# ':' as delimiter
awk -F: '{print $1}' /etc/passwd

# Print entire line with line number
awk '{print NR, $0}' test.txt

# Show lines containing "error"
awk '/error/'

# Count matching lines
awk '/error/ {count++} END {print count}'

# Lines ending with ".com"
awk '/\.com$/ {print}' emails.txt

# begin and end
awk '
BEGIN {print "Start"}
{print $1}
END {print "Done"}
' file

# Sum the values in the first column and print the total:
awk '{s+=$1} END {print s}' filename

# Print line 3 only
awk 'NR==3 {print}' file.txt

# Print lines 5-10
awk 'NR>=5 && NR<=10 {print}' file.txt

# Filter with regular expressions
awk '$1 ~ /^[0-9]+$/ && $2 !~ /admin/' users.txt
```

## if-else in awk:
```bash
# Simple if
awk '{if ($2 > 100) print $1, "is high"}' data.txt

# If-Else
awk '{if ($2 > 100) print "High"; else print "Low"}' data.txt

# Multiple conditions
awk '{if ($2 > 100 && $3 == "active") print $1}' data.txt

# Nested if
awk '{
    if ($2 > 100) {
        if ($3 > 50) print "Very high"
        else print "Medium high"
    }
}' data.txt
```

## Field Separators
```bash
# CSV files (comma separated)
awk -F',' '{print $2, $3}' data.csv

# Tab separated
awk -F'\t' '{print $1, $2}' data.tsv

# Colon separated (like /etc/passwd)
awk -F':' '{print $1, $6}' /etc/passwd

# Multiple separators
awk -F'[,;:]' '{print $1, $2}' data.txt

# Using FS variable inside script
awk 'BEGIN {FS=","} {print $2}' data.csv
```

## Output Field Separator
```bash
# Change output separator
awk 'BEGIN {OFS="|"} {print $1, $2, $3}' data.txt

# Combine separators
awk -F',' 'BEGIN {OFS=":"} {print $1, $2}' data.csv
```

## AWK Arrays

loop in arrays:
```bash
# make Array and loop in. Output order is NOT guaranteed!
awk 'BEGIN {
    arr["Apple"] = 5
    arr["Banana"] = 3
    arr["Cherry"] = 7
    arr["Date"] = 2
    
    # Loop through all keys
    for(key in arr) {
        print key, arr[key]
    }
}'

# Count word frequencies
echo "apple banana apple cherry banana apple" | \
awk '{for(i=1; i<=NF; i++) count[$i]++} 
     END {for(word in count) print word, count[word]}'
```

## AWK Substring 
```bash
substr(string, start_position, length)
substr(string, start_position)  # If length omitted, goes to end
```

```bash
# Basic extraction
awk 'BEGIN {
    text = "Hello World"
    print substr(text, 1, 5)    # "Hello" (positions 1-5)
    print substr(text, 4)       # "lo World" (position 4 to end)
    print substr(text, 8, 3)    # "orl"
}'

# Extract Date
echo "2026-06-25 15:30:45" | awk '{
    year = substr($1, 1, 4)      # "2026"
    month = substr($1, 6, 2)     # "06"
    day = substr($1, 9, 2)       # "25"
    print "Year:", year, "Month:", month, "Day:", day
}'

# Extract with position index
```


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
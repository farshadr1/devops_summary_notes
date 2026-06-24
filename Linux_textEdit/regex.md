# Regular Expression in Linux

## Common Regex Characters

| Character | Description |
|-----------|-------------|
| `.` | Any single character (except newline in most regex engines) |
| `\` | Escapes a special character. Example: `\.` matches a literal dot |
| `?` | Previous character is optional. Example: `hell?o` → `hello` and `helo` |
| `*` | Zero or more of the previous character. Example: `a*` → `""`, `a`, `aa`, `aaa` |
| `+` | One or more of the previous character. Example: `a+` → `a`, `aa`, `aaa` |
| `|` | OR operator. Example: `hello|world` |
| `()` | Group expressions. Example: `(ab)+` → `ab`, `abab`, `ababab` |
| `{n}` | Quantifier. Example: `a{3}` → `aaa` |
| `{n,m}` | Between n and m times. `a{2,4}` → `aa`, `aaa`, `aaaa` |
| `[]` | Match any character in this set. `-` defines ranges (e.g. `[a-z]` is any lowercase letter), `^` means “not” (e.g. `[^,]+` match any number of non-commas in a row) |
| `^` | Start of line (outside `[]`)|
| `^$` | End of line |

## Character Classes
| Pattern | Meaning |
| ------- | ------- |
| `\d`    | Digit (`0-9`) |
| `\D`    | Not a digit |
| `\w`    | Word character (`a-z`, `A-Z`, `0-9`, `_`) |
| `\W`    | Not a word character |
| `\s`    | Whitespace (space, tab, newline) |
| `\S`    | Not whitespace |

## Examples:

Email-like pattern: `\w+@\w+\.\w+` → user@example.com admin@test.org

Phone number: `\d{3}-\d{3}-\d{4}` → 123-456-7890

Only lowercase letters : `^[a-z]+$`

IP addresses: `[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+`

Usernames: `^[a-z][a-z0-9_]*$`

Find versions : `:[0-9.]+` → :1.27 , :8.0 , :17

Any characters, zero or more : `.*`

Any Errors : `ERROR:.*` → ERROR: disk full , ERROR: cannot connect , ERROR: timeout after 30s

Zero or one character : `.?` → "" , a , b , 1 , @

Empty lines : `^$`

| Pattern | Meaning                  |
| ------- | ------------------------ |
| `.`     | Any one character        |
| `.*`    | Any number of characters |
| `.+`    | One or more characters   |
| `.?`    | Zero or one character    |
| `a*`    | Zero or more `a`         |
| `a+`    | One or more `a`          |
| `a?`    | Optional `a`             |

## Common POSIX character classes

| Class         | Meaning          | Equivalent          |
| ------------- | ---------------- | ------------------- |
| `[[:digit:]]` | Digit            | `[0-9]`             |
| `[[:alpha:]]` | Letter           | `[a-zA-Z]`          |
| `[[:alnum:]]` | Letter or digit  | `[a-zA-Z0-9]`       |
| `[[:lower:]]` | Lowercase letter | `[a-z]`             |
| `[[:upper:]]` | Uppercase letter | `[A-Z]`             |
| `[[:space:]]` | Whitespace       | space, tab, newline |
| `[[:punct:]]` | Punctuation      | `. , ; : ! ? ...`   |

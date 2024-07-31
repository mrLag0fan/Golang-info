##### **Skills**
 - Works with numeric types to store and reuse respective information
 - Works with strings using operators like _+, +=, `==`, !=_ and built-in _len()_ function, coverts them to []byte and []rune
- Using T(v) expression for type conversions (strings, numeric)
- Works with types conventions methods (e.g: strconv.Atoi, strconv.Itoa, strconv.ParseBool, etc.)
- Uses the subslice syntax aString[start:end] to get a substring of a string
- Uses boolean type depends on use-cases
## What is the difference between string and []byte?
### String
A string is an immutable sequence of bytes. Strings may contain arbitrar y data, including bytes with value 0, but usually the y contain human-readable text. Text strings are conventionally interpreted as UTF-8-encoded sequences of Unicode code points (runes).
The built-in **`len`** function returns the number of bytes (not runes) in a string , and the index operation `s[i]` retrieves the i-th byte of string, where 0 ≤ i < len(s).
```go
s := "hello, world"
fmt.Println(len(s)) // "12" 
fmt.Println(s[0], s[7]) // "104 119" ('h' and 'w')
```
The substring operation `s[i:j]` yields a new string consisting of the bytes of the original string starting at index i and continuing up to, but not including, the byte at index j. The result contains 
j-i bytes. 
```go
fmt.Println(s[0:5]) // "hello"
```
Either or both of the i and j operands may be omitted, in which case the default values of 0 (the start of the string) and len(s) (its end) are assumed, respectively. 
```go
fmt.Println(s[:5]) // "hello"
fmt.Println(s[7:]) // "world"
fmt.Println(s[:]) // "hello, world"
```
Strings may be compared with comparison operator s like == and <; the comparison is done byte by byte, so the result is the natural lexicographic ordering.
String values are immutable: the byte sequence contained in a string value can never be changed, though of course we can assign a new value to a string variable. To append one string to another, for instance, we can write 
```go
s := "left foot" 
t := s 
s += ", right foot"
``` 
This does not modify the string that s originally held but causess to hold the new string formed by the += statement; meanwhile, t still contains the old string .
```go
fmt.Println(s) // "left foot, right foot" 
fmt.Println(t) // "left foot"
```
Immutability means that it is safe for two copies of a string to share the same underlying memory, making it cheap to copy strings of any length. Similarly, a strings and a substring like `s[7:]` may safely share the same data, so the substring operation is also cheap. No new memory is allocated in either case.
### []byte
A string contains an array of bytes that, once created, is immutable. By contrast, the elements of a byte slice can be freely modified.
Strings can be converted to byte slices and back again:
```go
s := "abc" 
b := []byte(s) 
s2 := string(b)
```
Conceptually, the []byte(s) conversion allocates a new byte array holding a copy of the bytes of s, and yields a slice that references the entirety of that array. An optimizing compiler may be able to avoid the allocation and copying in some cases, but in general copying is required to ensure that the bytes of s remain unchanged even if those of b are subsequently modified. The conversion from byte slice back to string with string(b) also makes a copy, to ensure immutability of the resulting string s2.
## How to get the numbers of characters in a string?
```go
import "unicode/utf8"

s := "Hello, 😁😁"
fmt.Println(len(s)) // "13" 
fmt.Println(utf8.RuneCountInString(s)) // "9"
```
## How to use escape sequences (ex. '`\n`')?
Within a double-quoted string literal, escape sequences that begin with a backslash \ can be used to insert arbitrar y byte values into the string . One set of escapes handles ASCII control codes like newline, carriage return, and tab:
`\a` ‘‘alert’’ or bell
`\b` backspace
`\f` form feed 
`\n` newline
`\r` carriage return
`\t` tab
`\v` vertical tab
`\'` single quote (only in the rune literal `'\''`)
`\"` double quote (only within "..." literals) 
`\\` backslash
## What is the integer overflow?
An integer overflow occurs when a value does not fit within its allocated memory space. This can occur for a variety of reasons, but an integer overflow should almost always be handled properly since they can cause unexpected or incorrect behavior and can pose serious security risks for a program.
## What are zero values for strings, ints, and boolean?
The zero value is:
- `0` for numeric types,
- `false` for the boolean type, and
- `""` (the empty string) for strings.
## What are the basic types for rune and byte in Go?
Golang has integer types called **byte** and **rune** that are aliases for **uint8** and **int32** data types, respectively.
The **byte** data type represents **ASCII** characters while the **rune** data type represents a more broader set of **Unicode** characters that are encoded in UTF-8 format.
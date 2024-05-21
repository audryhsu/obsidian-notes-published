---
creation date: 2022-03-20 08:20
aliases: 
tags: 🖥️
---

# [Go Lang Strings](Go%20Lang%20Strings.md)
---
A string is a *read only slice of bytes*. Strings are treated like containers of text encoded in UTF-8.
- Indexing into strings will return bytes. 
- Strings are made of `rune`s, which is an integer that represents a [Unicode](./Unicode.md) code point.
- The easiest way to work with strings is to convert to `byte` arrays and back.

To summarize, here are the salient points:
-   Go source code is always UTF-8 aka encodes Unicode code points to/from bits.
-   A string holds arbitrary *bytes*, which must be encoded/decoded.
-   A string literal always holds valid UTF-8 sequences.
-   Those sequences represent Unicode code points, called `runes`.

#### Strings are Byte Arrays, Characters are Runes
- To manipulate string characters, treat it like a slice (of bytes)!
- `"my string"` double quotes represent a string literal
- `'a'` single quotes represent a rune literal. We can compare a `rune` value to a rune literal using comparison operators.
	- Rune literals are ONE Unicode code point (uint8)
- Indexing into strings return type `int`s, because they are just bytes. 
- Iterating over a string using `range` will give you `runes`, not bytes. [💻](./Go%20Lang%20Snippets.md#Strings%20and%20Byte%20Arrays)

#### String Type Conversions using strconv
Basic conversion assumes decimal and `into` type:
- `strconv.Itoa(int)` - converts into to ASCII
- `strconv.Atoi(s string)` - converts ASCII to int

Convert from X to a string:
```go
s := strconv.FormatBool(true)
s := strconv.FormatFloat(3.1415, 'E', -1, 64)
s := strconv.FormatInt(-42, 16)
s := strconv.FormatUint(42, 16)
```
Convert from a string to X:
```go
b, err := strconv.ParseBool("true")
f, err := strconv.ParseFloat("3.1415", 64)
i, err := strconv.ParseInt("-42", 10, 64)
u, err := strconv.ParseUint("42", 10, 64)
```

#### String Methods from Strings package
- `strings.Split(string, sep) []string` - splits string into substrings separated by `sep` and returns a slice
- `strings.Fields(s string) []string` - splits string around each instance of one or more conesecutive white space characters, and returns a slice of substrings. 
	- If string only contains whitespace, returns empty slice
- `strings.Repeat(s string, count int) string` - repeats a string `count` number of times.
- `strings.Replace(s, old, new, n int) string` - returns a *copy* of the string s with the first `n` non-overlapping instances of `old` replaced by `new`
	- `n` represents number of replacements  -- if n < 0 , no limit on number of replaces; Alternatively, use `ReplaceAll` here.
- `strings.NewReplacer(old, new,...) *Replacer` - returns a new Replacer, which replaces a list of old, new string pairs. 
	- This lets us replace more than one pair of strings instead of calling `strings.Replace` multiple times
- `strings.Count(string, substr) int` - counts the number of non-overlapping instances of substring in a string

🔗 [String Functions](https://gobyexample.com/string-functions)

### `fmt.Printf` Format Verbs
- %v, which will be replaced with the general string representation of the corresponding argument.
- %T, which will be replaced with the type name or type literal of the corresponding argument.
- %x, which will be replaced with the hex string representation of the corresponding argument. Note, the hex string representations for values of some kinds of types are not defined. Generally, the corresponding arguments of %x should be strings, integers, integer arrays or integer slices (arrays and slices will be explained in a later article).
- %s, which will be replaced with the string representation of the corresponding argument. The corresponding argument should be a string or byte slice.
- Format verb `%%` represents a percent sign.

# Examples

Looping over characters in a string
```go
for i, c := range "go" {
	fmt.Printf("index: %d, rune: %d, character: %s \n", i, c, string(c))
}
//index: 0, rune: 103, character: g 
//index: 1, rune: 111, character: o 
```
- using `range` on strings iterates over Unicode code points. The `i` represents starting byte index of the `rune` and `c` represents the `rune` itself.
- coerce rune into a string to return actual character

Remove first and last letter of a string
```go
// Using slices
func RemoveChar(word string) string {
  return word[1:len(word)-1] //
}
// Using string concatenation and for loop
func RemoveChar(word string) string {
	var str string
	for i := 1; i < len(word)-1; i++ {
		str += string(word[i])
	}
	return str
}

func main() {
	fmt.Println(RemoveChar("hello")) // "ello"
}
```
- Using a `for` loop to build a string, we need to convert the byte `int` that represents the character into a string to use string concatenation.

`strings.Fields` from `strings` package splits the string around each instance of one or more consecutive white space characters into a slice
```go
import (
	"fmt"
	"strings"
)
func main() {
	strings.Fields("  foo bar  baz   "))
	// [foo bar baz]
}
```
- 

---
Tags: [Programming](Programming.md) - [Go Lang](./Go%20Lang.md) 

Reference:

Related: 
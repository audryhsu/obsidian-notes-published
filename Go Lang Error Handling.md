---
creation date: 2022-03-20 07:55
aliases: 
tags: 🖥️
---

---
Go philosophy is that *errors are values*. In Go, the Error interface is very minimal, which allows developers to easily define their own custom error types. [Wrapping errors](Go%20Lang%20Error%20Handling.md#Wrapping%20errors) became so pervasive that explict support to unwrap errors was added after Go 1.13.

### Wrapping errors
Wrapping errors allows developers to add more context to the error where it happens, while keeping the underlying error available in the error chain if more context is needed for handling the error

💻  [Playground Code](https://goplay.tools/snippet/i6UVZEmCwsj)

> [!tip] Errors should not expose internal details
> When you expose internal details (like a third party package's error) to consuming code, you are committing in the API contract to always return that type of error. 
> 
> When writing functions that return wrapped errors, convert the error to another form, like a custom `apiError` type that provides a consistent interface. Force the consumers to use `errors.Is()` or `errors.As()` to unwrap the error instead of using strict equality checks.


#### to wrap or not to wrap?

We can wrap original errors by using `fmt.Errorf("error occured %w", err)` and the `%w` format verb that makes underlying error available to `errors.Is` and `errors.As`

If you don't want to wrap, instead use the `%v` format verb, which uses the error's string only. 

✅  When to wrap --> consuming code m ay need more information about the underlying error
* could be useful to provide at what line a read error occurred, e.g. in `io.Reader`
* still, you do not want to expose internal details to the user aka exposing sentinel errors directly --> maintain abstraction layer!
```go
var ErrPermission = errors.New("permission denied")

// return an error wrapping a sentinel error
func DoSomething() error {
    if !userHasPermission() {
        return fmt.Errorf("%w", ErrPermission)
    }
    // ...
}
```
If we return `ErrPermission` directly, callers might come to depend on the exact error value, writing code like this:
```go
// 👎🏼 we don't want consumers to depend on the error like this 
if err := pkg.DoSomething(); err == pkg.ErrPermission { … }

// This will cause problems if we want to add additional
// context to the error in the future. 
```
To avoid this, we return an error wrapping the sentinel so that users must always unwrap it and check if `pkg.ErrPermission` exists in the error chain:
```go
// 👍🏻 this is how we want users to interact with our error
if err := pkg.DoSomething(); errors.Is(err, pkg.ErrPermission) { ... }
```

❎ When not to wrap original error --> you don't want to expose the underlying error
* when you don't want to expose implementation details
* if you expose the underlying error to the consumer, then you committing in the api contract to always returning that type of error (because you can't make any assumptions that they *wont* write code depending on that error)
* example: if you return a specific mysql error, you are locked into not changing db drivers.

```go
f, err := os.Open(filename)
if err != nil {
    // The *os.PathError returned by os.Open is an internal detail.
    // To avoid exposing it to the caller, repackage it as a new
    // error with the same text. We use the %v formatting verb, since
    // %w would permit the caller to unwrap the original *os.PathError.
    return fmt.Errorf("%v", err)
}
```

### Error basics
Go uses have multiple return values and we can use multiple variable assignment to check for the value of `err`.
- By convention, errors are the *last return value* and are of type `error`, a built-in interface
- When a function has defined an error as one of the return values, make sure to `specify a return type of  `nil` as the last value for the "happy path" 
- We can create a custom error using the errors package (similar to using `new Error` in javascript)
- Also can create custom error types via a struct (not shown)

### Errors
We can create custom errors using `errors.New`
```go
import (
	"errors"
	"fmt"
)
func f1(arg int) (int, error) {
	if arg == 42 {
		// `errors.New` constructs a basic `error` value
		return -1, errors.New("can't work with 42")
	}
	// A `nil` value in the error position indicates that
	// there was no error.
	return arg + 3, nil
}
```


# Examples
Note that go uses `nil` instead of `null.`
```go
import (
  "fmt"
  "os"
  "strconv"
)

func main() {
  if len(os.Args) != 2 {
    os.Exit(1)
  }

  n, err := strconv.Atoi(os.Args[1])
  if err != nil {
    fmt.Println("not a valid number")
  } else {
    fmt.Println(n)
  }
}
```

# Database Errors
At [[Ookla]]. we differentiate `dbr.ErrNotFound` errors from other database or server errors. We should *not* return the `ErrNotFound` as an `error`, but instead return a `nil, nil` 

```go
func (g *ReverseGeocodeGoogle) Country(lat, lng float64) (*geoip.Country, error) {
	// instatiate a new Country record 
	country := db.Country()
	err = country.FindByPlaceID(ctx, placeId)
	// if no country was found, return a nil Country pointer reference and a nil error
	if err == dbr.ErrNotFound {
		return nil, nil
	}
	// If it's not a NotFound error, then we should return it
	return country, err
}
```

Calling Code
```go
func (g *ReverseGeocodeChain) Country(lat, lng float64) (*geoip.Country, error) {
	for _, geocoder := range g.Geocoders {
		// return the first one that resolves anything
		place, err := geocoder.Country(ctx, lat, lng)
		// if no server error and a record was found, then 😀
		if err != nil || place != nil {
			return place, err
		}
	}
	logs.Increment("geocode.country.miss")
	// no record was found
	return nil, nil
}
```
---
Tags: [Programming](Programming.md) - [Go Lang](./Go%20Lang.md) 

Reference: https://go.dev/blog/go1.13-errors

Related: 
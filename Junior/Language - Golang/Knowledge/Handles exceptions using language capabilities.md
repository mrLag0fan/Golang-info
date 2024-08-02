## What is the error type?
In fac t, it’s just an interface type with a single method that returns an error message: 
```go
type error interface { 
	Error() string 
}
```
There are a few other important things to note in the example above.

- Errors can be returned as `nil`, and in fact, it’s the default, or “zero”, value of on error in Go. This is important since checking `if err != nil` is the idiomatic way to determine if an error was encountered (replacing the `try`/`catch` statements you may be familiar with in other programming languages).
    
- Errors are typically returned as the last argument in a function.
    
- When we do return an error, the other arguments returned by the function are typically returned as their default “zero” value. A user of a function may expect that if a non-nil error is returned, then the other arguments returned are not relevant.
    
- Lastly, error messages are usually written in lower-case and don’t end in punctuation. Exceptions can be made though, for example when including a proper noun, a function name that begins with a capital letter, etc.
## How to check for an error in Go?
```go
if err != nil {
    return err
}
```
### Defining Expected Errors[](https://earthly.dev/blog/golang-errors/#defining-expected-errors)

Another important technique in Go is defining expected Errors so they can be checked for explicitly in other parts of the code. This becomes useful when you need to execute a different branch of code if a certain kind of error is encountered.
```go
package main

import (
    "errors"
    "fmt"
)

var ErrDivideByZero = errors.New("divide by zero")

func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, ErrDivideByZero
    }
    return a / b, nil
}

func main() {
    a, b := 10, 0
    result, err := Divide(a, b)
    if err != nil {
        switch {
        case errors.Is(err, ErrDivideByZero):
            fmt.Println("divide by zero error")
        default:
            fmt.Printf("unexpected division error: %s\n", err)
        }
        return
    }

    fmt.Printf("%d / %d = %d\n", a, b, result)
}
```
#### Defining Custom Error Types[](https://earthly.dev/blog/golang-errors/#defining-custom-error-types)

Many error-handling use cases can be covered using the strategy above, however, there can be times when you might want a little more functionality. Perhaps you want an error to carry additional data fields, or maybe the error’s message should populate itself with dynamic values when it’s printed.

You can do that in Go by implementing custom errors type.

Below is a slight rework of the previous example. Notice the new type `DivisionError`, which implements the `Error` `interface`. We can make use of `errors.As` to check and convert from a standard error to our more specific `DivisionError`.
```go
package main

import (
    "errors"
    "fmt"
)

type DivisionError struct {
    IntA int
    IntB int
    Msg  string
}

func (e *DivisionError) Error() string { 
    return e.Msg
}

func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, &DivisionError{
            Msg: fmt.Sprintf("cannot divide '%d' by zero", a),
            IntA: a, IntB: b,
        }
    }
    return a / b, nil
}

func main() {
    a, b := 10, 0
    result, err := Divide(a, b)
    if err != nil {
        var divErr *DivisionError
        switch {
        case errors.As(err, &divErr):
            fmt.Printf("%d / %d is not mathematically valid: %s\n",
              divErr.IntA, divErr.IntB, divErr.Error())
        default:
            fmt.Printf("unexpected division error: %s\n", err)
        }
        return
    }

    fmt.Printf("%d / %d = %d\n", a, b, result)
}
```
## What is a panic in Go?
## How to compare errors?
## How to handle panic?
## What happens with an application when a panic occurs?
## Errors are values - what does this mean?
https://go.dev/blog/errors-are-values
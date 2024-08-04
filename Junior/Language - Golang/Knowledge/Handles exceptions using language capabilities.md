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
There are certain operations in Go that automatically return panics and stop the program. Common operations include indexing an [array](https://www.digitalocean.com/community/tutorials/understanding-arrays-and-slices-in-go#arrays) beyond its capacity, performing type assertions, calling methods on nil pointers, incorrectly using mutexes, and attempting to work with closed channels. Most of these situations result from mistakes made while programming that the compiler has no ability to detect while compiling your program.
### Anatomy of a Panic
Panics are composed of a message indicating the cause of the panic and a [stack trace](https://en.wikipedia.org/wiki/Stack_trace) that helps you locate where in your code the panic was produced.
The first part of any panic is the message. It will always begin with the string `panic:`, which will be followed with a string that varies depending on the cause of the panic
```
panic: runtime error: index out of range [3] with length 3
```
Following this message is the stack trace. Stack traces form a map that we can follow to locate exactly what line of code was executing when the panic was generated, and how that code was invoked by earlier code.
The stack trace is broken into separate blocks—one for each [goroutine](https://tour.golang.org/concurrency/1) in your program. Every Go program’s execution is accomplished by one or more goroutines that can each independently and simultaneously execute parts of your Go code. Each block begins with the header `goroutine X [state]:`. The header gives the ID number of the goroutine along with the state that it was in when the panic occurred. After the header, the stack trace shows the function that the program was executing when the panic happened, along with the filename and line number where the function executed.
## How to compare errors?
### Comparing new errors directly
```go
e1 := errors.New(“ohno”)  
e2 := errors.New(“ohno”)  
// What does this print?  
fmt.Println(e1 == e2)
```
Answer: `false`
We’re starting simple — all errors created via `errors.New` are distinct.
**Short answer**: These errors `e1` and `e2` are ultimately compared as pointers. Pointers are only equal in Go if they point to the same underlying object in memory. Since each call to `errors.New` is a new allocation (thus the name), the pointers are not equal.
**It DOES NOT MATTER that the errors have the same string value “ohno” within them.**
**Long answer:** The code for `errors.New` (as of Go 1.19) is as follows:
```go
// New returns an error that formats as the given text.  
// Each call to New returns a distinct error value even if the text is identical.  
func New(text string) error {  
return &errorString{text}  
}  
  
// errorString is a trivial implementation of error.  
type errorString struct {  
s string  
}  
  
func (e *errorString) Error() string {  
return e.s  
}
```
A few things to note:

1. First off, the docs on the function tell you that all values returned are distinct. Always read the docs.
2. For callers, `errors.New` returns an `error` interface.
3. The actual thing returned is of type `*errorString` , which implements the `error` interface using a pointer receiver on the `Error()` metho
So when we call the function twice, we get two `error` interface values implemented by an `*errorString` inside them. We then compare our two `error` interface values.

> Interface values are comparable. Two interface values are equal if they have [identical](https://go.dev/ref/spec#Type_identity) dynamic types and equal dynamic values or if both have value `nil`.

> The _static type_ (or just _type_) of a variable is the type given in its declaration, the type provided in the `new` call or composite literal, or the type of an element of a structured variable. Variables of interface type also have a distinct _dynamic type_, which is the (non-interface) type of the value assigned to the variable at run time (unless the value is the predeclared identifier `nil`, which has no type).

So here, for `errors.New`:

1. The static type is `error`.
2. The dynamic type is `*errorString`.
3. The dynamic value is `&errorString{s: "ohno"}`.
So two interface values are equal if they have identical dynamic types and equal dynamic values. Well, our two errors `e1` and `e2` certainly have identical dynamic types: `*errorString`. Do they have equal dynamic values? That would mean comparing the pointer values `&errorString{s: "ohno"}`.
>Pointer values are comparable. Two pointer values are equal if they point to the same variable or if both have value `nil`. Pointers to distinct [zero-size](https://go.dev/ref/spec#Size_and_alignment_guarantees) variables may or may not be equal.

Well, there you have it. The dynamic values are both separately-allocated struct pointers, e.g. `&errorString{s: "ohno"}`. The pointers point to different memory / different variables. So they are not equal.
#### Comparing new errors by value

> The `errors.Is` function compares an error to a value.

So we start by comparing two errors as values using `errors.Is`. What does this code print out?

```go
e1 := errors.New("ohno")  
e2 := errors.New("ohno")  
// What does this print?  
fmt.Println(errors.Is(e1, e2))
```

Answer: `false`
Yes. This is good. `errors.Is` should not change the fact that we’re comparing two distinct variables as values. Those variables are two distinct interface values that contain two distinct pointers to structs as their dynamic values.
### Comparing existing errors as values
```go
var (  
  ErrCustom = errors.New("ohno")  
)  
  
var e1 error = ErrCustom  
var e2 error = ErrCustom  
// What does this print?  
fmt.Println(e1 == e2)  
// And this?  
fmt.Println(errors.Is(e1, e2))
```

Answer:
![](https://miro.medium.com/v2/resize:fit:395/1*smUJ1wBfD_qOH1g__u6o_A.png)
This is good. Both variables are `error` interface values that contain and point to the same underlying error in memory. So this matches everything we want, and is indeed akin to how we compare error values most of the time.

Note, however — this is a bit contrived. In normal code, we’d usually compare some error returned by a function to a package-level variable. But this mimics the general idea.
### Comparing custom errors as values
```go
type CustomError struct {  
  Err string  
}  
  
func (ce CustomError) Error() string {  
  return ce.Err  
}  
  
func main() {  
  var e1 error = CustomError{Err: "ohno"}  
  var e2 error = CustomError{Err: "ohno"}  
  fmt.Println(errors.Is(e1, e2))  
}
```

Answer: **true!**

Can you see what happened? We’ve created a custom error type, which implements the `error` interface using **value receivers**. That means that variables of this type actually ARE compared by value!

Recall that `errors.New` returns an `error` interface value containing an `*errorString` dynamic type and value. It’s returned with a `*errorString` because that’s how the Go library writers chose to implement `error` for the type — using **pointer receivers**. And when we compare pointers, they have to point to the same thing to be equal. This was clearly an intentional choice of semantic — `errors.New` should return something that is distinct / not equal to any other error, every time! Otherwise they probably would’ve called it something else.

So now we have a custom error type, which is assigned to and contained within an `error` interface value. But these dynamic values implement the interface with value receivers, which means they get compared not as pointers, but just as structs! And per the [Golang spec](https://go.dev/ref/spec#Comparison_operators) again:

> Struct values are comparable if all their fields are comparable. Two struct values are equal if their corresponding non-[blank](https://go.dev/ref/spec#Blank_identifier) fields are equal.

Well, the only fields here are strings, and strings are also compared directly by content:

> String values are comparable and ordered, lexically byte-wise.

So these custom errors are equal.
## How to handle panic?
Panics have a single recovery mechanism—the `recover` builtin function. This function allows you to intercept a panic on its way up through the call stack and prevent it from unexpectedly terminating your program. It has strict rules for its use, but can be invaluable in a production application.
```go
package main

import (
	"fmt"
	"log"
)

func main() {
	divideByZero()
	fmt.Println("we survived dividing by zero!")

}

func divideByZero() {
	defer func() {
		if err := recover(); err != nil {
			log.Println("panic occurred:", err)
		}
	}()
	fmt.Println(divide(1, 0))
}

func divide(a, b int) int {
	return a / b
}
```
This example will output:

```
Output
2009/11/10 23:00:00 panic occurred: runtime error: integer divide by zero
we survived dividing by zero!
```
Our `main` function in this example calls a function we define, `divideByZero`. Within this function, we `defer` a call to an anonymous function responsible for dealing with any panics that may arise while executing `divideByZero`. Within this deferred anonymous function, we call the `recover` builtin function and assign the error it returns to a variable. If `divideByZero` is panicking, this `error` value will be set, otherwise it will be `nil`. By comparing the `err` variable against `nil`, we can detect if a panic occurred, and in this case we log the panic using the `log.Println` function, as though it were any other `error`.

## What happens with an application when a panic occurs?
When the code starts panicking, the normal execution flow of the program stops, and it prints an error message. In simple terms, the `panic` event happens when something goes unexpectedly wrong during execution.
## Errors are values - what does this mean?
https://go.dev/blog/errors-are-values
## ﻿How to declare a variable in Go?

```go
var name type = expression
```
Either the `type` or the `= expression` part may be omitted, but not both. If the type is omitted, it is determined by the initializer expression. If the expression is omitted, the initial value is the zero value for the type, which is 0 for numbers, false for booleans, "" for strings, and nil for interfaces and reference types. The zero value of an aggregate type like an array or struct has the zero value of all of its elements or fields.

Omitting the type allows declaration of multiple variables of different types:
```go
var i, j, k int // int, int, int 
var b, f, s = true, 2.3, "four" // bool, float64, string
```
A set of variables can also be initialize d by calling a function that returns multiple values:
```go
var f, err = os.Open(name) // os.Open returns a file and an error
```
### Short Variable Declarations

Within a function, an alternate form called a *short variable declaration* may be used to declare and initialize local variables.

```go 
name := expression
```

One subtle but important point: a short variable declaration does not necessarily declare all the variables on its left-hand side. If some of them were already declared in the same lexical block, then the short variable declaration acts like an assignment to those variables. In the code below, the first statement declares both `in` and `err`. The second declares out but only assigns a value to the existing err variable. 
```go
in, err := os.Open(infile) 
// ... 
out, err := os.Create(outfile)
```
A short variable declaration must declare at least one new variable, however, so this code will not compile: 
```go
f, err := os.Open(infile) 
// ... 
f, err := os.Create(outfile) // compile error: no new variables
```

The fix is to use an ordinary assignment for the second statement.

### Pointers

A pointer value is the address of a variable. A pointer is thus the locat ion at which a value is stored. Not every value has an address, but every variable does. With a pointer, we can read or update the value of a variable indirectly, without using or even knowing the name of the variable, if indeed it has a name.

If a variable is declared `var x int`, the expression `&x` (‘‘address of x’’) yields a pointer to an integer variable, that is, a value of type `*int`, which is pronounced ‘‘pointer to int.’’ If this value is called p, we say ‘‘p points to x,’’ or equivalently ‘‘p contains the address of x.’’ The variable to which p points is written `*p`. The expression `*p` yields the value of that variable, an int, but since `*p` denotes a variable, it may also appear on the left-hand side of an assignment, in which case the assignment updates the variable. 
```go
x := 1 
p := &x // p, of type *int, points to x
fmt.Println(*p) // "1"
*p = 2 // equivalent to x = 2 
fmt.Println(x) // "2"
```

Because a pointer contains the address of a variable, passing a pointer argument to a function makes it possible for the function to update the variable that was indirectly passed. For example, this function increments the variable that its argument points to and returns the new value of the variable so it may be used in an expression: 
```go
func incr(p *int) int { 
	*p++ // increments what p points to; does not change p
	return *p 
} 
v := 1 incr(&v) // side effect: v is now 2 
fmt.Println(incr(&v)) // "3" (and v is 3)
```
## What are the variable scope and lifecycle?
### Lifetime

The *lifetime* of a variable is the interval of time during which it exists as the program executes. The lifet ime of a package-level variable is the entire execution of the program. By contrast, local variables have dynamic lifetimes: a new instance is created each time the declaration statement is executed, and the variable lives on until it becomes unreachable, at which point its storage may be recycled. Function parameters and results are local variables too; the y are created each time their enclosing function. For example
```go
for t := 0.0; t < cycles*2*math.Pi; t += res {
	x := math.Sin(t) 
	y := math.Sin(t*freq + phase) i
	mg.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5), blackIndex) 
}
```
the variable t is created each time the for loop begins, and new variables x and y are created on each iteration of the loop.

A compiler may choose to allocate local variables on the heap or on the stack but, perhaps surprisingly, this choice is not determined by whether var or new was used to declare the variable. ```
```go
var global *int 
func f() {                           func g() {
	var x int                            y := new(int) 
	x=1                                  *y = 1 
	global = &x                      }
}                                    
```
Here , x must be heap-allocated because it is still reach able from the variable global after f has returned, despie being declared as a local variable; we say x escapes from f. Conversely, when g returns, the variable `*y` becomes unreachable and can be recycled. Since `*y` do es not escape from g, it’s safe for the compiler to allocate `*y` on the stack, even though it was allocated with new. In any case, the notion of escaping is not something that you need to worry about in order to write correct code, though it’s good to keep in mind during performance optimization, since each variable that escapes requires an extra memory allocation.

### Scope
A declaration associates a name with a program entity, such as a function or a variable. The scope of a declaration is the part of the source code where a use of the declared name refers to that declaration.

Don’t confuse scope with lifetime. The scope of a declaration is a region of the program text; it is a compile-time property. The lifetime of a variable is the range of time during execution when the variable can be referred to by other parts of the program; it is a run-time property.
## What are the shadowed variables in Go?

If a name is declared in both an outer block and an inner block, the inner declaration will be found first. In that case, the inner declaration is said to **shadow** or hide the outer one, making it inaccessible:
```go
func f() {} 

var g = "g"

func main() { 
	f := "f" 
	fmt.Println(f) // "f"; local var f shadows package-level func f 
	fmt.Println(g) // "g"; package-level var 
	fmt.Println(h) // compile error: undefined: h 
}
```
## What is the difference between function and method?
### Function

A function lets us wrap up a sequence of statements as a unit that can be called from else where in a program, perhaps multiple times. Functions make it possible to break a big job int o smaller pieces that might well be written by different people separated by both time and space. A function hides its implementation details from its users.
#### Function Declarations

A function declaration has a name, a list of parameters, an optional list of results, and a body: 
```go
func name(parameter-list) (result-list) {
	body 
}
``` 
The parameter list specifies the names and types of the function’s parameters, which are the local variables whose values or arguments are supplied by the caller. The result list specifies the types of the values that the function returns. If the function returns one unnamed result or no results at all, parentheses are optional and usually omitted. Leaving off the result list entirely declares a function that does not return any value and is called only for its effects

### Method

Since the early 1990s, object-oriented programming (OOP) has been the dominant programming paradigm in industry and education, and nearly all widely used languages developed since then have included support for it. Go is no exception.
Although there is no universally accepted definition of object-oriented programming, for our purposes, an object is simply a value or variable that has methods, and a **method** is a function associated with a particular type. An object-oriented program is one that uses methods to express the properties and operations of each data structure so that clients need not access the object’s representation directly

#### Method Declarations

A method is declared with a variant of the ordinar y function declaration in which an extra parameter appears before the function name. The parameter attaches the function to the type of that parameter

```go
func (receiver) name(parameter-list) (result-list) {
	body 
}
```

## What is the difference between value and pointer method receiver?
Because calling a function makes a copy of each argument value, if a function needs to update a variable, or if an argument is so large that we wish to avoid copying it, we must pass the address of the variable using a pointer. The same goes for met hods that need to update the receiver variable: we attach them to the pointer type, such as `*Point`. 
```go
func (p *Point) ScaleBy(factor float64) { 
	p.X *= factor p.Y *= factor 
}
```
Convention dictates that if any method of struct has a pointer receiver, then all methods of struct should have a pointer receiver, even ones that don’t strictly need it.

## What is the zero value of the function?

Func tions are first-class values in Go: like other values, function values have types, and they may be assigned to variables or passed to or returned from functions. A function value may be called like any other function. For example: 
```go
func square(n int) int { return n * n }
func negative(n int) int { return -n }
func product(m, n int) int { return m * n }

f := square
fmt.Println(f(3)) // "9" 
f=negative 
fmt.Println(f(3)) // "-3" 
fmt.Printf("%T\n", f) // "func(int) int" 
f=product // compile error: can't assign f(int, int) int to f(int) int
```
The zero value ofafunction type is nil. Calling a nil function value causes a panic: 
```go
var f func(int) int f(3) // panic: call of nil function
```
## How and in what cases to write an anonymous function?
Named functions can be declared only at the package level, but we can use a function literal to denote a function value within any expression. A function literal is written like a function declaration, but without a name following the func keyword . It is an expression, and its value is called an anonymous function.
```go
strings.Map(func(r rune) rune { return r + 1 }, "HAL-9000")
```
### Use Cases for Anonymous Functions
 One common use case is as callback functions, which are functions that are passed as arguments to other functions and are called when a certain event occurs.
 
Another use case for anonymous functions is in immediately invoked function expressions (**IIFE**). An IIFE is a function that is executed immediately after it is defined. This can be useful for creating a private scope for variables and functions. Here's an example of an IIFE:
```go
func(twoSeconds time.Duration) {
    // use twoSeconds
}(time.Second * 2)
```
## When to use unnamed parameters for the function?
https://stackoverflow.com/questions/40950877/is-unnamed-arguments-a-thing-in-go
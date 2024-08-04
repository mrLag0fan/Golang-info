## What is the difference between function and method in Go?
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

## What are the differences between value and pointer receiver and when to use which?
Because calling a function makes a copy of each argument value, if a function needs to update a variable, or if an argument is so large that we wish to avoid copying it, we must pass the address of the variable using a pointer. The same goes for met hods that need to update the receiver variable: we attach them to the pointer type, such as `*Point`. 
```go
func (p *Point) ScaleBy(factor float64) { 
	p.X *= factor p.Y *= factor 
}
```
Convention dictates that if any method of struct has a pointer receiver, then all methods of struct should have a pointer receiver, even ones that don’t strictly need it.
## Is it possible to create a method with a receiver type defined in another package?
**You cannot declare a method with a receiver whose type is defined in another package**
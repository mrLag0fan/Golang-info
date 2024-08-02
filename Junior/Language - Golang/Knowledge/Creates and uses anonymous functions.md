## How to describe the parameters list, their types of declarations, and named and unnamed parameters?

```go
func SquaresOfSumAndDiff(a int64, b int64) (s int64, d int64) {
	x, y := a + b, a - b
	s = x * x
	d = y * y
	return // <=> return s, d
}
```
We can find that, a function declaration is composed of several portions. From left to right,
1. the first portion is the `func` keyword.
2. the next portion is the function name, which must be an identifier. Here the function name is `SquareOfSumAndDiff`.
3. the third portion is the input parameter declaration list, which is enclosed in a pair of `()`.
4. the fourth portion is the output (or return) result declaration list. Go functions can return multiple results. For this specified example, the result declaration list is also enclosed in a pair of `()`. However, if the list is blank or it is composed of only one anonymous result declaration, then the pair of `()` in result declaration list is optional (see below for details).
5. the last portion is the function body, which is enclosed in a pair of `{}`. In a function body, the `return` keyword is used to end the normal forward execution flow and enter the exiting phase (see the section after next) of a call of the function.
The names in the result declaration list of a function declaration can/must be present or absent all together. Either case is used common in practice. If a result is defined with a name, then the result is called a named result, otherwise, it is called an anonymous result.

When all the results in a function declaration are anonymous, then, within the corresponding function body, the `return` keyword must be followed by a sequence of return values, each of them corresponds to a result declaration of the function declaration. For example, the following function declaration is equivalent to the above one.

```go
func SquaresOfSumAndDiff(a int64, b int64) (int64, int64) {
	return (a+b) * (a+b), (a-b) * (a-b)
}
```
If the type portions of some successive parameters in a parameter declaration list are identical, then these parameters could share the same type portion in the parameter declaration list. The same is for result declaration lists. For example, the above two function declarations with the name `SquaresOfSumAndDiff` are equivalent to

```go
func SquaresOfSumAndDiff(a, b int64) (s, d int64) {
	return (a+b) * (a+b), (a-b) * (a-b)
	// The above line is equivalent
	// to the following line.
	/*
	s = (a+b) * (a+b); d = (a-b) * (a-b); return
	*/
}
```
If the result declaration list in a function declaration only contains one anonymous result declaration, then the result declaration list doesn't need to be enclosed in a `()`. If the function declaration has no return results, then the result declaration list portion can be omitted totally. The parameter declaration list portion can never be omitted, even if the number of parameters of the declared function is zero.
https://stackoverflow.com/questions/40950877/is-unnamed-arguments-a-thing-in-go
## How data is passed into the functions in Go (value/reference)?
In Go, there are two ways to pass arguments to a function: pass-by-value and pass-by-reference. Pass-by-value means that the function receives a copy of the argument, while pass-by-reference means that the function receives a reference to the original argument.
Here are some examples of data types and their default pass-by-value or pass-by-reference behavior in Go:

###### Pass-by-value:

- Integers (`int`, `int8`, `int16`, `int32`, `int64`, `uint`, `uint8`, `uint16`, `uint32`, `uint64`)
- Floating-point numbers (`float32`, `float64`)
- Complex numbers (`complex64`, `complex128`)
- Booleans (`bool`)
- Strings (`string`)
- Structs
- Arrays
- Pointers (`*T`, where `T` is any type)
```go
package main  
  
import "fmt"  
  
type Person struct {  
	Name string  
	Age int  
}  
  
func modifyPerson(p Person) {  
	p.Age = 30  
}  
  
func main() {  
	myPerson := Person{Name: "Alice", Age: 25}  
	modifyPerson(myPerson)  
	fmt.Println(myPerson) // Output: {Alice 25}  
}
```
###### Pass-by-reference:

- Slices (`[]T`, where `T` is any type)
- Maps (`map[K]V`, where `K` and `V` are any types)
- Channels (`chan T`, where `T` is any type)
- Functions (`func(T)`, where `T` is any type)
- Interfaces (`interface{}`)
```go
package main  
  
import "fmt"  
  
func modifySlice(s []int) {  
s[0] = 10  
}  
  
func main() {  
mySlice := []int{20, 30, 40}  
modifySlice(mySlice)  
fmt.Println(mySlice) // Output: [10 30 40]  
}
```

## **When do we need unused parameters “func f (_ int)”????

Припускаю щоб передавати ці функції в інші функції щоб не порушити сигнатуру оутер функції та не в надобності параметра з інер фунції

## What is the difference between values vs pointers vs reference params?
- **Pass by value** will pass the value of the variable into the method, or we can say that the original variable ‘copy’ the value into another memory location and pass the newly created one into the method. So, anything that happens to the variable inside the method will not affect the original variable value.
- **Pass by reference** will pass the memory location instead of the value. In other words, it passes the ‘container’ of the variable to the method so, anything that happens to the variable inside the method will affect the original variable.
![[1_GXoMWqljArmbjB0ReNioag.gif]]![[1_GXoMWqljArmbjB0ReNioag.gif]]
## What's the syntax of functions as a value?
```go
func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main(){
	f := func (a, b int) int{
		return a + b
	}
	fmt.Println(f(1, 2)) //3
	fmt.Println(compute(f)) // 7
}
```
## What are the defer calls, and order of executions?
In Go, `defer` is a keyword used to delay the execution of a function until the surrounding function finishes.
```Go
func main() {
  defer fmt.Println("hello")
  fmt.Println("world")
}

// Output:
// world
// hello
```
It’s just like setting up a task to run later, right before the function exits. This is really useful for cleanup actions, like closing a database connection, freeing up a mutex, or closing a file:
```go
f, err := os.Open("phuong-secrets.txt")
  if err != nil {
    return err
  }
  defer f.Close()
```

> _“Okay, good, but why not put the f.Close() at the end?”_

There are a couple of good reasons for this:

- We put the close action near the open, so it’s easier to follow the logic and avoid forgetting to close the file. I don’t want to scroll down a function to check if the file is closed or not; it distracts me from the main logic.
- The deferred function is called when the function returns, even if a panic (runtime error) happens.
When you use multiple `defer` statements in a function, they are executed in a ‘stack’ order, meaning the last deferred function is executed first.
```go
func main() {
  defer fmt.Println(1)
  defer fmt.Println(2)
  defer fmt.Println(3)
}

// Output:
// 3
// 2
// 1
```
![[Pasted image 20240802142923.png]]

## How to pass values to deferred calls.
```go
func pushAnalytic(a int) {
  fmt.Println(a)
}

func main() {
  a := 10
  defer pushAnalytic(a) //10

  a = 20
}
```
What do you think the output will be? It’s 10, not 20.

That’s because when you use the defer statement, it grabs the values right then. This is called “capture by value.” So, the value of `a` that gets sent to `pushAnalytic` is set to 10 when the defer is scheduled, even though `a` changes later

There are two ways to fix this.

The first way is to use a closure. This means wrapping the deferred function call inside another function. That way, you capture the variable by reference, not by value like before.
```go
func main() {
  a := 10
  defer func() {
    pushAnalytic(a)
  }()

  a = 20
}
```
The second way is to pass the memory address of the variable instead of its value.
```go
func pushAnalytic(a *int) {
  fmt.Println(*a)
}

func main() {
  a := 10
  defer pushAnalytic(&a)

  a = 20
}
```

```go
type Data struct {
  a int
}

func (d Data) pushAnalytic() {
  fmt.Println(d.a)
}

func main() {
  d := Data{a: 10}
  defer d.pushAnalytic()

  d.a = 20
}

// Output:
// 10
```
The output is actually 10, just like before.

This happens because the defer statement also evalutes its receiver immediately, capturing the value of `d` at that moment. Under the hood, the receiver is like an argument, so the defer statement works like this:
```go
defer Data.pushAnalytic(d) // defer d.pushAnalytic()
```
So, the same rule applies: the arguments of the deferred function are evaluated right away.

Again, there are two ways to fix this, but they are a bit different from the previous examples with simple variables.

> _“We fix this by using a closure or pointer, right?”_

Using a closure works, but just using a pointer isn’t enough. Even if we change `Data{}` to `&Data{}`, it won’t fix the problem because we’re still passing the dereferenced value to the deferred function:

```go
d := &Data{}
defer Data.PushAnalytic(*d)
```

We need to change how we pass the receiver to the deferred function by switching from a value receiver to a pointer receiver.

```go
func (d *Data) pushAnalytic() {
  fmt.Println(d.a)
}
```
To sum up, the deferred function’s arguments are evaluated when the defer statement is executed, or `scheduled`, not when the deferred function is called.
## What are anonymus deffered functions?

```go
func pushAnalytic(a int) {
  fmt.Println(a)
}

func main() {
  a := 10
  defer pushAnalytic(a) //10

  a = 20
}
```
What do you think the output will be? It’s 10, not 20.

That’s because when you use the defer statement, it grabs the values right then. This is called “capture by value.” So, the value of `a` that gets sent to `pushAnalytic` is set to 10 when the defer is scheduled, even though `a` changes later

There are two ways to fix this.

The first way is to use a closure. This means wrapping the deferred function call inside another function. That way, you capture the variable by reference, not by value like before.
```go
func main() {
  a := 10
  defer func() {
    pushAnalytic(a)
  }()

  a = 20
}
```
## How does the panic and recovery flow work?
Besides compile-time errors, we have a bunch of runtime errors: divide by zero (integer only), out of bounds, dereferencing a nil pointer, and so on. These errors cause the application to panic.
Panic is a way to stop the execution of the current goroutine, unwind the stack, and execute the deferred functions in the current goroutine, causing our application to crash.
To handle unexpected errors and prevent the application from crashing, you can use the `recover` function within a deferred function to regain control of a panicking goroutine.

```go
func main() {
  defer func() {
    if r := recover(); r != nil {
      fmt.Println("Recovered:", r)
    }
  }()

  panic("This is a panic")
}

// Output:
// Recovered: This is a panic
```
Usually, people put an error in the panic and catch that with `recover(..)`, but it could be anything: a string, an int, etc.
## How to write a closure?
Closures are a powerful feature in Go programming that allow for encapsulating state and behaviour within functions. They provide a way to create self-contained units of code that can access and manipulate variables from their surrounding scope.
```go
package main

import "fmt"

func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
```
### What are Closures?

At its core, a closure is a function bundled together with its referencing environment. It captures variables from its outer scope, allowing those variables to be accessed and used within the closure. This enables the closure to maintain its own state and retain access to the captured variables even after the outer function has finished execution.
### Benefits and Use Cases

Closures provide several benefits in Go programming:

1. Encapsulating private state: Closures allow for the creation of functions with private variables that are inaccessible from outside the closure. This helps in building modular and secure code.
2. Function factories: Closures can act as factories for generating specialized functions based on specific configurations or parameters. They allow for the creation of custom functions with pre-set behaviors.
3. Maintaining state across multiple calls: Closures enable functions to retain state across successive invocations. The captured variables within closures store their values, allowing functions to remember and update their state as needed.
4. Callbacks and event handlers: Closures are commonly used for implementing callbacks and event handlers. They capture variables and provide a mechanism for executing specific actions when events occur.
5. Asynchronous operations: Closures are useful when dealing with asynchronous operations or goroutines. They help in passing data and behavior into goroutines, ensuring that each goroutine operates with its own set of captured variables.
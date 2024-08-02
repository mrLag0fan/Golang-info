## What is the syntax for the if, switch, and for (with different conditions) statements?
### An Introduction of Control Flows in Go
There are three kinds of basic control flow code blocks in Go:
- `if-else` two-way conditional execution block.
- `for` loop block.
- `switch-case` multi-way conditional execution block.
There are also some control flow code blocks which are related to some certain kinds of types in Go.
- `for-range` loop block to iterate integers, all kinds of [containers](https://go101.org/article/container.html#iteration), and [channels](https://go101.org/article/channel.html#range).
- `type-switch` multi-way conditional execution block for [interfaces](https://go101.org/article/interface.html#type-switch).
- `select-case` block for [channels](https://go101.org/article/channel.html#select).
Among the six kinds of control flow blocks, except the `if-else` control flow, the other five are called **breakable control flow blocks**. We can use `break` statements to make executions jump out of breakable control flow blocks.
`for` and `for-range` loop blocks are called **loop control flow blocks**. We can use `continue` statements to end a loop iteration in advance in a loop control flow block, i.e. continue to the next iteration of the loop.
### `if-else` Control Flow Blocks
The full form of a `if-else` code block is like
```go
if InitSimpleStatement; Condition {
	// do something
} else {
	// do something
}
```
The `InitSimpleStatement` portion is also optional. It must be a [simple statement](https://go101.org/article/expressions-and-statements.html#simple-statements) if it is present. If it is absent, we can view it as a blank statement (one kind of simple statements). In practice, `InitSimpleStatement` is often a short variable declaration or a pure assignment. A `Condition` must be an [expression](https://go101.org/article/expressions-and-statements.html#expressions) which results to a boolean value. The `Condition` portion can be enclosed in a pair of `()` or not, but it can't be enclosed together with the `InitSimpleStatement` portion.
### `for` Loop Control Flow Blocks
The full form of a `for` loop block is:
```go
for InitSimpleStatement; Condition; PostSimpleStatement {
	// do something
}
```
`for` is a keyword. The `InitSimpleStatement` and `PostSimpleStatement` portions must be both simple statements, and the `PostSimpleStatement` portion must not be a short variable declaration. `Condition` must be an expression which result is a boolean value. The three portions are all optional.
The `InitSimpleStatement` in a `for` loop block will be executed (only once) before executing other statements in the `for` loop block
The `Condition` expression will be evaluated at each loop iteration. If the evaluation result is `false`, then the loop will end. Otherwise the body (a.k.a., the explicit code block) of the loop will get executed.
The `PostSimpleStatement` will be executed at the end of each loop iteration.
```go
for i := 0; i < 10; i++ {
	fmt.Println(i)
}
```
If the `InitSimpleStatement` and `PostSimpleStatement` portions are both absent (just view them as blank statements), their nearby two semicolons can be omitted. The form is called as condition-only `for` loop form. It is the same as the `while` loop in other languages.
```go
var i = 0
for ; i < 10; {
	fmt.Println(i)
	i++
}
for i < 20 {
	fmt.Println(i)
	i++
}
```
If the `Condition` portion is absent, compilers will view it as `true`.
```go
for i := 0; ; i++ { // <=> for i := 0; true; i++ {
	if i >= 10 {
		// "break" statement will be explained below.
		break
	}
	fmt.Println(i)
}

// The following 4 endless loops are
// equivalent to each other.
for ; true; {
}
for true {
}
for ; ; {
}
for {
}
```
#### Use `for-range` Control Flow Blocks to Iterate Integers
`for-range` loop blocks can be used to iterate integers, all kinds of [containers](https://go101.org/article/container.html#iteration), and [channels](https://go101.org/article/channel.html#range).
```go
// i is declared earlier.
for i = range anInteger {
	...
}
```
is actually a short form of
```go
for i = 0; i < anInteger; i++ {
	...
}
```
Similarly,
```go
for i := range anInteger {
	...
}
```
is actually a short form of
```go
for i := 0; i < anInteger; i++ {
	...
}
```

### `switch-case` Control Flow Blocks
`switch-case` control flow block is one kind of conditional execution control flow blocks.
The full form a `switch-case` block is
```go
switch InitSimpleStatement; CompareOperand0 {
case CompareOperandList1:
	// do something
case CompareOperandList2:
	// do something
...
case CompareOperandListN:
	// do something
default:
	// do something
}
```
In the full form,
- `switch`, `case` and `default` are three keywords.
- The `InitSimpleStatement` portion must be a simple statement. The `CompareOperand0` portion is an expression which is viewed as a typed value (if it is an untyped value, then it is viewed as a type value of its default type), hence it can't be an untyped `nil`. `CompareOperand0` is called as switch expression in Go specification.
- Each of the `CompareOperandListX` (`X` may represent from `1` to `N`) portions must be a comma separated expression list. Each of these expressions shall be comparable with `CompareOperand0`. Each of these expressions is called as a case expression in Go specification. If a case expression is an untyped value, then it must be implicitly convertible to the type of the switch expression in the same `switch-case` control flow. If the conversion is impossible to achieve, compilation fails.
There can be at most one `default` branch in a `switch-case` control flow block.
The `InitSimpleStatement` will get executed firstly when a `switch-case` control flow gets executed. It will get executed only once during executing the `switch-case` control flow. After the `InitSimpleStatement` gets executed, the switch expression `CompareOperand0` will be evaluated and only evaluated once. The evaluation result is always a typed value. The evaluation result will be compared (by using the `==` operator) with the evaluation result of each case expression in the `CompareOperandListX` expression lists, from top to down and from left to right. If a case expression is found to be equal to `CompareOperand0`, the comparison process stops and the corresponding branch code block of the expression will be executed. If none case expressions are found to be equal to `CompareOperand0`, the default branch code block (if it is present) will get executed.
## How does the switch case work, and what are the differences from other languages?
The `InitSimpleStatement` will get executed firstly when a `switch-case` control flow gets executed. It will get executed only once during executing the `switch-case` control flow. After the `InitSimpleStatement` gets executed, the switch expression `CompareOperand0` will be evaluated and only evaluated once. The evaluation result is always a typed value. The evaluation result will be compared (by using the `==` operator) with the evaluation result of each case expression in the `CompareOperandListX` expression lists, from top to down and from left to right. If a case expression is found to be equal to `CompareOperand0`, the comparison process stops and the corresponding branch code block of the expression will be executed. If none case expressions are found to be equal to `CompareOperand0`, the default branch code block (if it is present) will get executed.
Unlike many other languages, in Go, at the end of each branch code block, the execution will automatically break out of the corresponding `switch-case` control block. Then how to let the execution slip into the next branch code block? Go provides a `fallthrough` keyword to do this task. For example, in the following example, every branch code block will get executed, by their orders, from top to down.
```go
switch n := rand.Intn(100) % 5; n {
case 0, 1, 2, 3, 4:
	fmt.Println("n =", n)
	// The "fallthrough" statement makes the
	// execution slip into the next branch.
	fallthrough
case 5, 6, 7, 8:
	// A new declared variable also called "n",
	// it is only visible in the current
	// branch code block.
	n := 99
	fmt.Println("n =", n) // 99
	fallthrough
default:
	// This "n" is the switch expression "n".
	fmt.Println("n =", n)
}
```
The `InitSimpleStatement` and `CompareOperand0` portions in a `switch-case` control flow are both optional. If the `CompareOperand0` portion is absent, it will be viewed as `true`, a typed value of the built-in type `bool`. If the `InitSimpleStatement` portion is absent, the semicolon following it can be omitted.

And as above has mentioned, all branches are optional. So the following code blocks are all legal, all of them can be viewed as no-ops.

```go
switch n := 5; n {
}

switch 5 {
}

switch _ = 5; {
}

switch {
}
```

For the latter two `switch-case` control flow blocks in the last example, as above has mentioned, each of the absent `CompareOperand0` portions is viewed as a typed value `true` of the built-in type `bool`. So the following code snippet will print `hello`.

```go
switch { // <=> switch true {
case true: fmt.Println("hello")
default: fmt.Println("bye")
}
```
Another obvious difference from many other languages is the order of the `default` branch in a `switch-case` control flow block can be arbitrary. For example, the following three `switch-case` control flow blocks are equivalent to each other.

```go
switch n := rand.Intn(3); n {
case 0: fmt.Println("n == 0")
case 1: fmt.Println("n == 1")
default: fmt.Println("n == 2")
}

switch n := rand.Intn(3); n {
default: fmt.Println("n == 2")
case 0: fmt.Println("n == 0")
case 1: fmt.Println("n == 1")
}

switch n := rand.Intn(3); n {
case 0: fmt.Println("n == 0")
default: fmt.Println("n == 2")
case 1: fmt.Println("n == 1")
}
```
## How to perform switch by type?
A [**switch**](https://www.geeksforgeeks.org/switch-statement-in-go/) is a multi-way branch statement used in place of multiple if-else statements but can also be used to find out the dynamic type of an interface variable.   
A type switch is a construct that performs multiple type assertions to determine the type of variable (rather than values) and runs the first matching switch case of the specified type. It is used when we do not know what the interface{} type could be.
```go
var value interface{} = "GeeksforGeeks"

switch t := value.(type){
	case int64:
		fmt.Println("Type is an integer:", t)
	case float64:
		fmt.Println("Type is a float:", t)
	case string:
		fmt.Println("Type is a string:", t)
	case nil:
		fmt.Println("Type is nil.")
	case bool:
		fmt.Println("Type is a bool:", t)
	default:
		fmt.Println("Type is unknown!")
}
```

## How does the for-range work, and what types can be ranged?
A for-range loop in Golang is used for iterating over elements in various data structures like an array, slice, map, or even a string, etc. The range keyword is used for iterating over an expression and to evaluate data structures like slice, array, map, channel, etc.
```go
for index, value := range datastructure {
        fmt.Println(value)
    }
```
Where, Index is the value that is been accessed. value is the actual value that we have in each of the iteration. datastructure is used for holding the values that is accessed by the loop.
- While iterating over an array or slice, it returns an index of an element in an int format. 
```go
 numbers := [5]int{32, 33, 20, 12, 22}
 
  for index, item := range numbers {
    fmt.Printf("numbers[%d] = %d \n", index, item)
  }
```
- Similarly when it is iterated over a map then it returns the key and value.
-  Using range on the string- in the string, the first value that it returns is the index and the second value is rune int.
```go
  for i, ch := range "Hello" {
    fmt.Printf("%#U starts at byte position %d\n", ch, i)
  }
```
-  Using the range on the map- the first value that is returned by the map is the key and the second value returns the value from the key-value pair.
```go
m := map[string]int{
    "one":   1,
    "two":   2,
    "three": 3,
  }
  for key, value := range m {
      fmt.Println(key, value)
  }
```
- Using the range on the channel- the first value that it returns is an element and the second value returned is none.
```go
mychannel := make(chan int)
  go func() {
      mychannel <- 1
      mychannel <- 2
      mychannel <- 3
      close(mychannel)
  }()
  for n := range mychannel {
      fmt.Println(n)
  }
```
## What are the possible conditions for the loop?
### Three-component loop
```go
sum := 0
for i := 1; i < 5; i++ {
	sum += i
}
fmt.Println(sum)
```
### While loop
```go
n := 1
for n < 5 {
	n *= 2
}
fmt.Println(n)
```
### Infinite loop
```go
sum := 0
for {
	sum++ // repeated forever
}
fmt.Println(sum)
```
### For-each range loop
```go
strings := []string{"hello", "world"}
for i, s := range strings {
	fmt.Println(i, s)
}
```
## What are the break and continue statements?
A `break` statement can be used to make execution jump out of a `for` loop control flow block in advance, if the `for` loop control flow block is the innermost breakable control flow block containing the `break` statement. For example, the following code also prints `0` to `9`.
```go
i := 0
for {
	if i >= 10 {
		break
	}
	fmt.Println(i)
	i++
}
```
A `continue` statement can be used to end the current loop iteration in advance (`PostSimpleStatement` will still get executed), if the `for` loop control flow block is the innermost loop control flow block containing the `continue` statement. For example, the following code snippet will print `13579`.
```go
for i := 0; i < 10; i++ {
	if i % 2 == 0 {
		continue
	}
	fmt.Print(i)
}
```
A `break` or `continue` statement can also contain a label, but the label is optional. Generally, `break` containing labels are used in nested breakable control flow blocks and `continue` statements containing labels are used in nested loop control flow blocks.

If a `break` statement contains a label, the label must be declared just before a breakable control flow block which contains the `break` statement. We can view the label name as the name of the breakable control flow block. The `break` statement will make execution jump out of the breakable control flow block, even if the breakable control flow block is not the innermost breakable control flow block containing `break` statement.

If a `continue` statement contains a label, the label must be declared just before a loop control flow block which contains the `continue` statement. We can view the label name as the name of the loop control flow block. The `continue` statement will end the current loop iteration of the loop control flow block in advance, even if the loop control flow block is not the innermost loop control flow block containing the `continue` statement.

The following is an example of using `break` and `continue` statements with labels.
```go
Outer:
	for n++; ; n++{
		for i := 2; ; i++ {
			switch {
			case i * i > n:
				break Outer
			case n % i == 0:
				continue Outer
			}
		}
	}
```

## How to perform a break from the switch inside the for loop to exit the for loop?
```go

Switch:
	switch {
		case i * i > n:
			for i := 2; ; i++ {
				if i == 3 {
					break Switch
				}
			}
	}
```
## What is an early exit, and why is it preferable?
One of my favorite localized Go refactorings is reducing nesting by using `return` as early as possible. Take this example, based on a recently-refactored function at Heroku:

```go
func example() error {
  if err := start(); err != nil {
    existing, err := fetchExisting()
    if err != nil {
      return err
    }

    if existing.IsWorking() {
      return nil
    } else {
      return errors.New("found existing but it's not working")
    }
  }

  return nil
}
```

This can be hard to follow due to nesting inside `if ...; err != nil` and use of `else`. I prefer to `return` as early as possible and avoid `else`. Applying that, it now looks something like this:

```go
func example() error {
  if err := start(); err == nil {
    return err
  }

  existing, err := fetchExisting()
  if err != nil {
    return err
  }

  if !existing.IsWorking() {
    return errors.New("found existing but it's not working")
  }

  return nil
}
```


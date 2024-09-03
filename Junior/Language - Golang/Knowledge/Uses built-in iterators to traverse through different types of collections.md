## What is the difference between the implementation of different indexed `For` loop types (usual/basic, continued, "while", forever)
```go
for [ Initial Statement ] ; [ Condition ] ; [ Post Statement ] {
    [Action]
}
```
### Basic
```go
for i := 0; i < 5; i++ {
	fmt.Println(i)
}
```
### Continued???
### "While"
```go
i := 0
for i < 5 {
	fmt.Println(i)
	i++
}
```
### Forever
```
for {
	if someCondition {
		break
	}
	// do action here
}
```
## How many and what exact values could be taken from each iteration on the map with the range form of `For` loop?
Kye and value
## How many and what exact values could be taken from each iteration on slice/array with range form of `For` loop?
Index and value
## How to omit to get value on iteration with range form of `For` loop?
## Is it possible to finish iteration in the middle of the execution/cycle?
Break and continue
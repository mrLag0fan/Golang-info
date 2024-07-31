## What is the standard for writing the comments to a function/method/Interface?
###  [Doc Comments](https://www.digitalocean.com/community/tutorials/how-to-write-comments-in-go#doc-comments)
https://go.dev/doc/comment
## How to host a go doc server?
https://pkg.go.dev/golang.org/x/tools/cmd/godoc
## How to compare different data types using comparison operators?
https://go101.org/article/value-conversions-assignments-and-comparisons.html
## What are the rules of comparability and the most common comparable and non-comparable types?
1. If `T` is a boolean type, then the two values are equal only if they are both `true` or both `false`.
2. If `T` is an integer type, then the two values are equal only if they have the same representation in memory.
3. If `T` is a floating-point type, then the two values are equal only if any of the following conditions is satisfied:
    - they are both `+Inf`.
    - they are both `-Inf`.
    - each of them is either `-0.0` or `+0.0`.
    - they are both not `NaN` and they have the same bytes representations in memory.
4. If `T` is a complex type, then the two values are equal only if their real parts (as floating-point values) and imaginary parts (as floating-point values) are both equal.
5. If `T` is a pointer type (either safe or unsafe), then the two values are equal only if the memory addresses stored in them are equal.
6. If `T` is a channel type, the two channel values are equal if they both reference the same underlying internal channel structure value or they are both nil channels.
7. If `T` is a struct type, when comparing two struct values of the same type, each pair of their corresponding fields will be compared (in the order shown in source code). The two struct values are equal only if all of their corresponding fields are equal. The comparison stops in advance when a pair of fields is found unequal or a panic occurs. In comparisons, fields with names as the blank identifier `_` will be ignored..
8. If `T` is an array type. Most array types are comparable, except the ones whose element types are incomparable types. When comparing two array values, each pair of the corresponding elements will be compared. We can think element pairs are compared by their index order. The two array values are equal only if all of their corresponding elements are equal. The comparison stops in advance when a pair of elements is found unequal or a panic occurs
9. If `T` is an interface type,  ```
```
There are two cases of comparisons involving interface values:

1. comparisons between a non-interface value and an interface value.
2. comparisons between two interface values.

For the first case, the type of the non-interface value must implement the type (assume it is `I`) of the interface value, so the non-interface value can be converted to (boxed into) an interface value of `I`. This means a comparison between a non-interface value and an interface value can be translated to a comparison between two interface values. So below only comparisons between two interface values will be explained.

Comparing two interface values is comparing their respective dynamic types and dynamic values actually.

The steps of comparing two interface values (with the `==` operator):

1. if one of the two interface values is a nil interface value, then the comparison result is whether or not the other interface value is also a nil interface value.
2. if the dynamic types of the two interface values are two different types, then the comparison result is `false`.
3. in the case where the dynamic types of the two interface values are the same type,
    - if the same dynamic type is an [incomparable type](https://go101.org/article/value-conversions-assignments-and-comparisons.html#comparison-rules), a panic will occur.
    - otherwise, the comparison result is the result of comparing the dynamic values of the two interface values.

In short, two interface values are equal only if one of the following conditions are satisfied.

1. They are both nil interface values.
2. Their dynamic types are identical and comparable, and their dynamic values are equal to each other.
```
10. If `T` is a string type
```
Above has mentioned that comparing two strings is comparing their underlying bytes actually. Generally, Go compilers will make the following optimizations for string comparisons.

- For `==` and `!=` comparisons, if the lengths of the compared two strings are not equal, then the two strings must be also not equal (no needs to compare their bytes).
- If their underlying byte sequence pointers of the compared two strings are equal, then the comparison result is the same as comparing the lengths of the two strings.

So for two equal strings, the time complexity of comparing them depends on whether or not their underlying byte sequence pointers are equal. If the two are equal, then the time complexity is `_O_(1)`, otherwise, the time complexity is `_O_(n)`, where `n` is the length of the two strings.
```
## How to compare slices. How to compare structs with slices?
### Comparing Equality of Slices in Golang

Slices are dynamic arrays that can grow or shrink as needed. When comparing two slices in Golang, you need to compare each element of the slice separately. You can do this by using a loop or by using the "reflect.DeepEqual" function.

Slice values are deeply equal when all of the following are true:
- They are both nil or both non-nil.
- Their length is same.
- Either they have same initial entry (that is, &x[0] == &y[0]) or their corresponding elements (up to length) are deeply equal.
## How to use Boolean and comparison operators for conditional expressions?
https://www.digitalocean.com/community/tutorials/understanding-boolean-logic-in-go
## How to operate with bitmasks?
https://www.ardanlabs.com/blog/2021/04/using-bitmasks-in-go.html
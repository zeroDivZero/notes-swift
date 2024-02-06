# `while`

Executes body while condition is true. Use `continue` to skip rest of body to next loop, or `break` to early exit.

```swift
var n = 2
while n < 100 {
    n *= 2
}
```

## `repeat while`

Same as `while`, but executes body at least once before checking condition (whereas `while` won't execute body if condition fails first time).

```swift
var roll = 0
repeat {
    roll = Int.random(in: 1...20)
    print("Hit damage: \(roll)")
} while roll != 20
print("Critical hit!")
```

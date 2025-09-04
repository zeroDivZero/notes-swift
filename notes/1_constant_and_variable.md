# CONSTANT AND VARIABLE

## Initialize

```swift
var myVariable = 42
myVariable = 50
let myConstant = 42
```

Type inferred from initial value, or declare explicitly:

```swift
let explicitFloat: Float = 64.5
```

Can assign initial value later, as long as it's before first read:

```swift
var environment = "development"
let maximumNumberOfLoginAttempts: Int  // has no value yet

if environment == "development" {
    maximumNumberOfLoginAttempts = 100
} else {
    maximumNumberOfLoginAttempts = 10
}
```

## Multi-Variable

Multiple constants or variables on single line, separated by commas:

```swift
var x = 0.0, y = 0.0, z = 0.0
```

No implicit value conversion; explicitly make instance of desired type:

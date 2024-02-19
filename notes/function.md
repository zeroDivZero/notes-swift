# FUNCTION

```swift
func isAnagram(string1: String, string2: String, isCaseSensitive cs: Bool = true) -> Bool {
    let str1 = cs ? string1 : string1.lowercased()
    let str2 = cs ? string2 : string2.lowercased()
    return str1.sorted() == str2.sorted()
}
isAnagram(string1: "cinema", string2: "iceman")  // true
isAnagram(string1: "cinema", string2: "Iceman")  // false
isAnagram(string1: "cinema", string2: "Iceman", isCaseSensitive: false)  // true
```

Omit params or return type clause `-> Type` if not needed.

Also illustrates specifying **default value** for param (no need to specify explicitly at call site), and **internal** vs **external** param name (external name makes call site read better; internal name, usually more terse, makes implementation code easier to read).

Even if function doesn't have return value, can have `return` statement in body for early return (exit).

## Omit First Param Name

If first param name obvious from function name, don't need label at call site:

```swift
func cubeOf(_ val: Decimal) -> Decimal {
    return pow(val, 3)
}
cubeOf(128)  // call site reads more naturally
```

## Omit `return`

If function has return value and only 1 line of code, can omit `return`:

```swift
func cubeOf(_ val: Decimal) -> Decimal {
    pow(val, 3)
}
```

## Param Type Categories

Params are constants (implicit `let`). Class object **passed by reference** (properties can be changed). Struct (such as `Array` and `Dictionary`) **passed by value**.

## Return Multiple Values

Can return collection like `Array` or `Dictionary`, but to access value by index or key can be messy and ambiguous, and Swift can't do compile-time safety check (e.g., doesn't know how many keys in dictionary). Named tuple comes handy:

```swift
func getUserName(userId id: Int) -> (firstName: String, lastName: String) {
    // look up user by `id` and get `userRecord`
    return (userRecord.firstName, userRecord.lastName)
}

let userName = getUserName(userId: 123)
print("Name: \(userName.firstName) \(userName.lastName)")
let (firstName, lastName) = getUserName(userId: 456)  // directly extract values
print("Name: \(firstName) \(lastName)")
```

Returned tuple doesn't have to name values, but need `userName.0` and `userName.1` to access values, which also isn't clear. Sometimes verbosity leads to clarity.

## Function as Param

```swift
func hasAnyMatch(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatch(numbers, lessThanTen)
```

Functions that take or return other functions are called higher order functions. 3 powerful generic higher order functions are `map`, `filter`, and `reduce` (see `Array` for examples).

Another syntax sugar - trailing closure:

```swift
let eval = hasAnyMatch(list: [3, 5, 7]) { val in
    val >= 10
}
```

## Nested Function

Can define function inside function, but inner function's scope only within body of outer function.

## Variadic Param

Variable number of args:

```swift
func sumOf(numbers: Int...) -> Int {
    var sum = 0
    for number in numbers {
        sum += number
    }
    return sum
}
sumOf()
sumOf(numbers: 42, 597, 15)
```

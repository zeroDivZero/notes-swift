# `for in`

To iterate over sequence. Use `continue` to skip rest of body to next loop, or `break` to early exit.

## Iterate through Array

```swift
let scores = [94, 59, 100, 87, 72]
var total = 0
for score in scores {
    total += score
}
let avg = Double(total) / Double(scores.count)
```

## Iterate through Range

```swift
for i in 0..<10 {
    print(i)
}  // 0 through 9
for i in 1...10 {
    print(i)
}  // 1 through 10

let interval = 5
for tick in stride(from: 0, to: 60, by: interval) {
    print(tick)
}  // 0, 5, 10, 15 ... 45, 50, 55
// use `stride(from:through:by:)` for closed range
```

## Ignoring Looping Index

```swift
print("haters gonna hate", terminator: "")
for _ in 0..<4 {
    print(", hate", terminator: "")
}
print()
```

## Iterate through Dictionary

```swift
let record: [String: Any] = [
    "name": "Peter Parker",
    "age": 19,
    "occupation": "Photographer",
    "married": false
]
for (k, v) in record {
    print("key: \(k), value: \(v)")
}
```

Not stable; order may differ each execution.

## Iterate through String

```swift
var reverse = ""
for c in "Hello, world!" {
    reverse.insert(c, at: reverse.startIndex)
}
```

## Filter for Enum Case

```swift
enum Expense {
    case housing(Decimal), transportation(Decimal), utility(Decimal), insurance(Decimal)
}
let expenses: [Expense] = [.housing(2500),
                           .transportation(150), .transportation(30),
                           .utility(120), .utility(65), .utility(50),
                           .insurance(200)]
var totalUtilities: Decimal = 0
for case .utility(let cost) in expenses {
    totalUtilities += cost
}
// otherwise need switch statement inside loop
```

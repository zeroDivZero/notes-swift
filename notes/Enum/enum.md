# `enum`

Value type. Like class and named types, can have methods (including computed properties).

## Raw Value

```swift
enum Rank: Int {
    case ace = 1, two, three, four, five, six, seven, eight, nine, ten
    case jack, queen, king

    func simpleDescription() -> String {
        switch self {
        case .ace:
            return "ace"
        case .jack:
            return "jack"
        case .queen:
            return "queen"
        case .king:
            return "king"
        default:
            return String(rawValue)
        }
    }
}
var rank = Rank.ace
rank = .four               // abbreviated form when type is known
let raw = rank.rawValue    // 4

if let convertedRank = Rank(rawValue: 12) {
    let desc = convertedRank.simpleDescription()     // "queen"
}

enum ControlCharacter: Character {
     case tab = "\t"
     case linefeed = "\n"
     case carriageReturn = "\r"
}
```

## No Underlying Value (Case is Value Itself)

``` swift
enum CompassPoint {
     case north, south, east, west
}

let label = UILabel()
label.textAlignment = .right
```

## Initializer, Property, Associated Value

```swift
enum TrainStatus {
     case onTime, delayed(Int)     // associated value

     init() {
          self = onTime
     }

     var description: String {
          switch self {
          case onTime:
               return "on time"
          case delayed(let minutes):     // equivalent to case let delayed(minutes):
               return "delayed by \(minutes) minute(s)"
          }
     }
}
var status = TrainStatus()
print("The train is \(status.description)")         // The train is on time
status = .delayed(42)
print("The train is now \(status.description)")     // The train is now delayed by 42 minute(s)
```

## Either Type

```swift
enum Either<T1, T2> {
     case first(T1)
     case second(T2)
}
```

## Nested Types

```swift
class Train {
     enum Status {
          case onTime, delayed(Int)
          init() {
               self = onTime
          }
          var description: String { ... }
     }
     var status = Status()
}
```

## Recursive

Values stored inline, recursive enum would have infinite size. Work-around:

```swift
enum Tree<T> {
     case leaf(T)
     indirect case node(Tree, Tree)
}
```

## Implicit Protocol Conformance

If enum doesn't have associated values, compiler implicitly makes it conform to `Equitable` and `Hashable` and automatically derives required methods.

# PROPERTY WRAPPER

Separation of property definition and management. E.g., properties with thread-safety checks or have their underlying data stored in database: need to write management code on every property. With property wrapper, write code once, apply to multiple properties.

Define wrapper as struct, enum, or class with `wrappedValue` property. E.g., `TwelveOrLess` ensures value it wraps always contains number less than or equal to 12:

```swift
@propertyWrapper
struct TwelveOrLess {
    private var number = 0
    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, 12) }
    }
}
```

To apply:

```swift
struct SmallRectangle {
    @TwelveOrLess var height: Int
    @TwelveOrLess var width: Int
}

var rectangle = SmallRectangle()
print(rectangle.height) // "0"

rectangle.height = 10
print(rectangle.height) // "10"

rectangle.height = 24
print(rectangle.height) // "12"
```

When applying property wrapper, compiler synthesizes code that provides wrapper storage and property access through wrapper (wrapper responsible for property storage). Essentially, without special attribute syntax:

```swift
struct SmallRectangle {
    private var _height = TwelveOrLess()
    private var _width = TwelveOrLess()

    var height: Int {
        get { return _height.wrappedValue }
        set { _height.wrappedValue = newValue }
    }

    var width: Int {
        get { return _width.wrappedValue }
        set { _width.wrappedValue = newValue }
    }
}
```

## Initial Value

Wrapper needs to add initializer:

```swift
@propertyWrapper
struct SmallNumber {
    private var maximum: Int
    private var number: Int

    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, maximum) }
    }

    init() {
        maximum = 12
        number = 0
    }

    init(wrappedValue: Int) {
        maximum = 12
        number = min(wrappedValue, maximum)
    }

    init(wrappedValue: Int, maximum: Int) {
        self.maximum = maximum
        number = min(wrappedValue, maximum)
    }
}
```

When specifying initial value for property, Swift uses `init(wrappedValue:)` to set up wrapper:

```swift
struct UnitRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber var width: Int = 1
}

var unitRectangle = UnitRectangle()
print(unitRectangle.height, unitRectangle.width) // "1 1"
```

To specify both:

```swift
struct NarrowRectangle {
    @SmallNumber(wrappedValue: 2, maximum: 5) var height: Int
    @SmallNumber(maximum: 4) var width: Int = 3
}

var narrowRectangle = NarrowRectangle()
print(narrowRectangle.height, narrowRectangle.width) // "2 3"

narrowRectangle.height = 100
narrowRectangle.width = 100
print(narrowRectangle.height, narrowRectangle.width) // "5 4"
```

## Projected Value

Expose additional functionality by defining projected value. E.g., wrapper around database access can expose `flushDatabaseConnection()` on its projected value. Projected value name is same as wrapped value, except it begins with dollar sign (`$`). Written code can't define properties starting with `$`, so projected value never interferes with defined properties.

Code below adds `projectedValue` to keep track of whether wrapper adjusted new value for property before storing:

```swift
@propertyWrapper
struct SmallNumber {
    private var number = 0
    var projectedValue = false
    var wrappedValue: Int {
        get { return number }
        set {
            if newValue > 12 {
                number = 12
                projectedValue = true
            } else {
                number = newValue
                projectedValue = false
            }
        }
    }
}

struct SomeStructure {
    @SmallNumber var someNumber: Int
}
var someStructure = SomeStructure()

someStructure.someNumber = 4
print(someStructure.$someNumber) // "false"

someStructure.someNumber = 55
print(someStructure.$someNumber) // "true"
```

Projected value can be of any type, including `self`.

Like other properties, can omit `self.` when accessing projected value from code within type:

```swift
enum Size {
    case small, large
}

struct SizedRectangle {
    @SmallNumber var height: Int
    @SmallNumber var width: Int

    mutating func resize(to size: Size) -> Bool {
        switch size {
        case .small:
            height = 10
            width = 20
        case .large:
            height = 100
            width = 100
        }
        return $height || $width
    }
}
```

Property wrapper syntax is just syntactic sugar for property with getter and setter; accessing wrapped property behaves same as accessing any other property.

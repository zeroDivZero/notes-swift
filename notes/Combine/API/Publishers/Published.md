# `@Published`

```swift
@propertyWrapper struct Published<Value>
```

Add this attribute to create publisher of property's type, publishing value when property set with new value. Access publisher by prepending `$` to property name:

```swift
class Weather {
    @Published var temperature: Double
    init(temperature: Double) {
        self.temperature = temperature
    }
}

let weather = Weather(temperature: 20)
cancellable = weather.$temperature
    .sink() {
        print("temperature now: \($0)")
    }
weather.temperature = 25

// temperature now: 20.0
// temperature now: 25.0
```

When property changes, publishing occurs in property's `willSet` block - subscribers receive new value before set on poperty.

Class-constrained; can only be used with class properties.

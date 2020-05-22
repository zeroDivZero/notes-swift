# `struct Published<Value>`

`@propertyWrapper struct Published<Value>`

Publishing property with `@Published` creates publisher of this type. Access publisher with `$` operator:

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
        print("Temperature now: \($0)")
}
weather.temperature = 25

// Prints:
// Temperature now: 20.0
// Temperature now: 25.0
```

When property changes, publishing occurs in property's `willSet` block - subscribers receive new value before set on poperty.

Class-constrained; can only be used with class properties.

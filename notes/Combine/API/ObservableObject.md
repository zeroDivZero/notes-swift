# `protocol ObservableObject`

Type of object with publisher that emits before object has changed.

By default `ObservableObject` synthesizes `objectWillChange` publisher that notifies subscribers when object about to change because one of its `@Published` properties is about to change.

```swift
class Contact: ObservableObject {
    @Published var name: String
    @Published var age: Int

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    func haveBirthday() -> Int {
        age += 1
        return age
    }
}

let john = Contact(name: "John Appleseed", age: 24)
cancellable = john.objectWillChange
    .sink { _ in
        print("\(john.age) will change")
    }
print(john.haveBirthday())
// 24 will change
// 25
```

Must have at least 1 `@Published` property to emit any event, and only changing a `@Published` property emits event.

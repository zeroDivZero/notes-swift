# `switch`

Most powerful control flow statement. Case does not fall through (no `break` needed). Must be exhaustive (most often need `default` case).

Can match any kind of data, like `Int`, `String`, or any object:

```swift
switch sender {
case executeButton:
     print("You tapped the Execute button")
case firstNameTextField:
     print("You tapped the First Name text field")
default:
     print("You tapped some other object")
}
```

Match multiple values:

```swift
switch legCount {
case 0:
     print("It slithers and slides around")
case 1, 3, 5, 7, 9, 11, 13:
     print("It limps")
default:
     print("It walks")
}
```

Match value range:

```swift
switch legCount {
case 0:
     print("It has no legs")
case 1...8:
     print("It has a few legs")
default:
     print("It has lots of legs")
}
```

Match condition:

```swift
let vegetable = "red pepper"
switch vegetable {
case "celery":
     let vegetableComment = "Add some raisins and make ants on a log."
case "cucumber", "watercress":
     let vegetableComment = "That would make a good tea sandwich."
case let x where x.hasSuffix("pepper"):
     let vegetableComment = "Is it a spicy \(x)?"
default:
     let vegetableComment = "Everything tastes good in soup."
}
```

Explicit fallthrough:

```swift
var testVal = "hello"
var result = 0
switch(testVal) {
case "one":
     fallthrough
case "two":
     result = 1
default:
     result = 3
}
```

## `@unknown default`

Usually applicable when switching over enum from framework that may add new cases in future updates. Like plain `default`, catches all new future cases without new code, but compiler will warn when new case becomes known to remind client to handle it.

```swift
switch userNotificationsAuthorizationStatus {
case .notDetermined:
    requestPermission()
case .authorized, .denied, .provisional:
    fallthrough
@unknown default:
    print("Didn't request permission for User Notifications")
}
```

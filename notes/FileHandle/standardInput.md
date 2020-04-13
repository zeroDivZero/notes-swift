# `standardInput`

Read-only class member, returns file handle associated with standard input file (**stdin**, typically attached to keyboard).

```swift
func getInput() -> String {
    let keyboard = FileHandle.standardInput
    let inputData = keyboard.availableData  // execution waits for data before continuing
    let str = String(data: inputData, encoding: .utf8)!
    return str.trimmingCharacters(in: .newlines)
}
```
